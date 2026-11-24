# Task 2.8 — Authorization Mutation Test Coverage Boundary (Increment 255)

## Scope
Read-only review of `tests/access-control-request-cache.test.js`, `AccessControlService.js`, `PermissionService.js`, and `Router.js` in the production-reference backend. No production changes or test runs.

## Evidence
- The inspected access-control test file uses source-text assertions to confirm request-scoped cache structures/reset behavior and permission-list cache reuse.
- AccessControlService has request cache fields and reset method. PermissionService similarly has a request-local cache.
- Router is a substantial route dispatch surface with branch-specific permission checks; previously audited route crosswalk remains provisional. No comprehensive route-by-route negative authorization matrix was evidenced by the inspected cache test itself.
- This increment does not assert that authorization tests are absent repository-wide; it is limited to this file and the selected source paths.

## Coverage gaps / candidate tests
A-01: Mutating-route matrix: each mutation endpoint denies a user lacking the required permission; verify both Router and service boundary where applicable.
A-02: Tenant isolation: same identifiers across different tenants must never expose or mutate foreign entities, including related data and polymorphic attachment/audit pointers.
A-03: Draft exceptions: explicit creator/visibility exceptions remain limited to the intended operations; test denial for non-creator and cross-tenant callers.
A-04: Admin exception/bypass: only active tenant-scoped admin roles receive intended bypass; stale/disabled role assignment must deny.
A-05: Cache isolation: request cache reset across successive web-app executions, token/user/tenant changes, and permission changes cannot reuse stale authorization.
A-06: Route-to-service parity: direct service calls fail closed where service-level authorization is required, and routes cannot bypass validation through alternative mutation entry points.
A-07: Permission regression: generated route-family matrix for project/event/workflow/forms/attachments/material planning/procurement/templates/users/admin, with positive and negative cases.

## V2 requirement
Centralize authorization at the API middleware/policy boundary, with tenant context derived from authenticated identity, deny-by-default, resource authorization, and auditable policy tests. Cache only within a bounded request and invalidate on policy change. Do not replicate legacy route-specific exception logic without explicit discovery.

## Verification
- Static review: performed for named paths.
- Runtime test execution: NOT RUN.
- Penetration/security test: NOT RUN.
- Production repository: READ ONLY.
- Task 2.8: IN PROGRESS.
