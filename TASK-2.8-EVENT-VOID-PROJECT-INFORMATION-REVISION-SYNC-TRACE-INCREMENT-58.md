# Task 2.8 — Event Void & Project Information Revision/Sync Trace (Increment 58)
Date: 2026-11-22
Status: Bounded READ-ONLY source trace.

## Sources
EventService.js, EventDataService.js, FormService.js and ProjectInformationService.js from READ-ONLY backend production reference.

## Observations
### Event void cascade
- EventService void validates tenant, linked project visibility, event type/AllowVoid, department/admin scope and later-event restrictions; checks snapshot/current-state consistency before restoring prior project state.
- It updates project state, event status and audit records in sequential writes, recalculates workflow step status, then invokes MaterialPlanningService.markEventVoided for project-linked events.
- Material reconciliation failure is caught and reported as FAILED, with a separate reconciliation-failure audit; the event void itself remains persisted. This is deliberate recoverability signaling but not atomic cascade.
- Independent events are tenant-validated but do not have project-state restoration path.
### Project Information
- ProjectInformationService validates project, information type, tenant and optional source-event linkage; ExpectedRevision protects against concurrent edits and same event/value replay is idempotent.
- Update of current information, history append, audit and live-reference synchronization occur as separate operations; failure atomicity and cross-project sync completeness cannot be proven by source review.
### Forms/EventData
- EventDataService sampled retrieval validates event tenant and linked-project visibility, then retrieves event form data; no DB-level immutable definition snapshot proof.
- FormService sampled reusable-form mutation and event-form edit paths include tenant/event/project checks and locking, but full mutation routes, all form-response lineage and persistence atomicity remain unverified.

## V2 implications
1. Event void and project/workflow restoration should be a single DB transaction, with downstream material reconciliation in transaction or a durable outbox/workflow with explicit state and retry.
2. Avoid silently treating partial cascade as fully completed; surface durable reconciliation status and allow controlled retry.
3. Project information current value, history, audit and reference synchronization should transact or use a durable sync/outbox; retain immutable revisions.
4. Preserve ExpectedRevision optimistic concurrency and idempotent event/value replay at DB-level.
5. Event form responses should link immutable form-definition/version snapshots, with tenant-qualified relations and explicit event void/revision semantics.
6. Negative tests: failed project restore, event status write, audit, material cascade, retry, stale expected revision, sync propagation failure, cross-tenant source event, and event/form definition changes.

## Evidence limits
Bounded E1 source review only; no runtime tests, live data, DDL, migrations, provisioning, production writes or deployment. G-03/G-10/G-01 remain OPEN; schema readiness NOT APPROVED.
