# Task 2.6 — Resolve Discovery Blockers Before Schema Design

Status: IN PROGRESS (started 2026-11-22)

Purpose: resolve critical domain contracts and discovery blockers from Task 2.5 before any physical schema design. Work is read-only against legacy repositories. Decisions below are V2 design recommendations derived from sampled source evidence, not approved business policy unless explicitly marked.

## Workstream 1 — Procurement correction/reversal contract (G-02)

### Increment 1 — bounded source review (2026-11-22)
Reviewed legacy ProcurementService createOrder, receipt creation and batch receipt paths, plus ProcurementEventIntegrationService save/validate/apply and ProcurementRepository context.

Evidence:
- Order creation uses lock/idempotency replay, tenant/project/need validation, order-item normalization and allocation-related calculations; writes span multiple sheets and are not shown to be transactionally atomic.
- Existing orders/items/allocations influence ordered-quantity projections. Statuses CANCELLED, VOID, DELETED and NEEDS_REVIEW are excluded from certain allocation/projection calculations, showing status semantics affect derived quantities.
- Receipt creation and batch receipt paths have idempotency checks, tenant/order/item/project validations, allocation constraints and source-event association. The sampled paths are write-oriented and their full write/reversal/status semantics require further source review.
- Procurement event integration currently maps a configured event to confirmed-quantity recalculation. Sampled comments/code explicitly treat legacy receipt mappings as inert and Receipt APIs as independent. Applying integration recalculates project material needs from confirmed quantity.
- No general correction/reversal contract for order, item, allocation or receipt was proven in the bounded sample. Existing partial compensation/review status semantics must not be assumed to be accounting-grade reversals.

### V2 recommendation — proposed contract pending further discovery
1. Persist procurement actions as auditable domain operations; distinguish cancellation before fulfillment from correction after fulfillment.
2. Do not physically delete procurement orders/items/allocations/receipts once they affect fulfillment or reporting. Use explicit lifecycle states and compensating records/adjustments.
3. Receipts should be immutable ledger-like records after posting; correction should be a linked reversal/adjustment, preserving original record, reason, actor, timestamp and references.
4. Order/item cancellation should be allowed only when received/allocated/fulfilled quantities and downstream links are reconciled; otherwise require controlled adjustment or review workflow.
5. Derive quantities from valid status-scoped operations and enforce invariants within database transactions; never rely on cached spreadsheet projections as correctness source.
6. Idempotency keys must be tenant-scoped, operation-scoped and backed by uniqueness constraints; retries should return the prior operation.
7. Maintain audit and reconciliation reports for discrepancies and partial legacy migration states.

Open questions requiring additional source coverage before finalizing: exact receipt void/deletion paths, allocation creation/reassignment/adjustment, order status transitions, over-receipt/surplus policy, procurement event dependencies, reports/dashboard formulas, and project deletion behavior for all procurement states.

No tests, live data, SQL/DDL, migration, DB provisioning, production modification or deployment.
