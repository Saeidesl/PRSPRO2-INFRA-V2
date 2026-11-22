# Task 2.8 — Identity & RBAC Repository Tenant Boundary Trace (Increment 55)
Date: 2026-11-22
Status: Bounded READ-ONLY repository source review.

## Sources
UserRepository.js, RoleRepository.js, DepartmentRepository.js, UserRoleRepository.js and RolePermissionService/Repository path from READ-ONLY backend reference.

## Observations
- UserRepository.findById and RoleRepository.findById use generic ID-based repository lookup. UserRepository and RoleRepository expose tenant filtering helpers, but direct ID methods are not tenant-qualified.
- DepartmentRepository.findById also uses generic ID lookup; findByTenantId is a separate filtering helper.
- UserRoleRepository.findByUserId/findByRoleId/find/find exists queries use relation IDs and do not directly scope results by TenantID in the inspected methods.
- Sampled RolePermissionService list path resolves the current user, validates target Role tenant, then filters association records by TenantID. The service-level pattern is not proven across all assignment/mutation routes.
- These are source-level findings; repository method behavior and service callers were not exhaustively reviewed.

## V2 implications
1. Tenant-aware query signatures for users/roles/departments and role associations should avoid exposing unqualified reads as domain APIs.
2. Composite tenant FK constraints should enforce tenant match between user-role and role-permission association and their referenced entities.
3. Permission catalog may remain global only after explicit policy classification.
4. Uniqueness scope for username, role code, department code and assignment records remains a business/schema decision; no header/source assumption should settle it.
5. Test cross-tenant association insertion, direct ID retrieval, inactive/deleted role handling and tenant switching in isolated runtime.

## Limits
E1 source inspection only; no runtime tests, live data, schema, SQL, migrations, provisioning, production writes or deployment. G-01/G-10 remain OPEN; schema readiness NOT APPROVED.
