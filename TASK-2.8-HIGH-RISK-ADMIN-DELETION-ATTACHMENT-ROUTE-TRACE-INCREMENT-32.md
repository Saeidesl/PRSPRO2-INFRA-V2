# Task 2.8 — High-Risk Admin, Destructive & Attachment Route Permission Trace Increment 32
Date: 2026-11-22
Scope: Read-only focused Router.js source trace for project deletion preview/remove, admin session management and attachment route gates.

## Project deletion
- GET `/admin/projects/{id}/deletion-preview`: resolves current user and explicitly requires `AccessControlService.isAdmin`; dispatches ProjectDeletionService.preview.
- DELETE `/admin/projects/{id}`: resolves current user and explicitly requires admin; dispatches ProjectDeletionService.remove with confirmation code/project code.
- ProjectDeletionService dependency and failure boundaries were separately documented in Increment 19; route gates are now crosswalked. Full runtime auth, project-ID encoding edge cases and failure recovery not verified.

## Admin sessions
- GET `/admin/sessions`: explicitly requires admin before SessionManager.listForAdmin.
- DELETE `/admin/sessions/{sessionId}`: explicitly requires admin before revokeForAdmin; current token supplied for session protection.
- Session lifecycle, revocation persistence and token race behavior not runtime tested.

## Attachment access/mutation
- GET `/attachments` and GET `/attachments/{id}`, thumbnail, download routes require `ATTACHMENT_VIEW` at Router.
- GET event attachment list requires `ATTACHMENT_VIEW`.
- POST `/attachments` and project attachment route require `ATTACHMENT_UPLOAD`; project route binds EntityType/EntityID from path rather than trusting submitted ownership values.
- AttachmentService performs additional owner/tenant/project access checks in sampled flows; failure-boundary review in Increment 20 remains applicable.
- This is a bounded route trace. Other attachment/event paths, owner types and runtime permission checks are not exhaustive.

## Risk notes
Positive explicit route gates were observed for these sampled high-risk routes. However, tests are not executed, no dedicated route authorization test evidence was established, and admin/tenant checks in service or repository are not fully verified for every route variant.

## V2 recommendations
- Require audited admin actions with actor/session, reason, operation correlation ID and transactional state changes.
- Deletion dry-run and actual delete should share a validated dependency manifest; execution should be transactional/archivable with recovery/outbox.
- Attachment route + owner visibility checks must be duplicated defensively at service and storage authorization layers; DB should enforce tenant ownership where possible.
- Test privilege denial, cross-tenant identifiers, admin exceptions, session self-revocation and attachment owner polymorphism.

## Limits
Source-only route review; no tests executed. G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production modifications.
