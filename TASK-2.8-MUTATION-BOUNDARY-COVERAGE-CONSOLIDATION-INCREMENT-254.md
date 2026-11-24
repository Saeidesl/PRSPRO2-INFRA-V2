# Task 2.8 — Mutation Boundary Audit Coverage Consolidation (Increment 254)

## Purpose
Consolidate mutation boundary observations across earlier increments and increment 253 into a risk-driven coverage backlog, and distinguish already evidenced safeguards from unverified runtime/repair guarantees.

## Cross-domain mutation boundary map
| Domain | Existing static evidence | Remaining coverage / verification risk |
|---|---|---|
| Event create | user lock, idempotency/replay, PROCESSING/COMPLETED semantics, selected tests | multi-side-effect failure windows; durable repair and concurrency/platform semantics |
| Event void | permission/config/later-event/snapshot checks; material planning reconciliation surfaced | injection between project restoration, event status, workflow recompute, planning and audit; retry/recovery |
| Project deletion | dependency collection, lock, tenant/admin and dependency policy; test coverage for collection | orchestration failure injection, safe retry convergence, recovery ledger |
| Attachments | access checks, upload validation, metadata/storage paths and selected access tests | lifecycle delete failure boundaries, audit/retry, stale metadata/storage reconciliation |
| Procurement receipt | lock, validation, idempotency, batch/single crosswalk, status gate/aggregation candidates | receipt path parity, status countability, partial write/audit recovery, concurrency |
| Material planning | versioning, calculation idempotency, void status marking, lock-unavailable | injected failures across multi-row updates and recoverability |
| Audit | audit service is called at various mutation boundaries in inspected traces | audit persistence failure behavior, observable repair and guarantees; no test proof |
| Workflow mutation | lock/revalidation and versioned rule coverage in selected tests | downstream failure/rollback semantics and cross-route permission parity |

## Coverage strategy prioritization
**P0 (data-integrity / duplicate / false-success):**
1. Mutation boundary failure-injection tests for receipt, event void, project removal, attachment deletion, material planning.
2. Replay/retry convergence and duplicate prevention after failure at each boundary.
3. Concurrency tests on idempotency keys, project/order state and receipt limits using a faithful platform model.
4. Tenant isolation and permission verification for mutation entry points, including direct service invocation where supported.

**P1 (repairability / operational control):**
5. Audit/outbox failure behavior and reconciliation visibility.
6. Durable stuck-operation repair workflow (PROCESSING, NEEDS_REVIEW, reconciliation).
7. Storage/database consistency reconciliation (orphaned files, missing files, DELETED metadata vs trash state).

**P2 (observability / scale):**
8. Operation-specific diagnostic correlation IDs, redacted error surfaces, failure counters and retry/manual-review metrics.
9. Bound/performance checks for dependency collection and reconciliation scans at realistic scale.

## Reusable test harness acceptance requirements
For every mutating scenario, test must assert:
- persisted final state of all affected entities, not merely the returned response;
- stable operation key and idempotent replay behavior;
- tenant isolation and permissions;
- side-effect call order, failure boundary and surfaced status;
- retry convergence without duplicate rows/notifications/files or unintended shared-data deletion;
- audit record or explicitly observable pending-audit/reconciliation status;
- no false success when an operation is incomplete.

## Search limitation
A GitHub code-search attempt for specific audit/void/delete terms returned no usable results. This is not treated as evidence of absence. Named test files were inspected directly where fetch access was available; broader test coverage remains unverified.

## Task state
This is a coverage blueprint and audit consolidation, not an implementation. Production remains read-only. No runtime tests/failure injection/concurrency runs performed. Task 2.8 remains IN PROGRESS and awaits subsequent uncovered audit increments plus explicit Product Owner acceptance.
