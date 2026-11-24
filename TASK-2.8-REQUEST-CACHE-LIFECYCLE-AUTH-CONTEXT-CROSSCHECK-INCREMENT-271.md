# Task 2.8 — Increment 271: Request cache lifecycle and authentication context cross-check (2026-11-24)

## Scope
Read-only review of AccessControlService, PermissionService, GoogleSheetsRepository request caches, AuthService and selected entry-point files.

## Findings
- AccessControlService, PermissionService and GoogleSheetsRepository expose explicit request-cache reset methods; cache correctness depends on invocation at the request boundary and reset behavior under exceptions.
- AuthService.me validates session, active user status, and tenant alignment against session tenant. Login rejects username matches that are not exactly one record, avoiding ambiguity across tenants in the reviewed path.
- PermissionService caches permission results keyed by tenant/user within its cache object; correctness relies on request cache reset and role/permission changes not being stale across requests.
- The reviewed entry point Code.js delegates ordinary requests to webAppDoGet/webAppDoPost, but the reset lifecycle implementation was not available in the selected file set, so reset-at-boundary and exception cleanup remain unverified.
- GoogleSheetsRepository maintains request-scoped record/list/find/column caches; write invalidation and request isolation require execution tests.

## Test targets
RC-01..RC-10: request-start reset on GET/POST, reset after thrown exceptions, token/user cache isolation, role changes visibility across requests, repository cache invalidation after mutations, duplicate-token cache identity, execution-boundary isolation, lock/cache interaction, no cache leakage between tenant identities, and performance behavior under representative request sizes.

## Limits
Static review only; no runtime tests, no production data or writes. Candidate gaps only, not confirmed defects. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
