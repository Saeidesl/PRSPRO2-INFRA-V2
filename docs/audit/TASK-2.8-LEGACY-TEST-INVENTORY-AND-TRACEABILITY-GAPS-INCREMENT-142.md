# Task 2.8 Increment 142 — Legacy test-file inventory mapped to audit domains

Date: 2026-11-23  
Evidence: E2 — test-file existence and selected assertions inspected; no tests executed.

## Backend test inventory (41 files)
- Auth/permission/request cache: access-control-request-cache, user-validator.
- Attachment/storage: attachment-service, Google Drive folder, project image gallery API/thumbnail.
- Event/form: event idempotency/schema recovery, event idempotency, event-form-definition batch/cache, event-type-form-prefetch, forms API contract, reusable forms, event picker projection.
- Information types: information cardinality, file types, foundation.
- Material/procurement: material planning/schema cache, procurement event integration/service.
- Projects: created-at datetime, deletion, draft information flow, repeat reference copy, single-project entry.
- Workflow: workflow event enforcement/rules.
- Reports/print: report service, print-template batch resolve/runtime/revision/print templates.
- Performance/cache: dashboard performance contract, GET diagnostics, performance diagnostics, Sheets cache invalidation/column index.
- Telegram: bot service and notification tests.
- Schema/setup: schema-contract.

## Frontend test inventory (46 files)
- Auth/core/routing/cache: router production base path, shared page cache.
- Events: context filtering/panel scroll, A4 modal, cache policy/single mode, history filters/cards, image preview, picker performance, queue cards, submit latency/progress, type loading, create-project entry/follow-up/progress.
- Projects: project picker, project color/size cleanup, created-at, draft information flow, history shortcut, image gallery UI, information dossier, material create cleanup, modal accessibility, print designer/output, reference conflict/sync, repeat sync, report builder, single project entry.
- Configuration/forms: information files/page, reusable forms, event types/forms, workflow management.
- Procurement: order/receipt form loading.
- UI/platform: Iran Sans fonts, Jalali calendar/date-time.

## Test evidence quality and traceability
- Test-file names indicate intended coverage but do not establish test execution/pass.
- Selected backend tests use source assertions, fake/stub environment or unit test harness; test fidelity to Apps Script/Sheets runtime and real transaction semantics varies.
- The inventory does not map every route/branch, service invariant, tenant constraint or mutation effect to a specific assertion. The detailed route matrices in increments 139–140 remain PARTIAL.
- Broad missing/weakly evidenced test classes include exhaustive authorization denial/tenant mismatch, concurrent writes/uniqueness, fault injection/rollback/reconciliation, migration validation, live storage recovery, snapshot-consistency reporting and performance at realistic data volumes.

## V2 test-traceability requirement
For every critical route/operation, record: route/method, DTO, permission, tenant/resource predicate, service invariant, DB constraint/transaction, idempotency, audit/outbox, test IDs, runtime test environment and residual gap. Gate readiness on an agreed coverage threshold and explicit residual risk acceptance.

## Limits
- Test inventory only; no CI or test execution and no production changes.
- E2 source evidence only; G-01/G-10 remain blockers; schema readiness NOT APPROVED.
