# Task 2.8 Increment 234 — Procurement test coverage inventory (2026-11-24)

## Evidence
Read-only inventory of backend tests/ and tests/procurement-service.test.js.

## Test inventory
A procurement-service.test.js file exists, along with procurement-event-integration.test.js and material-planning.test.js.
The inspected procurement-service.test.js currently includes tests for:
- Project visibility lookup/bulk-read behavior.
- Tenant-scoped collection reuse within workspace.
- Allocation-key equivalence between estimated/confirmed needs.
- Cumulative allocation cap and surplus calculation.
- Workspace reuse of order mapping, user context, and project-visibility checks.

## Gap against mutation integrity requirements
In the inspected test file, no direct test cases were found for:
- Single or batch receipt creation.
- Receipt idempotency/replay and duplicate batch keys.
- Receipt quantity ceiling and allocated/unallocated receipt enforcement.
- Tenant isolation of receipt/item/order/event/project payloads with negative cases.
- VOID/reversal semantics in aggregate calculations.
- Failure injection after receipt insertion, order status update, or audit log.
- Receipt/order reconciliation or recovery.
- Lock acquisition failure or release under exceptions.

This is limited to the inspected file; broader test suite may have coverage elsewhere, which must be searched and verified.

## Status
No tests executed. Test source inspection only. Task 2.8 incomplete; G-01/G-10 open; schema readiness NOT APPROVED.

## Next
Increment 235: inspect procurement-event-integration.test.js and MaterialPlanning tests for cross-domain receipt coverage, then expand coverage inventory if applicable.
