# Task 2.8 — Project/Event/Information/Material/Attachment boundary trace (Increment 70)

Date: 2026-11-23
Evidence level: E1 bounded read-only source review. No runtime validation.

## Project and event observations
- ProjectService sampled paths derive TenantID from authenticated user, validate project/workflow/status/department relations, and use locks in project-code update and project-copy paths. Project-copy includes source information/attachment planning and reference-sync policies; full atomicity of the multi-entity copy is not established.
- EventService sampled read paths tenant-filter events and enrichment dependencies. Event mutation/update path uses a script lock and optimistic UpdatedAt check, verifies project/event/type tenant relationships and logs updates. Creation/idempotency path uses a user lock in the excerpt, apparently to avoid nested script-lock conflict with procurement integration; full lifecycle/concurrency review remains open.
- EventDataService sampled entry points validate event and project tenant association.

## Project information and reference synchronization
- ProjectInformationService validates project, information type and event association tenant boundaries and validates value payloads against columns.
- Save path supports expected-revision conflict checks and same-event/value replay avoidance, increments revisions, writes current record/history/audit and then performs live-reference synchronization. These sequential writes are not proven atomic; no runtime concurrency test was performed.
- Approval, conflict-resolution and live-reference synchronization paths use revision and sync-log comparisons, idempotency keys and conflict records. Sync log/current/history/audit writes are sequential in inspected excerpts.

## Material planning
- MaterialPlanningService uses script locks in selected mutation/recalculation/void-reconciliation paths, versioned specs and calculation history, idempotency keys, and tenant checks for source events/specs/needs.
- Spec revision and marking prior revision operations are separate writes in sampled code; calculation and needs records plus audit are also multi-step. Runtime integrity under interruption/concurrency is unverified.

## Attachment and audit boundaries
- AttachmentService sampled paths validate tenant ownership and entity/project/event/type/status constraints and perform attachment create/delete/copy operations with separate storage metadata/audit side effects. No atomic storage recovery/reconciliation proof.
- AuditService sampled read methods retrieve repository collections then filter by tenant at service layer; source review does not establish repository-level query isolation, performance, or complete authorization.

## V2 requirements
- Model Project/Event/EventData/ProjectInformation/history/approval/sync/material-spec/calculation/need/attachment/audit relationships with tenant-aware FK/unique constraints and transaction boundaries.
- Use optimistic concurrency tokens and explicit transaction isolation/locking for revision, workflow transition, material recalculation, receipt allocation and event mutation; define retry semantics.
- Design copy operations as transaction-backed metadata graph copy with staged external file copy and durable reconciliation.
- Audit should be transactional or durable-outbox; immutable history and policy for revisions, voids, approvals, conflict resolution and reference synchronization must be explicit.
- Attachment storage should use staged upload/delete, DB metadata transaction, outbox/reconciliation, retention, restore and access tests.
- Cross-tenant negative tests must cover all association paths, copy/sync paths, file access, event/project relations, audit read/write and workflow/material rules.

## Limits and gaps
- Bounded source excerpts, E1 only; no runtime/integration/concurrency/recovery tests.
- G-01/G-10 remain OPEN/BLOCKER; G-02/G-03/G-04 remain OPEN/HIGH. Schema/migration readiness NOT APPROVED.
- No production changes, live data, DDL, migration, deployment or provisioning.
