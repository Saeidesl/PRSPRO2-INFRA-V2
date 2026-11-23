# Task 2.8 Readiness Audit — Attachment Association, Storage & Lifecycle Trace (Increment 159)

Date: 2026-11-23
Scope: Read-only source review of AttachmentService.js, AttachmentRepository.js and GoogleDriveStorage.js in PRSPRO2BACKEND-PRODUCTION. No production repositories changed.

## Observed attachment model
- Attachments are records in 15_Attachments and repository methods are generic sheet reads/find/insert/update.
- AttachmentService supports associations with EntityType PROJECT or EVENT and EntityID, with TenantID, FileName, MimeType, FileSize, StorageProvider, StorageKey, UploadedBy, UploadedAt, AttachmentType, Status. Copy paths also include SourceAttachmentID (added lazily).
- No observed relational FK enforcement or unique constraints in these sampled repository/service paths. This is not evidence that none exists elsewhere.

## Authorization and parent visibility
- _isAttachmentVisibleToUser_ verifies attachment tenant and resolves its project/event parent; it verifies parent tenant and project visibility. Project attachments inherit project availability; project-linked event attachments inherit parent-project visibility; independent events are allowed at the sampled visibility branch.
- list/get/listByEntity/upload/download/thumbnail/delete paths perform tenant/visibility checks. listByEntity accepts only PROJECT or EVENT and checks entity/parent access.
- Upload validates entity existence and tenant scope, and parent project visibility for project-linked events. UploadedBy is sourced from session user.
- Gallery paths exclude VOID/CANCELLED events and deleted/void attachments and limit to images; sampled image-count/gallery helpers use tenant/project visibility.

## Upload and storage path
- Upload uses AttachmentValidator.validateCreate, server-generated filename rules for projects, and StorageRepository.uploadBase64; metadata is inserted then audit is written. These operations are sequential. A failure after storage creation but before metadata/audit completion can leave orphan storage or incomplete audit; compensating cleanup/atomicity was not evidenced in the ordinary upload path.
- GoogleDriveStorage accepts only configured allowed MIME types, checks maximum size, decodes base64 and performs a PDF signature check for PDF uploads. No comprehensive content-signature/virus-scanning evidence was found in this bounded sample.
- Drive folders can be configured or discovered/created by application name; tenant upload helper creates/reuses TENANT_<safeTenant> folders. The ordinary upload flow calls uploadBase64 without a destination folder argument in the sampled service.
- Provider is hard-coded as GOOGLE_DRIVE in metadata paths; StorageKey is the Drive file ID. copyFile places copied files in the root folder.
- Download returns bytes encoded in base64 after metadata and parent visibility checks. Thumbnail is image-only and validated as image MIME. Storage provider adapter is directly dependent on DriveApp.

## Copy and deletion
- copyProjectImage checks source/target tenant, visibility, attachment type, active status and image MIME, then copies physical storage, inserts new attachment metadata with SourceAttachmentID and audit; on metadata insertion failure it attempts storage cleanup. Existing copy lookup is tenant/project/source/status-scoped.
- Ordinary delete performs storage deletion (Drive trash) before updating attachment status to DELETED and then auditing sequentially. If metadata/status/audit fails after the physical deletion, consistency can diverge. There is no evidenced restore/untrash path in this sample.
- Project deletion previously documented as dependency-aware and includes attachment/storage handling, but cascaded multi-record and storage operations are not transactionally demonstrated; retain/recover semantics remain unresolved.

## V2 design implications (requirements, not decisions)
1. Model attachment metadata relationally with tenant-qualified owner references or explicit owner association tables; prevent cross-tenant/orphan associations by constraints and service authorization.
2. Define owner scope and allowed attachment types for PROJECT, EVENT and any future owner types before polymorphism is expanded.
3. Separate storage provider abstraction from domain persistence; use provider-neutral keys, object metadata, checksum, versioning, and lifecycle state.
4. Prefer upload intents/direct streaming, size limits, MIME/content validation, malware scanning policy, and access-controlled download streaming; avoid routinely transporting large base64 payloads.
5. Make storage and metadata lifecycle recoverable: staged upload, finalize transaction, compensating cleanup, deletion tombstone, restore policy, reconciliation/orphan scanner, and durable audit/outbox.
6. Preserve source-copy lineage and establish idempotency/uniqueness for copies.
7. Explicitly define deletion retention, soft-delete, purge, recovery, and storage orphan handling across project/event deletion.
8. Migration must inventory legacy Drive files and associations without reading/extracting production data during this audit; later rehearsal needs checksums, missing-file reports, duplicates, orphan reconciliation, and rollback plan.

## Evidence limitations / readiness
- Source-level observations only. No live Drive, production data, user files, secrets, runtime calls, or tests accessed.
- Router route gates, attachment validator/config, StorageRepository adapter, project deletion collector and full event/attachment paths were not exhaustively traced in this increment.
- G-04 and G-11 remain OPEN/BLOCKER or HIGH per register; G-01/G-10 and other readiness gaps remain unchanged. Schema readiness remains NOT APPROVED.
