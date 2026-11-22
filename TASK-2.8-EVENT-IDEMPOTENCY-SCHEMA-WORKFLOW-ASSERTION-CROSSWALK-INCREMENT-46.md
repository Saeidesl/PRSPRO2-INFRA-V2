# Task 2.8 — Event Idempotency, Schema Contract & Workflow Assertion Crosswalk (Increment 46)
Date: 2026-11-22
Status: source/assertion inspection; runtime tests not executed.

## Reviewed test sources
- `tests/event-idempotency.test.js`
- `tests/event-idempotency-schema-recovery.test.js`
- `tests/schema-contract.test.js`
- `tests/workflow-event-enforcement.test.js`
- `tests/workflow-event-rules.test.js`

## Assertion scope observed

### Event idempotency
The harness asserts duplicate request replay returns prior event without repeating create, and scopes idempotency keys by tenant and creator in sampled scenarios. It uses mocked repository/user/lock functions and replaces unlocked creation with harness logic, so it does not prove end-to-end persistence, atomicity, real lock behavior, payload equivalence or tenant constraints.

### Idempotency schema recovery
Mock sheet tests assert that a missing idempotency status header is appended, existing columns/rows are preserved, and stale header cache is refreshed. This is a mocked sheet surface, not actual spreadsheet runtime or migration execution.

### Schema contract
Source-text assertions check material specification/calculation schema headers, additive header behavior, and guarded manually triggered Apps Script sync workflow. Assertions confirm source text patterns; they do not validate real database constraints, migration safety, execution permissions or deployment outcomes.

### Workflow enforcement
Source-text assertions cover active-step event validation, project/tenant access-scoped workflow progress, workflow version/step metadata, repeatable rules, explicit completion and revalidation under a repository write lock. A syntax parsing test exists in the file, but it has not been run in this audit. The file's source assertions do not simulate actual request authorization, lock concurrency, persisted workflow state or tenant cross-contamination.

### Workflow event rules
Inspected assertions cover schema/version headers, Router permission gates, tenant checks in WorkflowService, rule validator requirements/sequence/display order, published-version lookups and independent display order. A small validator harness tests FREE ordering and duplicate display order, but it does not establish database constraints, full publish concurrency, runtime API behavior or cross-tenant integration.

## Evidence boundaries
- E2 assertion source inspected; no E3 runtime execution performed.
- Harness/mocks and regex/source assertion boundaries are recorded.
- No live data, schema execution, migration, provisioning, deployment or production mutation.

## Remaining coverage risks for G-01/G-03/G-10
1. Payload-bound idempotency/replay under real concurrency and failures.
2. Full tenant-crossing route/API tests with persisted database constraints.
3. Workflow publish/version race and lock/failure behavior.
4. Event create/void/reversal and material/procurement downstream lifecycle consistency.
5. Migration rehearsal and failure/recovery tests against isolated PostgreSQL in V2, not legacy production.

## Safety
Production repositories remain read-only; no tests run, live data or production changes.
