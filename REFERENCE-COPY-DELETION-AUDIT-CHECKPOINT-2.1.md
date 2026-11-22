# Reference Copy and Deletion Recovery — audit checkpoint 2.1

Read-only source review 2026-11-22 of ProjectService.js, ProjectDeletionService.js, AttachmentService.js and AuditService.js.

## Reference project creation and copy workflow (verified excerpt)
- Repeat/reference projects require source project and published repeat policy sourced from workflow (client policy overrides ignored).
- Policy per information type is LIVE_SYNC, INITIAL_COPY or INDEPENDENT; validation against active information types occurs at creation.
- Source project must be active, tenant-matched and accessible; self-reference rejected.
- Reference-copy operation ID is used for replay/recovery lookup; if an operation already created an accessible project, creation returns prior project with copy plan status.
- Copy plan is stored in ReferenceCopyStatusJSON and includes per-type policy, status and source revision. File-based information can be skipped where unsupported, and project profile image is also considered in copy plan.
- Project creation is lock-protected with tenant-scoped duplicate-code validation.
- Copy plan appears to be progressive; failure/rollback/retry mechanics for all individual copy items require inspection of remaining ProjectService methods.

## Project dependency deletion (verified)
- Admin-only preview and deletion; confirmation requires exact ProjectCode.
- Dependency collection includes events, event data, attachments, material needs, specifications, calculations, procurement allocations, items, orders and receipts.
- Shared procurement order lines/items are protected if another project has an allocation. Orders are removed only if all active items are selected for removal and no other allocation remains.
- Delete operation re-collects dependencies under lock and uses soft-delete/status operations for children; event status uses VOID due to validation constraints.
- Attachment records are marked deleted before storage files are trashed. Failures are collected.
- If any dependency processing fails, the project row is not physically deleted; otherwise project row is physically deleted after dependency handling. Audit record reports operation.
- This workflow is not a single atomic database transaction; partial failures are reported. V2 must implement transaction boundaries, explicit retention/recovery and dependency-safe deletion.

## Audit service semantics (verified)
- Audit records include ID, TenantID, UserID, Action, EntityType, EntityID, OldData JSON, NewData JSON, Reason and CreatedAt.
- Lists are tenant-filtered; entity/user filters are also tenant-scoped.
- Source coverage is explicit calls from services, not guaranteed database-wide auditing; comprehensive audit coverage remains unverified.

## Attachment findings
- Attachment visibility is tenant scoped and project/event visibility inherited from parent project; event attachments are project-access checked when event is project linked.
- Project attachment names are canonicalized by server using project code, attachment type and safe filename.
- Gallery/count queries are tenant and access filtered; event visibility excludes void/cancelled.
- Attachment deletion/recovery and retention policy remains incomplete; current cascade deletion trashes stored files after soft deletion of metadata, raising a recovery/retention design question for V2.

## Remaining discovery
- Complete progressive reference-copy retry, rollback and sync conflict paths.
- Audit event coverage and retention.
- Attachment delete/recover paths and retention.
- Full API mutation matrix and auxiliary sheet.
