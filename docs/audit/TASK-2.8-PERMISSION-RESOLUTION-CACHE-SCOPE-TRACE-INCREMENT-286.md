# Task 2.8 Increment 286 — Permission resolution and source-name reconciliation

Date: 2026-11-24
Pinned source: backend tree SHA 8253eb3718d1a2a1bd5de03b6b5090ba69b94b67
Read-only review.

## New evidence (not repetition of 285)
- PermissionService resolves effective permission codes by loading permission catalogue, tenant-filtered user-role assignments, active roles in that tenant, and tenant-filtered RolePermission links. Role links are constrained to active role IDs. Permission catalogue entries themselves are not visibly filtered by an active/status field during resolution. RP-01 active semantics remains unresolved.
- /auth/me/permissions Router path derives the current authenticated user from request context, uses all permission codes for an admin, and uses the effective resolver for non-admins. This narrows the previously noted arbitrary-user resolver concern: this endpoint is current-user scoped.
- PermissionService.getUserPermissions/hasPermission are callable internal methods taking userId/userRecord; bounded trace did not find direct public Router exposure of arbitrary user permission lookup. Full caller inventory still pending.
- Request cache uses tenant:user key for permission resolution and only holds result in request-local object. AccessControlService role snapshots similarly tenant/user scoped and active role list tenant scoped. Cache invalidation/in-request mutation ordering and actual request isolation still require dynamic tests.
- Source-file name discrepancy is resolved: file path RolePermissionService.js contains RolePermissionRepository; RolePermissionRepository.js contains RolePermissionService. This was a naming/content mismatch in the prior trace, not missing source.
- The admin permission endpoint builds its permission list from PermissionRepository.getAll without visible active filtering; expected semantics for inactive catalogue entries need clarification.
- Static only; no tests executed, data access, production change, or mutation.

## Assurance targets
PRR-01 active permission semantics across admin and non-admin paths.
PRR-02 caller inventory and negative scope tests for internal permission resolver.
PRR-03 cache reset/isolation/in-request changes and role deactivation tests.
PRR-04 permission catalogue tenancy/global ownership semantics.
PRR-05 route tests for /auth/me/permissions admin/non-admin outputs.
