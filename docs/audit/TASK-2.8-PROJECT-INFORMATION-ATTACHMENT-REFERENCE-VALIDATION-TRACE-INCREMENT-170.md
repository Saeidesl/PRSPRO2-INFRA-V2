# Task 2.8 — Project-information attachment-reference validation — Increment 170

Date: 2026-11-23. Read-only trace of ProjectInformationService.js scalar normalization for image/file fields.

## Observed validation
- File/image values are attachment metadata references, not arbitrary URLs/base64 values in the inspected validation path.
- AttachmentID/ID lookup verifies the attachment exists, same tenant, PROJECT owner type, exact project owner ID, active status, and allowed AttachmentType (INFORMATION; PROFILE permitted for image).
- MIME is validated against AttachmentValidator.FILE_TYPES; image requires image MIME.
- Image fields allow array form, max 20 images; non-image file fields reject array values (single attachment).
- Normalized response stores attachment ID, filename, MIME, file size and type metadata.
- Validation alone does not establish retention/cleanup/restore, immutability, content scanning, checksum, or cross-version durable association.

## V2 implications
- Attachment references should be tenant/project-scoped relational associations, not loose metadata.
- Version snapshots must explicitly determine whether referenced files are pinned to immutable attachment IDs or copied/versioned, and how deletions affect historical views.
- Single-file and multi-image cardinality rules need persisted field definitions and integrity checks; preserve the 20-image cap only if Product Owner confirms it as a business constraint, otherwise treat as legacy limit for review.
- Storage object deletion must be coordinated with DB references and retention; file MIME and content security validation at upload.

## Status
G-04/G-09/G-11 remain open. Bounded source trace only; no test, production changes, migration, live data or deployment.
