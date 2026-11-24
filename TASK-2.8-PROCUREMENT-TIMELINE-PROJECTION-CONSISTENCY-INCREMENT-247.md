# Task 2.8 — Procurement Timeline Projection Consistency (Increment 247)

Date: 2026-11-24
Evidence: E1 static source.

## Scope
Read-only review of EventService._procurementTimelineEvents_ and its project-detail timeline composition.

## Findings
- Project detail timeline synthesizes purchase-order and receipt cards from procurement records, rather than storing duplicate event-sheet rows.
- Purchase-order timeline projections appear to include orders based on project allocations/project-line matching, without excluding order statuses such as CANCELLED, VOID, DELETED or NEEDS_REVIEW in the reviewed loop.
- Receipt timeline projections include every tenant receipt collection entry whose ProjectID matches the requested project; the reviewed segment does not filter receipt status.
- Project-detail event query separately filters actual event rows to exclude VOID/CANCELLED, but procurement timeline projections are appended independently and appear to bypass that same status filtering convention.
- This is a potential display/projection consistency concern only. Whether cancelled/void orders or invalid receipts are intended to remain visible historically is a business policy question; projection cards may intentionally represent historical records. No defect confirmed.
- Receipt timeline rows are filtered by receipt.ProjectID. Allocation-backed orders are shown if project allocation line(s) exist; unallocated item fallback uses item/need/order project association.
- Projection generation loads full tenant collections for orders, items, allocations, needs, receipts, then performs repeated item lookup for receipt rows. Potential performance inefficiency for large datasets, static hypothesis, not benchmarked.

## V2 implications
1. Define projection visibility/status policy for historical procurement records separately from current-state totals.
2. Preserve history but represent cancelled/void status distinctly rather than ambiguously using SUBMITTED.
3. Ensure project receipt timeline inclusion is consistent with receipt invalidation semantics, while retaining historical visibility where required.
4. Replace full collection scans with tenant/project indexed queries in PostgreSQL and a projection/query layer.
5. Add contract tests for cancelled/needs-review orders, invalidated receipts, allocations across projects, unallocated order lines, sorting/pagination, tenant isolation and large project histories.

## Verification
Static only; no tests or benchmarks run. No production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
