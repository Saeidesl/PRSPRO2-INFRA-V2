# TASK-2.8-ADMINISTRATIVE-FRONTEND-CALLER-TRACE-INCREMENT-86

Date: 2026-11-23
Evidence level: E1 source inspection
Read-only reference: PRSPRO2-PRODUCTION
No tests, production modifications, migrations or deployments.

## Frontend administration caller families
- users.js SHA 82b2ba7d163660105467f2ca3f5e63e81b2fd794: /users and /users/{...} paths.
- roles.js SHA d7a76ab0acc2984917bd82ab9ae0b5b71d1306ff: /roles and available permissions.
- departments.js SHA 799d97245b660521c42cecf88d3319166972a0f3: department endpoint families.
- project-statuses.js SHA 2a4b5365cc4d768789fbb6eaf216ff16e67b2d7a: status endpoint families.
- permissions.js SHA 941b04f2beeccfc7e3bfd85ebcc726e36b207119: permission endpoint.
- audit.js SHA d26f4bb8c921a86afd93eaacebdbdb6a8668fa52: audit endpoint.
- settings.js SHA 8a061efaf711eb1fcaee23a2ae72a1709f6b9534: workflow caller family.

## V2 implications
- Admin/identity configuration should use consistent tenant-scoped APIs and transactional user-role-permission changes.
- Role permission catalog and actual authorization middleware must be aligned and tested.
- Audit retrieval/reporting and administrative changes need immutable transactional audit/outbox semantics.
- This is endpoint-family extraction only; dynamic paths, all methods, permission contracts and payloads not exhaustively traced.

E1 only; no tests or production changes. G-01/G-10 blockers remain.
