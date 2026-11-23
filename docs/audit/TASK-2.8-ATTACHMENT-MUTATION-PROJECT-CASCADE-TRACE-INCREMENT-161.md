# Task 2.8 Attachment Mutation Route & Project Cascade Deletion Trace — Increment 161

Date: 2026-11-23. Read-only.

## Router mutation permissions
Observed in Router.js:
- POST /attachments requires ATTACHMENT_UPLOAD and invokes AttachmentService.upload.
- DELETE /attachments/:id requires ATTACHMENT_DELETE and invokes AttachmentService.delete using supplied reason.
- The bounded earlier GET trace showed ATTACHMENT_VIEW gate for list/get/download/thumbnail. Services repeat tenant and owner visibility checks.
- The attachment-delete service does not itself visibly check ATTACHMENT_DELETE; route is the gate in this observed dispatch. This highlights need for centralized authorization testing and service-call boundary controls.

## ProjectDeletionService dependency collection
Observed dependency collector includes tenant-scoped active:
- Events and event data.
- Material needs, specs, calculations.
- Purchase orders/items/allocations/receipts, with preservation rules for shared orders/items allocated to other projects.
- Attachments associated with project directly and events belonging to collected project.
- Collection depends on rows read from configured sheets and uses tenant and owner/link checks.

## Cascade deletion orchestration
- Admin-only context, tenant validation, project-code confirmation, script lock, dependency re-collection under lock.
- Sequential soft deletion/void of child rows and status columns, attachment metadata soft-delete, then Drive trash operations, then event VOID.
- Project row is physically deleted only if earlier operations have no recorded failures.
- Audit is recorded with deleted counts and failure count; errors can be surfaced after a partial sequence.
- Script lock is not a relational transaction. Sequential sheet updates and Drive trash calls can fail partway; lock alone does not ensure rollback.
- Reconciliation/restore strategy after partial failure was not evidenced. Preview calls MaterialPlanningService schema ensure (side effect on schema), which means this preview path is not purely observational in the sampled source.

## V2 implications
1. Implement deletion as a durable, resumable workflow with explicit states, progress records, transactional database mutations where possible, external-storage saga/compensation and reconciliation.
2. Preserve shared procurement dependencies using relational constraints and explicit project allocation linkage; do not replicate legacy cascading behavior without tests/policy.
3. Prefer soft delete/tombstones and retention over physical project row deletion; define restoration and purge approvals.
4. Define deletion preview as read-only and ensure it does not silently alter schema or data.
5. Build failure-injection tests for each child persistence and external storage step; ensure repeated attempts are idempotent and recoverable.
6. Validate authorization for mutation paths at both routing and service boundaries and test cross-tenant scenarios.

## Evidence boundary
Source inspection only. No deletion previews/actions, runtime, production data/files, secrets, tests or modifications to production repositories. G-04/G-09/G-10/G-11 and mutation coverage remain open; schema readiness NOT APPROVED.
