# Task 2.8 — G-01 Frontend/Backend Test Tree Reconciliation (Increment 44)
Date: 2026-11-22
Status: source/tree inventory only; READ-ONLY.

## Exact recursive-tree evidence
- Frontend repository PRSPRO2-PRODUCTION recursive tree: 187 entries; tests directory contains 43 test files.
- Backend repository PRSPRO2BACKEND-PRODUCTION recursive tree: 153 entries; tests directory contains 43 test files.
- These counts represent filenames/tree entries, not test execution, coverage percentages, or full behavioral coverage.

## Frontend test file inventory (43)
draft-event-type-visibility.test.js
- event-context-filtering.test.js
- event-context-panel-scroll.test.js
- event-form-a4-modal.test.js
- event-form-cache-policy.test.js
- event-form-performance-audit.test.js
- event-form-single-mode.test.js
- event-history-filters.test.js
- event-history-project-cards.test.js
- event-image-preview-modal.test.js
- event-project-picker-performance.test.js
- event-queue-project-cards.test.js
- event-submit-latency.test.js
- event-submit-progress-modal.test.js
- event-type-loading-state.test.js
- events-create-project-entry.test.js
- events-create-project-followup.test.js
- events-project-create-progress.test.js
- information-file-types.test.js
- information-types-page.test.js
- iransans-fonts.test.js
- jalali-calendar-datetime.browser.cjs
- jalali-calendar-datetime.test.js
- print-template-library.test.js
- procurement-form-loading.test.js
- procurement-receipt-form-loading.test.js
- project-color-size-cleanup.test.js
- project-created-at-datetime.test.js
- project-draft-information-flow.test.js
- project-event-history-shortcut.test.js
- project-image-gallery-ui.test.js
- project-information-dossier.test.js
- project-material-create-cleanup.test.js
- project-modal-accessibility.browser.cjs
- project-modal-accessibility.test.js
- project-picker.test.js
- project-print-designer.test.js
- project-print-output-batch.test.js
- project-reference-conflict-resolution.test.js
- project-repeat-reference-sync.test.js
- project-report-builder.test.js
- reusable-forms.test.js
- router-production-base-path.test.js
- shared-page-cache.test.js
- single-project-entry.test.js
- workflow-management.test.js

## Backend test file inventory (43)
access-control-request-cache.test.js
- attachment-service.test.js
- dashboard-performance-contract.test.js
- event-form-definition-batch.test.js
- event-form-definition-cache.test.js
- event-idempotency-schema-recovery.test.js
- event-idempotency.test.js
- event-project-picker-projection.test.js
- event-type-form-prefetch.test.js
- forms-api-contract.test.js
- get-performance-diagnostics.test.js
- google-drive-storage-folder.test.js
- google-sheets-cache-invalidation.test.js
- google-sheets-column-index.test.js
- information-cardinality.test.js
- information-file-types.test.js
- information-type-foundation.test.js
- material-planning.test.js
- material-schema-cache.test.js
- performance-diagnostics.test.js
- print-template-batch-resolve.test.js
- print-template-batch-runtime.test.js
- print-template-revision.test.js
- print-templates.test.js
- procurement-event-integration.test.js
- procurement-service.test.js
- project-created-at-datetime.test.js
- project-deletion.test.js
- project-draft-information-flow.test.js
- project-image-gallery-api.test.js
- project-image-gallery-thumbnail.test.js
- project-repeat-reference-copy.test.js
- report-service.test.js
- reusable-forms.test.js
- schema-contract.test.js
- single-project-entry.test.js
- telegram-bot-service.test.js
- telegram-notification.test.js
- user-validator.test.js
- workflow-event-enforcement.test.js
- workflow-event-rules.test.js

## Preliminary thematic mapping (filename-level only)
- Frontend: event registration/context/history; forms and cache; project entry/draft/reference; images/gallery; information and dossier; procurement/receipts; print/report; accessibility; router/cache; workflow.
- Backend: authentication/access cache; attachments; event/form/idempotency; Sheets/Drive/cache; information model; material planning/procurement; project deletion/reference; print/report; Telegram; validator; workflow enforcement/rules.

## Reconciliation limitations
1. This does not map each test file to specific assertions, code paths, route permissions, data constraints or business invariants.
2. Filename-based themes are weak evidence; files may be stale, partial, skipped, or narrowly scoped.
3. Test runs/results have not been executed/inspected in this increment.
4. Missing test names cannot be treated as missing implementation, and existing names cannot be treated as verified coverage.
5. G-01 remains OPEN pending operation-level mapping and test assertion inspection; G-02/G-03/G-04/G-10 remain separate blockers.

## Safety
No source writes, PRs, branches, runtime tests, live data, production repository mutation, DDL, migrations, deployment or provisioning.
