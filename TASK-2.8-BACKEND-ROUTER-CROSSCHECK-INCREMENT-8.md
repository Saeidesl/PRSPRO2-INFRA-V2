# Task 2.8 — Backend Router Cross-check of Sampled Frontend Calls (Increment 8)

Date: 2026-11-22
Scope: READ-ONLY targeted cross-check of sampled frontend endpoint families against backend Router.js route/dispatch text. No tests or runtime execution.

## Cross-check notes
- Procurement workspace, dashboard, needs, orders, and batch receipt route text was located in Router.js for targeted strings. This is evidence of route text presence only; branch-specific method, authorization, handler and side-effect coverage must be fully reconciled.
- Form and event-type/form mapping route families, form-field routes, project card image and image-gallery count routes, complete-step, reference-copy, and attachment route family were also located in Router.js by targeted text search.
- The literal string search for workflow-progress did not find a match in the retrieved Router.js text. This is an unresolved route alias/dispatch/caller discrepancy requiring examination of actual frontend path construction and backend route resolution. It is not asserted as a confirmed missing endpoint.
- Some frontend calls are constructed dynamically (API wrapper handles HTTP path), so wrapper source alone is not expected to contain all endpoint literals. A literal string search in API wrapper is not valid caller coverage evidence.
- This cross-check is intentionally conservative: route-string presence does not establish method support, exact handler match, permission/tenant enforcement, validation, tests or behavior.

## Unresolved reconciliation targets
1. Resolve the workflow-progress discrepancy by following backend route dispatch, helper/alias logic and frontend path composition.
2. Reconcile HTTP verbs and exact handlers for each sampled path family, including event/form updates and procurement receipts.
3. Establish per-route permission, tenant-scoping, validation, idempotency and transaction behavior from handler/service sources.
4. Map tests by exact route/service and verify tests run later under an approved non-production environment.

## Limits
Bounded string/source cross-check only; no test execution, live data, SQL/DDL, migrations, provisioning, production writes or deployment. G-01 remains OPEN.
