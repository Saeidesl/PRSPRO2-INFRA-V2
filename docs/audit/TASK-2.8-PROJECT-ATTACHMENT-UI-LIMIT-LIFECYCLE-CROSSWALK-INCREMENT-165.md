# Task 2.8 Project Attachment UI Limit & Upload Lifecycle Crosswalk — Increment 165

Date: 2026-11-23. Read-only frontend source.

## Project creation/edit upload
- Frontend js/modules/projects.js uses POST /projects/{projectId}/attachments after project save for a selected project attachment.
- Selected project attachment client-side size limit in the inspected path: 5 MB.
- AttachmentType is selected, with PROFILE image-specific validation. Non-profile attachment selection is checked against ATTACHMENT_UPLOAD permission.
- For event-form file columns, prior increment traced a different path with 10 MB client-side limit and upload-before-response; this yields differing client caps (5 MB in project form sample vs 10 MB event-form sample), subject to server configured max and validators.

## V2 design consideration
- Centralize upload policy (per owner/field/category) with server-enforced quota/limits and consistent frontend messaging, rather than preserving inconsistent client limits as business rules.
- Make upload-before-project/event save recoverable; project edit flow saves project first then uploads selected file, so file upload failure may leave project edit committed but attachment step failed. UI error/retry and user-visible state need explicit design.
- Include staged upload/attachment finalization and orphan reconciliation; distinguish user-facing file selection from completed persisted attachment state.

## Evidence boundary
Read-only trace; no runtime testing, files/data, production changes or migration. G-04/G-09/G-11 remain open; schema readiness NOT APPROVED.
