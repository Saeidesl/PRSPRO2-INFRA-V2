
# Task 2.5 — Evidence Gap Register & Source Audit Increment 1
Updated: 2026-11-22
Status: IN PROGRESS — read-only source audit; readiness not established.

## Audit scope completed in this increment
Production backend source files re-read from the default branch of PRSPRO2BACKEND-PRODUCTION: DatabaseSetup.js, ProcurementService.js, AttachmentService.js, EventService.js, ProjectInformationService.js and FormService.js. This audit used source only; no live records, operational configuration, secrets, test execution or production changes.

### Evidence confirmation
- DatabaseSetup.js confirms registry headers for Projects, Workflow, EventTypes, Events, EventData, Attachments, AuditLogs, MaterialNeeds, PurchaseOrders, PurchaseOrderItems, PurchaseAllocations, MaterialReceipts, material specification/calculation, ProjectInformation/history, Forms/FormFields/EventTypeForms/EventFormData, print templates, workflow event rules and reference-sync logs. Registry/header evidence does not establish deployed sheet contents or nullability.
- ProcurementService exposes distinct create-order, single-receipt, batch-receipt, request-row calculation, needs/orders listing, workspace/report/dashboard operations. This corroborates separate write paths requiring a unified target contract; it does not prove parity between single and batch behavior or correction/return behavior.
- The existing procurement cancel/adjustment audit (2.1) remains consistent: the inspected service has no identified dedicated order cancellation or receipt-return/adjustment operation. Cancellation is observed as a state check in receipt validation, not proof of a complete cancellation lifecycle.
- AttachmentService exposes upload, download, thumbnail, list/by-entity, project-image/gallery/count, copy-project-image and delete operations. This establishes lifecycle breadth but not exhaustive finite owner-type coverage, storage-provider migration contract, scanning/retention, orphan recovery or authorization matrix.
- EventService source has create, void, workflow validation/progress, project-detail/list, event data enrichments and field/template update pathways. The audit must still systematically map payload mutability, snapshot authority, amendment/void and attachment interactions across all paths.
- ProjectInformationService and FormService are present and reviewed at file level in this increment; this is not yet a complete write-path/field mutation map.

## Readiness gap register (initial prioritized state)
| ID | Priority | Gap / question | Evidence status | Next evidence or decision |
|---|---|---|---|---|
| G-01 | BLOCKER | Exhaustive table/entity-to-write-path coverage and mutation matrix | Partial registry + selected services | Trace all repositories/services/routes and classify create/update/delete/archive |
| G-02 | BLOCKER | Procurement cancellation, receipt correction/return, reversal, replay and quantity reconciliation contract | Partial source evidence; no standalone cancellation/adjustment method identified in inspected service | Trace all receipt/order pathways and document PO business decision |
| G-03 | BLOCKER | Event/form response payload structure, query/report/search/filter expectations, immutable snapshot and amendment model | DataJSON/ResponseJSON evidence; behavior partially reviewed | Cross-map payload schema, mutation paths, reporting use and performance workload |
| G-04 | BLOCKER | Attachment owner types and relationship integrity | PROJECT/EVENT evidence and generic EntityType/EntityID observed; finite inventory incomplete | Trace all callers and list owner types, tenant inheritance, visibility, lifecycle |
| G-05 | HIGH | Identifier strategy and legacy mapping | IDs generated/consumed in source; formats and migration scope incomplete | Enumerate entity ID generation, reference/copy IDs, tenant scope, external references |
| G-06 | HIGH | Decimal precision, quantity/unit, currency and rounding semantics | Material/procurement quantities present; exhaustive definitions unknown | Trace validators/calculations and units, bounds, rounding, conversion |
| G-07 | HIGH | Timestamp semantics, timezone and Jalali display policy | Tenant timezone configured (Asia/Tehran default); occurrence/create times exist | Trace date conversion, user input/output and storage representation; PO policy |
| G-08 | HIGH | Identity, password and tenant-membership migration | User/role/permission structures source-visible; migration semantics unresolved | Trace login/password hashing, membership and session/token handling; migration decision |
| G-09 | HIGH | Deletion, retention, archive, recovery and purge policy | Project deletion dependencies and attachment trash partial paths audited | Map all entity lifecycle and storage recovery windows; PO retention decisions |
| G-10 | HIGH | Tenant-safe constraints on indirect links and polymorphic entities | Logical relations source mapped; integrity matrix not exhaustive | Trace all indirect paths and confirm FK/unique candidates |
| G-11 | MEDIUM | File storage-provider migration, checksum, size limits, scanning and orphan reconciliation | Google Drive provider and metadata paths observed | Detail source validation, storage operation failure/retry, access policy |
| G-12 | MEDIUM | Configuration/catalog scope and revision semantics | Workflows/forms/event types/information types versioned in places | Establish global vs tenant-local catalogs and mutable/published revision contract |

## Prioritized continuation
1. Continue route/service/repository mutation tracing to close G-01 and G-10 (read-only).
2. Trace procurement receipt/order pathways end-to-end and enumerate all fields/state/replay and dependency edges (G-02).
3. Trace event payload, form response, amendments, voids, reports and attachments together (G-03/G-04).
4. Subsequently close identifier, precision/timezone, identity and retention policy gaps (G-05–G-09), recording decisions separately from source facts.

## Guardrails and limitations
No executable SQL/DDL, migration code, database provisioning, live production data access, production writes, deployment, VPS action or tests were performed. This increment is evidence progress only; it does not declare schema/migration readiness or Task 2.5 completion.

## Increment 2 — mutation-path and tenant-boundary sampling (2026-11-22)
Read-only source sampling expanded to Router.js, ProjectService.js, ProjectRepository.js, UserService.js, UserRepository.js, RolePermissionService.js, WorkflowService.js, WorkflowRepository.js, WorkflowStepDepartmentService.js, EventTypeService.js, InformationTypeService.js, ProjectDeletionService.js, StorageRepository.js, AttachmentRepository.js, EventRepository.js and EventDataService.js.

### Observed evidence (bounded sample; not exhaustive)
- Router.js is the dispatch/authorization layer and contains specific routes for project-information deletion, draft-project checks and permission-gated management routes. Route-level authorization and service/repository tenant constraints are separate evidence layers; both must be audited for target integrity.
- ProjectService source includes create, update, draft/project-code flows and reference-image/information copy behavior. Several lookup operations explicitly filter departments by TenantID; project update paths validate the project tenant and code scope. Coverage of every nested dependency remains open.
- UserService list/get/create/update flows are tenant scoped; update payload explicitly strips TenantID to prevent changing tenant ownership. User role/membership and credential migration remain distinct.
- WorkflowService filters workflow and department lookups by tenant and checks workflow tenant ownership in step operations. This sample does not establish that all workflow-associated records have tenant-safe relational constraints.
- EventTypeService validates department and project-status references against tenant scope and validates amendment form references against tenant-owned active forms. The candidate target constraints need to encode these relationships, not only rely on application validation.
- InformationTypeService has tenant-aware type lookup and create/update/deactivate and column operations. Revision/history and form-cache invalidation behavior are application-level concerns requiring explicit target design.
- ProjectDeletionService uses tenant-scoped project context and dependency collection; dependency inventory/recovery and partial failure handling require a transactional V2 redesign. Legacy soft-delete/purge semantics remain not migration-ready.
- Repositories sampled are thin sheet adapters. Their methods do not themselves provide relational FK enforcement; tenant integrity is currently dependent on service validation and path consistency in the legacy architecture. This is a structural observation, not a claim about deployed data quality.

### Updated gap status
G-01 and G-10 remain BLOCKERS: the sampled paths establish a cross-cutting pattern of tenant-aware service validation but do not provide exhaustive endpoint-to-write-path, indirect-relationship and constraint coverage. No claim of completeness.
New sub-findings to carry into design:
- Router authorization, service validation and relational constraints must be evaluated as three separate layers.
- User TenantID immutability is explicit in sampled update code, but transfer/disable/reassignment and identity migration policy remain open.
- Project deletion currently depends on multi-entity collection and partial-failure handling; V2 should require transactionality and explicit recovery/retention policy.
- Thin repository adapters mean application-only relationship validation cannot be treated as sufficient FK evidence for V2.

No executable schema, SQL, migrations, database setup, live data access, tests, production changes or deployment performed.

## Increment 3 — Procurement, material calculation, event integration and attachment owner evidence (2026-11-22)
Read-only method/body sampling was extended across ProcurementService, MaterialPlanningService, ProcurementEventIntegrationService, AttachmentService and ProjectDeletionService.

### Evidence
- Order creation includes an optional IdempotencyKey replay check scoped to the current tenant before processing items; this must be considered in V2 idempotency design, including uniqueness scope and replay response.
- Single receipt creation checks replay using SourceEventID and PurchaseOrderItemID for non-void receipts in the inspected path; tenant and item/order validation are present.
- Batch receipt processing has a distinct locked pathway, tenant-scoped receipt inventory and item/order/allocation quantity maps. The source evidence supports separate pathway semantics; parity and transactional guarantees require detailed full-path review.
- Material planning recalculation validates the source event tenant/project association, selects active latest specification revisions and stores calculation provenance. Source-event void handling updates calculations and affected needs to a voided-source state. This requires append/history and correction semantics in V2.
- Procurement event integration supports a configuration action for recalculation of confirmed needs, validates configured information type and source-event linkage, and calls material recalculation. Event-to-material calculation is an indirect relationship that must be modeled and tenant-constrained.
- Attachment visibility in the inspected code currently recognizes PROJECT and EVENT owners, validates tenant ownership, and applies project visibility checks. Upload rejects other owner types in the inspected pathway. This is a concrete observed owner-type set for the sampled code, but it is not necessarily exhaustive of all attachment callers or historical data.
- Project deletion dependency collection preserves general/shared procurement orders and order items where other-project allocations remain. This relationship model should be explicit in V2; project deletion must not imply deleting shared purchasing records.
- Attachment deletion calls storage deletion and then updates metadata status in the inspected path; this ordering presents a failure-recovery/consistency question for V2 (storage and DB operation atomicity cannot be assumed).

### Updated gap assessment
- G-02 remains BLOCKER: receipt correction/return, order cancellation, adjustment, state transitions, quantity reconciliation and idempotency scopes are not fully specified.
- G-04 reduced from unknown owner set to observed PROJECT/EVENT in sampled upload/visibility paths; exhaustive caller and historical owner inventory still pending.
- G-03 remains BLOCKER: Event/Form payload and revision/snapshot/reporting behavior remains to be mapped.
- G-09 and G-11 remain HIGH: storage failure recovery, retention, orphan cleanup and deleted-file lifecycle need explicit policy.
- General/shared order preservation and allocation linkage are source-supported and must be represented explicitly.

No executable schema, SQL, migration, database provisioning, live data, tests, production changes or deployment occurred.

## Increment 4 — Event/Form payload, snapshots, amendments, voids and central-information history (2026-11-22)
Read-only source sampling across EventService, EventDataService, FormService, EventTypeService, ProjectInformationService, and associated repositories.

### Evidence-backed observations
- Event creation includes request idempotency handling scoped in the inspected path by tenant and user, including an explicit PROCESSING/recovery guard. This is a distinct contract from procurement idempotency.
- Event void is a dedicated pathway with tenant/project visibility checks and event-type permission/configuration validation. The downstream consequences across all related data still require a complete dependency map.
- Form responses are stored separately from generic central project-information values. In the inspected FormService, response persistence stores ResponseJSON and DefinitionSnapshotJSON, with form version and form definition snapshot. This supports historical event context and must not be collapsed into a live-form-only model.
- Form response validation checks mappings, active forms, submitted form versions, requiredness, uniqueness of submitted FormIDs, allowed information types and values; there is a bounded cap of 30 forms per event in the inspected path.
- Form responses may also update current central project information, but empty/omitted values are not treated as erasing previously saved values in the observed path. The central projection and event snapshot are distinct persistence concepts.
- Event form response amendment uses an AmendmentEventID tied to the same project and validates amendment event type configuration/allowed forms. The source contains a legacy marker heuristic alongside explicit configuration; V2 design should not rely on name-based markers as the primary authorization contract.
- EventType storage normalizes configured array/boolean fields; amendable-form references are validated against active tenant forms and project-connection constraints.
- Project information has separate history and approval records, revision numbers and conflict resolution for live reference synchronization. This history is distinct from event response snapshots and audit log records.
- Project-information save uses a script lock wrapper; in V2 concurrency must be handled at the database transaction/version level.

### Remaining gaps
G-03 remains BLOCKER: full response/value data types, field cardinality, file references, reporting/search/filter workload, snapshot compatibility, amendments/corrections and void dependency effects are not yet exhaustively traced.
G-01 remains BLOCKER: routes and all write paths still need a complete cross-reference.
G-10 remains BLOCKER: tenant integrity across form/event/project-information/approval/history/attachments and indirect relationships needs exhaustive constraint matrix.
G-12 remains open: full configuration/form/field revision publication and compatibility semantics remain to be mapped.

No executable SQL/DDL, migration, DB setup, live data, tests, production changes or deployment performed.
