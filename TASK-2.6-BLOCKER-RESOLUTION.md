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


### Increment 4 — order creation recovery and single-receipt parity (2026-11-22)

Reviewed remaining order creation writes, single receipt writes and Router write-path inventory.

Evidence:
- Order creation validates/normalizes full payload before writing, then inserts order, each item and its allocations sequentially. On failure after header insertion, it attempts to mark the order NEEDS_REVIEW and adds a failure note rather than deleting partial records. This is a compensating recovery marker, not atomic transaction rollback; update failure/recovery guarantees remain unproven.
- Order creation allocations are derived from need quantity, prior counted allocations, surplus and explicit allocation payload; total allocation is validated against ordered quantity. Need/project tenant and visibility validation is applied in sampled code.
- Single receipt checks replay by source-event/item and idempotency key at outer method, then validates project/item/order/allocation and ordered quantity, inserts receipt, updates order status and audit. These writes are sequential and no atomic transaction is demonstrated. The single path's quantity aggregation includes prior receipt rows without a clearly demonstrated common status filter, unlike the replay predicate that excludes VOID in one branch.
- Router inventory confirms write service calls for createOrder, createReceipt and createReceiptsBatch; sampled read endpoints include workspace, reports, dashboard, needs and orders. Route-level write permissions were not fully captured from this limited excerpt and remain to verify in later security/tenant work.

### G-02 contract consolidation — still provisional
- Legacy failure recovery leaves order header NEEDS_REVIEW when item/allocation write sequence fails; V2 should transactionally prevent partial graph writes, while maintaining a durable failed-operation/reconciliation record when external side effects are involved.
- Single and batch receipt flows must share identical status eligibility and validation policy. Replay by source-event and idempotency key must be standardized and DB-enforced.
- Status drift and partial legacy graph recovery are explicit migration/reconciliation cases; not a reason to silently discard records.

G-02 remains BLOCKER until receipt corrections/reversals, allocation mutation/deallocation, over-receipt/surplus, lifecycle states and dependency policies are sufficiently discovered and decision-ready.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 5 — lifecycle surface inventory and allocation/surplus semantics (2026-11-22)

Read-only source inventory of ProcurementService public method list, ProcurementRepository, event-integration service and test-source assertions.

Evidence:
- ProcurementService public method inventory includes createOrder, createReceipt, createReceiptsBatch, listNeeds, listOrders, workspace, reports and dashboard; no allocation-update/deallocation, order-cancel, receipt-void or receipt-adjustment public method appeared in the sampled service inventory.
- ProcurementRepository is read-only collection access for needs, orders, items, allocations and receipts; it exposes no dedicated mutation/lifecycle API. Mutation logic is embedded in ProcurementService.
- Existing test source includes allocation cap/surplus behavior: cumulative order allocations against a need are capped at requested quantity and remaining ordered quantity is recorded as surplus. Estimated and confirmed need records share allocation key semantics for existing procurement.
- Tests are only inspected, not executed. These are bounded observations, not complete proof that no lifecycle operations exist in another file or indirect route.

### Proposed procurement lifecycle matrix — provisional
- Order: DRAFT/ACTIVE -> PARTIALLY_RECEIVED -> RECEIVED; CANCELLED allowed only before posted receipt or after explicit reconciled adjustment; NEEDS_REVIEW is an operational exception state, not an ordinary lifecycle transition. Confirm whether draft state exists in product requirements before adopting.
- Receipt: POSTED (immutable) -> linked REVERSAL/ADJUSTMENT; no silent deletion or overwrite after posting. Void semantics, if allowed before posting, need an explicit state and reason.
- Allocation: explicit allocate/reallocate/release operations with quantity invariant: active allocation sum <= order-line quantity; project-specific received quantity <= active allocation quantity unless approved surplus/exception path. Release/reallocation must preserve receipt history and reconcile downstream needs.
- Surplus: separate unallocated quantity from need-allocated quantity. Do not treat surplus as negative need or silently alter material requirements. Confirm warehouse/general procurement policy and how surplus can be allocated later.
- NEEDS_REVIEW: controlled repair/reconciliation workflow with reason, actor, timestamp, prior/current snapshots and resolution record.

### G-02 resolution status
G-02 is NOT resolved. The source inventory suggests the legacy service primarily supports creation and read projections; corrective lifecycle contracts and operational policy remain insufficiently discovered. Next steps should inspect project/business requirements, downstream event/report consumers, and any deletion/void/adjustment routes in other services before treating the matrix as finalized.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 6 — cross-service consumers and project deletion retention review (2026-11-22)

Reviewed MaterialPlanningService method inventory, ProcurementEventIntegrationService and ProjectDeletionService collector/preview context.

Evidence:
- MaterialPlanningService versioned specification and calculation-history functions create/revise specs, record/recalculate needs and mark source-event voids. Its header and sampled logic intentionally leave existing orders/receipts intact; calculation updates can be linked to material-need records and source event status.
- Procurement event integration explicitly treats receipt mappings as inert and Receipt APIs independent; its active integration recalculates confirmed material needs from project central information. V2 should not couple event integration directly to posted receipt ledger.
- Project deletion dependency collection includes events/event-data, needs, order/items, allocations, receipts, specs, calculations and attachments. It is allocation-aware for shared procurement items/orders and receipt association through item/project/order. However, delete collection is a legacy destructive cascade, not a universal retention policy.
- A project-delete cascade should not be copied literally into V2 for procurement history: V2 should preserve posted fulfillment and audit records, anonymize/archive project data only under approved retention policy, and use referential restrict/soft-archive or explicit retention-aware workflow when linked procurement history exists.

### G-02 decision refinement
- Procurement needs are recalculated from central project information/event integration, but posted receipts must remain independent from need recalculation. Changes to needs should not automatically reverse received quantities.
- Need/spec/calculation revisions and procurement allocations are separate concepts; preserve their linkage without treating a revised need as an order/receipt mutation.
- Project deletion and order deletion are not interchangeable; fulfillment records must survive project archival/deletion policy.

G-02 remains BLOCKER. The lifecycle contract is now sufficiently drafted for internal technical design but requires remaining discovery (write routes/other consumers and report calculations) and final consistency review before this gap can be marked resolved.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.
