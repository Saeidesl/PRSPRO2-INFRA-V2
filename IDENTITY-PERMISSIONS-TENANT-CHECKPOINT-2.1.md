# Phase 2.1 — Identity, permissions, tenant isolation and repository checkpoint

Date: 2026-11-22
Read-only source audit: PRSPRO2BACKEND-PRODUCTION AuthService.js, AccessControlService.js, PermissionService.js, SessionManager.js, UserRepository.js, RoleRepository.js, UserRoleRepository.js, PermissionRepository.js, and DatabaseSetup.js.

## Identity and session observations
- Usernames can repeat across tenants; login path fails closed if username matches more than one record, unless a unique match is found (no tenant selector path observed in this excerpt).
- Active account status is validated; password comparison is against the source's hash function.
- Session records include user ID and tenant ID and have an expiry based on configuration; session state is stored via script properties in the current implementation.
- Auth/me revalidates user status and tenant against the session.
- Session last-seen updates are throttled.

## Role/permission observations
- Tenant-scoped role assignments use UserRoles and RolePermissions.
- Active roles are filtered by tenant; ADMIN/SYS_ADMIN role codes identify administrative users in inspected path.
- Permissions are resolved through role-permission mappings and cached within request scope.
- Router handlers enforce endpoint-specific permission codes; AccessControlService.enforceRouteAccess's generic method does not itself implement a deny-by-default permission matrix, so endpoint authorization coverage must be audited at route/service level. This is an observation about the inspected architecture, not a claim of a known vulnerability.
- V2 should model tenant-scoped role assignment, role/permission relations, session lifecycle and permission checks consistently, and centralize authorization with explicit route/service policy.

## Tenant isolation and data access observations
- Auth/session identity includes tenant context.
- Repository patterns frequently retrieve all sheet rows and filter by TenantID in service/repository logic; project visibility may additionally depend on department/user availability.
- Tenant scoping should be an enforced database/API invariant in V2, not merely a query convention. Where applicable, PostgreSQL constraints, scoped service methods, and integration tests should verify isolation.
- Project visibility, event visibility inherited from project, and attachment visibility were previously observed in the source audit checkpoints.

## Source tables confirmed in DatabaseSetup
- 01_Tenants
- 02_Users
- 03_Departments
- 04_Roles
- 05_Permissions
- 06_UserRoles
- 07_RolePermissions
- 08_Projects
- 09_Workflows
- 10_WorkflowSteps
- 11_EventTypes (previous checkpoint)
- Remaining table/header mappings continue to be audited.

## Provisional V2 implications
1. Tenant context must be consistently attached to tenant-owned entities and protected by database-level integrity.
2. Users, roles, permissions, role grants, sessions and departments are separate domain concepts.
3. Endpoint access policies should be explicit and testable; avoid reliance on broad route guard shortcuts.
4. Authentication secrets, password hashes, session tokens and audit data require secure storage and handling.
5. Scope checks must cover project visibility, event inheritance, attachments, material and procurement data, not only user identity.

## Pending
- Exhaustive route-by-route authorization and permission list mapping.
- Tenant relationship constraints and full table mapping.
- Password hashing algorithm/upgrade policy and session revocation/rotation semantics.
- Audit/event logging coverage and repository constraints.
- No schema, migration, production modifications or live data access.


## Router permission coverage spot-check (continued)
- Inspected Router.js (1094 lines) and located explicit permission checks throughout GET and mutation handlers, including project/event/attachment, workflow/event-type, user/role, reporting and procurement routes.
- Checks observed include PROJECT_VIEW/CREATE/EDIT, EVENT_VIEW/CREATE/EDIT, ATTACHMENT_VIEW/UPLOAD/DELETE, WORKFLOW_MANAGE, EVENT_TYPE_MANAGE, USER_VIEW/CREATE/EDIT, ROLE_MANAGE, PERMISSION_MANAGE, REPORT_VIEW, PROCUREMENT_VIEW/MANAGE, SYSTEM_SETTINGS, DEPARTMENT_MANAGE and PROJECT_STATUS_MANAGE.
- Some routes use requireAny combinations, while others use single permission checks. A complete endpoint-to-permission matrix, tests for missing route checks and service-level checks remain outstanding; the spot-check is not an exhaustive security review.
