# Task 2.8 Frontend Form-File Upload & Reference Semantics Trace — Increment 164

Date: 2026-11-23. Read-only frontend source trace.

## Source reviewed
- Legacy frontend PRSPRO2-PRODUCTION: js/modules/events.js (bounded file-field upload/response and image-preview/gallery excerpts), js/modules/attachments.js, js/components/form.js.
- No frontend changes.

## Actual observed file field behavior
- Configured reusable event form fields with file/image columns upload before event response submission.
- Upload route observed: POST /projects/{projectId}/attachments, passing FileName, MimeType, Base64 and AttachmentType. This is project-scoped rather than an event-owned upload in the inspected frontend path.
- Upload value embedded into the form response is a metadata object containing AttachmentID, FileName, MimeType, FileSize and AttachmentType. This is a reference payload rather than raw binary, but durable referential integrity is not evidenced.
- Image fields allow multiple selected files and are handled by uploadConfiguredFormImages; general file fields upload one selected file in sampled row path.
- The UI stores prior upload metadata in dataset.uploadedAttachment and in per-file maps, then reuses it when collecting form responses. Hidden fields and retained attachments are handled specially by form collection.
- For project-based form upload, AttachmentType uses PROFILE (toggle) or INFORMATION; default logic can assign INFORMATION. Backend validator requires PROJECT attachment type format and accepts PROFILE default for image omission.
- Independent-event file upload is explicitly rejected in the inspected helper because no project ID is available.
- Frontend accepted formats align with images/PDF/Word/Excel allowlist and uses 10 MB client cap. Client validation does not replace server validation.

## Semantics/risk
- The form-response value references an AttachmentID, but the uploaded file metadata owner remains PROJECT in this observed path. Thus form response/file linkage appears reference-based and project-owned, not directly event-owned.
- Multiple form responses may theoretically reference same attachment unless a policy or server-side constraint prevents it; the inspected trace does not establish exclusive ownership.
- Deleting/clearing/revising form responses does not establish automatic file deletion. Reference lifecycle, duplicate reference, clear/remove, and orphan cleanup policy remains undiscovered.
- V2 should model attachments as separate entities with explicit project ownership and a typed reference/link table or validated FK strategy for form response values; define reuse, clear/remove, amendment, and deletion behavior without orphaning referenced files.
- Migration needs preserve attachment references and detect missing/cross-project/invalid reference IDs; don't assume one-to-one ownership.

## Readiness
- G-03, G-04, G-09, G-10, G-11 remain open; schema readiness NOT APPROVED.
- Source-only: no live file/data access, tests, production changes, schema, migrations, or deployment.
