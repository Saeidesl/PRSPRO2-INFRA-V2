# Task 2.8 — Attachment Visibility, Copy Idempotency and Cleanup Trace (Increment 104)

Date: 2026-11-23
Scope: READ-ONLY source review of AttachmentService visibility, listing, and copyProjectImage.
Evidence level: E1.

## Findings
- Attachment visibility checks tenant ownership and resolves parent project/event; project visibility is delegated to ProjectService access policy. Event attachments for tenant-owned independent events are allowed by the visible helper.
- General list excludes DELETED records and applies visibility; the helper treats VOID as not downloadable for thumbnail but general listing only checks DELETED before helper. Potential status-policy consistency discrepancy requires verification.
- Project image listing derives visible projects then filters image type, profile attachment type, and DELETED/VOID status; returns latest per project.
- copyProjectImage restricts source to visible same-tenant project image attachments of PROFILE/INFORMATION types, validates target visibility, and records SourceAttachmentID.
- copyProjectImage uses existing-copy lookup to make repeated requests effectively idempotent at service level. However concurrent copies may race absent a demonstrated DB unique constraint.
- copyProjectImage wraps metadata insert/audit in try/catch and attempts storage cleanup on failure, a compensating pattern not visible in the regular upload method inspected in Increment 103.

## V2 implications
- Consolidate attachment visibility and status policy; distinguish independent-event access from project-linked event access explicitly.
- Use database unique key for source-to-target copy deduplication, plus idempotency token.
- Preserve compensating cleanup and/or staged operation reconciliation for upload/copy.
- Test status inconsistencies, permission boundaries, repeated/concurrent copy, and storage cleanup failures.

## Limitations
Source-level only. No tests, live data, production modifications, SQL/DDL, migration or deployment. These are policy/consistency questions, not confirmed vulnerabilities. G-01/G-04/G-10/G-11 remain open.
