# Task 2.8 Increment 229 — Authentication, session identity and user-mutation boundary trace

Date: 2026-11-24

## Scope
Bounded read-only inspection of production Backend `AuthService.js`, `AccessControlService.js`, `UserService.js`, `UserRepository.js` and selected `Router.js` dispatch logic. E1 source evidence only.

## Observations
- Login resolves username matches across the repository and refuses authentication unless exactly one match exists; usernames are therefore treated as globally unique at login despite earlier tenant-aware role/user domain concepts.
- Login checks active status and compares a SHA-256 digest produced by `Utilities.computeDigest`; source inspection does not show a password-specific salt or adaptive work factor. This is a security-hardening review candidate, not a runtime finding.
- Session creation follows successful credential validation. `me` resolves the token through SessionManager, fetches the user by session userId, checks active status and compares stored user TenantID with session tenantId.
- AccessControlService caches user snapshots and tenant-filtered role/permission-related data during a request; its reset behavior and lifecycle coupling to request entry were not traced here.
- Department lookup for profile/department code checks department TenantID and active status.
- UserService list filters users by current tenant; get verifies tenant after ID-based repository lookup. Create assigns current tenant and validates department tenant. Update checks existing user's tenant, validates any supplied department tenant and explicitly excludes TenantID from mutable update data.
- Username duplicate checks in create/update are global (case-insensitive); login fails closed when duplicate matches exist.
- User create/update persistence and AuditService logging are sequential operations. Atomicity, failure recovery, lock/concurrency protection and audit completeness are not demonstrated by this bounded trace.
- Router selected GET and authorization logic is documented separately in prior increments; route-by-route exhaustive permission mapping and negative testing remain outstanding.

## Review candidates / unresolved evidence
1. Verify password storage policy and migration/compatibility implications before any change; do not infer exploitable exposure from source alone.
2. Reconcile global username uniqueness/login behavior with tenancy/business decision and user identity model.
3. Verify session expiration, invalidation, cache reset timing and tenant context derivation under concurrent/malformed requests.
4. Verify create/update plus audit atomicity or documented recoverable semantics.
5. Confirm test coverage for tenant mismatch, inactive users, duplicate username ambiguity, session invalidation and permission denial.

## Disposition
E1 source-only trace; no tests executed, no live data, production changes, schema/DDL, migration, deployment or implementation repository changes. Task 2.8 remains IN PROGRESS; G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED.
