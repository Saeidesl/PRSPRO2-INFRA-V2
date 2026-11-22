# Task 2.8 — Legacy Test Inventory & V2 Verification Gap Map (Increment 63)
Date: 2026-11-22
Status: READ-ONLY test source inventory; tests not executed in this session.

## Test inventory verified from repository tree
The legacy backend repository contains 40 test files in its tests directory. The inventory includes focused suites for:
- Event idempotency/recovery and form definition batch/cache.
- Attachment access and event-project visibility.
- Project deletion dependency collection and retry-safe handling.
- Procurement service behavior and event integration.
- Material planning specification/calculation lifecycle.
- Workflow event enforcement/rules.
- Information cardinality/file types, reusable forms and schema contract.
- Permission cache, user validation, reports, storage folder, performance/cache and print templates.

## Representative test-name evidence read
- Attachment suite: project visibility denial/allow cases for event attachments.
- Project deletion suite: dependency collection boundaries, shared order preservation, deleted rows and VOID retry semantics.
- Procurement suite: tenant/project lookup reuse, allocation caps and surplus, workspace cache/read reuse.
- Workflow event rules suite: schema/version-aware rules, permission/tenant scope, validation/order semantics and published workflow version lookups.
- Event idempotency suite: replay, tenant+creator scope, interrupted requests, completion state and failed completion persistence.
- Material planning suite: spec revision/history, calculations, confirmed-quantity event replay, source-event void reconciliation, lock contention and cross-tenant/invisible project rejection.

## Coverage gaps (not established by inventory)
- Tests were not run in this session and their pass status is unknown.
- Existing tests are isolated source-driven Node tests; no real PostgreSQL or migration runtime validation evidence.
- Comprehensive DB composite FK/unique/check constraints, RLS policy verification, concurrent transaction behavior, full API route permission matrix, storage disaster recovery, audit tamper-resistance, outbox/retry workflow and end-to-end tenant-isolation across all relationships remain unverified.
- The test inventory cannot establish coverage completeness or runtime production behavior.

## V2 verification requirements
1. Build an explicit coverage matrix mapping each discovered business entity/operation to source evidence, V2 schema constraint, service/API authorization and automated test.
2. Define isolated PostgreSQL test fixture and deterministic cross-tenant negative tests.
3. Require CI checks for migrations from clean DB, upgrade path, rollback/recovery, API integration and permission matrix.
4. Add concurrency and failure-injection tests for transaction boundaries, outbox and storage operations.

## Evidence limits
E1 test-source inventory only; no tests executed and no production repository changes. G-01/G-10 remain OPEN; schema readiness NOT APPROVED.
