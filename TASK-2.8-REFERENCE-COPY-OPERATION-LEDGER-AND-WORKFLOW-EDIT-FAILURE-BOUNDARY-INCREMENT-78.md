# TASK-2.8-REFERENCE-COPY-OPERATION-LEDGER-AND-WORKFLOW-EDIT-FAILURE-BOUNDARY-INCREMENT-78

Date: 2026-11-23
Evidence level: E1 source inspection
Reference: PRSPRO2BACKEND-PRODUCTION
No production writes, tests, live data, migrations or deployment.

## Project reference-copy operation
- ProjectService stores a ReferenceCopyOperationID on source-derived project creation and searches tenant project records for a prior operation to return a replay/recovery response.
- Copy planning is persisted as ReferenceCopyStatusJSON items and completion timestamps; each information-type copy is processed under lock and records per-item status.
- Image copying is scoped/validated in inspected helper excerpts, while item-level failures are recorded as FAILED and state is updated for retry.
- These are application-level recovery and replay patterns; atomic aggregate creation, full durable operation ledger, storage rollback, audit transaction, and replay collision/expiry policy are not established.

## Workflow edit path
- WorkflowService excerpts show DraftVersion selection, cloning published rules to a draft version, lifecycle transition to DRAFT, version-scoped validation and rule replacement.
- Several mutations are sequenced across repository calls. Atomicity, durable publish ledger, complete clone rollback and concurrent edit handling were not verified.

## V2 implications
1. A dedicated durable operation table with unique tenant-scoped idempotency/operation identity, lifecycle, checkpoints, failure and retry state.
2. Project copy transaction for DB-local aggregate and outbox for storage copy; validate all source/target associations.
3. Transactional workflow draft creation and rule replacement, published version immutability, unique version enforcement.
4. Retry/recovery and fault-injection tests at every operation checkpoint; cross-tenant replay rejection.
5. Coverage reconciliation remains partial; G-01 blocker and G-10 blocker remain.

Source evidence only. No runtime test or implementation; schema readiness NOT APPROVED.
