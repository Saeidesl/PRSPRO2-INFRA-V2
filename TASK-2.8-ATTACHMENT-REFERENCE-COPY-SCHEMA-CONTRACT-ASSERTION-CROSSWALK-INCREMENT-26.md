# Task 2.8 — Attachment, Reference Copy & Schema Contract Assertions Increment 26
Date: 2026-11-22
Scope: Read-only inspection of actual assertions in selected backend tests.

## Attachment test assertions (`tests/attachment-service.test.js`)
- denies listing attachments for event in a project not visible to user;
- denies uploading to an inaccessible event;
- permits listing for an event in visible project.
Limits: sample is narrowly scoped to event project visibility; it does not assert upload-storage compensation, delete recovery, copy failure cleanup, file authorization lifecycle, thumbnails, metadata/storage consistency or all polymorphic owner types.

## Repeat-reference project copy assertions (`tests/project-repeat-reference-copy.test.js`)
- repeat creation persists a copy plan rather than inline copying;
- copy one information type at a time and insert history/sync log with ScriptLock;
- handles SKIPPED_FILE and FAILED states;
- active/deleted status behavior for copied information;
- stable operation ID for response recovery;
- initializes live-sync baseline and idempotency log;
- sync conflict resolution uses source revision/value;
- policy defaults from workflow and ignores client overrides;
- workflow policy validates active tenant types and published snapshot;
- copy route is POST and sampled access permissions require project edit/create.

## Schema-contract assertions (`tests/schema-contract.test.js`)
- material specification and calculation-history schema headers;
- DatabaseSetup append-only missing headers behavior and preservation of data;
- event idempotency key/status schema and source code lock/replay/completed path;
- deployment workflow is manually triggered and guarded by confirmation/config variables. This review treats workflow as repository source only; no deployment attempted.

## Coverage interpretation and gaps
These tests represent useful source assertion coverage. They do not establish execution/pass status or comprehensive runtime protection. Attachment lifecycle failure boundaries and storage reconciliation remain weakly demonstrated. Schema-contract tests check source patterns rather than PostgreSQL DDL/migration correctness (legacy schema only).

## V2 recommendations
- Attachment storage lifecycle state machine, checksum, idempotent object operations, transactional metadata/outbox, compensating reconciliation and polymorphic owner resolution.
- Reference-copy durable operation record and progress/retry semantics; transactional batches and tenant-qualified references.
- Migration/DDL verification via PostgreSQL integration tests, constraint tests and schema drift checks.
- Keep deploy authorization/approvals external and segregated from audit discovery.

## Limits
Source-only inspection; tests not run. G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migrations, provisioning, deployment or production modification.
