# Task 2.8 — Username policy conflict and legacy SHA-256 authentication trace — Increment 175

Date: 2026-11-23. Read-only trace of AuthService.login and UserService create/update validation.

## Verified source behavior
- UserService create and update comments/duplicate scans intend global case-insensitive username uniqueness (excluding the user being updated). These are read-then-write checks; database uniqueness and atomic concurrency protection are not evidenced.
- AuthService.login retrieves all matching usernames and fails closed unless exactly one result exists. Its comment explicitly states usernames may repeat across tenants and that login has no tenant selector. This is inconsistent with create/update's global uniqueness intent and may reflect historical data or policy drift.
- Password hash function uses SHA-256 over UTF-8 password, hex encoded. No salt, work factor or password-specific key is observed in this bounded function.
- User creation/update and AuditService.log are sequential; transaction atomicity is not evidenced.

## V2 decisions/gates
- Clarify and adopt a single login identity policy: global normalized username, or tenant-scoped identity with explicit tenant selection/tenant-aware login. Do not retain ambiguous policy.
- Remediate legacy duplicate usernames through discovery and user-approved mapping; do not assume none exist or query live production data during this audit.
- Enforce uniqueness atomically in PostgreSQL.
- Use modern password hashing (Argon2id or bcrypt with appropriate settings), controlled password reset or safe credential-upgrade strategy; do not directly port SHA-256.
- Account mutation and audit coupling should be transactionally protected.

## Status
G-05/G-08/G-10 remain open. Source-only, no live data, test, migration, or production modification.
