# Task 2.8 Increment 212 — Attachment ownership/storage lifecycle source trace

Date: 2026-11-24

## Source
Read-only trace of AttachmentService.js, blob SHA `74492d147499024b6416555a31dd1ee294a746cb`.

## Observed owner and tenant checks
- Visibility helper requires attachment TenantID and loads the owning PROJECT or EVENT entity; entity tenant must match.
- PROJECT visibility delegates to project-availability check.
- EVENT with ProjectID also checks parent project tenant and availability; event without project uses tenant-scoped event visibility.
- Upload validates data, current user and entity type (PROJECT/EVENT), verifies owner entity tenant and project visibility, and for project-linked EVENT uploads also checks parent-project visibility.
- UploadedBy is derived from session/current user; project file name canonicalization is based on authorized project data.
- Download/thumbnail paths check tenant, owner visibility, status and image type where relevant.

## Upload sequencing
The traced upload path writes the file to StorageRepository first, constructs attachment metadata with StorageKey and tenant/entity ownership, inserts metadata, then AuditService.log. These are separate operations; no transaction/compensating recovery is demonstrated in this source path.

## Delete sequencing
The traced delete path requires a reason, validates tenant and owner visibility/status, deletes the storage object first, updates attachment metadata to DELETED, then records audit. This storage-first sequence exposes a recovery/reconciliation question if metadata update or audit fails after storage deletion. This is a potential failure surface, not an observed runtime failure.

## Project-image copy
copyProjectImage validates source and target tenant, project visibility, source PROJECT ownership, image MIME/status/type (PROFILE or INFORMATION), and checks for existing active copy by SourceAttachmentID before copying storage and creating a clone. Further metadata/audit sequencing needs complete trace and tests.

## G-04 closure evidence still missing
- Complete owner taxonomy and all attachment owner/path categories (including templates, event attachments and reference copies).
- Storage object reconciliation, missing-object detection, orphan-object inventory, restore/retention/purge policy and recovery tooling.
- Fault-injection evidence for storage/metadata/audit ordering.
- Exact project/event visibility and cross-tenant negative tests.
- External storage/provider independence and migration/retention requirements.

## Status
G-04 remains OPEN; G-10 and G-01 remain OPEN. Source evidence is not runtime/test proof.

## Safety
Read-only source audit only. No production modification, tests, live data, SQL/DDL, migration, VPS or deployment.
