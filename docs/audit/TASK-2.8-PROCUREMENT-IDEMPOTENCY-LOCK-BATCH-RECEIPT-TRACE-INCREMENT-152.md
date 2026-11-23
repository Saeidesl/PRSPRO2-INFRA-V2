# Task 2.8 — Procurement order/receipt locking and batch-path trace (Increment 152)

Date: 2026-11-23
Source: bounded read-only retrieval of ProcurementService.js in PRSPRO2BACKEND-PRODUCTION.

## Observations
- createOrder obtains a script lock (tryLock with 10-second wait), delegates to unlocked path and releases in finally. The unlocked path resolves current user and performs tenant-scoped replay lookup by IdempotencyKey before validation and processing.
- createReceipt similarly acquires/releases a script lock, checks tenant-scoped replay by IdempotencyKey and delegates to unlocked receipt logic.
- createReceiptsBatch is a separate pathway. It requires a non-empty Receipts array and locks the operation; it builds batch state from existing tenant receipts, orders/items, allocations, and received/planned quantities, with batch-specific replay handling. The retrieved excerpt establishes separate batch logic, but not equivalence to the single receipt contract.
- Source evidence indicates application lock/idempotency mechanisms, not relational uniqueness or atomic multi-row transaction guarantees.

## V2 design implications (candidate requirements)
- Durable idempotency keys should be tenant-scoped and enforced by a DB uniqueness constraint; replay responses should be defined for order, single receipt and batch receipt.
- Order/item, receipt, allocation and quantity reconciliation should be transactionally coordinated in PostgreSQL, with explicit validation and failure recovery.
- Batch and single receipt operations should have one documented business invariant/contract or explicitly separate behavior; no parity assumption.
- Concurrency tests, duplicate replay tests, partial-failure recovery and cross-tenant replay negative tests are required before migration readiness.

## Limits
This is a bounded source observation; exact field-level quantity/accounting invariants and every mutation branch are not yet traced. E1 evidence only. G-01/G-02/G-06/G-10 remain open. No runtime tests, production data, SQL/DDL, migrations, provisioning, deployment or production changes.
