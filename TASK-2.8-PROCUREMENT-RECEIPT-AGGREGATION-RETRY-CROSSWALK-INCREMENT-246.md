# Task 2.8 — Procurement Receipt Aggregation / Retry Crosswalk (Increment 246)

Date: 2026-11-24
Evidence level: E1 static source.

## Scope
Read-only ProcurementService inspection of receipt batch/single paths, receipt aggregation into procurement workspace and order summaries. Production repo untouched.

## Observations
- Batch receipt flow acquires ScriptLock, loads tenant receipts and allocations, validates receipt entries and allocation/project limits, then bulk inserts receipt rows. It subsequently recalculates order totals/status and emits per-receipt audit records.
- Batch flow can return existing receipt records by idempotency key; request-local duplicate keys are rejected. Replay records may be returned alongside newly created records.
- The batch flow excludes cancelled order headers from certain checks but preserves NEEDS_REVIEW order status while updating totals.
- The single receipt path acquires ScriptLock and has replay checks for SourceEventID+item and/or idempotency-key wrapper, then inserts a receipt, recomputes order status, and audits. In the inspected portion, prior receipts are summed without an explicit void/status filter.
- The listNeeds aggregation adds quantities from the tenant receipt collection to received-by-item totals; the reviewed receipt aggregation did not filter a receipt status. listOrders similarly sums receipts per item and records receipt history from the collection without an explicit status filter in the inspected segment.
- Batch/single paths differ in structure and replay keys. Batch validates project allocations and planned batch totals in request memory before inserting. Single path validates against accumulated prior receipts, then writes receipt and updates order.
- Receipt insert, order-status update and audit are sequential. Failure after receipt persistence may leave status/audit incomplete; no atomic transaction or durable repair workflow evidenced in the reviewed path.

## Candidate risk / validation question
If the data model permits void/cancelled receipt rows, whether those rows should contribute to received quantities must be explicitly defined and verified. Source review alone does not establish that void receipt rows exist, that UI/API permits voiding, or that this is a defect. The inspected receipt aggregators appear status-unaware, so this is a targeted crosswalk candidate.

## V2 recommendations
- Define immutable receipt correction/void policy and status semantics.
- Ensure every aggregation excludes invalidated receipts consistently, if voiding is supported.
- Use transactional receipt insert + order-state recalculation + audit/outbox in V2.
- Add test cases for batch and single receipts, idempotent replay, duplicate keys, project allocation caps, order NEEDS_REVIEW, failure after insert, and receipt invalidation effects on workspace/order totals.

## Verification
Static source only; no tests executed. No production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
