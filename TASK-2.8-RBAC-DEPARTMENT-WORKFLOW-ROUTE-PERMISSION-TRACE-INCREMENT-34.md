# Task 2.8 — User, Role, Permission, Department & Workflow Route Permission Trace Increment 34
Date: 2026-11-22
Scope: Read-only Router.js trace for selected RBAC, department and workflow read route gates.

## Users/RBAC GET routes observed
- `/users`: USER_VIEW.
- `/users/{id}/roles`: USER_EDIT.
- `/roles/available-permissions`: ROLE_MANAGE.
- `/roles`: ROLE_MANAGE or USER_EDIT.
- `/roles/{id}/permissions`: ROLE_MANAGE.
- `/permissions`: PERMISSION_MANAGE.
- Role/permission mutation families sampled elsewhere in Router require ROLE_MANAGE; user-role assign/remove requires USER_EDIT; user create/update requires USER_CREATE/USER_EDIT.

## Department GET routes observed
- `/departments` and `/departments/{id}` use `requireAny` over DEPARTMENT_MANAGE, USER_VIEW, USER_CREATE, USER_EDIT, WORKFLOW_MANAGE, EVENT_TYPE_MANAGE, EVENT_CREATE.
- The breadth of this OR permission list is intentional source behavior but least-privilege appropriateness has not been validated against business roles or runtime tests.

## Workflow GET routes observed
- `/workflows` permits WORKFLOW_MANAGE, PROJECT_CREATE or SYSTEM_SETTINGS (project creators may read choices).
- `/workflows/{id}/steps/event-rules` and management-data require WORKFLOW_MANAGE.
- `/workflows/{id}/steps` permits WORKFLOW_MANAGE or PROJECT_VIEW.
- Other workflow path operations in the sampled prefix require WORKFLOW_MANAGE, with separate branch dispatch.

## Interpretation / risk
Explicit gates are present, but broad `requireAny` lists and route-prefix dispatch increase the need for route-specific least-privilege tests. Source does not prove business-role correctness, service tenant checks, or runtime authorization outcomes. Route patterns with other methods and branch paths require separate review.

## V2 recommendations
- Define permission matrix from business operations, not legacy OR lists alone; preserve intended read access but document why each permission is allowed.
- Include role/permission lifecycle, tenant-scoped role assignment, inactive role handling and permission revocation tests.
- Tenant-qualified DB constraints for role/user/dept associations and route-specific authorization tests.

## Limits
Read-only Router trace; tests not executed. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production modifications.
