# Task 2.8 — Frontend-to-Backend Contract Assertion Crosswalk Increment 28
Date: 2026-11-22
Scope: Read-only assertion inspection of selected production frontend tests (repository is reference-only).

## Project information dossier tests
Assertions check project information and event-backed forms display, retry/stale-response handling, edit/delete/history actions, before/after revision values, central information and event-form panel separation, and renderer data-type handling.

## Event submission progress tests
Assertions check progress UI timing, preparation/event/refresh steps, successful attachment upload counting, retry path on upload/event failure, separate refresh warning, accessibility/responsive/reduced-motion/focus behavior, result panel removal, five-second success auto-close and warning display for failed procurement integration.

## Repeat reference synchronization tests
Assertions check source/local conflict resolution, revision guards, workflow-level reference-copy policy, no project-level override, resumable copy progress and retry of SKIPPED_FILE items; selected deployment workflow branch/path patterns are asserted. This does not verify actual deployment.

## Receipt form UX tests
Assertions check receipt form renders before project lookup completes, project selector and submit disabled until lookup succeeds, and visible lookup error keeps submission disabled.

## Interpretation
Selected tests assert UI source contracts and some workflow affordances; they are not end-to-end evidence or executed tests. Frontend repository is production reference only and remains unmodified. These tests do not prove API permission enforcement, transaction integrity or tenant isolation.

## V2 cross-system recommendations
- Keep frontend progress states subordinate to authoritative backend operation outcomes.
- APIs should expose operation IDs and stable reconciliation status for retries.
- Validate frontend-to-backend route/method/permission contracts with integration tests in V2.
- Preserve accessibility and responsive behavior in frontend acceptance tests; test receipt failure paths against API integration and transaction rollback.

## Limits
Read-only source assertion review; tests not executed. G-01 remains OPEN. No production modifications, tests, live data, DDL, migrations, provisioning or deployment.
