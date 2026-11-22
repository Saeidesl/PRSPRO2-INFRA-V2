# Task 2.8 — Procurement/Material Planning Transaction & Lifecycle Trace (Increment 57)
Date: 2026-11-22
Status: READ-ONLY source trace, bounded paths.

## Sources inspected
MaterialPlanningService.js, ProcurementService.js, ProcurementRepository.js and ProcurementEventIntegrationService.js from READ-ONLY production reference.

## Source-confirmed observations
- ProcurementRepository reads tenant-scoped material needs, orders, order items, allocations and receipts by filtering TenantID.
- ProcurementService order and receipt creation paths use ScriptLock and support IdempotencyKey-based replay. The order path validates the graph before writing, then inserts the order and its items; on partial item-write failure, it updates the order to NEEDS_REVIEW instead of removing partial history. This compensating status is not atomic rollback.
- Batch receipts validate entries, allocations, project shares and existing receipts before processing; locks protect sampled paths. No multi-sheet DB transaction exists in legacy architecture.
- MaterialPlanningService calculations are tied to tenant/project/source event and active specification revisions. Calculation history is appended; source-event void handling marks associated calculations SOURCE_EVENT_VOIDED and affected confirmed needs NEEDS_REVIEW, followed by audit.
- Specification revision writes a new record and marks previous version SUPERSEDED in separate writes; audit follows separately. Failure atomicity is not guaranteed by Sheets.
- EventTypeProcurementSettings is tenant-scoped, validates confirmed-quantity InformationType shape, and writes setting then audit separately in sampled save path.

## V2 requirements/recommendations
1. Transactionally persist order, order items, allocations and audit; define recoverable workflow only where business intentionally needs partial processing.
2. Receipt posting must be atomic with quantity ledger, allocation/remaining quantities, audit and idempotency keys; consider immutable receipt records and explicit reversal records instead of destructive deletion.
3. Enforce tenant-qualified FK graph across order->items->allocation->receipt, material need, project and source events.
4. Calculation and specification version history should be immutable and transactionally linked; source event void/revision must produce explicit downstream impact policy and lifecycle state transitions.
5. Idempotency keys need DB unique constraints with tenant scope; lock semantics should be replaced by DB concurrency controls.
6. Include isolated tests for concurrent order/receipt, partial-write recovery, duplicate replay, allocation overrun, receipt overrun, cross-tenant references, void source event, superseded specs, audit failure and retry.

## Evidence limits
Bounded E1 source review only; no runtime tests, live data, SQL/DDL, migration, provisioning, production changes or deployment. G-02/G-03/G-10 remain open; schema readiness NOT APPROVED.
