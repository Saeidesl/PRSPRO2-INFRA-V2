# Task 2.8 — PUT/DELETE Route Crosswalk Increment 39
Date: 2026-11-22
Scope: Read-only Router.js method dispatch inventory for selected PUT/DELETE route families.

## PUT families
- Project status update: PROJECT_STATUS_MANAGE.
- Role update: ROLE_MANAGE.
- Department update: DEPARTMENT_MANAGE.
- Information type/column update: SYSTEM_SETTINGS.
- Print template update: SYSTEM_SETTINGS; project binding update relies on service-level source-specific authorization (Increment 36).
- Material specification revise: PROJECT_EDIT.
- Project update: PROJECT_EDIT, with same-tenant own-DRAFT PROJECT_CREATE fallback.
- Workflow event rules/workflow update: WORKFLOW_MANAGE.
- Form/form field update: SYSTEM_SETTINGS.
- Event type update: EVENT_TYPE_MANAGE.
- Event form response amendment: EVENT_EDIT.
- User update: USER_EDIT.
- Project information save: PROJECT_EDIT (POST branch documented previously).

## DELETE families
- Project information deactivate: PROJECT_EDIT.
- Information-type column and information-type deactivate: SYSTEM_SETTINGS.
- Print-template deactivate: SYSTEM_SETTINGS.
- Admin project delete: explicit admin-only.
- Admin session revocation: explicit admin-only.
- Role permission removal: ROLE_MANAGE.
- User-role removal: USER_EDIT.
- Workflow department-step removal: WORKFLOW_MANAGE plus workflow/assignment relationship check.
- Form field removal: SYSTEM_SETTINGS.
- Event-type form unassignment: EVENT_TYPE_MANAGE or SYSTEM_SETTINGS.
- Attachment deletion: ATTACHMENT_DELETE.

## Special observations
- Some mutations are soft deactivation or relationship removal rather than physical delete; underlying service behavior must be mapped to retention and dependent-effect rules.
- Project deletion and attachment deletion failure boundaries remain documented in increments 19/20; no atomicity/recovery proof.
- Event/form amendments, workflow changes, schema configuration and permission removal require tenant-qualified constraints and immutable audit/outbox behavior in V2.

## Coverage interpretation
Dispatch gates and handler names were inspected in source. This does not prove every service's own tenant guard, complete path/method caller mapping, runtime authorization, atomicity or tests. Tests not executed.

## V2 requirements
- Preserve deactivate-vs-delete distinctions; prohibit destructive hard delete where facts/history/dependencies require archival.
- Authorization, tenant ownership, concurrency and state-transition validation at service/domain layer plus DB constraints.
- Route tests for each operation and destructive/admin edge; fault-injection/recovery tests for multi-step operations.

## Limits
Read-only Router trace. G-01/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production modifications.
