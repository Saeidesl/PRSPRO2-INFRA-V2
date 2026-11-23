# Task 2.8 Increment 130 — Frontend material planning, procurement, workflows, reports and print designer callers

Date: 2026-11-23  
Evidence: E1 — read-only frontend source inspection

## Files inspected
- js/modules/material-planning.js
- js/modules/procurement.js
- js/modules/workflows.js
- js/modules/workflow-department-assignments.js
- js/modules/reports.js
- js/modules/print-designer.js

## Observed caller patterns
### Material planning
- UI presents project material specifications and calculation history, supports multi-row create/revision, and sequentially POSTs new specifications or PUTs revisions. After save it refreshes specifications/calculation history; saving is UI-serialized and error feedback is local.

### Procurement
- Procurement UI loads workspace/dashboard/needs/orders and project lookups, applies client filters and status presentation, and gates mutation controls by PROCUREMENT_MANAGE in frontend. It supports order create, receipt batch and related material/project views. Backend permissions remain authoritative.
- UI renders quantities, statuses, order/need projections and print-oriented order sheets; error/loading/saving state is maintained locally. The exact full caller payload matrix remains pending exhaustive extraction.

### Workflow management and department assignments
- Workflow UI loads management bootstrap and workflow list, loads event types/steps/rules, and uses cache refresh callbacks/request IDs for stale-load protection. Create/update/publish/rule-save operations invalidate workflow/bootstrap caches.
- Department assignment UI loads bootstrap and workflow management-data projections, then supports batch/single assignment and removal. Cache invalidation spans management data, assignments, steps, workflows and department lookups.

### Project reporting
- Report UI loads visible projects and selected project report data, filters events client-side, and offers section/template controls. Report templates are stored locally in browser storage, not server-side, in the inspected caller. Export CSV and print rendering are client-side.

### Print designer/output
- Print-template library synchronization uses a revision endpoint and template version metadata to fetch only changed templates, keeps last known cache when refresh fails, and invalidates local template cache on mutations.
- Designer handles definitions, bindings, images and PDF background uploads/downloads; preview/resolve APIs, live project data bindings and output-edit flows are invoked. Template project-data edits delegate to the source record APIs through backend.
- UI has multiple caching, optimistic presentation and recovery paths; behavior under stale revisions, simultaneous edits, browser storage failure and partial image/PDF upload remains unverified.

## V2 implications
1. Consolidate procurement workspace/report contracts and provide typed API projections; minimize caller-side derivation of status/quantity.
2. Material-spec multi-row save should be an atomic server-side bulk operation or explicitly partial with durable result/retry semantics.
3. Workflow mutations/publish/rules/assignments require optimistic concurrency and durable transaction boundaries; frontend cache invalidation should be event-driven and centrally defined.
4. Reports and templates need server-owned/versioned template storage or a deliberate product decision to retain local-only templates; localStorage is not cross-device persistence.
5. Print template revision synchronization needs consistent revision snapshot, reliable changed-template fetch and cleanup semantics.
6. Output source edits require explicit permission, source revision checks and durable audit; attachments/PDF/image lifecycle needs reconciliation.
7. Verify mobile, browser, keyboard/accessibility, local storage failures, concurrent edits, partial save/upload and report correctness with runtime tests.

## Limits
- Source-only inspection; no tests or browser/runtime checks; no production changes or live data accessed.
- E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.
