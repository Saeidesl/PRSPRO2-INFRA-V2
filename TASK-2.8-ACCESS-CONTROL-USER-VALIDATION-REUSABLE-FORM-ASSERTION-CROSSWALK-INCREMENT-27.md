# Task 2.8 — Access Control, Reusable Forms & User Validation Assertion Crosswalk Increment 27
Date: 2026-11-22
Scope: Read-only actual assertion inspection in selected backend test files.

## Access-control request-cache tests
Assertions check request-scoped cache behavior for user/admin/roles/permission resolution, reset at beginning of GET/POST, shared tenant-scoped role snapshots and per-user permission cache. These are source-pattern assertions; they do not verify cache behavior with runtime concurrency, revocation mid-request or complete route authorization.

## User validation tests
Assertions check required nonblank password for creation, minimum eight-character password, update omission allowed but explicit blank rejected and short update password rejected. No broader password security (hashing, brute-force, reset flow, rate limits, session expiry) is established by this file.

## Reusable form tests
Assertions observed:
- malformed response collections rejected and required forms cannot be omitted;
- response validation returns immutable definition snapshot;
- stale form version and unassigned form rejected;
- conditional visibility handles hidden required fields and rejects submitted hidden values;
- response persistence idempotent per event/form in harness.

## Form API contract assertions
- schema and Router form APIs exist;
- central information schema cannot be changed in ways that invalidate active forms;
- event creation validates and persists form responses before idempotency completion;
- amendments are permission guarded, event/tenant validated, optimistic concurrency and idempotent replay under lock;
- amendment forms must be explicitly configured on event type;
- project code preservation and explicit persistence outcome flags are asserted.

## Coverage interpretation / gaps
Positive source assertion coverage across user validation, request caches and forms. Tests not executed. Runtime authorization behavior, tenant isolation at database level, route matrix, auth/session security and migration constraints remain unverified. Stubbed test environments may differ from production behavior.

## V2 implications
- Design permission model around tenant-scoped RBAC with explicit route/domain matrices, revocation behavior and transaction-aware authorization.
- Password hashing, rate limits, session/token lifecycle, reset and audit policy need verified implementation.
- Reusable form version snapshots and amendment concurrency should be preserved with normalized relations, constraints and transaction tests.
- Validate visibility/required rules and amendment event provenance in PostgreSQL integration tests.

## Limits
Source-only assertion review; tests not executed. G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production modifications.
