# Task 2.8 — Test Surface Inventory Increment 11
Date: 2026-11-22

## Scope
Read-only recursive Git tree inventory of test paths in both legacy repositories. This is a filename/path inventory only; no test runner was invoked and no test result is inferred.

## Backend test surface
Observed test files include access-control request cache, attachment service, event/form definition cache/batch, event idempotency, forms API contracts, sheet cache/column index, information cardinality/file types, material planning/schema cache, procurement service/integration, project deletion/draft flow/image gallery/reference copy, report service, reusable forms, schema contract, workflow event enforcement/rules, and other tests.

## Frontend test surface
Observed test files include draft event-type visibility, event context filtering, event-form performance/cache/modal/single-mode, event history/cards, image previews, project picker, event submit latency/progress, event type loading, project create flow, information types/file types, Jalali date/calendar, print templates/designer/output, procurement loading/receipt loading, project information/report, reference conflict/sync, accessibility, router production base path, shared cache, and workflow management.

## Coverage limitation / reconciliation gap
- Filename presence is not evidence that tests execute or pass, nor that the tests cover all route handlers, tenant predicates, authorization, transaction boundaries, mutation failure cases, or migration invariants.
- The test inventory does not map individual test assertions to every service/route/source surface; test-to-requirement traceability remains an open G-01 evidence gap.
- No tests were run.

## Status
G-01 remains OPEN; schema readiness NOT APPROVED. No production changes, live data, DDL, migration, provisioning, or deployment.
