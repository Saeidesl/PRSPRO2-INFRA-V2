# Task 2.8 — Increment 243: Event idempotency replay and recovery test boundary
Date: 2026-11-24
Scope: read-only inspection of EventService keyed create/replay path and tests/event-idempotency.test.js plus schema recovery test.

## Static findings
- Idempotency key lookup is scoped to TenantID + CreatedBy + key; a user lock serializes keyed calls for the same user execution path.
- Prior event status PROCESSING is explicitly rejected on replay with a message requiring inspection; it is not automatically resumed or reconciled in this path.
- A prior record not marked PROCESSING is treated as a replay candidate; source excerpt does not explicitly require COMPLETED, so statuses other than PROCESSING need policy/test review.
- Successful keyed creation sets IdempotencyStatus COMPLETED only after side-effect sequence. Completion-marker persistence failure is reported as failure with warning that the event already exists; the stored record remains a recovery candidate.
- `tests/event-idempotency.test.js` includes replay, tenant/creator scope, stuck PROCESSING rejection, COMPLETED marker and failed completion update tests. The file builds a VM harness and replaces `_createUnlocked_` with a model implementation; therefore it verifies the wrapper behavior under mocks, not full production integration or real Apps Script lock semantics.
- `tests/event-idempotency-schema-recovery.test.js` verifies ensureColumn append/no-reorder and stale header-cache recheck in a sheet mock. Static inspection only; tests were not executed in this audit.
- Recovery/repair procedure for stuck PROCESSING records, deciding side effects already applied and safely marking/replaying, is not evidenced in inspected code/tests.

## Risk candidates (not confirmed defects)
1. PROCESSING records require manual inspection; recovery protocol may be undocumented or absent.
2. Non-PROCESSING/non-COMPLETED statuses may be replayed as completed-like results unless additional code filters them.
3. Mocked tests do not establish real GAS lock concurrency or full side-effect rollback/recovery.

## Verification boundary
No tests run. E1 source/test-file inspection only. No production changes. G-01/G-10 OPEN; schema readiness NOT APPROVED.
