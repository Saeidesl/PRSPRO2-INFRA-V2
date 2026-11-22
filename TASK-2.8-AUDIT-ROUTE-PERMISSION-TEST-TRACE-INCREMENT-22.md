# Task 2.8 — Audit Route Permission & Test Trace Increment 22
Date: 2026-11-22
Scope: Read-only trace of audit read endpoints, permission enforcement, validators and available backend tests.

## Findings
- Router.js explicitly gates `/audit`, `/audit/entity/{type}/{id}` and `/audit/user/{id}` with `REPORT_VIEW` before calling AuditService.
- AuditService further resolves current user and filters by TenantID. listByUser checks target user exists and is in same tenant, then tenant-filters returned audit rows. listByEntity tenant-filters rows after repository lookup.
- AccessControlService.require resolves current user and permits tenant-admin role or delegates to PermissionService.requirePermission. Route authorization is therefore evidenced for the three sampled audit GET paths, subject to broader route/permission implementation audit.
- AuditRepository lookup methods are global-sheet scans by entity/user then service-layer tenant filtering. This is an efficiency and defense-in-depth concern, not proof of tenant leakage in these sampled endpoints.
- AuditValidator.validateCreate validates basic action/entity strings and optionally UserID/TenantID/Reason, but AuditService.log itself uses Validator.object and requires TenantID, Action, EntityType, EntityID. The validator appears separate from log's implementation in this bounded trace; wiring/use not established.
- Backend test tree has no clearly named dedicated audit-service or audit-route test file in the sampled test inventory. Existing test filenames include general report tests, but audit-specific assertions/route tests were not established. This is a filename-based absence observation only, not proof no audit assertions exist elsewhere.
- No source evidence here establishes audit mutation endpoint exposure, storage append-only controls, retention, encryption, complete redaction policy, or full REPORT_VIEW least-privilege correctness.

## V2 recommendations
- Explicit audit-read permission and tenant-scoped query in repository/SQL, not broad scan followed by filter.
- Immutable append-only audit records with actor identity, tenant, operation correlation ID, request context, before/after or structured change data, and retention policy.
- Transactional audit persistence/outbox with mutations; fail policy (block vs degrade) explicitly defined per domain operation.
- Dedicated tests for audit routes: permission-denied, admin bypass, cross-tenant entity/user isolation, malformed IDs, audit query paging, and append-only behavior.
- Ensure entity lookup and tenant filtering are coupled at DB query layer; consider actor/target referential integrity where appropriate while supporting system actors.

## Limits
- Read-only source trace; no test execution or runtime permission probing.
- Task 2.8 gaps remain OPEN and schema readiness NOT APPROVED.
- No production repositories modified; no live data/tests/DDL/migration/provisioning/deployment.
