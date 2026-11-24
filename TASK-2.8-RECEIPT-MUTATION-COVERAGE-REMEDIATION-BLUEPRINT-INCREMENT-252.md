# Task 2.8 — Receipt Mutation Coverage Remediation Blueprint (Increment 252)

Date: 2026-11-24
Status: coverage-gap specification and execution boundary (not implementation in production repository).

## Boundary
The production backend is a read-only reference repository by project rule. The uncovered scenarios are addressed here by specifying an implementation-ready test suite and verification strategy; no test code is committed to the production reference and no tests were executed.

## Required test matrix
| ID | Scenario | Expected invariant | Path |
|---|---|---|---|
| R-01 | receipt quantity > 0; zero/negative/NaN | invalid rejected, no writes | single + batch |
| R-02 | tenant mismatch item/order/project/event | reject, no writes | single + batch |
| R-03 | order CANCELLED / VOID / DELETED / NEEDS_REVIEW | canonical eligibility policy enforced | single + batch |
| R-04 | parent order statuses excluded from procurement need/workspace totals | excluded consistently | workspace + order creation |
| R-05 | receipt VOID versus ACTIVE/legacy blank status | documented counting policy applied | totals + single + batch + projection |
| R-06 | 100 ordered, 70 allocated; receive 30 general | same policy and result across paths | single + batch |
| R-07 | general receipt > surplus | reject without mutation | single + batch |
| R-08 | allocated project receives within/outside cap | accept within; reject over cap/unallocated project | single + batch |
| R-09 | cumulative multi-entry batch and duplicate item rows | validate cumulative sum, no over-order | batch |
| R-10 | SourceEventID duplicate active receipt | replay without duplicate write | single |
| R-11 | SourceEventID match VOID record | policy defined; retry/recreate/reject deterministic | single |
| R-12 | IdempotencyKey repeat, partial repeat, conflicting key | replay only identical intended request; conflict for mismatch | batch + wrapper |
| R-13 | mixed batch valid+invalid entries | no receipt writes on validation failure (preflight) | batch |
| R-14 | insert succeeds, order update fails | recoverable, observable state and retry | single + batch |
| R-15 | order update succeeds, audit fails | operation not silently ambiguous; durable repair/reconciliation | single + batch |
| R-16 | receipt insert fails | no downstream status/audit side effects | single + batch |
| R-17 | simultaneous requests exceed remaining quantity | serialized or database constraint prevents overshoot | concurrency |
| R-18 | partial batch/replay ordering | deterministic responses and linkage | batch |
| R-19 | tenant-scoped receipt aggregation and project visibility | no cross-tenant leakage | workspace/projection |
| R-20 | order status recompute with blank, VOID and legacy rows | canonical quantity/state result | single + batch |

## Test harness requirements
- Isolated VM/context with faithful service/repository stubs, scripted write failure at each mutation boundary, and observable call log.
- Assertions must cover final sheet state, write order, return/error result, idempotency state, audit payload and retry outcome—not only mocked function invocation.
- Fixture factory: Tenant T1/T2, projects P1/P2, order O1 (100), allocations P1=70, general surplus=30, active/void receipts and idempotency keys.
- Mutation boundaries: receipt insert, order recompute/update, audit log, idempotency marker/link, allocation/need updates.
- Concurrency test cannot be proven by synchronous VM mocks; requires isolated GAS runtime or equivalent faithful locking simulation and, ultimately, platform test.
- Use deterministic clock/IDs and remove external Drive/Sheets side effects from test harness.

## Implementation ownership / repository safety
This blueprint is for V2 architecture and for a future approved test-only PR in an isolated branch/repository. Existing production repository remains untouched. For V2, build PostgreSQL transactionality around receipt insert, order state and audit/outbox; enforce allocation constraints in transactional service/DB design and reconcile idempotency keys.

## Acceptance
Each R-01..R-20 must be implemented and run in isolated environment; critical failure-boundary tests and concurrency tests require faithful platform or DB-level execution. Results must be evidence-backed before closing audit gap.

## Verification
Blueprint written from static findings. No tests run, no production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
