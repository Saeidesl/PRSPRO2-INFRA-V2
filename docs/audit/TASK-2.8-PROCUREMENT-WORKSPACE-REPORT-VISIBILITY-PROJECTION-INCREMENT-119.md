# Task 2.8 Increment 119 — Procurement workspace, reporting and visibility projections

Date: 2026-11-23  
Evidence level: E1 — read-only source inspection  
Source: ProcurementService.js in legacy backend (READ-ONLY)

## Findings
- listOrders constructs order-item-allocation-receipt projections, computes associated project IDs per item/order, and includes allocation/receipt details. Visibility is checked against every project linked to the order; an order is omitted entirely if any linked project is not visible to the user, preventing mixed-project order details from leaking through this projection.
- listNeeds calculates order/receipt quantities and derived procurement status using tenant-scoped rows and project visibility. Its projected quantities are capped or normalized for UI display, and legacy line-without-allocation fallback is included.
- workspace combines summary, needs and orders into one response and reuses request-scoped collection caches and a shared project visibility cache.
- reports assembles an on-demand dataset of visible projects, material needs, orders and material calculation ledger rows, enriches project/spec labels, and exposes it for frontend filtering/export. The report path uses tenant filters and project visibility for the project list, needs/orders, and calculation rows.
- dashboard aggregates quantities and counts from the visible orders/items/receipts and visible needs, so hidden project records should not contribute to its totals based on inspected source.
- These projections are computed in application code from multiple sheet collections and relationships. Their consistency under concurrent writes, exact visibility behavior in every edge case, and runtime data-leak behavior are not verified here.

## V2 implications
1. Define row-level/project visibility as a domain policy applied consistently to reads, reporting, aggregates and nested multi-project order views.
2. For mixed-project orders, prefer permission-filtered projection behavior (whole-order suppression or explicit item-level projection) with a documented policy and test matrix; do not expose hidden labels/quantities through rollups.
3. Use database views/query projections and request-scoped caching where appropriate, but ensure visibility predicates are enforced before aggregate/report totals.
4. Reports need explicit snapshot/as-of semantics and consistent transaction/snapshot reads for material needs, orders, allocations, receipts and calculation ledger.
5. Reconcile projections against underlying records with automated integrity tests.

## Limits
- E1 only; no runtime or privacy tests executed, no live records accessed, and no production changes.
- Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain open.
