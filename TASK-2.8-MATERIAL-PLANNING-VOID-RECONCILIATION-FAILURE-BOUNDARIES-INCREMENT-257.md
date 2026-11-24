# Task 2.8 — Material Planning Event-Void Reconciliation Failure Boundaries (Increment 257)

## Source trace
Read-only trace of MaterialPlanningService.markEventVoided:
- ensures schema, derives current user, and acquires ScriptLock (10-second tryLock);
- scans calculation history for same tenant/source event excluding SOURCE_EVENT_VOIDED;
- sequentially updates each calculation to SOURCE_EVENT_VOIDED and collects affected MaterialNeedIDs;
- scans confirmed ACTIVE needs for same tenant/source event;
- sequentially marks needs NEEDS_REVIEW and appends a review note;
- writes a SOURCE_EVENT_VOIDED audit record and returns counts;
- releases lock in finally.

Selected tests cover a normal event-void marking scenario and lock unavailable, but not write/audit injection at individual boundaries.

## Failure boundaries
MPV-01: first calculation row update succeeds, next update fails => mixed calculation statuses.
MPV-02: calculations are marked voided but needs scan/update fails => needs may remain ACTIVE despite voided source history.
MPV-03: some needs become NEEDS_REVIEW and a subsequent need update fails => mixed status and partially appended notes.
MPV-04: all domain rows update but audit write fails => missing completion audit.
MPV-05: repeat after partial failure may skip already SOURCE_EVENT_VOIDED calculation rows and still process remaining needs; this is potentially useful retry behavior but convergence is unverified and no durable retry state is evidenced.
MPV-06: duplicate invocation concurrency behavior relies on ScriptLock and platform semantics; test mock lock is not a faithful concurrency proof.
MPV-07: ensureSchema/additive schema failure recovery and subsequent reentry remain unverified for this mutation.
MPV-08: operation returns counts only on success; partial failure may throw without a durable operation status that can be queried/repaired.

## Test blueprint MPV-01..MPV-12
1. Inject failure on calculation row 1 and later calculation row.
2. Inject failure during calculation-to-need link/status processing.
3. Inject failure on first need update and on a later need update.
4. Inject audit failure after all rows updated.
5. Retry after each failure, assert eventual state convergence and no duplicate notes/audit.
6. Assert calculation and need rows across tenants are unaffected.
7. Assert only CONFIRMED ACTIVE needs are marked NEEDS_REVIEW and historical rows retained.
8. Assert idempotent status/note behavior on repeated success (no note duplication).
9. Simulate lock unavailable and concurrent attempts in a faithful model.
10. Assert failure is observable and operation can be resumed.
11. Verify schema-preparation failure leaves no hidden partial mutation.
12. Verify counts reflect exactly affected records after retries.

## V2 design
Use transactional status changes and durable reconciliation task keyed by source event. Keep immutable calculation history; use normalized event-void reconciliation status, idempotent note/transition semantics and bounded retries/manual-review. Domain transaction should persist reconciliation outbox entry atomically; workers should lock/retry and expose operation status. Avoid duplicate notes by structured reason codes rather than string concatenation.

## Verification
Static source review only. No tests/failure injection/concurrency run; production references read-only. Task 2.8 IN PROGRESS.
