# Task 2.8 Increment 146 — Auth/session and identity route-to-evidence crosswalk

Date: 2026-11-23  
Evidence: E1/E2 — bounded Router/service/frontend source crosswalk; tests not run.

## Route matrix
| Route | Gate observed | Source evidence | Side effect/data | Status |
|---|---|---|---|---|
| POST /auth/login | Authentication entry | AuthService username uniqueness/active/hash/session creation; source trace in 106/120/137 | session token record | PARTIAL |
| GET /auth/me | Authenticated token | AuthService session/current user and tenant | current-user projection | PARTIAL |
| GET /auth/me/permissions | Authenticated token; admin returns catalogue else resolved user permissions | tenant-scoped role/permission caches and active-role resolution | permission projection | PARTIAL |
| POST /auth/logout | Authenticated token | session revocation path sampled | session status/revoke | PARTIAL |
| GET /admin/sessions | Admin role | SessionManager tenant/admin listing | session projection | PARTIAL |
| DELETE /admin/sessions/{id} | Admin role | target/session tenant and admin checks | revoke session | PARTIAL |
| GET /users | USER_VIEW | tenant-scoped user list | user projection | PARTIAL |
| POST /users | USER_CREATE | tenant/department validation, username/password update/create | user mutation + audit | PARTIAL |
| PUT /users/{id} | USER_EDIT | update field allowlist and tenant validation sampled | user mutation + audit/session implications | PARTIAL |
| GET/POST/DELETE /users/{id}/roles | USER_EDIT | target user/role tenant/status checks | role association read/insert/delete + audit | PARTIAL |
| GET /roles /available-permissions /permissions | ROLE_MANAGE/USER_EDIT alternatives; ROLE_MANAGE; PERMISSION_MANAGE | catalog and tenant role-resolution | catalog projection | PARTIAL |
| POST/PUT/DELETE roles and role permissions | ROLE_MANAGE | tenant validation and mutation service source | role/link mutation + audit | PARTIAL |

## V2 requirements
1. Sessions and credentials: secure password hashing, session rotation/revocation/expiry, secure transport, credential migration/reset decision and account-switch invalidation.
2. Authorization: central deny-by-default policy with tenant/resource predicates, role assignment temporal/audit policy, explicit global catalog scope and sensitive admin control.
3. User-role and role-permission mutations require composite unique constraints, transaction/outbox, concurrency tests and actor integrity.
4. Permission catalog, role codes and admin privilege semantics should be declarative and versioned; UI/menu permission is presentation only.
5. API should return structured error envelope, correlation IDs and avoid leaking username/tenant existence details.

## Remaining verification
- Every auth/admin nested route, permission alternative and tenant mismatch test remains unverified at runtime.
- Password hash migration, username ambiguity and account/session invalidation policies need business/security decisions.
- Test source does not prove pass; no tests run.

## Limits
- Bounded crosswalk only; E1/E2. No runtime tests, live users, credentials or production changes. G-01/G-10 remain blockers; schema readiness NOT APPROVED.
