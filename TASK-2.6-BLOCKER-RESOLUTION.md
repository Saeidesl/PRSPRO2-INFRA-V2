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


### Increment 2 — projection/report/deletion semantics (2026-11-22)

Additional bounded source review of ProcurementService dashboard/reports/listOrders/workspace, ProcurementRepository, and ProjectDeletionService dependency collection.

Evidence:
- Procurement workspace composes needs, visible orders and dashboard. Report dataset includes needs, orders, projects, material specs and calculations.
- Dashboard aggregates receipt quantities by PurchaseOrderItemID, then caps displayed received quantity at ordered quantity and computes pending as max(ordered-received,0). Surplus is separately aggregated. In the sampled dashboard path, receipt aggregation does not visibly filter receipt status; this requires explicit confirmation against status conventions and other consumers before V2 semantic equivalence is defined.
- Report/workspace and visibility logic can omit orders associated with inaccessible projects; order visibility is evaluated across linked projects. Shared order/item allocations therefore have cross-project visibility and dependency consequences.
- Project deletion collector links procurement to project material needs and allocations. Shared order lines are preserved where another project's allocation remains. Orders are cascade candidates only when all active lines are project-linked and no other allocation remains. Receipt collection also includes links via item, project or order. Soft-delete/cascade writes and Drive trash operations are multi-step, not a demonstrated transaction.
- The sampled service exposes create-order, receipt create and batch receipt, list/workspace/reports/dashboard. No explicit order-cancel, receipt-void, allocation-adjustment or reversal API appears in the sampled ProcurementService function inventory. This is bounded negative evidence, not proof no such path exists elsewhere.

### Increment 2 decisions and unresolved G-02 items
- V2 projections must define receipt-status eligibility centrally, with one shared calculation rule used by dashboard, workspace and reports.
- Receipts, allocation changes and cancellations must not be physically removed when they affect shared orders or reports; reversals/corrections should be linked records and the projection should derive net eligible quantities.
- Project deletion should be a policy-driven archive/retention operation with a dependency preview that includes shared orders, allocations, receipts and retained audit/history. A project delete must not erase fulfillment history belonging to another project.
- G-02 still BLOCKER: over-receipt, surplus, item/order state transitions, receipt correction and allocation rebalancing require further coverage and policy decisions.

No tests, live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 3 — receipt batch invariants and API surface (2026-11-22)

Reviewed batch-receipt implementation, Router procurement read endpoints and existing test files (tests were read, not executed).

Evidence:
- Batch receipts normalize and validate all entries before the insertMany call; duplicate idempotency keys within a batch are rejected, and previously recorded keys are replayed.
- Receipt batch enforces item ordered-quantity ceiling, allocation-to-project limits and unallocated general-warehouse limits. It rejects receipts against CANCELLED orders; NEEDS_REVIEW orders are treated specially during order status recalculation.
- Batch inserts receipt rows, recalculates order status (ACTIVE/PARTIALLY_RECEIVED/RECEIVED) based on aggregated quantities, then audits each receipt. These steps are multi-write and not demonstrated atomic. Status recalculation is skipped for NEEDS_REVIEW orders.
- Single receipt and batch receipt paths have slightly different implementation details and replay handling; both require a shared canonical policy in V2.
- Router sampled read routes expose workspace, reports, dashboard, needs and orders under permission checks. EventType procurement settings route is separate. Procurement create/receipt write endpoints were not fully enumerated in this increment, so route coverage remains pending.
- Existing tests assert idempotency/quantity allocation behavior, workspace caching and event integration separation, but were not run. Test presence is evidence of intended legacy behavior, not a successful test result.

### V2 design direction refined
- Receipt posting should validate and post in one DB transaction; order status should be a derived projection or updated within the same transaction.
- The same receipt eligibility, allocation and idempotency rules must apply to single and batch paths.
- Order lifecycle states should be explicit and mutually coherent, with NEEDS_REVIEW treated as a controlled state rather than a generic active/inactive flag.
- Implement reconciliation for status/quantity drift and preserve audit links for all corrections.

G-02 remains BLOCKER: correction/reversal paths, allocation mutations, route write surface, surplus/over-receipt policies and report semantics still require source coverage.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.
