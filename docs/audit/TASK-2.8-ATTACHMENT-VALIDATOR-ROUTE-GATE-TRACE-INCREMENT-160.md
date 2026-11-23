# Task 2.8 Attachment Validation & Route Gate Crosswalk — Increment 160

Date: 2026-11-23. Read-only source audit.

## Validation
AttachmentValidator.validateCreate:
- Restricts EntityType to PROJECT or EVENT.
- Requires entity ID, filename, MIME, base64; checks input types.
- MIME allowlist and extension matching from FILE_TYPES and AppConfig allowlist.
- Filename rejects path separators/control characters, dot/dotdot and lengths >240.
- Base64 length pre-check against configured max size; actual byte length is checked in GoogleDriveStorage.
- AttachmentType only permitted on PROJECT records with constrained identifier format; project image defaults to PROFILE when omitted.
- Allowlist observed includes JPEG/PNG/WebP/GIF, PDF, legacy DOC, DOCX, XLS, XLSX. Storage provider's PDF signature check exists, but sampled implementation does not demonstrate signature validation for every file type or malware scanning.

## Router permission gates (bounded excerpt)
- GET /attachments and GET /attachments/:id and /download or /thumbnail variants require ATTACHMENT_VIEW; service repeats tenant/parent-visibility checks.
- Event attachment listing under /events/:id/attachments requires ATTACHMENT_VIEW after event handling.
- Project gallery/count endpoints combine project/event visibility permission and ATTACHMENT_VIEW.
- Upload/delete dispatch branches were not fully captured in this bounded pass and remain to be traced; do not infer missing/complete permission coverage.

## V2 requirements and gaps
- Keep authorization centralized and owner-qualified; enforce tenant-scoped relational association and storage access.
- Reassess extension/MIME validation and use actual file signatures, safe scanning, quotas, upload/download streaming and least-privilege storage.
- Preserve non-disclosing not-found behavior across tenant boundaries.
- Define durable upload/finalization/deletion workflows and recovery.
- G-04/G-11 and permission/cross-tenant validation remain open; no schema-readiness approval.

No live data, secrets, Drive files, runtime, tests, production modifications, SQL or migration accessed.
