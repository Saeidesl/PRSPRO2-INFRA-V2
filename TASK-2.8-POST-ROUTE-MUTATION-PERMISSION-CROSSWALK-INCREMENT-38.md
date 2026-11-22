# Task 2.8 — POST Route Mutation Permission Crosswalk Increment 38
Date: 2026-11-22
Scope: Read-only POST Router dispatch trace across project, workflow, schema/config, event, attachment and RBAC operations.

## Project operations
- `/projects`: PROJECT_CREATE.
- Material specification create: PROJECT_EDIT, with own same-tenant DRAFT fallback to PROJECT_CREATE.
- Reference-copy `/projects/{id}/reference-copy/{type}`: PROJECT_EDIT, own same-tenant DRAFT fallback to PROJECT_CREATE.
- Central information save/approve/reference resolve: PROJECT_EDIT.
- Manual complete-step: EVENT_CREATE.

## Workflow
- `/workflows` create, publish, step create and department assignments: WORKFLOW_MANAGE.

## Schema/configuration
- Information type and columns create: SYSTEM_SETTINGS.
- Form create/add field: SYSTEM_SETTINGS.
- Event type form assignment: EVENT_TYPE_MANAGE or SYSTEM_SETTINGS.
- Event type create and procurement integration config save: EVENT_TYPE_MANAGE.

## Event/attachment
- `/events`: EVENT_CREATE.
- Event attachments upload: ATTACHMENT_UPLOAD and owner EntityType/EntityID set from route.
- Project attachments upload: ATTACHMENT_UPLOAD and owner set from route.
- `/attachments`: ATTACHMENT_UPLOAD.

## RBAC/user
- User create: USER_CREATE.
- User role assignment: USER_EDIT.
- Role permission single assignment and bulk operations: ROLE_MANAGE.

## Risk/coverage caveats
- This is a bounded route crosswalk. Service-level validation, tenant qualification, payload constraints, concurrency and effects are not exhaustively traced.
- Own-DRAFT permission fallback is a special-case path and needs tests proving tenant, creator and state requirements cannot be bypassed.
- POST idempotency and transactional guarantees are separately documented for events, procurement, reference copy and attachment operations but remain not runtime-tested.

## V2 requirements
- Route method/path permission matrix, explicit domain authorization, tenant-scoped reads/writes and transaction boundaries.
- Route tests for special DRAFT fallback and owner binding, cross-tenant rejection, payload validation, concurrency and idempotency.
- Separate system configuration authorization from operational mutation and preserve business workflow.

## Limits
Read-only route review; tests not executed. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migrations, provisioning, deployment or production changes.
