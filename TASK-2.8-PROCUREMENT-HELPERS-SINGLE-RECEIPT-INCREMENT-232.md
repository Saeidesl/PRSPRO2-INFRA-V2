# Task 2.8 Increment 232 — Procurement helper and single-receipt audit (2026-11-24)

## Scope
Read-only source tracing of ProcurementService helper validation, repository tenancy filtering and single receipt vs batch behavior. No production edits and no tests run.

## Findings
- _validateProject_ resolves project by ID and requires TenantID equality and ProjectService availability-to-user.
- _validateEvent_ resolves event by ID and requires TenantID equality; when event belongs to a project, it also checks project visibility via service helper.
- ProcurementRepository uses getTenantRows; each collection filters rows by TenantID.
- Single receipt wrapper acquires script lock, checks IdempotencyKey within tenant-scoped receipts, and releases lock in finally.
- _createReceiptUnlocked_ has SourceEventID+item replay check (excluding VOID), validates item/order TenantID and cancelled order, project and allocations, quantity against order, then validates source event before insert. Order status is recomputed after insert and audit is logged.
- Single-receipt allocation checks project allocation and per-project received quantity. Batch path handles allocated and unallocated quantities separately.

## Potential inconsistency to validate (not confirmed defect)
The single receipt and batch paths' aggregate quantity calculations visibly sum receipt rows by item without a clearly consistent Status filter in the examined snippets, whereas SourceEvent replay explicitly excludes VOID. Downstream listNeeds logic appears to have special countability filters for order status and likely receipt filtering. This could lead to different treatment of voided receipt rows across operations. Need trace void semantics/schema and compare every aggregate before declaring defect.
- Both single and batch inserts occur before order-status update and audit, so possible partial-write failure/recovery exposure remains.
- Batch operation's idempotency replay and duplicate-key handling were traced earlier.

## Completion limitations
- No runtime tests, failure-injection, access-controlled test tenant or data sandbox available in current context.
- Void/reversal path and status-repair mechanism not yet located; cross-path totals reconciliation still open.
- Task 2.8 remains incomplete, G-01/G-10 open, schema readiness NOT APPROVED.

## Next increment
Increment 233: search repository/source for VOID, reversal/receipt cancellation and order-status recomputation/repair; trace full listNeeds count logic and reconcile aggregates.
