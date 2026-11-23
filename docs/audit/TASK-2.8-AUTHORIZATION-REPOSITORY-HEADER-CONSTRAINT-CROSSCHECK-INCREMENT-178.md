# Task 2.8 — Tenant-scoped role membership and permission repository/constraint cross-check — Increment 178

Date: 2026-11-23. Read-only cross-check of repositories and DatabaseSetup headers for the Identity/Authorization cluster.

## Source header evidence
- 03_Departments: ID, TenantID, Name, Code, Status, CreatedAt, UpdatedAt.
- 04_Roles: ID, TenantID, Name, Code, Status, CreatedAt, UpdatedAt.
- 05_Permissions: ID, Code, Name, Module, Description, Status (no TenantID in header).
- 06_UserRoles: ID, TenantID, UserID, RoleID, CreatedAt.
- 07_RolePermissions: ID, TenantID, RoleID, PermissionID, CreatedAt.

## Repository observations
- Department/Role repository findByCode and existsByCode are global-code helpers, not tenant-aware; service code relies on broad scans to perform tenant-local comparisons.
- UserRoleRepository findByUserId/findByRoleId/find/exists reads the full table and matches IDs without tenant in repository logic.
- PermissionRepository catalog is global; findByModule also reads all and filters.
- RolePermissionRepository implementation path retrieved under expected filename returned RolePermissionService content (file naming/content mismatch observed); service checks tenant, but repository implementation needs verified correct path/source before further claims.
- No FK, uniqueness, cascade, atomic, or indexed query guarantees can be inferred from sheet headers or repository code.

## V2 model candidates (not approval)
- Tenant-qualified membership rows with FK constraints tying TenantID/UserID/RoleID consistently.
- Tenant-scoped unique normalized Role/Department codes; permission catalog global if policy confirms.
- Unique association keys (TenantID, UserID, RoleID) and (TenantID, RoleID, PermissionID) and database enforced.
- Repository queries require tenant scope except explicitly global identity/catalog paths.
- Define role/membership deletion and temporal/revocation semantics; audit coupling transactionally.

## Status
G-01/G-05/G-10 remain open. Evidence is source-only; no tests, production data or changes.
