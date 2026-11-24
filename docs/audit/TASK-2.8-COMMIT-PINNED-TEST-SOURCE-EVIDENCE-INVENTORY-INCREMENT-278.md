# Task 2.8 Increment 278 — Commit-pinned test-source inventory and assurance boundary

Date: 2026-11-24
Reference snapshot: backend production repository main tree SHA `8253eb3718d1a2a1bd5de03b6b5090ba69b94b67` (READ-ONLY inspection only).
Production source repository was not changed.

## Newly verified test-source evidence
Read the following test source files at the snapshot:
- `tests/schema-contract.test.js`: static assertions for selected schema header definitions, append-only header behavior, manually-triggered guarded sync workflow, and event idempotency fields/locks.
- `tests/access-control-request-cache.test.js`: static source-pattern assertions for cache definitions/reset and WebApp reset-at-entry, plus shared tenant role-cache patterns.
- `tests/event-idempotency-schema-recovery.test.js`: mock-sheet assertions for adding idempotency columns and stale header cache recheck.
- `tests/project-deletion.test.js`: mocked collection boundary tests for event/project tenant separation, shared procurement order protection, inactive rows, retry-safe VOID event selection.
- `tests/google-drive-storage-folder.test.js`: mocked Drive folder selection, upload/copy location, failure behavior, and configuration pattern assertions.
- `tests/procurement-service.test.js`: mocked procurement project visibility threshold/bulk reads, request collection reuse, allocation caps/surplus, workspace shared-context assertions.

## Test coverage type and limits
These are test-source assertions and mocks. The inspected files do not establish that suites were executed in this audit, that tests exercise a real Apps Script environment, or that live Google Sheets/Drive behavior, lock contention, concurrent requests, permission denial end-to-end, and failure injection are verified. Some assertions inspect source strings rather than executing domain logic. Mocked test cases provide useful evidence but not runtime assurance.

## Evidence disposition
- E1 source/test-source evidence: CONFIRMED at the referenced tree snapshot for the listed files.
- E2 execution evidence: NOT ESTABLISHED (no test-run logs or CI run results inspected in this increment).
- E3 runtime/platform assurance: NOT ESTABLISHED.
- Exhaustive test inventory: NOT CLAIMED; selected files only.

## Gate impact
This increment strengthens bounded evidence inventory but does not close G-01/G-02/G-03/G-04/G-10 or schema readiness. It clarifies the difference between existing tests, source assertions, mocked behavior and executed assurance.

No production modification, test execution, data access, DDL/migration, provisioning or deployment occurred.