# Task 2.8 Increment 132 — CI and regression-test coverage crosswalk (source inventory)

Date: 2026-11-23  
Evidence: E1 — read-only inspection of CI workflow files and selected test source; tests not executed.

## Backend CI observed
- Backend source-check workflow runs Node test runner across `tests/*.test.js`, validates Apps Script manifest JSON and syntax for JavaScript source files, and checks duplicate Apps Script target names/source-count baseline.
- Workflow triggers are path-scoped for main pushes, pull requests and manual dispatch. CI is a source/test check; it does not establish runtime behavior against Apps Script/Sheets.
- Schema-contract, permission request-cache and reusable-form API-contract tests were inspected. They assert source-text contracts and ordering/patterns; they do not execute domain services against a real DB or Apps Script runtime.

## Frontend CI observed
- Frontend check workflow runs Node syntax checks over JS modules, regression tests, local asset existence and CSS brace balance.
- A write-enabled staging procurement E2E is expected to fail closed when staging API credentials/URL are absent, and workflow checks this safety outcome. It is not a live production test.
- The source audit did not run CI or browser tests; workflow source only was inspected.

## Selected test families inventoried
Backend: schema contract, auth/access-control cache, forms API contract, project/event lifecycle, workflow rules/enforcement, procurement, attachment/storage, reports/print templates, performance and Telegram. Coverage and test depth varies; file names and assertions are not proof of complete behavior.
Frontend: projects/events/dashboard, picker/cache, form/A4, procurement, project images/detail, workflow, print designer/output, theme/calendar/accessibility and performance tests; some browser tests use separate scripts/workflows.

## Coverage limitations
1. Many test assertions are static-source contracts, not service/runtime integration tests.
2. Runtime GAS, Sheets, Drive, external Telegram and browser execution remain separate validation environments.
3. Test inventory is not an exhaustive trace from every route and business rule to one or more tests.
4. Concurrency, fault injection, rollback/recovery, authorization negative cases, tenant isolation, migration correctness, report snapshot consistency and large-data performance remain broad gaps.
5. CI workflows may use scoped triggers and do not prove all files/modules are covered by every run.

## V2 implications
1. Define layered tests: unit, PostgreSQL integration, API contract, authorization/tenant isolation, migration validation, fault injection, browser E2E and performance.
2. Build a requirements-to-test traceability matrix with explicit gaps and risk severity.
3. Use synthetic/sanitized datasets, repeatable isolated environments and deterministic fixtures.
4. CI should gate PRs with lint/typecheck, unit/integration, migrations, security checks, contract and browser tests; deployments should be approval-gated.
5. Test recovery and idempotency paths, not only happy-path assertions; measure performance at realistic data volumes.

## Limits
- Source inventory only; no test, CI job, browser or production run executed.
- E1 only; Task 2.8 remains IN PROGRESS; G-01/G-10 remain open; schema readiness NOT APPROVED.
