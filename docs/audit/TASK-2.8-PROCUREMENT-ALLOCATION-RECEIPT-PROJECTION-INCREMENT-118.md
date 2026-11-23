# Task 2.8 Increment 118 — Procurement allocation, receipt and projection boundary

Date: 2026-11-23  
Evidence level: E1 — source-only  
Legacy backend inspected read-only: ProcurementService.js

## Observed source behavior
- Order creation is script-lock guarded and supports request idempotency. It calculates existing ordered allocations/legacy unallocated line amounts to avoid double-counting material needs.
- Order payloads are normalized and validated before writes: tenant/project/need links, project-specific need restrictions, quantity/price, allocation quantities and allocation totals are checked. Automatic allocation covers remaining need; extra quantity is tracked separately as surplus, while project order lines without a linked need are treated as unplanned/surplus.
- Order header, items and allocations are inserted sequentially. On partial failure the header is marked NEEDS_REVIEW and error context is appended, rather than pretending the order is complete. Audit occurs after successful item/allocation persistence. Atomicity and reliable recovery are not demonstrated.
- Receipt registration and batch receipt registration are lock-protected. The batch path validates order/item/tenant/project context, replay keys, receipt quantity against ordered quantity, allocation and project limits, and surplus/unallocated stock rules. Batch writes and order status/audit updates are sequential.
- Single receipt path also supports source-event/item replay protection, validates item/order and allocation-to-project rules, and checks cumulative limits. Its validation and replay shape differs in some respects from the batch path; complete parity is not established.
- Receipt aggregation is used to update order status (ACTIVE, PARTIALLY_RECEIVED, RECEIVED) except orders already marked NEEDS_REVIEW. In the single path, a receipt and status update are sequential.
- listNeeds derives ordered/received/remaining quantities from needs, items, allocations and receipts. It excludes invalid/non-countable order statuses, handles older items without allocation rows, caps projected quantities at requested quantity, computes over-ordering, and derives procurement status. Estimated needs may be hidden when confirmed needs exist for the same project/spec key.
- Request-scoped caching of tenant procurement collections is used for composite reads; it is deliberately request lifetime only.
- Project/order visibility projection builds multi-project allocation summaries. Logical joins are source-code joins; schema-level foreign keys/unique constraints are not demonstrated.

## V2 design implications
1. Use relational allocations as first-class entities with tenant/project/need/order-item constraints and quantity invariants.
2. Treat order creation as one database transaction (header, lines, allocations, idempotency record, audit/outbox) while preserving a durable NEEDS_REVIEW/reconciliation concept for external or exceptional recovery.
3. Receipts need transactional validation and write, durable idempotency, and a unified single/batch domain path to prevent rule divergence.
4. Derive procurement status/remaining quantity through a consistent projection or carefully maintained transactional ledger; define how surplus and multi-project receipt distribution behave.
5. Preserve legacy no-allocation order-line compatibility only as a migration adapter, not as an ongoing ambiguous domain rule.
6. Validate allocation, ordered, receipt, surplus and project quantities with exact decimal arithmetic and concurrency-safe aggregate locks.

## Limits
- Source inspection only; tests were not executed, live data was not accessed, and production repositories were not changed.
- E1 source observations; no runtime/operational defect is asserted.
- Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain open.
