# Task 2.8 Increment 209 — Domain service operation inventory (selected core services)

Date: 2026-11-24

## Exact source snapshots fetched
- EventService.js SHA `1fe31ea9ea2a048a870ecbc47c11512bc2942108`
- FormService.js SHA `abddf1851d5721347ac07618e85bfa75cef61589`
- ProjectService.js SHA `5c9b5bb3f79a2f85472d2cc2ec3d8c3d1e31321f`
- WorkflowService.js SHA `eb8373c14085975a9eb521d5ba20ec2e5146bfad`
- ProcurementService.js SHA `c0e16aa769cd88f85ba8d65ce12e093b636accf0`
- AttachmentService.js SHA `74492d147499024b6416555a31dd1ee294a746cb`
- MaterialPlanningService.js SHA `ff083babf9ef6c3e9e0763ae3c1773db83fc5485`
- ProjectInformationService.js SHA `965dfbb92b064f129aefaf38e5a8485bd836b2a8`
- ProjectDeletionService.js SHA `62482bfb42c2c7bb3329becbb2f821e1efbb4c8e`

## Method inventory (static method-name extraction; not behavioral proof)
- Event: list, listByProject, getProjectDetailsPage, getProjectDetails, completeStep, workflowProgress, updateFieldFromTemplate, create, _createUnlocked_, void; plus validation/department/workflow helpers and enrichment.
- Form: list/get/create/update, addField/updateField/removeField, form/event-type assignment, prepare/save responses, update event form field/response, get responses; cache/snapshot/visibility helpers.
- Project: project-form/dashboard lookups, list/get/create/update, project-code-from-event validation/update, reference-copy information/image handling and template updates.
- Workflow: management bootstrap/data, list/get/create/update, step CRUD, event-rule retrieval/saving, publish.
- Procurement: createOrder, createReceipt, createReceiptsBatch, listNeeds/listOrders/workspace/reports/dashboard; unlocked helpers and project/event validation.
- Attachment: copy project image, list/list project images/gallery/counts, get/listByEntity, upload, thumbnail/download/delete and owner-visibility helper.
- Material planning: list/create/revise specs, calculation history/record calculation, recalculation, event-void handling.
- Project information: list/get/save/approve/conflict resolution/live reference sync/deactivate/saveWithLock/history.
- Project deletion: preview/remove/softDelete plus dependency collector/context helpers.

## Domain risk/coverage prioritization
The method inventory confirms major lifecycle operation families and supports targeted tracing:
1. Event creation, void, workflow progression and effect propagation.
2. Form-definition and response revision/snapshot semantics.
3. Project copy/update/delete lifecycle and image/information copy.
4. Workflow publish/rules/assignment invariants.
5. Procurement canonical receipts, batch/single parity, correction and replay.
6. Attachment ownership/storage/copy/delete/recovery.
7. Material spec revision/calculation ledger and procurement integration.
8. Project-information approval/conflict/live reference synchronization.

This list is a prioritized tracing backlog, not proof of behavior. For each method, inspect guard, tenant/relationship check, validation, repository writes, lock/atomicity, idempotency, downstream effect, frontend caller and test.

## Safety/status
Read-only selected service inventory. No production changes, test execution, DDL/migration, live data, VPS or deployment. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED.
