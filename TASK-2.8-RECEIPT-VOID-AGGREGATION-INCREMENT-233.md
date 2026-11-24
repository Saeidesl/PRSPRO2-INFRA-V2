# Task 2.8 Increment 233 — Receipt void/status aggregation audit (2026-11-24)

## Source evidence
ProcurementService.js and ProcurementRepository.js read-only.

## Findings
- Service defines _active_(status) which treats CANCELLED, VOID and DELETED as inactive.
- listNeeds aggregation excludes orders with CANCELLED, VOID, DELETED, NEEDS_REVIEW status from countable items.
- Receipt replay based on SourceEventID+item explicitly excludes VOID.
- Repository reads tenant-scoped collections but does not filter receipt statuses itself.

## Cross-path consistency issue to validate
The receipt creation paths' per-item and allocation aggregate calculations in the reviewed excerpts sum receipt rows without a visible status filter, while replay and other list/countability code apply status-aware filtering in certain contexts. The source snippets support a status-filter inconsistency concern, but VOID lifecycle semantics and any unseen callers are not fully established. Record as potentially consequential for future reversal/void support, not confirmed bug.

## Recovery/repair
No dedicated receipt-void or receipt-reversal service method was located in ProcurementService. No repair/reconciliation method identified in this file during this pass. This does not establish that no mechanism exists elsewhere.

## Risk assessment
- Potential overcounting of receipt quantities if VOID records are retained and not excluded in specific validations.
- Potential partial-write consistency after receipt insert, order-status update, and audit sequence.
- Further whole-repository search and tests needed to validate both.

## Status
Static trace only. No tests. Task 2.8 incomplete, G-01/G-10 open, schema readiness NOT APPROVED.

## Next
Increment 234: inspect Procurement tests and broader backend source for receipt void/reversal, status repair, and failure handling; inventory available test coverage.
