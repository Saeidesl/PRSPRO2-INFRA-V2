# Task 2.8 — Legacy test intent review (Increment 71)

Date: 2026-11-23
Evidence level: E1 test-source inspection; tests were not executed.

## Test-tree inventory
- The current backend tests directory listing returned 41 test files (earlier checkpoint documented 40; this count difference is noted as snapshot drift, not reconciled by execution).
- Six representative test sources were inspected: material-planning, procurement-service, project-deletion, workflow-event-enforcement, attachment-service and event-idempotency-schema-recovery.

## Coverage intent observed in test sources
- Material planning: specification validation/revision, calculation replay/idempotency, variance and confirmed-event need creation; mock-based service tests.
- Procurement: mock repository/service tests for tenant-scoped data, request-level collection reuse, visibility and lifecycle behaviors.
- Project deletion: dependency collection filtering by tenant/project/status, shared-order protection and retry-oriented VOID handling.
- Workflow enforcement: source-contract assertions for workflow event rules, tenant/project-scoped progress, event snapshots, repeat rules, manual completion and lock revalidation; syntax parsing assertions.
- Attachment: mocked service tests asserting project visibility gates event attachment list/upload and permitted visible-event listing.
- Idempotency schema recovery: mocked sheet tests asserting idempotency-header insertion without column reorder, preservation of rows and stale header-cache recovery.

## Verification limitations and V2 test requirements
- These test files reveal test intent and existing legacy checks only. They were not executed and do not establish pass status or runtime coverage.
- The reviewed test patterns are largely isolated/mocked/source-contract tests; V2 requires PostgreSQL integration tests for FK/unique/RLS, transaction rollback, concurrent mutation, tenant isolation, audit durability, external-storage reconciliation, migrations and restore.
- Build a test traceability matrix linking every business rule and API mutation to unit, integration, authorization-negative, concurrency and recovery tests.
- G-01/G-10 remain OPEN/BLOCKER; G-02/G-03/G-04 remain OPEN/HIGH. Schema/migration readiness NOT APPROVED.
- No production changes, live data, migration, deployment or provisioning.
