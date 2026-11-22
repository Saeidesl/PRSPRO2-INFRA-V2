# Task 2.8 — Selected Legacy Test Assertion Crosswalk (Increment 45)
Date: 2026-11-22
Status: assertion-source inspection only; no tests executed in this increment.

## Reviewed test source files
- `tests/access-control-request-cache.test.js`
- `tests/user-validator.test.js`
- `tests/project-deletion.test.js`
- `tests/procurement-service.test.js`
- `tests/attachment-service.test.js`

## Assertion-level observations

### Access-control request cache
The inspected test source asserts presence and reset wiring of request caches for authenticated user/admin/roles, shared tenant-scoped role snapshots, and permission-list caching per user/request. Assertions primarily use regex/source-text checks; they do not instantiate real session/auth flows, database joins, tenant-crossing requests or Router permissions.

### User validator
The inspected tests cover create/update password presence, blank-password handling, minimum length and omitted update password. They do not establish username uniqueness, tenant-specific uniqueness, user tenant ownership, role assignment isolation, or session/security behavior.

### Project deletion
The inspected service test harness uses seeded sheet arrays and asserts dependency collection for a target project/tenant, filtering foreign-tenant rows, preserving shared order dependencies, ignoring deleted rows, and treating VOID events as inactive. It tests collector behavior in a mocked in-memory harness; it does not establish actual multi-sheet mutation atomicity, recovery, audit completion, live storage behavior or real database constraints.

### Procurement service
The inspected harness models tenant-scoped collections and mock project visibility. Selected assertions cover request-level reuse of procurement collections and switching repeated project visibility lookups to bulk-read behavior. Test harness wiring includes mocked repository/service/lock/audit behavior; source inspection cannot prove runtime test success or actual DB atomicity.

### Attachment service
The inspected tests assert denial of listing/upload to an event whose project is inaccessible and permit listing for a visible project. The harness mocks project/event repositories and access visibility; these assertions do not prove storage lifecycle, tenant isolation across every attachment route, deletion recovery, MIME validation or real authorization execution.

## Coverage interpretation
- E2 classification: assertion source inspected (not test execution).
- Findings are limited to assertion intent and mock boundaries.
- No successful runtime execution is claimed.
- This increment identifies test evidence gaps rather than confirming or disproving system behavior.

## Gap candidates for G-01/G-10
1. Authenticated session/user/Tenant mismatch negative tests against actual auth service/Router.
2. Cross-tenant CRUD/read tests for all tenant-owned entities and dynamic paths.
3. Tenant-qualified constraints and duplicate business-key behavior.
4. Failure injection across multi-sheet procurement, event void, project deletion and attachment operations.
5. Route permission matrix execution and service-level caller tests.
6. End-to-end verification beyond mocked in-memory repositories.

## Safety
Read-only source/test inspection only. No tests executed, no production writes, live data, DDL, migrations, provisioning or deployment.
