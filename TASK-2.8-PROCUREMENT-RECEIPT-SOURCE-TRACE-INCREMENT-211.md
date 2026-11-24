# Task 2.8 Increment 211 — Procurement receipt source-trace checkpoint (single/batch)

Date: 2026-11-24

## Source
Read-only trace of `ProcurementService.js` blob `c0e16aa769cd88f85ba8d65ce12e093b636accf0`.

## Single receipt operation — observations
- `createReceipt` acquires ScriptLock with a 10-second attempt, then releases in finally.
- Tenant-scoped user resolution occurs inside the locked path.
- Optional request IdempotencyKey replay lookup is performed against tenant receipts.
- SourceEventID replay check also exists in unlocked receipt path for same item/event where receipt status is not VOID.
- Purchase order item and parent order are checked for TenantID; cancelled parent is rejected.
- Project validation and event validation occur.
- Allocation-linked receipts require project selection and enforce allocated-to-project cumulative quantity; unallocated items validate expected project match.
- Cumulative prior receipt quantity is compared to ordered quantity; over-receipt is rejected in this path.
- Receipt row is inserted, order status projection recalculated and updated, then AuditService.log is called.
- The visible sequence is multiple writes (receipt, order projection update, audit). No database transaction primitive is visible in this traced path. This supports a recovery/atomicity question, not a runtime failure claim.

## Batch receipt operation — observations
- `createReceiptsBatch` also acquires ScriptLock.
- Tenant-scoped receipt collection is used for replay lookups; per-entry idempotency keys and duplicate keys within the request are checked.
- Entries are validated for item/order tenant ownership, active order, project/event validation, allocation and cumulative receipt limits (source trace within the method).
- The batch path performs processing and write/projection work sequentially in the method; atomic rollback capability is not demonstrated by this source path.
- Batch and single receipt paths share some validation concepts but are not proven behaviorally equivalent. Specific parity and partial-failure recovery tests remain needed.

## Decision/policy implications (no silent policy adoption)
- Existing source appears to reject quantity above ordered quantity in receipt paths; PO policy decision about allowed over-receipt remains unresolved because the business policy must reconcile with implementation and correction handling.
- Receipt correction/reversal, surplus handling and cancellation semantics remain PO decision items.
- The potential multi-write partial-failure surface reinforces the proposed canonical receipt ledger, transaction/outbox/reconciliation and explicit correction design for V2; this is a design recommendation, not a change to production.

## Evidence gaps
No test execution or fault-injection. Audit logging sequence, projection recovery, source-event replay semantics, batch/single parity, cross-project allocation boundaries and tenant negative tests remain unverified. G-02/G-10/G-01 remain OPEN.

## Safety
Read-only source trace; no production modification, test execution, DDL, migration, live data, VPS or deployment.
