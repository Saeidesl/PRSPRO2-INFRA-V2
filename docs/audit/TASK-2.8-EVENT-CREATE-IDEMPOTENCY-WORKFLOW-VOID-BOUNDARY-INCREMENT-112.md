# Task 2.8 Increment 112 — Event creation, idempotency, workflow and void lifecycle (2026-11-23)

## Scope and evidence
READ-ONLY inspection of EventService.js, EventRepository.js and EventValidator.js. Trace includes create, form-response integration, project workflow/status changes, idempotency markers, void guards (bounded excerpt) and event listing/timeline projections.

## Observations (E1 source-only)
- Event create validates event payload, event type tenant/status, project availability and workflow validity for project-bound events, draft restrictions, department/user tenant and department policy; independent event types can omit ProjectID based on ConnectionType.
- Admin and non-admin PerformedBy handling differs; non-admins use their own ID and department access is checked.
- Event payload variant rows are capped at 500, duplicate color/size combinations rejected; quantities are validated as positive integers when event type tracks quantity.
- Form responses are prepared/validated before event insert and saved after event insertion; event creation, responses, procurement integration, project mutation and audit are sequential and not shown as one transaction.
- Procurement integration failure after event persistence is caught and recorded in a failure audit; event is not rolled back.
- Idempotency key is optional. With key, user lock serializes duplicate lookup/create, key scope is tenant + creator + key, key capped at 120 chars; prior PROCESSING blocks replay, completed replay returns stored event and response summary. The insert is marked PROCESSING and becomes COMPLETED after persistence/integration/project/audit sequence. Storage-level unique key and recovery of stuck PROCESSING requests are not demonstrated; events without key retain non-idempotent behavior.
- Project revalidation occurs in repository beforeWrite callback under the repository ScriptLock for project events, including project step revalidation. Lock boundaries and transaction coupling to later operations are not demonstrated.
- Event-type version and workflow version/step snapshot fields are stored in event record; event-type versioning behavior/immutability not fully audited in this increment.
- Event void guards tenant/project access, event status, event type tenant and AllowVoid, department permission, and later events in other departments. Project restoration relies on expected changes and stored previous-state snapshot in continuation; full rollback behavior was not fully retrieved/verified.
- EventRepository stores event sheet data; project event listing uses whole-sheet reads and sorts, while detail-page path paginates to 30 records but also reads EventData/EventField collections for projections. No DB index or runtime performance test.
- Procurement timeline rows are synthesized from source procurement records, not persisted duplicate event rows, in the inspected path.
- EventValidator validates create/update payload shape and status enum; service-level contextual validations remain central.

## V2 implications
- Event creation as transaction boundary including event, form data, project changes, procurement integration/outbox and audit; isolate best-effort notification.
- Idempotency key persisted with DB unique constraint (tenant/user or policy-approved scope), durable processing state, retry/recovery and expiration policy.
- Preserve event type/workflow snapshots and version semantics; define immutability/retention.
- Event void/reversal must be explicit domain operation with dependency ordering and atomic state restoration, history/audit.
- DB query/index design for project timelines and event-data joins; pagination and batch projections.
- Test partial failures at each stage, concurrent duplicate request, stuck PROCESSING recovery, tenant crossing, draft event constraints, independent events, void after downstream event, quantity duplicates, workflow race, procurement integration and audit failure.

## Verification
E1 source-only; no runtime tests executed; no production changes. Transactional and recovery concerns are not confirmed defects. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 blockers remain.
