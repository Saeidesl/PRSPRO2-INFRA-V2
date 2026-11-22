# Task 2.8 — Frontend Assertion Crosswalk (Increment 47)
Date: 2026-11-22
Status: frontend assertion source inspection only; no execution.

## Files reviewed
- `tests/router-production-base-path.test.js`
- `tests/project-draft-information-flow.test.js`
- `tests/procurement-receipt-form-loading.test.js`
- `tests/project-reference-conflict-resolution.test.js`
- `tests/project-information-dossier.test.js`

## Assertions inspected
### Router base path
Tests evaluate frontend router source in a VM and assert production GitHub Pages, explicit legacy/staging paths, and no fallback to development path for root. This is a limited path-resolution unit test; it does not exercise real browser loading, API calls or permissions.

### Draft project flow
Source assertions cover user-entered project code, absence of implicit draft creation, reopening/completing drafts, own-draft access/permission UI, draft display and card-related wiring. The tests assert source patterns and do not exercise actual backend permission enforcement or tenant isolation.

### Procurement receipt UI loading
Assertions extract the modal function and check UI-first loading, disabled selector/submit during lookup, enabling after success, and visible failure handling. These are source-pattern checks, not executed browser/network scenarios.

### Reference conflict resolution
Source assertions check source/local decision buttons, expected revision, resolve API call, reload, confirmation and click lockout. They do not simulate backend conflict concurrency or tenant-scoped source authorization.

### Project dossier
Assertion source covers project forms/information panels, editing, revision history, retries, stale response handling, and cache versioning. This is mainly source-text validation; it does not establish runtime UX/accessibility, authorization or data integrity.

## Interpretation
E2 assertion-source inspection only. Runtime tests, browser E2E, backend integration and tenant-crossing validation are not established by this increment. G-01 remains OPEN.

## Safety
No frontend/backend production repository mutation, test execution, live data, DDL, migrations, provisioning or deployment.
