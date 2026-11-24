# Task 2.8 — Increment 276: File upload/storage boundary and content integrity cross-check (2026-11-24)

## Scope
Read-only review of AttachmentValidator, GoogleDriveStorage and AttachmentService storage access paths.

## Observations
- AttachmentValidator allowlists MIME and extension pairs, checks configured MIME allowlist, filename constraints, and approximate Base64 size before decoding. GoogleDriveStorage verifies decoded byte size and PDF signature.
- Content-signature validation appears limited to PDF in the inspected storage implementation; image and Office document payloads rely on declared MIME/extension rather than comprehensive content sniffing. This is a security hardening/test candidate, not a confirmed exploitable defect.
- Drive storage root may be selected by configured folder ID, otherwise first folder matching application name is used or a new folder is created. Folder-selection uniqueness and ownership/access policy require operational verification.
- AttachmentService performs tenant/entity visibility checks in inspected upload/get/download/delete paths. StorageRepository is a thin adapter.
- Trashing Drive files and database metadata updates are separate operations; failure/retry consistency and orphan-file reconciliation need testing.
- No confirmed defect.

## Test targets
FS-01..FS-12: MIME spoofing for each accepted type, PDF signature mismatch, image signature mismatch, Office content validation, oversize decode, corrupt Base64, traversal-like filename inputs, folder selection ambiguity, file sharing/permission boundary, upload Drive success but metadata failure, metadata soft-delete vs Drive trash failure, orphan reconciliation and idempotent deletion.

## Limits
Static only; tests and failure injection not executed; no production data/writes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
