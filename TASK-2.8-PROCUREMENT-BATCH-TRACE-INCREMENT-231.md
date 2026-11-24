# Task 2.8 Increment 231 — Procurement batch receipt integrity tracing (2026-11-24)

## Source reviewed
READ-ONLY ProcurementService.js createReceiptsBatch, frontend procurement.js caller, Router.js dispatch. No production changes; no tests executed.

## Observed controls
- Script lock acquisition is attempted before the batch receipt processing and released in finally.
- User TenantID is resolved via service _user_.
- Existing receipts and allocations are queried tenant-scoped.
- Existing receipt IdempotencyKeys are used for replay detection; duplicates within the incoming batch are rejected.
- Item and order lookups check TenantID; cancelled orders are rejected.
- Project and source event validation methods are invoked for each entry.
- Total quantity checks consider existing receipts and current batch planned quantities against ordered quantity.
- Allocation logic distinguishes allocated project portions from unallocated general stock and rejects excess quantities.
- Batch receipt rows are inserted together; order statuses are then recalculated; audit entries are logged for created receipts.
- Replay responses return existing keyed receipts, and batch lock is released in finally.

## Potential transactional gap (not confirmed defect)
The batch write is followed by order-status updates and audit logging. The source uses Sheets operations, not a database transaction visible in this method. Failure injection is needed to determine partial-write exposure (receipt rows inserted but order updates/audit incomplete), retry/reconciliation behavior, and whether the current design has compensating recovery. Do not assume atomicity.

## Crosswalk gap
Exact tenant-safe semantics of _validateProject_ and _validateEvent_, repository consistency, duplicate-key tenant uniqueness, write failure handling, and receipt/order status repair are not yet fully traced. Single-receipt path also requires comparison with batch semantics.

## Readiness
No runtime tests executed. Task 2.8 remains incomplete; G-01/G-10 open; schema readiness NOT APPROVED.

## Next
Increment 232: trace procurement validation helpers, repository methods, single-receipt path, and status recomputation/repair behavior.
