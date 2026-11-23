# Task 2.8 — Procurement Receipt Quantity Reconciliation & Projection Trace — Increment 153

Date: 2026-11-23
Scope: READ-ONLY source inspection of legacy `ProcurementService.js`, selected excerpts, reference repository only.
Evidence level: E1 source evidence. No runtime test execution, live data, or production mutation.

## Findings
1. Receipt batch path preloads tenant receipts, allocation rows, and computes item/project cumulative totals before validating each requested entry. It validates item/order tenant ownership, order cancellation, project/event visibility, item-level ordered cap, allocated-project caps, and unallocated-surplus caps.
2. Batch replay behavior is entry-key based: existing tenant receipt idempotency keys are returned as replay entries; repeated keys within the request are rejected. If every entry replays, no new receipt rows are inserted.
3. Batch creates receipt records in a single insertMany call, then recalculates affected order statuses using order-item totals and receipt totals. Status update and audit writes are sequential and outside an evidenced DB transaction; sheet atomicity/recovery is not established.
4. Single-receipt path uses a separate replay check based on SourceEventID + item (non-VOID), plus IdempotencyKey at wrapper level. It validates project allocation, cumulative project allocation receipts, item ordered quantity, and source event, then writes receipt and recalculates order status and audit sequentially.
5. Observed differences remain: batch can accept general/unallocated stock receipt with no ProjectID only up to the unallocated portion; single receipt requires a project when allocations exist. Batch and single paths have different replay and status/accounting flow. Their contract equivalence is not demonstrated.
6. In read projections, listNeeds derives receipt distribution across allocations proportionally to total allocations and clamps need-level values; listOrders displays item-level receipt sums and remaining quantities; dashboard clamps received to ordered quantity. These projection rules are not proven to be a canonical accounting contract and may diverge for edge cases (allocation proportions, surplus, malformed/legacy rows, replayed or void records).
7. The sampled source does not establish a correction/reversal API for receipts, explicit receipt reversal lineage, nor a canonical net-receipt ledger. Do not infer absence across the entire codebase from this sample.

## V2 design implications / candidate requirements
- Define a canonical receipt ledger with explicit reversal/correction lineage and net quantities; do not silently edit/erase receipt history.
- Use a shared command contract for single and batch receipt entry, with one set of invariants, validation and replay semantics.
- Tenant-scoped durable idempotency uniqueness; separate replay-key types/policies must be specified.
- Transactionally persist receipts, order-state projection and audit/outbox, with a deterministic reconciliation process.
- Define allocation-aware accounting: project allocations, unallocated/general stock, surplus, returns/reversals, and rounding/precision.
- Rebuild list/workspace/report/dashboard quantities from the same canonical projection or shared calculation logic.
- Add tests for batch-vs-single parity, concurrent receipts, idempotency replay, cross-tenant access, partial failures, allocated/general receipt caps, reversals, voids, and projection consistency.

## Gap status
G-02 remains OPEN/BLOCKER; G-01/G-06/G-10 remain OPEN. Schema readiness remains NOT APPROVED.

## Evidence limits
This is source inspection only. No tests executed, no production data read, no live system interaction, no schema or migration implemented, and no production repository changed.
