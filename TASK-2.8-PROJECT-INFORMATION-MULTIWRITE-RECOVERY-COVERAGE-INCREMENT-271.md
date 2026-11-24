# Task 2.8 — Project Information Revision/History/Audit/Live-Sync Failure Coverage (Increment 271)

## Source trace
ProjectInformationService.save validates tenant/project/type/source event/value, checks ExpectedRevision, performs update/insert, writes history, writes audit and invokes live-reference sync. Approve writes approval history and audit, then sync. Conflict resolution validates source/target revisions and policy, may update target information and history, updates sync log and writes audit. These are sequential multi-write operations; atomicity/recovery is not demonstrated in the inspected code.

## Selected test evidence
`tests/project-draft-information-flow.test.js` covers central information save from event form, replay idempotency for same event value, draft behavior, project code and other selected information behaviors. `tests/project-repeat-reference-copy.test.js` covers copy plans, per-type copy/live sync, conflict resolution and stable operation key recovery. These selected test files do not establish full failure-injection coverage for history/audit/live-sync/approval/conflict-resolution boundaries.

## Test blueprint
- PI-01 ExpectedRevision conflict and stale update refusal.
- PI-02 Current value write succeeds, history insert fails.
- PI-03 History succeeds, audit insert fails.
- PI-04 Audit succeeds, LIVE_SYNC propagation fails.
- PI-05 Source revision advances while child propagation is partial; replay reconciles.
- PI-06 Empty-value deactivate/history/audit/live-sync failures and retry.
- PI-07 File-type data path and retention/storage boundary.
- PI-08 Approval history/audit/sync partial failures and idempotent approval replay.
- PI-09 Approval of superseded/current revision conflict.
- PI-10 USE_SOURCE conflict resolution fails between target update, history, sync log and audit.
- PI-11 KEEP_LOCAL resolution must close conflict state and update log/audit without silently changing value.
- PI-12 Replay after partial conflict resolution converges.
- PI-13 Tenant/source/target revision mismatch fail-closed.
- PI-14 Concurrent save/approve/conflict resolution with version/lock/DB constraints.
- PI-15 Cross-tenant duplicate/type/source references rejected.

## V2 requirements
Use relational transactions for information value, revision/history, audit and sync-operation ledger. Live-sync external/project child propagation should use durable idempotent outbox, revision pinning and conflict state. Approval must be tied to a specific immutable revision.

## Verification
Source/test-file review only; no runtime tests or failure injection. No production changes.
