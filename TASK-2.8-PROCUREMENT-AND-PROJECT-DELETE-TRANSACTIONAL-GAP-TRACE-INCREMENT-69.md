# Task 2.8 — Procurement and project deletion transactional gap trace (Increment 69)

Date: 2026-11-23
Evidence: E1 bounded read-only source inspection; no runtime tests.

## Procurement observations
- ProcurementService uses script locks for order and receipt mutations and tenant-scoped collections. It validates order graph/items/allocations before first write, and supports idempotency keys/replay paths.
- Source comments explicitly acknowledge Google Sheets cannot commit multiple sheets atomically. Order creation includes compensating/NEEDS_REVIEW handling and audit is a subsequent operation; this is not equivalent to a DB transaction.
- Receipt creation paths also lock, validate tenant-scoped order/item/project/source-event relations and track idempotency; sampled audit writes follow receipt mutation.
- Reporting/read collection helpers cache per-request tenant-scoped collections. This source observation does not verify data correctness under concurrency.

## Project deletion observations
- ProjectDeletionService performs dependency preview and tenant scoping, then takes a script lock and revalidates. It sequentially soft-deletes or marks dependencies, trashes Drive files, and physically deletes the project row; audit/failure handling is later in the flow.
- The cascade intentionally treats cross-project/general purchase-order dependencies carefully in selected code. Sequential operations still carry partial-failure risk.

## V2 requirements
- Implement order, allocation, receipt and project-cascade operations as PostgreSQL transactions with foreign keys, tenant-aware constraints and deterministic lock ordering.
- Use explicit idempotency table/key uniqueness scoped by tenant and operation; response replay must be transactionally consistent.
- Audit writes should participate in transaction or a durable outbox; file-storage side effects require staged/deferred actions and retry/reconciliation design.
- Deletion policy must be approved: soft-delete/retention, immutable event/audit history, dependency behavior, file retention, restore and project-code reuse constraints.
- Add concurrency tests for duplicate idempotency keys, competing receipts, allocation/quantity invariants, cascade failures, retries and cross-tenant payloads.

## Limits
- E1 only; source paths inspected in bounded scope; no runtime or integration tests.
- G-02/G-03/G-04/G-10 remain open; G-01 remains open. Schema/migration readiness NOT APPROVED.
- No production changes, live data, schema execution, migration, deployment or provisioning.
