# Task 2.8 Increment 205 — Exhaustive recursive tree inventory and coverage evidence (EP-01 advancement)

Date: 2026-11-24

## Method and read-only scope
Retrieved GitHub recursive Git trees via REST for both production reference repositories, default branch `main`. These are immutable tree snapshots (not runtime execution). `truncated=false` for both.

- Backend root tree SHA: `8253eb3718d1a2a1bd5de03b6b5090ba69b94b67`; 153 tree entries total (147 blob/file entries).
- Frontend root tree SHA: `b74424ccb8d981172384d4eddbf6910a8f09b09c`; 187 tree entries total (172 blob/file entries).

The exact path manifests were returned in tool output and categorized below. These SHAs identify tree objects; the parent commit SHA was not separately verified here, so the snapshots must not be represented as reviewed commit IDs.

## Inventory-level counts
- Backend: 147 file entries; 41 files under `tests/`.
- Frontend: 172 file entries; 46 files under `tests/`.
- Test-file presence is inventory evidence only. No test execution, test assertion review, or runtime verification is implied.

## Backend source-family inventory (from recursive paths)
Core/source families identified: authentication/session, access control/permission and role, project/status/deletion, workflow/steps/departments/rules, event/event-type/event-data, form, project information, material planning, procurement and event integration, attachment/storage/Drive, audit, reporting, print-template, diagnostics/performance, Telegram, Sheets repository/configuration, validators, router/API, deployment workflows and test files.

## Frontend source-family inventory
Core source families identified: core API/router/session/cache/permission/theme, UI components, page HTML, module scripts for admin/auth/dashboard/events/forms/projects/project-detail/images/workflows/procurement/material planning/attachments/reports/print designer/roles/permissions/users/settings/audit, shared CSS and fonts, test scripts, staging/performance/audit workflows and test-results artifacts.

## Coverage reconciliation
Tree inventory advances G-01/EP-01 from sampled file fetches to an exhaustive path inventory for the two captured default-branch tree snapshots. It does NOT close G-01 because:
- exact parent commit refs were not independently captured/verified in this operation;
- file-level route/handler, callers, consumers, tenant/security predicates, mutation effects, and dependencies have not yet been exhaustively traced against every path;
- excluded, generated, unused, and legacy files require disposition;
- tests need evidence review and/or execution in a safe non-production environment.

## Next evidence work
Prioritize a route/handler/caller crosswalk using Router.js and frontend router/API plus domain service files. Map each route/operation to permission/tenant guard, input validation, mutation/read side effects, projections/history/audit/attachments/notifications/reports, caller, and existing tests. Reuse existing path manifest rather than repeating tree retrieval.

## Safety
Both production repositories remain read-only. No file changes, commits, branches, PRs, tests, live data, DDL, migrations, deployment, VPS or provisioning. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED.
