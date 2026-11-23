# Task 2.8 — Tenant-boundary and role mutation trace (Increment 68)

Date: 2026-11-23
Evidence level: E1 — read-only source inspection. No runtime/negative tests.

## Sources inspected
- UserRoleService.js
- RoleService.js
- UserService.js (bounded excerpt)
- PermissionService.js
- WorkflowService.js (bounded excerpt)
- ProjectRepository.js
- EventRepository.js
- ProcurementRepository.js

## Observations
- User-role assignment checks both target user and role TenantID against the current tenant, requires active role status, rejects user/role tenant mismatch, and checks for duplicate association. Assignment and audit writes are separate operations. Removal similarly checks target user and association tenant, then deletes and logs separately (source observation; complete atomicity not verified).
- RoleService list/get/create/update are tenant-filtered or tenant-validated. Role creation checks duplicate Code within tenant; update checks duplicate code within tenant. No runtime test of uniqueness/concurrency or protected-role invariant was performed.
- UserService excerpt shows tenant filtering on list/get and tenant validation on department association during create; excerpt is bounded, not a full mutation audit.
- PermissionService resolves user roles, filters active tenant roles, and joins tenant-scoped RolePermissions to permission IDs. Permission catalog read appears global in this path. Cache is request-level keyed by tenant/user. Full mutation/catalog exposure audit remains open.
- WorkflowService management/list/get excerpt tenant-filters workflows, departments, event types and information types and tenant/version filters steps/assignments. Full mutation and publish/revision lifecycle remains unverified.
- ProjectRepository and EventRepository expose generic ID lookup and unscoped-by-tenant repository methods; tenant enforcement depends on callers. ProcurementRepository's listed retrieval methods explicitly filter rows by TenantID. This does not establish full cross-tenant isolation.

## V2 design implications (requirements, not implementation)
- Enforce tenant scope at persistence/service boundaries and consider PostgreSQL row-level security as defense in depth; RLS policy design and operational context are pending.
- Tenant-aware composite constraints should prevent cross-tenant associations (for example tenant/user/role relation integrity) and duplicate role assignments.
- Permission assignment mutations should be transactional with audit records or use an explicit reliable audit-outbox strategy.
- Define protected/system role invariants and bootstrap/admin recovery before schema implementation.
- Add negative tests for cross-tenant user-role assignment/removal, role updates, project/event access, workflow joins, procurement and attachment access.

## Limits and gaps
- Source retrieval and inspection were bounded; no full mutation coverage or runtime tests.
- No verified PostgreSQL schema, RLS, transaction or integration behavior.
- G-01 route/dependency coverage and G-10 tenant integrity remain OPEN/BLOCKER. G-02 procurement, G-03 lifecycle and G-04 storage recovery remain OPEN/HIGH.
- No production changes, live data access, schema execution, migration, deployment or VPS provisioning.
