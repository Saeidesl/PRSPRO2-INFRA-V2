# Task 2.8 Increment 106 — Authentication, session and user mutation boundary trace

Date: 2026-11-23  
Scope: READ-ONLY source audit of authentication/session/user lifecycle.  
Evidence level: E1 source inspection only.

## Source inspected
- Read-only production backend: `AuthService.js`, `SessionManager.js`, `UserService.js`, `AccessControlService.js`, `PermissionService.js`, and selected `Router.js` dispatch branches.
- No production writes, runtime tests, live user records, or secrets were accessed.

## Observations
1. Login: username lookup is global and login fails closed when the repository returns other than exactly one match; account status must be ACTIVE; password comparison uses `AuthService.hashPassword_`.
2. Password hash: the observed hash helper uses SHA-256 digest of the password without an observed per-user salt or work factor. This is a source observation and security design concern, not proof of stored hashes or compromise.
3. User create/update: create checks duplicate username across all tenants (case-insensitive), consistent with tenant-agnostic login. Update checks uniqueness on change and explicitly limits writable fields, validates department tenant and prevents TenantID mutation. Route samples require USER_CREATE / USER_EDIT.
4. Sessions: token is generated from UUID entropy and stored as a Script Properties key with session JSON. Session lookup checks active status and configured expiry, and updates lastSeen at a throttled interval. Expiry is disabled when configured duration is non-positive; the deployment setting was not read.
5. Session lifecycle: logout deletes the token property. Admin listing/revocation is tenant-scoped and hashes session identifiers for display. The sampled user update path does not show invalidating sessions when a user's status or password changes; this must be verified across all lifecycle paths before treating it as confirmed behavior.
6. Authorization: sampled router user mutations require USER_CREATE / USER_EDIT. Permission resolution filters role assignment and active roles by tenant and caches for request scope. Authorization is distributed between Router and services; the generic route-access hook itself is not the permission enforcement layer.

## V2 security/design requirements
- Use a modern password hashing scheme with per-user salt and configurable work factor (e.g. Argon2id or bcrypt; selection during backend foundation).
- Model sessions in durable storage with token hash-at-rest, expiry policy, explicit revocation, and lifecycle invalidation on account disable/password reset/credential change; test concurrency and tenant boundaries.
- Enforce tenant-aware username uniqueness only if product policy chooses it; current login behavior implies globally unique usernames. Preserve or deliberately change only with PO decision.
- Centralized auth middleware and route permission registry; fail closed and test unauthorized/tenant-negative routes.
- Audit user and session mutation within transaction/outbox as appropriate; avoid exposing credentials or raw tokens.

## Test gaps
No runtime tests executed. Add tests for wrong credentials, inactive user, username collisions, hash upgrade/verification, expiration, logout, account disable/password change session revocation, admin tenant isolation, and role/permission cache scope.

## Status
Findings are source-level only. This does not constitute a penetration test or runtime validation. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED.
