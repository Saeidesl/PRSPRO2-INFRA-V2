# Task 2.8 Increment 115 — Project information revisions, approvals and LIVE_SYNC conflicts (2026-11-23)

## Scope
READ-ONLY source inspection of ProjectInformationService.js and ProjectInformationRepository.js. ProjectInformationValidator.js was not present at the queried path (404); validation is implemented in the service excerpt.

## Observations (E1)
- Project information is tenant/project/type scoped; project access is checked through the project visibility helper.
- Scalar validation covers text/select, numeric bounds, booleans, date/datetime, attachment references. Image/file values must reference active attachments associated with the same project and tenant; image arrays capped at 20.
- Structured types validate active columns, required values, unknown keys and SINGLE/MULTIPLE cardinality; multi-row values capped at 500.
- Value JSON is size-limited (~45,000 chars). Save validates optional SourceEventID against same project/tenant and supports ExpectedRevision concurrency check.
- Save updates or inserts current information, increments revision, then inserts history and audit; LIVE_SYNC fan-out follows. Atomicity across value/history/audit/sync is not demonstrated.
- Same event/value replay is idempotent in a limited sourceEventID + exact JSON comparison path.
- Approval records an APPROVED history entry at current revision, audit, then syncs. Only source projects (not repeat children) can approve.
- LIVE_SYNC fan-out only targets active children with matching tenant/source relationship and policy. File-based values are skipped with a sync-log status.
- Idempotency key for fan-out includes source project, target project, type and source revision; existing logs inform replay status.
- If target has local active information, or target revision differs from last successful sync expectation, a CONFLICT sync log is created instead of overwriting.
- Conflict resolution requires a LIVE_SYNC policy and existing CONFLICT log, expected target revision and unchanged source revision. USE_SOURCE updates target value/history/log; KEEP_LOCAL records resolution. File conflicts are not resolved via USE_SOURCE in this path.
- Fan-out writes target current information, history, sync log and audit sequentially; cross-record atomicity, DB unique key on sync idempotency, retry consistency and race behavior are not proven.
- Approval and conflict resolution source excerpts show try/finally without lock acquisition in the visible methods; locking/authorization wrapper behavior may exist elsewhere and is not proven here.
- Repository helper methods use sheet-wide reads and filtered arrays; no DB-index/runtime performance evidence.

## V2 implications
- Relational revision history and approval records; approval as revision-specific state.
- Transactional update+history+audit/outbox and reliable fan-out queue.
- Unique sync operation key, idempotent retries, explicit sync state machine and conflict preservation.
- Attachment references governed by object-storage ownership metadata; file sync handled via durable transfer jobs.
- Test revisions, stale ExpectedRevision, approval replay, local edits, sync conflict, KEEP_LOCAL/USE_SOURCE, source revision changes, duplicate sync jobs, concurrency and partial failures.

## Verification
E1 source-only; no tests executed; no production changes. Potential synchronization/locking concerns not confirmed defects. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.
