# Procurement Cancellation and Adjustment Path Audit — Phase 2.1

Read-only review of ProcurementService.js and ProjectDeletionService.js on 2026-11-22.

## Findings
- In the inspected ProcurementService, no standalone cancellation method for purchase orders and no dedicated receipt-return/receipt-adjustment method were identified. Order cancellation is handled as a state check in receipt validation (cancelled orders reject new receipts). This finding is scoped to the inspected service and is not proof that no other route or event-driven pathway exists.
- Receipt creation recalculates order state based on total received quantity vs total ordered quantity: RECEIVED, PARTIALLY_RECEIVED, or ACTIVE, except NEEDS_REVIEW orders retain that review state. This aggregate logic uses receipt records and ordered quantities.
- Project deletion dependency collection avoids deleting purchase lines used by other projects. Orders are cascade-selected only when all active lines on that order are included and have no other-project allocation. General/shared orders are preserved where other project items/allocations remain. Receipts are collected by linked item, project, or order linkage.
- The cascade routine uses a script lock and re-collects dependencies before removal; it attempts soft deletion across child rows but ultimately physically deletes the project row when dependency handling succeeds. Partial failures are collected and surfaced rather than concealed.

## V2 design questions
1. Define a formal purchase-order cancellation policy: restrictions after receipts, allocation/need state consequences, audit history and reopen rules.
2. Define receipt correction/return as append-only adjustment or reversal records, never silently mutating historical receipt quantities.
3. Preserve shared-order item/allocations when deleting a project; use explicit relationship semantics and transactional safeguards.
4. Make project deletion a retention-aware operation with recoverable soft deletion, dependency inventory, workflow stages, and an explicit final purge policy.

## Status
No schema/migration work started; production source remains unchanged. This audit is source inspection only and does not establish deployed data state.
