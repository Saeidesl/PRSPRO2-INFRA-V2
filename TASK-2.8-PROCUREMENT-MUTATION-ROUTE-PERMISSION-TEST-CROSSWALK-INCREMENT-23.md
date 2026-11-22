# Task 2.8 — Procurement Mutation Route Permission Test Crosswalk Increment 23
Date: 2026-11-22
Scope: Read-only mapping of selected procurement mutation routes to service operations and test inventory.

## Route permission evidence
Router.js POST route evidence:
- `/procurement/orders` requires `PROCUREMENT_MANAGE`, dispatches `ProcurementService.createOrder`.
- `/procurement/receipts/batch` requires `PROCUREMENT_MANAGE`, dispatches `ProcurementService.createReceiptsBatch`.
- `/procurement/receipts` requires `PROCUREMENT_MANAGE`, dispatches `ProcurementService.createReceipt`.
Read-side routes `/procurement/orders`, `/procurement/needs`, workspace/dashboard were sampled in the prior audit and use `requireAny` for PROCUREMENT_VIEW/PROCUREMENT_MANAGE in their GET handlers.

## Service-level defenses
- `createOrder` uses ScriptLock and tenant user context; performs normalization/validation before writes in the sampled flow; order/item/audit writes remain multi-step and atomicity not demonstrated.
- `createReceipt` uses ScriptLock, tenant/item/order checks, project/allocation validation and replay handling; write/audit boundary remains sequential.
- `createReceiptsBatch` uses ScriptLock, prevalidation/normalization and insertMany for receipt rows, then order status updates and individual audit writes; no cross-sheet transaction demonstrated.
- Route permission plus service validation are positive bounded defense layers, but tests and runtime enforcement are not verified here.

## Test inventory evidence
Backend test tree contains `tests/procurement-service.test.js` and `tests/procurement-event-integration.test.js`, plus material-planning tests. Filenames suggest relevant surface but do not establish route-permission coverage, batch fault injection, atomicity, cross-tenant isolation, or successful execution. No tests were executed.

## Remaining evidence gap
- Map actual assertions in procurement-service test to createOrder/createReceipt/createReceiptsBatch, idempotency replay, quantity bounds, allocation checks, tenant mismatch and fault boundaries.
- Establish dedicated Router authorization tests for denied/allowed PROCUREMENT_MANAGE and view permissions.
- Fault-injection/transaction recovery cannot be proven through source-only static review; defer runtime test design to V2 test environment.

## V2 recommendations
- Use transactional normalized writes and outbox audit; tenant-qualified constraints and payload-bound idempotency.
- Keep route authorization and service/domain authorization as defense-in-depth, but test both layers.
- Test batch atomicity, concurrency, retry/replay, cross-tenant identifiers, allocation overrun and status recalculation.

## Limits
Source review only. G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No test execution, live data, DDL, migration, provisioning, deployment or production modification.
