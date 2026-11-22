# PMS V2 — Testing Strategy & Quality Gates (Draft)

Updated: 2026-11-22
Status: Proposed testing baseline; Product Owner review required. No tests are implemented or claimed as run.

## 1. Goals and principles
- Protect verified business behavior, data integrity, tenant isolation and user workflows.
- Shift verification left; test at appropriate levels and keep PR checks deterministic.
- Use synthetic data by default; production data and production systems are excluded from ordinary testing.
- Tests must be reproducible, report failures clearly, and never silently skip safety checks.
- Tests are evidence, not security certification or migration readiness by themselves.

## 2. Testing layers
### Unit tests
Test isolated domain rules, validation, state transitions, calculations, transformations and edge cases. No external services or real credentials. Fast and deterministic.

### Repository/DB integration tests
Use disposable PostgreSQL instances/databases. Test repositories, constraints, indexes/query behavior where relevant, transaction boundaries, locking/concurrency and rollback. Fixtures are synthetic. Never target production or shared databases.

### API/contract tests
Validate request/response schemas, status codes, error codes, authentication, authorization, tenant/resource scope, pagination and idempotency behavior. Contract tests should be coordinated across frontend and backend.

### Frontend tests
Test critical UI behavior, state transitions, loading/empty/error states, form validation, responsive layouts, accessibility basics, session behavior and API integration using isolated mocks/test services.

### End-to-end tests
Use a dedicated isolated environment, synthetic data and explicit target allow-list. Fail closed when environment safety configuration is absent or target is not approved. Cover only stable high-value workflows initially.

### Migration tests
Test deterministic transformations, mappings, repeatability, integrity validation, exception reporting and recovery against isolated test datasets and disposable PostgreSQL. Live data requires separately approved protected process.

### Security tests
Test authorization matrix, tenant isolation, resource-level access, session lifecycle, input validation, CSRF where applicable, rate limiting, redaction, file access and upload validation. Include dependency/static checks as tooling matures; avoid treating automated scanners as complete assurance.

### Performance tests
Establish baseline before targets are fixed. Benchmark representative synthetic data and workloads. Test critical queries, API latency, concurrency, resource use and failure/recovery. No arbitrary numeric SLOs are assumed in this draft.

## 3. Priority workflow coverage
Initial risk-based coverage order:
1. Authentication/session, tenant isolation and authorization.
2. Project creation/identity, lifecycle/status and project information history.
3. Workflow definitions, versioning and project progression.
4. Events, form data and snapshot preservation.
5. Procurement, requirements, allocations, receipts and quantity integrity.
6. Files, associations, permissions and lifecycle/reconciliation.
7. Audit records, redaction and attribution.
8. Notifications/outbox, retry, deduplication and failure handling.
9. Reporting and printing.
10. Responsive frontend, critical loading/error states and user workflows.

Coverage ordering is a risk recommendation, not a verified exhaustive feature list.

## 4. CI quality gates (proposed incremental adoption)
PR validation should progressively include:
- Reproducible dependency installation and lockfile validation.
- Lint/format checks as selected.
- TypeScript type-check and build.
- Unit tests.
- Database migration and integration tests using disposable PostgreSQL.
- API contract tests.
- Frontend checks and build.
- Safety checks for E2E configuration and synthetic-only test fixtures.
- Dependency/security checks as tools and policies are established.
- Documentation/configuration validation in Infra repo.
- Minimal workflow permissions, explicit timeouts and concurrency control.

Required checks and branch protections must be configured in GitHub settings after repository setup; workflows alone do not establish protection.

## 5. Test data and environment safety
- Synthetic fixtures are the default and must not contain credentials or real personal/production data.
- Isolated test DB/storage/services; never point automated tests at live endpoints.
- E2E and destructive test jobs require explicit target/environment checks and fail closed.
- Test secrets, if later needed, belong in protected environment secret storage and must not be exposed to fork PRs or logs.
- Reset/cleanup procedures must be verified and constrained to test resources.
- No automatic deploy or production migration from routine CI.

## 6. Failure and evidence reporting
- Report actual commands, environments and results; distinguish skipped, blocked, flaky and passed.
- Keep artifacts limited to sanitized logs/results.
- Failures must not be hidden by broad catches, permissive retries or conditional skips.
- A merge/release decision considers scope, risks, review and test evidence; passing CI is not PO acceptance.

## 7. Proposed minimum gate by change type
- Documentation-only: relevant markdown/links and formatting checks when available.
- Backend: lint/typecheck/unit/build plus applicable DB/API/security tests.
- Database/migration: migration validation, fresh DB, applicable upgrade/recovery and integrity tests.
- Frontend: lint/static/unit/build, contract tests and UI tests for changed critical flows.
- Infra/CI: YAML/shell/Compose/Nginx validation, permissions and secret/deployment-safety review.
- Cross-repository changes: coordinated contract checks and integration verification across affected repos.

## 8. Open decisions
- Tooling choices (test framework, coverage, lint/formatter, browser automation).
- Coverage thresholds and branch protection policy.
- E2E environment provisioning and secrets policy.
- Performance targets/workload profiles.
- Accessibility testing depth.
- Security scanning cadence and triage policy.
- Release gates and acceptance evidence format.

## 9. Safety boundary
This draft does not authorize application implementation, database schema/migration, access to production data, production testing, deployment or VPS acquisition. All test environments must remain isolated and synthetic unless a separate approved process is established.
