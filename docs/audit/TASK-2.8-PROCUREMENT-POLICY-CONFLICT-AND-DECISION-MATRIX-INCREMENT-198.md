# Task 2.8 Increment 198 — Procurement policy conflict and unresolved decision matrix

Date: 2026-11-23
Status: design-only, evidence synthesis; no implementation or tests.

## Purpose
Turn the existing procurement lifecycle/defaults drafts and increments 12–14/195 into a bounded decision matrix, avoiding repeated source audit and distinguishing technical invariants from business policy.

## Evidence inputs
- TASK-2.6-BLOCKER-RESOLUTION.md, increments 12–14 (verified read-back in this checkpoint)
- TASK-2.8-BLOCKER-POLICY-DECISION-PACK.md
- TASK-2.8-RECOMMENDED-POLICY-DEFAULTS.md
- TASK-2.8-LIFECYCLE-TRANSACTION-CONTRACTS.md
- Increment 195 material planning/procurement boundary audit

## Stable technical requirements (recommended; not implemented)
1. Preserve receipt postings as immutable accounting facts; corrections are linked reversal/replacement postings.
2. Single and batch submissions share one validation and posting core; batch orchestration must have explicitly documented atomicity.
3. Persist tenant-scoped idempotency with payload fingerprint: same payload returns same outcome; payload mismatch conflicts.
4. Use exact decimal quantities, explicit base units/conversion versions and deterministic rounding rules.
5. Compute projections from canonical receipt/allocation/reversal facts; order status is a projection, not independently editable accounting truth.
6. Keep allocation destinations explicit (project allocation vs unallocated/general stock), and retain surplus/over-receipt separately from fulfillment against need.
7. Domain writes, projection state, audit and outbox/reconciliation rows are transactionally coupled in PostgreSQL; external operations are retryable and idempotent.
8. Tenant-safe references and visibility must cover order, line, need, project, allocation, receipt, reversal, source event and tenant.

## Policy decision matrix
| Topic | Recommended default | Business decision / evidence still needed |
|---|---|---|
| Over-receipt | Block; authorized exception only with reason | PO approval level and exception threshold |
| Receipt unit | Base unit or versioned conversion | Confirm unit catalog and allowed conversions |
| General-stock receipt | Explicit unallocated destination; no implicit project | Define inventory ownership/custody and stock ledger scope |
| Cross-project allocation | Explicit, auditable allocation row | Define permission/approval and reallocation timing |
| Cancellation with posted receipt | Never erase facts; require resolution/reversal/return policy | Define disposition and approval |
| Cancellation with active allocation | Require release/reallocation before cancellation | Define permitted allocation release and stock impact |
| Batch posting | Entire batch atomic by default, or explicitly partial-result command | Confirm operational preference for all-or-nothing |
| Duplicate source event | Unique tenant/item/source-event rule; conflict/replay outcome | Confirm source-event semantics and whether reversals permit re-post |
| Receipt reversal | Linked reversal posting, reason, actor and authorization | Define return-to-supplier vs accounting correction vs stock adjustment |
| Surplus | Separate from need fulfillment; visible ledger/projection | Confirm storage/valuation/reporting treatment |
| NEEDS_REVIEW | Holds sensitive lifecycle actions; explicit resolution command | Define role, resolution workflow and allowed actions |
| Historical retention | No automatic purge until approved schedule | Retention schedule and legal/accounting requirements |

## Reconciliation/projection contract
One domain projection function should supply procurement workspace, project detail, need coverage, dashboard and report consumers. It should expose, at minimum, gross posted, reversed, net received, allocated, unallocated/general stock, need-covered quantity, surplus, outstanding quantity and review state, with consistent status eligibility. Consumer-specific presentation may filter but must not silently redefine accounting totals.

## Proposed command contract (conceptual only)
- ReceiptPost: tenant context, order/item, destination/allocation, quantity/unit, source-event (optional but semantics controlled), idempotency key, payload fingerprint, actor, timestamp.
- ReceiptReverse: receipt posting reference, quantity/full-or-partial reversal, reason, destination disposition, idempotency key, actor.
- AllocationCreate/Release: order/item, need/project destination, exact quantity, reason/source, idempotency.
- OrderCancel: order, reason, explicit disposition for outstanding quantity and allocations; rejects unresolved posted receipt facts unless policy permits linked actions first.
- OrderResolveReview: order, resolution code, evidence/reason and authorized actor.
These are design candidates; endpoints/names not finalized.

## Risks and open coverage
- Legacy source retrieval remains limited; this matrix relies on documented bounded evidence and synthesis, not fresh exhaustive source retrieval.
- No evidence of complete deployed runtime parity or all downstream consumers.
- No Product Owner decisions captured for business-policy items.
- G-02 remains OPEN; G-01/G-10 remain blockers.
- No schema approval, SQL/DDL, migrations, live data, runtime tests, provisioning, deployment or production changes.

## Next bounded step
Create the event/form policy decision matrix using the same evidence discipline, then consolidate PO decision items without expanding into repeated source retrieval.
