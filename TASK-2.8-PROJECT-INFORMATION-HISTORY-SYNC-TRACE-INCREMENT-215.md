# Task 2.8 Increment 215 — Project Information revision/history/live-reference trace

Date: 2026-11-24

## Source
Read-only trace of ProjectInformationService.js blob `965dfbb92b064f129aefaf38e5a8485bd836b2a8`.

## Save/revision observations
- Resolves tenant-scoped user, project and information type; rejects inactive type.
- Validates optional SourceEventID against tenant and project association.
- Validates payload/value against type columns and enforces serialized size ceiling.
- Optimistic revision check using ExpectedRevision is supported.
- Same active event/value replay returns existing record without a duplicate revision.
- Existing information updates revision/value/status and creates history row; new information inserts record and history.
- Audit logging follows the information/history write sequence (exact transaction/recovery is not demonstrated).
- `saveWithLock` wraps save with ScriptLock.

## Approval/live-reference observations
- Approval requires project/type/current information and checks source-project status for duplicates.
- Approval replay of already-approved revision returns sync result; otherwise history action APPROVED and audit are recorded, then live-reference sync is invoked.
- Live-reference conflict resolution requires target to be linked to source, LIVE_SYNC policy, recorded conflict, expected target revision match, source revision match, and a valid USE_SOURCE/KEEP_LOCAL decision.
- USE_SOURCE on file-based information is explicitly rejected in the observed conflict path; safe file transfer remains pending.
- History is tenant-filtered in the observed history method.

## G-03/G-10 gaps
- Exhaustive history mutation paths, payload snapshot semantics, conflict logging/recovery, file-based live sync and caller/report consistency remain to trace.
- Multi-write atomicity between current value, history, audit and sync log is not demonstrated.
- Permission/tenant/object ownership negative tests and concurrent revision tests are not executed.

## Status
G-03 and G-10 remain OPEN; G-01 remains open pending operation/caller mapping. This is source evidence only.

## Safety
No production changes, tests, SQL/DDL, migration, live data, provisioning, VPS or deployment.
