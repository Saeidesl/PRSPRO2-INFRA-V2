# Task 2.8 Increment 107 — Role and user-role mutation boundary (2026-11-23)

## Scope and evidence
Read-only inspection of backend production sources: RoleService.js, RoleRepository.js, PermissionRepository.js, UserRoleService.js, RoleValidator.js, RolePermissionService.js (retrieved file content is repository implementation despite the filename), and bounded Router.js mutation routes. Code search for role-permission assignment orchestration returned no indexed results; its internals and batch behavior are not asserted.

## Observations (E1 source-only)
- Role list/get/create/update paths filter or validate TenantID against current user. Role creation assigns TenantID from current user.
- Role code duplicate checks are tenant-scoped and case-insensitive at service level, but storage/database uniqueness and race protection are not demonstrated. Repository findByCode/existsByCode helpers are not tenant-scoped.
- Role update builds a field-limited object (Name, Code, Status, UpdatedAt); TenantID is not mutable through this sampled service. Audit follows update sequentially.
- User-role assignment validates target user tenant, role tenant and active role, checks duplicate relationship, then inserts and audits sequentially. Removal validates user and relationship tenant before delete and audit. Atomicity/recovery if audit fails is not demonstrated.
- User-role listing filters relationship tenant after lookup by user.
- Router sampled mutation routes: role create/update and single/bulk role-permission routes require ROLE_MANAGE; user-role assignment/removal routes require USER_EDIT. Route coverage is bounded.
- RolePermissionRepository insertMany/deleteMany use ScriptLock and direct sheet operations. InsertMany checks expected headers, writes rows and invalidates cache; deleteMany removes rows bottom-up and invalidates cache. Validation of referenced IDs, tenant alignment, duplicate-key constraints, and batch audit semantics is not established. Single delete delegates to deleteMany; single insert is a direct write.
- The role-permission assignment service implementation could not be indexed or fetched; orchestration remains unknown.

## V2 requirements
- Enforce tenant-scoped foreign keys and composite uniqueness for role-code and user-role/role-permission edges, preserving observed tenant semantics.
- Transactional assignment/removal plus audit/outbox; atomic bulk operations with explicit limits and idempotency.
- Validate both referenced entities, tenant scope, role status and duplicate state server-side; fail closed.
- Test concurrency, tenant-crossing IDs, inactive roles, duplicates, partial failures, audit recovery and bulk-size boundaries.

## Verification / status
E1 only; no tests executed; no production changes. Not a confirmed defect. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.
