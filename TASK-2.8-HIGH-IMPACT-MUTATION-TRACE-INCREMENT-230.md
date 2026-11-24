# Task 2.8 Increment 230 — High-impact mutation tracing: event, project, attachment, procurement (2026-11-24)

## Scope and evidence
READ-ONLY review of production frontend modules and backend router/service files. Retrieved:
- Frontend: js/modules/events.js, projects.js, procurement.js, attachments.js, forms.js, workflows.js
- Backend: Router.js, EventService.js, ProjectService.js, AttachmentService.js, ProcurementService.js, ProjectDeletionService.js
No production modifications and no tests executed.

## Crosswalk observations (static, partial)

### Event creation / project step completion
- Frontend events module calls POST /events and PUT /events/{eventId}/forms/{formId}; it also calls POST /projects/{projectId}/complete-step.
- Router GET /events and /events/{id...} routes require EVENT_VIEW; event form-response update route is gated by EVENT_EDIT.
- Router POST /projects/{id}/complete-step requires EVENT_CREATE; EventService.completeStep validates project TenantID, availability-to-user, assignment-based completion permission, configured manual completion and requirements satisfied, and handles already-completed state.
- EventService.create implements optional idempotency-key handling and user-scoped locking; legacy clients without a key retain original behavior.
- Exact create-route guard/caller crosswalk remains incomplete; these are observations only.

### Project create/update/reference copy
- Frontend calls project create, update, attachment, and reference-copy endpoints.
- ProjectService.create derives TenantID from authenticated user, validates project creation and handles reference-copy operation ID deduplication.
- ProjectService.update checks existence, TenantID and availability-to-user; draft lifecycle rules are also enforced.
- Router's reference-copy mutation has PROJECT_EDIT with fallback restricted to tenant-scoped draft ownership and PROJECT_CREATE. More detailed service-level tracing and tests remain pending.

### Attachment lifecycle
- Frontend attachment calls include POST /attachments, project/event upload routes, download and delete.
- Router upload endpoints are gated by ATTACHMENT_UPLOAD; attachment download/list routes by ATTACHMENT_VIEW; delete route requires ATTACHMENT_DELETE.
- AttachmentService.upload validates entity type, loads project/event entity, and checks tenant match; project entities also check project availability.
- AttachmentService.delete checks tenant and visibility, rejects already-deleted records, then calls StorageRepository.delete before metadata status update and AuditService.log.
- Risk to verify with failure-injection tests: ordering of storage deletion before metadata update/audit may leave metadata/audit inconsistent if later operations fail. This is a potential failure-mode, not a confirmed defect. Determine storage adapter semantics, retries/idempotency, and recovery before schema design.

### Procurement receipt batch
- Frontend posts /procurement/receipts/batch.
- Router requires PROCUREMENT_MANAGE.
- ProcurementService.createReceiptsBatch validates receipt-array presence, performs schema ensure, and attempts script lock acquisition (10 seconds); remaining algorithm/tenant allocation/idempotency checks not fully traced in this increment.

## Unresolved gates
- Full caller-to-router-to-service-to-repository trace not complete.
- Tenant boundary and role-negative tests absent.
- Attachment failure ordering requires targeted tests.
- Procurement batch allocation, duplicate handling, tenant and rollback behavior require further tracing.
- G-01 and G-10 remain open; schema readiness remains NOT APPROVED.

## Next
Increment 231: continue procurement batch tracing (tenant scoping, order/item/project allocation, idempotency, lock release, error rollback) and verify frontend receipt workflow against service behavior.
