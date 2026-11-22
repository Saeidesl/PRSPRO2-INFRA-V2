# Task 2.8 — Attachment Storage/Delete Lifecycle Trace (Increment 59)
Date: 2026-11-22
Status: Bounded READ-ONLY source trace.

## Sources
GoogleDriveStorage.js, StorageRepository.js, AttachmentValidator.js and AttachmentService.js from READ-ONLY production reference.

## Observations
- Storage backend currently uses Google Drive, validates allowed MIME types and upload metadata, and stores a storage key/provider in attachment metadata.
- Delete path calls Drive trash operation first, then updates attachment record to DELETED, then writes audit; operations are sequential and not atomic. Failure between storage and metadata/audit can create inconsistency.
- Upload path writes file to storage, then attachment metadata, then audit. No transaction spanning file storage/metadata/audit; cleanup is shown in copied-image flow, but regular upload path bounded trace does not establish cleanup on metadata/audit failure.
- Attachment visibility checks tenant, owner entity and project visibility; the storage abstraction is separate from authorization.
- No verified retention policy, immutable archive, backup/recovery testing, orphan-file cleanup, checksum/virus scanning, or storage migration strategy established by the sampled files.

## V2 requirements/recommendations
1. Object storage metadata and DB record creation should use staged upload/commit and orphan-cleanup workflow; use durable event/outbox for audit.
2. Deletion should be soft-delete first, with explicit retention, legal/business policy, and delayed purge; use recoverable lifecycle states.
3. Define tenant-isolated storage paths/buckets or equivalent, signed URL policy, access audit and key rotation.
4. Enforce MIME/extension/size and content sniffing, malware scan, checksums, quotas, and secure file-name handling.
5. Define backup and restore RPO/RTO, object versioning, disaster recovery and periodic recovery drills before production.
6. Cross-tenant tests, access bypass, owner deletion, storage failure, metadata/audit failure, duplicate upload, orphan reconciliation and restore tests.

## Evidence limits
Bounded E1 source review only; no runtime tests, live file data, SQL/DDL, migrations, provisioning, production writes or deployment. G-04/G-10 remain OPEN; schema readiness NOT APPROVED.
