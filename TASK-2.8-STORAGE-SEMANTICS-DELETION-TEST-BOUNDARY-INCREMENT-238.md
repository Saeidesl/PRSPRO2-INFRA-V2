# Task 2.8 — Storage semantics and deletion test boundary (Increment 238)
Date: 2026-11-24
Read-only review: GoogleDriveStorage.js plus tests/project-deletion.test.js and tests/attachment-service.test.js in production backend.

## Storage behavior observed
- GoogleDriveStorage.delete(key) resolves Drive file by ID and calls Drive File.setTrashed(true), returning true. It trashes rather than permanently deleting.
- StorageRepository.delete delegates directly to this behavior.
- Project cascade invokes storage delete after attachment metadata soft-delete, collects storage failures, and only physically removes the project when failure list is empty.
- Tenant folder support exists for uploadBase64ToTenant; copyFile path uses root folder. This increment did not fully audit upload routing/folder ownership.

## Test evidence boundary
- project-deletion.test.js exercises _collect dependency selection, tenant/project boundaries, shared order preservation and inactive/VOID event retry selection.
- It does not invoke remove() orchestration in inspected tests; no staged-write, storage, audit, lock or injected failure tests observed in this file.
- attachment-service.test.js covers selected visibility and upload denial/allow cases, but inspected file does not exercise delete lifecycle/storage failure or audit failure.
- Test files were read, not run.

## Risks (not confirmed defects)
- Retry after partially soft-deleted attachments but failed storage deletion: collect() may exclude deleted metadata, potentially making the file key no longer discoverable by the normal cascade retry path. This is a hypothesis from ordering/status selection; requires isolated test verification.
- Drive trash operation may succeed but subsequent metadata/audit failure can leave cross-layer state mismatch.
- No observed durable retry queue/reconciliation mechanism in these inspected paths.

## Status
E1 static evidence only. Task 2.8 remains IN PROGRESS; G-01/G-10 open and schema readiness NOT APPROVED.
