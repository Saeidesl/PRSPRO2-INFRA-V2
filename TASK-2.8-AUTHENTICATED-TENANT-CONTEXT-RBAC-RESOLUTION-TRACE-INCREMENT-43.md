# Task 2.8 — Authenticated Tenant Context & RBAC Resolution Trace (Increment 43)
Date: 2026-11-22

## Scope
Read-only source trace of AuthService, AccessControlService, PermissionService and selected Router paths from the existing backend production repository.

## Observed source behavior
- Login resolves matching usernames across the UserRepository; login fails closed unless exactly one matching record exists. The source comment explicitly recognizes that usernames may repeat across tenants, but no tenant selector is provided in this login path.
- AuthService.me validates session existence, retrieves the session user, verifies active status, and compares User.TenantID to Session.TenantID before returning user context.
- AuthService.getProfile_ tenant-filters Department and role association snapshots and active roles.
- AccessControlService.getCurrentUser resolves token via AuthService.me and request-caches user data keyed by token.
- AccessControlService.getUserRolesForRequest_ filters UserRole rows by both user ID and tenant ID; getActiveRolesForTenantRequest_ filters active roles by tenant.
- isAdmin uses tenant-filtered role assignments and active roles within that tenant.
- PermissionService resolves the user record, derives tenant from it, filters UserRole by tenant/user and active Roles by tenant, then filters RolePermissions by tenant and matched RoleID. Permission registry itself is read globally.
- Router.requireAny obtains authenticated user, applies admin bypass within tenant context or checks listed permissions.
- AccessControlService.enforceRouteAccess is not the complete route authorization barrier in the inspected source: it authenticates (and admin shortcut) but returns true otherwise; route-specific checks are delegated to Router/service paths. This is a documented architectural reliance, not a confirmed vulnerability.

## V2 requirements and risks
1. Derive tenant context from verified session and attach to request context; reject client-supplied tenant mismatch.
2. Centralize route authorization and tenant-aware service access; no permissive generic hook may be mistaken for route authorization.
3. Tenant-qualified role-assignment and permission-join constraints, plus session tenant/user binding, must be enforced at DB and service levels.
4. Every route branch and service operation requires a systematic positive/negative cross-tenant test matrix; sampled source evidence is not proof.
5. Auth failure behavior, session expiry/revocation, token storage and password hash design require separate security review; this trace does not establish their adequacy.

## Evidence limits
E1 source inspection only. No runtime/auth tests, penetration testing, live sessions/data or DB constraint execution.
Production repository remains read-only. No DDL, migrations, provisioning, deployment or production changes.
