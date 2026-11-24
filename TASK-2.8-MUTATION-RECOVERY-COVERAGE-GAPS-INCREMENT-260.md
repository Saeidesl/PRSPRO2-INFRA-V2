# Task 2.8 — Mutation Recovery Coverage Gap: Event Void, Project Removal, Attachment Cleanup and Audit Failure (Increment 260)

## Scope and method
Read-only inspection of production-reference test files and relevant service method surfaces. No production writes, test runs, or runtime failure injection. This report identifies bounded gaps in the inspected tests, not repository-wide proof of absence.

## Source-backed coverage snapshot
- `tests/project-deletion.test.js` currently tests dependency collection boundaries, shared purchase-order preservation, ignoring deleted rows and treating VOID events as inactive during dependency collection. It does not exercise the `remove` orchestration path or injected failures across soft-delete, storage-trash, event void, audit, and final project removal in this inspected file.
- `tests/attachment-service.test.js` tests visibility denial for event listing/upload and listing from a visible project. It does not cover the AttachmentService delete lifecycle, storage-provider failure, metadata write failure, audit failure, or retry convergence in the inspected file.
- `tests/material-planning.test.js` includes a happy-path void reconciliation and lock-unavailable check, but not injected failures between need-status changes, calculation-history changes, and audit/reconciliation outcomes.
- `tests/event-idempotency.test.js` tests replay, user/tenant scoping, PROCESSING replay refusal, completion persistence failure. It does not establish a durable repair mechanism for stranded PROCESSING markers.
- The inspected workflow enforcement tests cover pre-insert enforcement, scoping/versioning, repeatable rules and lock revalidation, but do not evidence injected downstream mutation/audit failures in event void.
- No dedicated audit failure/retry test was found in the inspected paths. This is a bounded observation; further repository-wide test discovery is still possible.

## Gap matrix / proposed tests
### D-01 Project removal orchestration failure matrix
Inject failures after each boundary: event VOID; event-data soft deletion; attachment metadata DELETED; Drive trash; material needs/spec/calculation updates; procurement dependency handling; audit; final project row removal. Assert failure response identifies affected dependency, unrelated/shared order data is preserved, retry is safe and converges, and no false success is reported.

### D-02 Attachment delete lifecycle
Test storage-trash failure before metadata mutation, metadata DELETED write failure after trash, audit failure after both, retry behavior, idempotent repeated delete, tenant/entity visibility, and stale/trashed storage objects. Validate expected ordering and explicitly characterize irrecoverable/non-recoverable paths.

### D-03 Event void side-effect/recovery
Inject failures between snapshot validation, project-state restoration, event VOID update, workflow progress recalculation, material-needs reconciliation and audit. Verify the event and project state do not falsely appear fully voided/restored, replay/retry semantics, and surfaced reconciliation status.

### D-04 Material-planning void/recalc failure
Inject failures during need marking and calculation-history updates. Verify status reconciliation, preservation of history, no duplicate calculation on retry, and explicit surfaced failure.

### D-05 Audit write failure semantics
For representative mutating operations, simulate audit failure after business write. Establish whether the business operation returns failure, exposes a pending audit state, or supports repair; ensure retry cannot duplicate the mutation and audit omission is observable.

### D-06 Durable repair/reconciliation
Specify a durable reconciliation ledger/outbox for V2 (transactional with domain writes), status transitions, retry count, next-attempt scheduling, terminal/manual-review state, and operational visibility. Legacy tests cannot prove platform-level reliability under GAS lock behavior.

## V2 requirements (candidate; not implemented)
- Domain mutations and audit/outbox record commit in one PostgreSQL transaction.
- External file/storage actions use durable jobs/outbox and idempotent reconciliation; no claim of atomic storage/database transaction.
- Deletion is archive/deactivation and dependency-policy driven, not unbounded destructive cascade; preserve shared procurement data.
- Event void must use explicit state-transition semantics, immutable event history and reconciliation status.
- Retry-safe operations require stable operation keys, conflict detection, bounded retry, and visible manual-review state.
- Test suite must include failure injection at every persisted boundary plus retry/convergence assertions.

## Verification status
- Static source/test review: completed for the named files only.
- Runtime/automated tests: NOT RUN.
- Failure injection/concurrency: NOT RUN.
- Production repositories: READ ONLY; no changes.
- Task 2.8: IN PROGRESS, not accepted/complete.

