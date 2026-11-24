# Task 2.8 — Receipt Status Enforcement Cross-Path Audit (Increment 248)

Date: 2026-11-24
Evidence: E1 static source review of ProcurementService.js.

## Scope
Compared single receipt and batch receipt paths at their existing receipt aggregation points, validation, project allocation limits and order recomputation.

## Findings (static)
- Single-receipt path computes prior receipt totals for item and project allocations by summing records without an explicit Status exclusion in the inspected aggregation segments.
- Single receipt path replays a matching SourceEventID+item receipt only when the prior receipt status is not VOID; however, subsequent quantity totals shown in inspected segments include receipt records without status filtering. These replay and quantity semantics differ.
- Batch receipt path initially aggregates all existing receipts into receivedByItem and receivedByItemProject without an explicit Status filter in the inspected block.
- Both paths therefore appear to risk treating voided receipts as still contributing to quantity constraints/reconciliation, if VOID receipts exist and are intended to be excluded. This is a conditional candidate consistency issue, not a confirmed runtime defect.
- Single receipt order recomputation also totals receipt quantities without explicit status filtering in the inspected segment. Full batch remainder not completely reviewed in this increment.
- Existing locking and validation are present in inspected methods; audit does not establish actual race/failure behavior.

## V2 requirements / test cases
1. Define canonical receipt statuses and whether VOID contributes to accepted totals, historical totals, allocation totals and order completion.
2. Centralize receipt quantity aggregation to prevent single/batch/reports/workspace/timeline drift.
3. Test partial and full void, duplicate SourceEventID replay, idempotency replay, over-receipt validation, cross-project allocations, legacy rows with blank status, order recalculation and audit failure.
4. Add data migration reconciliation for legacy receipt statuses, if required.

## Verification
Static only; no tests or production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
