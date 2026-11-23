# Task 2.8 Repeat-project image-reference copy trace — Increment 166

Date: 2026-11-23. Read-only backend source trace in legacy PRSPRO2BACKEND-PRODUCTION ProjectService.js.

## Findings
- Repeat-project workflow copy plan includes a dedicated project profile-image copy item and information-type copy plan (previous increments traced persisted plan and retry statuses).
- _copyReferenceImageValue_ validates the source attachment by ID, tenant, EntityType=PROJECT, EntityID=source project and image MIME before calling AttachmentService.copyProjectImage to create a copy owned by target project.
- Copied response/reference value is a new attachment reference metadata object (AttachmentID, FileName, MimeType, FileSize, AttachmentType), rather than reusing source attachment ID.
- _copyReferenceInformationImages_ scans active InformationType columns for image data types and applies image-copy logic to image codes in scalar/table values. Non-image values are not copied by this helper.
- copyReferenceInformationType has a lock and status/retry persistence. Copying is sequential at the orchestration level; multi-file copy atomicity and rollback cleanup are not proven in this trace.

## V2 implications
- Copy policy must distinguish deliberate cloning (new target-owned file, source preserved) from reuse/shared-reference.
- Copy process needs idempotency per target/reference copy operation and image/reference value, durable transaction/recovery boundaries and compensating cleanup for storage/metadata partial failures.
- Preserve validation of tenant and source project ownership; relational model should enforce attachment ownership/reference links, not just application checks.
- Audit whether copied attachments are removed if copied information revision is cancelled/cleared or target project deleted.

## Status
G-04/G-09/G-10/G-11 remain open; schema readiness NOT APPROVED. Source trace only; no live data/runtime tests/production modifications/migration.
