# Task 2.8 — Authorization router permission crosswalk — Increment 179

Date: 2026-11-23. Read-only inspection of Router.js branches for Department, Role, Permission, UserRole, RolePermission.

## Route gates observed
- GET /departments and GET /departments/:id: requireAny DEPARTMENT_MANAGE, USER_VIEW, USER_CREATE, USER_EDIT, WORKFLOW_MANAGE, EVENT_TYPE_MANAGE, EVENT_CREATE.
- POST /departments: DEPARTMENT_MANAGE; PUT /departments/:id: DEPARTMENT_MANAGE. No department DELETE route found in inspected Router delete branches.
- GET /roles: requireAny ROLE_MANAGE or USER_EDIT; role permission list /roles/:id/permissions requires ROLE_MANAGE.
- GET /roles/available-permissions: ROLE_MANAGE; GET /permissions: PERMISSION_MANAGE.
- POST /roles: ROLE_MANAGE; bulk permission assign/remove: ROLE_MANAGE; single permission assign/remove: ROLE_MANAGE.
- User role list: GET /users/:id/roles requires USER_EDIT; assignment and removal use USER_EDIT.
- Permission catalog GET is gated by PERMISSION_MANAGE, but roles/available-permissions is gated by ROLE_MANAGE and delegates PermissionService.list. This access surface warrants permission-negative/runtime validation; no conclusion about exploitable behavior.
- Role and Department mutation observed only create/update; no delete route in bounded scan. User-role membership removal and RolePermission removal routes exist.

## Gaps
- Route gate/source/service/DTO/test exhaustive crosswalk remains incomplete.
- No tests executed; permission denial, tenant-negative, direct service and concurrency checks not run.
- Whether combined requireAny gates are intended business policy is a requirement discovery item, not changed.

## Status
G-01/G-10 remain open; source-only.
