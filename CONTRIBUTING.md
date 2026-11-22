# PMS V2 — Collaboration & Engineering Conventions

These conventions apply to the independent V2 repositories only. Production repositories remain READ-ONLY references.

## Repository boundaries
- `PRSPRO2-V2`: frontend UI and client-side integration.
- `PRSPRO2BACKEND-V2`: Node.js/TypeScript API and business services.
- `PRSPRO2DATABASE-V2`: PostgreSQL schema, migrations, seed/test fixtures and migration tooling.
- `PRSPRO2-INFRA-V2`: infrastructure, CI/CD, project governance and documentation.
- Changes must stay within the owning repository unless a cross-repository contract requires coordinated changes. Document cross-repo dependencies and sequence them explicitly.

## Branch and pull-request workflow
- Keep the default branch stable; use short-lived task branches named `feat/<short-topic>`, `fix/<short-topic>`, `docs/<short-topic>`, `test/<short-topic>`, or `chore/<short-topic>`.
- Do not push directly to the default branch for implementation work when PR workflow is available.
- PRs should be small and task-focused, include rationale, scope, linked task, risk/rollback notes, and test evidence.
- Cross-repository changes should reference counterpart PRs and compatible commit/API/schema expectations.
- Do not merge until required CI checks pass and review/PO approval is obtained where required by the project protocol.

## Commit conventions
Use concise imperative messages, preferably with a scope:
- `feat(api): add project listing contract`
- `fix(auth): enforce tenant scope on resource access`
- `docs(infra): record migration guardrails`
- `test(db): cover migration constraint`

## Quality gates
- CI must run reproducible checks (install, formatting/lint where adopted, type-check, unit tests, build, and relevant integration checks).
- Do not mark a check as passed unless it actually ran and passed.
- Any change to API contracts, database migrations, authorization, tenant isolation, workflow transitions, procurement, file lifecycle or audit behavior requires targeted tests and a documented risk/verification plan.
- Test data must be synthetic or sanitized. Production data must not be used in development or CI.
- Integration and end-to-end tests must fail closed if their isolated test environment is not configured; they must never target production.

## Security and privacy
- Never commit secrets, credentials, access tokens, private keys, personal or production data.
- Use environment variables or approved secret storage; document required names and purpose without values.
- Avoid credentials in URLs. Redact sensitive payloads from logs and diagnostics.
- Validate inputs server-side. Authorization must be tenant- and resource-aware and fail closed.
- Do not expose sensitive details in error responses.

## Change and task management
- Read `TODO.md` and `ARCHITECTURE.md` before starting a major task.
- One major task at a time; keep status truthful: NOT STARTED, IN PROGRESS, IMPLEMENTED, TESTED, ACCEPTED, COMPLETE.
- COMPLETE requires verification and explicit Product Owner acceptance.
- Preserve current production PMS behavior and availability; no production repository changes.
- No real-data migration, dual-writing, deployment or VPS purchase before explicit authorization.

## Documentation
- Update README, TODO, ARCHITECTURE and related docs as decisions or task status change.
- Separate confirmed facts from proposals, assumptions and unknowns.
- Record test evidence, blockers, dependencies and risks in PR descriptions or project docs.
