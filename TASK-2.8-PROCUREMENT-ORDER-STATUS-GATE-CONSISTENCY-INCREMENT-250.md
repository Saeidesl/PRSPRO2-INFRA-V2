# Task 2.8 — Procurement Order Status Gate Consistency (Increment 250)

Date: 2026-11-24
Evidence: E1 static source review of ProcurementService.js.

## Scope
Compared order status filtering in purchase-order creation/reconciliation and single/batch receipt entry.

## Findings
- In inspected single and batch receipt validation, the order is rejected when missing, tenant mismatch, or status exactly CANCELLED. Other statuses such as VOID, DELETED and NEEDS_REVIEW are not included in this explicit reject predicate.
- Separate order-item countability logic excludes CANCELLED, VOID, DELETED and NEEDS_REVIEW parent orders.
- These status gates therefore differ: the receipt-entry path may admit an order status that other procurement calculations exclude. Whether such order records can exist through supported operations and intended policy requires further evidence; candidate inconsistency, not a confirmed defect.
- The order status recomputation logic preserves NEEDS_REVIEW but otherwise recalculates ACTIVE/PARTIALLY_RECEIVED/RECEIVED; receipts and order updates remain sequential, as documented in prior increments.

## V2 implications
1. Define one canonical order eligibility state machine for order creation, allocations, receipt entry, workspace totals and reporting.
2. Reject or explicitly handle terminal/non-operational statuses in receipt entry, including CANCELLED/VOID/DELETED and review states.
3. Test each status against single and batch receipt API paths, material need reconciliation, allocations, visibility, and audit/recovery.

## Verification
Static only; no tests or production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
