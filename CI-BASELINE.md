# PMS V2 — CI Baseline Inventory

Updated: 2026-11-22
Status: Baseline inventory documented; V2 CI implementation not yet established.

## V2 repository state
- PRSPRO2-V2 (frontend): verified earlier as accessible and currently empty.
- PRSPRO2BACKEND-V2: verified earlier as accessible and currently empty.
- PRSPRO2DATABASE-V2: verified earlier as accessible and currently empty.
- PRSPRO2-INFRA-V2: contains project governance documents only; no CI workflow currently established.
- The three implementation repositories were previously confirmed Private. Infra repo remains public; no secrets or production data are stored in this documentation task.

## Read-only reference CI patterns
Current production repositories were read only; they were not modified.

### Legacy frontend workflow — observed patterns
- GitHub Actions on push to feature/fix/main branches and pull requests to main.
- Node.js runtime setup.
- JavaScript syntax checks.
- Node built-in test runner.
- Local asset integrity and CSS brace checks.
- A write-enabled staging E2E safety test that verifies fail-closed behavior without isolated staging configuration.

### Legacy backend workflow — observed patterns
- GitHub Actions on main push, pull request and manual dispatch.
- Read-only repository contents permission in the workflow.
- Node.js runtime setup.
- Node built-in tests.
- Manifest parsing and source syntax validation.
- Duplicate target-name validation.

These patterns are reference evidence only; legacy GAS-specific checks must not be blindly copied into V2.

## Proposed V2 baseline — subject to implementation task and repository owner approval
### Backend (Node.js + TypeScript)
1. Pull request and push validation; default branch protection/required checks should be configured separately in GitHub settings.
2. Pinned supported Node LTS runtime (select when package/toolchain is established).
3. Reproducible install using lockfile (npm ci).
4. Formatting/lint checks once selected.
5. TypeScript typecheck.
6. Unit tests.
7. Build.
8. Integration tests using isolated disposable PostgreSQL service/container only when test suite exists.
9. Minimal workflow permissions (contents: read); no secrets in validation jobs.
10. Explicit timeout and concurrency cancellation to avoid stale duplicate CI runs.

### Database/migrations
1. Validate migration files and naming/order.
2. Lint/format migration and SQL as adopted.
3. Test fresh database migration from empty state.
4. Test upgrade path from selected prior migration states.
5. Verify schema constraints/indexes and migration reversibility/recovery policy.
6. PostgreSQL service isolated to CI; no external or production database connection.
7. Seed/test fixtures strictly synthetic.

### Frontend
1. Reproducible install and lockfile.
2. Syntax, lint/format and static checks.
3. Unit tests.
4. Build/packaging checks if a bundler is adopted.
5. Contract tests against documented API types or isolated mock/test server.
6. Responsive/accessibility checks as tooling and app structure are established.
7. E2E tests only against a dedicated isolated ephemeral environment; fail closed if target/config is absent.

### Infrastructure/documentation
1. YAML and shell validation.
2. Dockerfile and Compose static validation when created.
3. Nginx configuration validation in container.
4. Security checks for secrets/unsafe configuration and image/dependency vulnerabilities as tooling is adopted.
5. Documentation links and required runbook completeness checks.

## CI design guardrails
- CI is a validation mechanism; passing CI does not prove business correctness, security certification, or migration readiness.
- No secrets, real user data, production data or live-system credentials in CI.
- No workflow should deploy, migrate, or access a live PMS as part of ordinary PR checks.
- Integration/E2E jobs must use disposable isolated resources and fail closed.
- Workflows should have least privilege, explicit timeouts and clear test reporting.
- Do not make CI overly complex before code/tooling exist; establish a minimal baseline incrementally.

## Findings and limitations
- V2 implementation repositories are empty; their CI baseline cannot currently be run.
- Infra repository has documentation only; no code tests or CI checks were executed.
- Existing production workflows were inspected as read-only references only.
- No changes were made to production repositories.
- This inventory is a design recommendation, not implemented CI or a verified pipeline.
