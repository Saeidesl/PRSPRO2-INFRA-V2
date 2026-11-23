# TASK-2.8-EVENT-VOID-LIFECYCLE-RESTORE-AUDIT-AND-MATERIAL-RECONCILIATION-INCREMENT-91

Date: 2026-11-23
Evidence: E1 source inspection
Reference: PRSPRO2BACKEND-PRODUCTION/EventService.js SHA 1fe31ea9ea2a048a870ecbc47c11512bc2942108
No runtime tests, production writes, migrations or deployment.

## Void controls observed
- Validates user, event tenant and linked-project visibility.
- Rejects already-VOID event.
- Validates event type tenant and AllowVoid setting.
- Non-admin caller must match event department.
- Rejects void when later cross-department event exists.
- Compares expected project changes against current state and snapshot before restoring.
- Restores project lifecycle/department state from prior snapshot, then updates event status VOID.
- Recomputes workflow step status when relevant.
- Writes audit records for project restoration, step-status recalculation and event void.
- Calls material planning reconciliation after event void; failures are caught and recorded as FAILED with additional audit.

## Failure boundaries
- Project restore, event status update, multiple audit writes, step-status update and material reconciliation are sequential, not transactionally grouped.
- Event may be marked VOID while material reconciliation fails; response surfaces status and audit attempts.
- Audit failure at an earlier stage may interrupt sequence. No transactional event lifecycle or durable reconciliation queue proved.
- Snapshot consistency checks mitigate incorrect restore but no concurrency lock or compare-and-swap transaction proved in this sampled path.

## V2 requirements
- Transactional void + project restore + audit/outbox.
- Versioned snapshot and optimistic concurrency constraint.
- Durable reconciliation state and retry workflow for material ledger.
- Explicit immutable void/reversal records.
- Fault-injection tests at each step and concurrent void/update tests.

E1 only; no runtime tests. G-01/G-03/G-10 remain open/blocking as applicable. No production changes.
