# Task 2.8 — Race, Lock, and Replay Convergence Test Specification (Increment 263)

## Bounded coverage observations
Inspected test files have single-request mocked locking for event creation and material planning, and workflow event creation revalidation under shared write lock. The selected test suites do not establish real concurrent execution behavior for project deletion, attachment deletion, event void, receipt create/replay, material recalculation, or event creation under platform-level lock semantics. This is not an exhaustive repo-wide absence claim.

## Concurrency/lock test matrix
- C-01 Two simultaneous identical event-create idempotency requests: one event, one side-effect set, replay response.
- C-02 Two simultaneous different event-create keys on same project/step: sequencing rule prevents invalid parallel progression.
- C-03 Event creation and void overlap: deterministic status and no false success.
- C-04 Two simultaneous voids of same event: one transition; other conflict/invalid-state.
- C-05 Project deletion and event creation overlap: lock/revalidation prevents creating children on a deleting/deleted project.
- C-06 Project deletion and attachment upload overlap: attachment is either included in dependency sweep or blocked; no orphan.
- C-07 Project deletion and procurement receipt overlap: no orphan/unsafe deletion and shared orders preserved.
- C-08 Two attachment delete requests: idempotent/conflict result and no duplicate audit.
- C-09 Attachment delete and download race: authorization and storage state coherent.
- C-10 Two material-needs recalculations on same project/spec/event: deterministic results and no duplicate needs/calculation.
- C-11 Material-spec revision and recalculation overlap: calculation uses a consistent revision snapshot.
- C-12 Event void and material recalc overlap: no stale ACTIVE needs after convergence.
- C-13 Receipt single-path and batch-path concurrent writes: allocation/quantity caps remain invariant.
- C-14 Two batch receipt submissions with different keys: cumulative caps respected.
- C-15 Receipt replay concurrent with order status recomputation: deterministic replay and aggregate status.
- C-16 Schema additive preparation concurrent with mutation lock acquisition: headers/indexes stable, no duplicate column.
- C-17 Lock timeout/no lock: no write side effects.
- C-18 Exception after lock acquisition: release occurs; no lock leak.
- C-19 Lock release itself throws/returns false: operation status and diagnostics clear; document platform limitation.
- C-20 Nested/reentrant lock acquisition scenario: avoid deadlock; prove design or remove nested locks.
- C-21 Tenant boundary under concurrent requests: no cross-tenant mutation or cache contamination.
- C-22 Audit/outbox correlation under concurrent replay: unique operation IDs and immutable records.

## Execution architecture
1. Unit-level deterministic interleaving harness (not adequate as final proof).
2. Integration tests with isolated real database transactions and constraint contention for V2.
3. Platform behavior characterization where the legacy Google Apps Script lock primitive must be verified, without production data or writes; use isolated test project/environment only if available.
4. Repeat after injected failure with same operation ID; assert final state and invariants.

## V2 invariants
- Tenant-scoped uniqueness and idempotency.
- Referential integrity and status transition constraints.
- Transactional domain state + audit/outbox.
- External side effects reconciled idempotently.
- Locking should rely primarily on DB constraints/transactions, not application-only mutexes.
- Concurrent operations must return deterministic conflict/replay states.

## Verification
Blueprint only. No concurrency tests run; no platform lock experiments; no production mutation.
