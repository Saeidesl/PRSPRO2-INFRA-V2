# Task 2.8 — Event Void and Project Reference-Copy Reconciliation (Increment 75)

Date: 2026-11-23
Evidence: E1 bounded source inspection; not runtime verified.

## Sources
- EventService.js SHA 1fe31ea9ea2a048a870ecbc47c11512bc2942108
- ProjectService.js SHA 5c9b5bb3f79a2f85472d2cc2ec3d8c3d1e31321f

## Event void path observations
- Validates event tenant, linked-project visibility, event-type tenant and AllowVoid, department/admin role, and blocks void when a later event exists in another department.
- Uses configured changes and previous-state snapshots to restore project state, then updates event and writes audit.
- Material-needs reconciliation is attempted afterward. A failure is captured and additional audit attempted, returning a FAILED reconciliation state rather than guaranteeing atomic rollback.
- The path spans project/event/material/audit state; atomicity, retry/replay safety and fault recovery are not established by source inspection.

## Reference-copy observations
- Project creation supports ReferenceCopyOperationID replay lookup within tenant.
- Reference-copy image path validates source attachment tenant, project ownership and image MIME type, then copies the project image.
- Copy-information-type path is exposed and performs source/target context validation (bounded code sample); aggregate operation durability and full copy dependency set not fully audited.
- Operation-state fields on Projects indicate planned/progress/recovery state, but persistence/recovery guarantees remain unverified.

## V2 requirements
- Void should be one transactional state transition plus durable reconciliation outbox, with retry-safe idempotency and material recalculation ledger.
- Copy should be persisted as an operation with explicit dependency plan, checkpoints, retry/reconciliation, tenant checks and transactional target metadata; file copying via outbox/staged storage.
- Include crash/failure tests between event/project update, audit, material reconciliation, source/target info, attachment copy and operation completion.

## Status
E1 only. No runtime test execution, migrations, data changes or production changes. G-01/G-10 remain blockers; G-02/G-03/G-04 open/high; schema readiness NOT APPROVED.