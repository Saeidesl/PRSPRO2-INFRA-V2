# TASK-2.8-PROCUREMENT-ORDER-RECEIPT-ATOMICITY-AND-ALLOCATION-INCREMENT-90

Date: 2026-11-23
Evidence: E1 source inspection
Reference: PRSPRO2BACKEND-PRODUCTION/ProcurementService.js SHA c0e16aa769cd88f85ba8d65ce12e093b636accf0
No runtime tests, production writes, migrations or deployment.

## Positive controls observed
- Project and source-event validation checks tenant and project visibility.
- createOrder and receipt entry points use ScriptLock.
- Order creation checks tenant-scoped IdempotencyKey replay.
- Receipt create and batch processing include idempotency/replay logic.
- Receipt validation checks tenant-owned item/order, project and source-event, quantity against order, and allocated project shares / unallocated balance.
- Batch receipt processing validates the batch before insertMany of receipts, then updates order states and writes audit entries.
- Order-item allocations are validated for tenant project/need consistency and aggregate allocated quantity <= item quantity.
- On partial order/item/allocation write failure, order is retained and marked NEEDS_REVIEW with explanatory note rather than hidden or deleted.

## Failure boundaries
- Order, items, allocations and audit are separate sheet writes. Compensation marks order NEEDS_REVIEW, but successful earlier item/allocation writes remain. Repair/replay semantics require investigation.
- Receipt batch inserts receipt records as one sheet batch, then updates order statuses and audit sequentially; a later status/audit failure can leave records persisted with partial follow-up. No transaction or durable outbox verified.
- Idempotency keys are sheet-backed lookup, no uniqueness constraint or atomic database index proved.
- ScriptLock reduces concurrent execution within the GAS lock scope but is not equivalent to DB transaction/unique constraint.

## V2 design requirements
- Transactional purchase order + line + allocation creation; DB constraints for tenant, need/project coherence, quantities and uniqueness.
- Unique idempotency indexes scoped by tenant/operation.
- Receipt batch atomically records receipts, updates order aggregate/status, and creates audit/outbox entries.
- Reconciliation for partial legacy operation; immutable receipt history and explicit void/reversal.
- Test concurrency, duplicate replay, batch fault injection, cross-tenant negative cases, allocation ceiling and partial-write recovery.

E1 only; no tests. G-01/G-02/G-03/G-10 remain open/blocking. No production changes.
