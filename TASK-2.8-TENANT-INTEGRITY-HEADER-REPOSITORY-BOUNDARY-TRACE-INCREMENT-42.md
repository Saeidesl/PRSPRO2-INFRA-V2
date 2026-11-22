# Task 2.8 — Tenant Integrity: Header & Repository Boundary Trace (Increment 42)
Date: 2026-11-22

## Scope and evidence
Read-only source trace of selected tenant/security headers and repository access patterns in the existing backend production repository. Production repository remains READ-ONLY.
Reviewed source excerpts: AccessControlService.js, ProjectRepository.js, UserRepository.js, PermissionRepository.js, DatabaseSetup.js. No live records or secret/config values accessed.

## Header-grounded tenant/global observations
- Tenants sheet: ID, Name, Code, Timezone, Status, CreatedAt, UpdatedAt.
- Users: ID, TenantID, Name, Username, PasswordHash, DepartmentID, Status, timestamps.
- Departments and Roles: tenant-owned headers include TenantID.
- UserRoles and RolePermissions: TenantID is explicitly included in relation headers.
- Projects: TenantID is explicit alongside ProjectCode and other fields.
- Permissions: header set is global-looking (ID, Code, Name, Module, Description, Status); no TenantID column is declared in the reviewed header definition. This is source evidence of current header design, not proof that global scope is business-approved.
- UserRepository.findByTenantId filters the in-memory getAll() result by TenantID. findByUsername normalizes and returns a user only when exactly one match exists; findAllByUsername exposes matching rows.
- ProjectRepository findById/findByProjectCode and CRUD delegate to generic GoogleSheetsRepository methods without tenant predicates in the repository wrapper. Tenant scoping must therefore be enforced by caller/service paths or other layers; repository wrapper alone does not establish it.
- PermissionRepository.findByCode/findByModule and CRUD similarly delegate to generic repository methods; permission catalog appears shared/global at header level.
- AccessControlService request role cache keys include tenant and user; user-role snapshots are filtered on both UserID and TenantID, and active-role snapshots filter Role.TenantID and Status. This is a positive source-level control in the sampled service.

## Integrity implications / V2 design requirements
1. Tenant-owned tables should use tenant-qualified PK/unique/FK design (e.g., tenant-qualified references or unique composite keys supporting FKs) where domain ownership applies.
2. Shared permission registry should be explicitly modeled as global only if confirmed as a business/system policy; tenant-scoped role-permission associations must reference a valid permission catalog entry.
3. Service/repository tenant-scoping cannot be the sole integrity barrier: database constraints should prevent cross-tenant association, especially UserRole, RolePermission, Department, Project, Event, Workflow and procurement relations.
4. Tenant context should be derived from authenticated session, never trusted from arbitrary client payload; all route/query mutation paths must validate parent ownership.
5. ProjectRepository-style ID-only repository access is a legacy boundary pattern: V2 repositories should expose tenant-scoped operations or enforce tenant context consistently.
6. Username uniqueness policy remains unresolved: source shows normalization and ambiguity-safe findByUsername behavior, but header/repository evidence does not establish a DB uniqueness rule or tenant-vs-global uniqueness decision.
7. Permission global scope and tenant scope of any system templates/reference data remain policy decisions, not schema assumptions.

## Evidence classification and gaps
- E1 source inspection only for the statements above.
- No runtime tests, database constraints, concurrency behavior, live data, or exhaustive service-call-site proof.
- This increment is a focused tenant boundary trace and does not close G-10 or G-01.

## Safety
No production repository writes, branches, PRs, commits, live data, tests, DDL, migrations, provisioning, deployment, or real-data migration.
