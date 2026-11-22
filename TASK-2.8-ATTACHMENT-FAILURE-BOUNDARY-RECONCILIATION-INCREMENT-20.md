# Task 2.8 — Attachment Failure-Boundary and Recovery Reconciliation Increment 20
Date: 2026-11-22
Evidence: targeted read-only AttachmentService source trace (upload, delete, download, copyProjectImage, visibility paths).

## Observed behavior
- Upload validates tenant/entity/project visibility, uploads object bytes to storage first, then inserts attachment metadata, then writes audit. The inspected normal upload path has no demonstrated compensation if metadata insert or audit fails after object upload; orphan risk is therefore a source-level concern, not an observed runtime failure.
- Delete checks tenant/owner visibility, calls StorageRepository.delete first, then updates metadata to DELETED, then writes audit. If metadata or audit write fails after storage deletion, state may diverge. This ordering is direct source evidence.
- `copyProjectImage` checks source/target visibility and source attachment type; it copies storage bytes before metadata insert and audit, and has cleanup for copied storage if the metadata/audit sequence throws. This is positive bounded compensation evidence for that operation only.
- Download and thumbnail paths validate tenant, owner visibility, status and image type for thumbnails; no complete storage-access/recovery audit performed.
- Owner type is limited to PROJECT/EVENT in sampled validator/service paths, but owner reference remains polymorphic and attachment visibility for event-owner paths follows linked project validation.

## V2 design implications (recommendations)
1. Adopt staged upload: quarantine/object staging, DB metadata transaction, durable storage-operation/outbox state, then finalize; reconcile orphan objects.
2. Use recoverable delete: mark deletion intent and tombstone metadata in DB, asynchronously delete storage, retry and record final state. Do not irreversibly delete bytes before durable metadata intent.
3. Keep storage provider abstraction and durable object keys; store checksum/size/content-type validation and lifecycle state.
4. Copy operation should be idempotent, record source provenance, clean partial objects and support reconciliation.
5. Tenant/typed-owner authorization must be enforced server-side at every download, thumbnail, list, copy and delete path; independent object URL access should not bypass authorization.
6. Audit failure should not silently lose operation history; transactional outbox or equivalent recovery log should capture pending audit/storage side effects.

## Limits / gate
This is source inspection only, not storage audit or runtime failure testing. No live files, secrets, tests, SQL/DDL, migration, provisioning, deployment or production writes.
G-04/G-10/G-01 remain OPEN and schema readiness NOT APPROVED.
