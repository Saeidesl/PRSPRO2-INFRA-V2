# Task 2.8 Increment 125 — Role-permission assignment and permission resolution

Date: 2026-11-23  
Evidence: E1 — read-only source inspection

## Sources inspected
RolePermissionService.js (legacy file named RolePermissionService contains repository), RolePermissionRepository.js (legacy file named RolePermissionRepository contains service), PermissionService.js, PermissionRepository.js and AccessControlService.js.

## Observed behavior
- Role-permission list/assignment/removal validates target role tenant ownership through service helper. Permission IDs are resolved against the global permission catalogue; role-permission rows are filtered by tenant on read and assignment duplicate checks.
- Single assignment checks for existing link, inserts and audits sequentially. Batch assignment deduplicates submitted IDs, resolves catalog entries, skips already assigned IDs and reports invalid/skipped/created outcomes; insertMany is lock/batch-write guarded, but audit records are emitted after the batch write per item, without demonstrated transaction coupling.
- Batch removal resolves role-linked items in tenant scope, deletes rows in a locked bulk operation, then writes per-item audit records sequentially. Partial audit failure recovery is not demonstrated.
- Repository deleteMany physically removes link rows by row number under ScriptLock. Duplicate link protection is read-before-write and no storage-level composite unique constraint is demonstrated.
- Permission resolution loads permission catalog, tenant-scoped user-role links, active tenant roles and role-permission rows, then maps permission codes; request caches reduce repeated lookups. Cache reset is performed at request boundaries in WebApp.
- Admin detection is based on active tenant role assignments whose role code is SYS_ADMIN or ADMIN. User/role caches are request scoped.
- AccessControlService.enforceRouteAccess only enforces authentication for sampled paths and returns true for other authenticated paths; explicit route permission checks are delegated to Router and services. This is a source-level architecture boundary, not a finding of bypass without complete route tracing.

## V2 implications
1. Use composite uniqueness on (tenant, role, permission) and relational FK constraints; make assignment/removal concurrency-safe.
2. Batch assignment/removal should be transactional, including audit/outbox, with defined partial-failure semantics.
3. Define whether permission catalogue is global or tenant-specific and enforce this consistently in data model and APIs.
4. Centralize backend authorization middleware/policy enforcement, deny by default for mutation/read routes, and generate an exhaustive permission matrix.
5. Keep admin-role semantics explicit and avoid role-code-only privilege ambiguity; document system roles and tenant boundaries.
6. Permission caches must be scoped and invalidated reliably on role, user-role and permission changes.

## Limits
- Source-only; no tests, production changes, live user records or credentials accessed.
- Route enforcement remains subject to exhaustive Router crosswalk; source-level observations are not runtime-confirmed defects.
- E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.
