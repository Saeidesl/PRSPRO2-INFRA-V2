# Task 2.8 — Service-level authorization and storage boundary trace (Increment 284)
Date: 2026-11-24
Backend reference snapshot: 8253eb3718d1a2a1bd5de03b6b5090ba69b94b67
READ-ONLY.

## Traces
### Print-template project binding
Router PUT branch has no route-level permission check, but PrintTemplateService.updateProjectBinding loads current user, validates template tenant and active state, binding existence and LATEST selection, resolves project data with user context and expected-source conflict protection, then requires PROJECT_EDIT or EVENT_EDIT according to source type and delegates to specialized update methods. The prior candidate is resolved as covered by service-level checks in source, though runtime tests remain pending.

### User-role assignment
UserRoleService.assign validates user and role exist, both in current tenant, role active, tenant equality and duplicate prevention; assignment carries tenant ID and audit log. Remove verifies target user tenant, mapping tenant and mapping existence. Runtime/multi-tenant negative tests pending.

### Workflow department assignment removal
Router checks WORKFLOW_MANAGE and validates workflow/assignment relationship before service call. Service independently checks assignment tenant, then sets INACTIVE. Service remove does not visibly revalidate workflow association; Router performs that relationship check. Direct service invocation must remain inaccessible except controlled server callers. Negative IDs and tenant tests pending.

### Attachments
Service read/list/download/upload/delete/copy operations perform tenant and resource visibility checks. Event resources inherit parent project access where ProjectID exists; independent events remain tenant scoped. Upload validates target entity and storage data. Copy validates source/target visibility, type and image type, then cleans up storage on metadata insert failure.
- Delete sequence calls StorageRepository.delete before metadata update and audit. This is a failure window: storage deletion can succeed but metadata update fail, leaving active metadata pointing to absent storage. Recovery/failure-injection not tested.
- Storage and Sheets metadata are non-transactional, consistent with prior audit storage risk.

### Role-permission mapping
Router gates operations with ROLE_MANAGE; service/repository trace and tenant validation details remain insufficiently confirmed from this increment, so retained as an open trace rather than assumed secure.

## Evidence status
Static source trace only, E1. No tests executed, runtime checks, tenant probes, or failure injection.

## Follow-up
Trace RolePermissionService mapping validations, inspect Attachment delete reconciliation and ProjectDeletion sequencing, and add these specific risks to cross-domain evidence matrix.
