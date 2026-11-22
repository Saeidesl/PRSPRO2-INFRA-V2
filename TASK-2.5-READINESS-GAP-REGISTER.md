
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

## Increment 5 — Payload shape and form-definition rules (2026-11-22)
Further read-only review of EventDataService, EventDataRepository, FormService, FormValidator and ProjectInformationRepository.

### Evidence
- Legacy EventData is read-only through the sampled EventDataService and parses DataJSON as a JSON object; malformed JSON and non-object/array payloads are rejected. Its repository stores a single lookup by EventID. This does not prove the legacy payload's full field inventory or complete historic consistency.
- Reusable forms have tenant-scoped form definitions and fields referencing InformationType records; definition assembly includes active columns and visibility rules. Disabled/inactive type handling and active status are checked in the form-definition pathway.
- Form field definition supports ordered fields, group/label/help overrides, requiredness and visibility rules referencing a preceding active field in the same form (with operators EMPTY/FILLED/EQUALS/NOT_EQUALS).
- Form response snapshots capture form ID/code/name/version and field definitions, information-type metadata, columns, visibility and requiredness. Thus the snapshot is more than just the response values and may increase payload/storage cost; size and query strategy require workload evidence.
- Information-type cardinality defaults to SINGLE in observed helper logic, supports MULTIPLE, and field empty-value shape depends on active columns and cardinality (array, object or scalar). The full cross-product of information type data types, columns, cardinality and validation still needs inventory.
- ProjectInformationRepository maintains separate value, history and sync-log repositories; history retrieval is revision ordered. These relationships need explicit normalized constraints and unique/revision rules in V2.
- The legacy EventData pathway and reusable Form responses are separate; migration discovery must preserve both, identify records with legacy DataJSON, and not assume every historical event has reusable-form responses.

### Gap implications
G-03 remains BLOCKER: complete InformationType data-type/cardinality/column semantics, payload field inventory, reporting/query requirements, attachment references and historical data shape are not yet fully enumerated. Snapshot size, retention and query indexing need evidence-based design.
G-01/G-10 remain BLOCKERS: repository and service relationship coverage and tenant-safe constraint matrix are incomplete.
G-12 remains open: configuration/form/field revision compatibility and publishing semantics are not fully evidenced.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment occurred.


## Increment 6 — Information-type/column validation and report query evidence (2026-11-22)
Read-only review of InformationTypeValidator, InformationTypeService, AttachmentValidator and ReportService/ReportRepository, plus a focused test fixture for cardinality.

### Evidence
- Source validator lists nine data types: text, number, date, datetime, boolean, select, textarea, image and file; cardinality is SINGLE/MULTIPLE.
- Information type definition validation includes code/name, cardinality, data type, options JSON (array capped at 500 in the inspected validator), validation-rule JSON and ShowOnCard; PROJECT_CODE is constrained to text/SINGLE.
- Validation-rule keys observed include min/max/minLength/maxLength and pattern (length bounded and regex checked). This is partial evidence, not a full semantic contract for every type.
- Column definitions include name/code/data type, requiredness, options, validation rules, ordering and status. Column type allow-list matches the observed base type list; options array limit and rule checks apply.
- Type update increments a revision field and blocks structural DataType/Cardinality change if already used in project information or active form fields. Deactivation is also restricted when referenced in active forms. This indicates structural immutability/change policy in the source, but complete revision lifecycle and compatibility contract remain unresolved.
- Information type columns have their own create/update/soft-delete pathways and trigger form-definition cache invalidation. Column deletion is logical (DELETED) in the sampled method.
- Attachment validation in the sampled upload contract limits EntityType to PROJECT/EVENT, validates MIME+extension allowlist, filename and size bounds, and permits AttachmentType only for PROJECT with a constrained token. It shows a finite upload-owner contract for this validator, not a complete historical inventory.
- ReportService project report includes project data, non-void/cancelled events, and project-associated procurement needs/orders/calculations; report dashboard aggregates tenant-visible projects, events (including independent events), attachments and other reference datasets. These paths indicate distinct reporting query/aggregation needs, not necessarily suitable direct table layouts.
- The existing test fixture tests/information-cardinality.test.js uses column-based SINGLE and MULTIPLE examples and project-information storage, supporting that tabular cardinality has functional test coverage in the legacy repository. No test was executed in this audit.

### Gap implications
G-03 remains BLOCKER pending full field-value validation matrix, all reports/search/filter consumers, payload/file association inventory, and snapshot lifecycle.
G-04 remains BLOCKER pending exhaustive historical attachment-owner and reference inventory, though sampled upload validator supports PROJECT/EVENT.
G-06 remains HIGH pending quantitative precision, unit and rounding contract.
G-10 remains BLOCKER pending relational constraint matrix across these indirect references.
G-12 remains OPEN pending comprehensive configuration/field/column revision and lifecycle rules.

No tests were run; no SQL/DDL, migration, DB provisioning, live data, production changes or deployment occurred.


## Increment 7 — Identifier strategy, tenant key evidence and relationship constraint sampling (2026-11-22)

### Evidence (bounded, source-only)
- The legacy ID convention is mixed by domain: Constants.js defines prefix tokens for core entities, while ProcurementService generates UUID-derived short identifiers for procurement records and material item codes. This does not establish a universal, collision-proof or externally stable identifier contract.
- Session tokens are generated from UUIDs, stored in Script Properties and session records carry user ID and tenant ID. This is session-storage behavior, not proof of identity migration strategy or DB uniqueness constraints.
- Authentication lookup allows usernames to repeat across tenants but the sampled login path fails closed unless username resolves to exactly one user. This implies tenant selection/login disambiguation needs an explicit V2 contract rather than assuming username uniqueness globally or tenant-locally.
- DatabaseSetup registry shows tenant columns on many operational records and association tables (e.g., UserRoles, RolePermissions, Projects, Workflows and WorkflowSteps), but sheet header presence does not establish relational enforcement.
- Sampled ProjectValidator validates workflow ID and user/department arrays structurally; EventValidator permits nullable ProjectID but requires EventTypeID and PerformedBy. These validators alone do not prove same-tenant integrity for all indirect relationships.
- Project deletion collects tenant-scoped events and associated event data, attachments, needs, purchase orders/items, allocations, receipts, specifications and calculations; preservation of shared purchasing records with other allocations is a critical relationship rule already evidenced in earlier increment. Deletion dependency collection remains application-side and is not equivalent to database FK/transaction guarantees.
- Access checks and tenant filtering exist in sampled service pathways, but the exhaustive cross-domain tenant constraint matrix (including association tables, references through event/type/form/workflow/department/user and shared procurement) is still not complete.

### Gap implications / recommendations
- G-05 remains HIGH: ID generation and legacy-to-V2 mapping should be domain-by-domain, with a retained legacy ID/source mapping record and uniqueness policy; UUID-based V2 identity may be considered but requires a design decision and data reconciliation evidence.
- G-10 remains BLOCKER: create an exhaustive relationship graph and tenant-safe FK/unique/check constraint matrix, including indirect and shared entities. DB constraints should enforce tenant alignment where relational design permits; application authorization remains separate.
- G-01 remains BLOCKER: mutation paths, lifecycle dependencies and delete/restore/reversal paths need coverage for all registered entities, not only sampled services.
- G-08 remains HIGH: authentication lookup and session observations do not close user, password, tenant membership, role and permission migration semantics.

No SQL/DDL, migration, DB provisioning, live data access, test execution, production changes or deployment occurred.


## Increment 8 — Quantity, unit, numeric precision and temporal evidence (2026-11-22)

### Evidence (bounded, source-only)
- Project creation validation requires supplied project Quantity to be a positive integer; this is not evidence that all material/procurement quantities are integer-valued.
- Procurement and material-planning code use JavaScript numeric conversion/validation (`Number`, finite/non-negative checks) in sampled paths. This does not establish domain-specific precision, decimal scale, rounding, unit conversion or currency rules.
- The registry and sampled services contain quantity/unit-related fields and material specification/calculation concepts, but the audit has not exhaustively traced all unit-bearing fields, conversion sources, rounding stages or historical calculated values.
- Event validation accepts OccurredAt when parseable by JavaScript Date and EventService contains occurrence/creation time handling; sampled session timestamps are ISO strings. This bounded evidence does not establish a complete timezone contract, UTC/local storage policy, daylight-saving behavior, Jalali display rules or historical timezone normalization.

### Gap implications
- G-06 remains HIGH: define precision/scale per quantity/cost/rate domain, rounding points, unit catalog and conversion policy, and how legacy numeric values are reconciled without loss.
- G-07 remains HIGH: define UTC storage, explicit timezone metadata where required, timezone conversion/display, Jalali calendar presentation only at UI boundary, and import/export rules. These are recommendations requiring evidence/decision, not observed contracts.
- G-01 remains BLOCKER: all quantity and timestamp field usages across write/read/report/export paths need a complete inventory.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment occurred.


## Increment 9 — Identity, audit, deletion, retention and file recovery evidence (2026-11-22)

### Evidence (bounded, source-only)
- UserService supports tenant-scoped user list/update flows in sampled code, status-based user lifecycle and audit logging; update strips TenantID from accepted update data. This does not close identity migration, account merge, password reset/rehash or membership policy.
- UserRepository exposes physical deleteById capability in its repository adapter. This demonstrates a deletion operation exists, but its complete caller coverage, permissions, cascade behavior and use in production lifecycle were not exhaustively verified.
- RolePermissionService exposes bulk-delete behavior for role-permission association records. This is evidence of relationship mutation, not a confirmed retention policy.
- AuditService requires TenantID, Action, EntityType and EntityID and captures optional UserID and serialized old/new data. Audit querying is by entity/user in sampled repository. Retention, tamper resistance, completeness of audit coverage and audit redaction rules remain unresolved.
- ProjectDeletionService describes dependency-aware soft delete and trashes Drive attachments; sampled behavior includes dependency collection, preservation of shared procurement items/orders and project-scoped associations. Recovery/restore, durable tombstones, failure compensation and transactional consistency are not established.
- GoogleDriveStorage.delete sets the file to trash; sampled attachment deletion path has storage and metadata operations that may fail separately. Reconciliation, restore from trash, permanent purge and orphan cleanup contracts remain unresolved.
- Attachment/storage metadata and Tenant folder paths exist in sampled source, but a full inventory of file lifecycle states and all delete/copy/restore callers is still pending.

### Gap implications
- G-08 remains HIGH: establish identity mapping, login identifier normalization/uniqueness, password handling migration, disabled/inactive accounts, membership and role/permission reconciliation, and recovery procedure.
- G-09 remains HIGH: specify logical/physical deletion, tombstone, restore, purge, retention/archival, audit retention and recovery/rollback boundaries for all domains.
- G-11 remains HIGH: define file metadata contract, storage key migration, checksum, orphan/metadata reconciliation, restore, purge and retry/compensation.
- G-01 remains BLOCKER: full caller/lifecycle inventory for delete/update/restore/reversal is still required.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment occurred.


## Increment 10 — Configuration, workflow, project status, template and relationship graph sampling (2026-11-22)

### Evidence (bounded, source-only)
- WorkflowService and WorkflowStepDepartmentService provide tenant-scoped workflow and active step-department associations; workflow management bootstrap also combines tenant workflows, departments and project-connected active event types. Cross-entity alignment and relational constraint enforcement remain unproven.
- DepartmentService tenant-filters department access. Department lifecycle/cascade effects on workflow steps, project assignments and historical event data require full inventory.
- ProjectStatusService implements tenant context and permission-gated management; ProjectStatusSetup is additive setup evidence, not migration behavior. Project-status history/transition rules and foreign-key impacts remain unresolved.
- PrintTemplateService/repository implement tenant-scoped reusable templates, versioned definitions, status and storage metadata for PDF/assets. Template version immutability, asset migration, historic rendered-output retention and associations with project/event data need further lifecycle evidence.
- EventFieldRepository is sampled as read-only for historical dynamic event-field labeling. This implies legacy field-definition context may be needed for historical EventData interpretation; complete EventFields inventory and payload decoding remain pending.
- Repository/service boundaries and registry structures are not sufficient evidence of database-level FK, unique, check or tenant alignment constraints; association and configuration entities must be included in the full dependency matrix.

### Dependency graph additions (observed or candidate relationships, not schema declarations)
- Workflow -> WorkflowStepDepartment -> Department and workflow-linked EventType associations.
- Project -> configured ProjectStatus (status lifecycle/history contract pending).
- PrintTemplate -> versioned DefinitionJSON and storage-backed PDF/image asset metadata (tenant scope observed).
- Historical EventData -> legacy EventField definition context (read-only sampled).

### Gap implications
- G-01 remains BLOCKER: complete lifecycle/mutation/read/report consumer coverage is still incomplete.
- G-10 remains BLOCKER: relationship/tenant alignment matrix and constraint evidence are not exhaustive.
- G-12 remains OPEN/HIGH pending full revision, scope and dependency lifecycle review across configuration entities.
- G-03 and G-11 remain unresolved regarding historical payload interpretation and template/file asset lifecycle.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment occurred.


## Increment 11 — Registry-based entity inventory and coverage baseline (2026-11-22)

### Evidence source
Read-only inspection of the production backend `DatabaseSetup.js` APP_CONFIG.SHEETS registry (definitions and header arrays only; no live spreadsheet or data access). The registry enumerates 38 configuration keys/sheet definitions, including separate entries `MATERIAL_NEEDS` and `PROJECT_STATUSES` both mapped to the `18_` prefix, and supplemental domain tables. Registry names are a baseline, not proof of active deployment, completeness of actual data, relationships, or constraints.

### Registry inventory baseline (grouped)
- Identity/access: TENANTS, USERS, DEPARTMENTS, ROLES, PERMISSIONS, USER_ROLES, ROLE_PERMISSIONS.
- Projects/workflows: PROJECTS, WORKFLOWS, WORKFLOW_STEPS, WORKFLOW_STEP_DEPARTMENTS, WORKFLOW_STEP_EVENT_RULES, PROJECT_STATUSES, PROJECT_REFERENCE_SYNC_LOG.
- Events/forms/configuration: EVENT_TYPES, EVENT_FIELDS, EVENTS, EVENT_DATA, EVENT_TYPE_FORMS, EVENT_FORM_DATA, FORMS, FORM_FIELDS, INFORMATION_TYPES, INFORMATION_TYPE_COLUMNS, PROJECT_INFORMATION, PROJECT_INFORMATION_HISTORY.
- Materials/procurement: MATERIAL_NEEDS, PROJECT_MATERIAL_SPECS, MATERIAL_NEED_CALCULATIONS, PURCHASE_ORDERS, PURCHASE_ORDER_ITEMS, PURCHASE_ALLOCATIONS, MATERIAL_RECEIPTS, EVENT_TYPE_PROCUREMENT_SETTINGS.
- Files/templates/operations: ATTACHMENTS, AUDIT_LOGS, PRINT_TEMPLATES.

### Coverage interpretation
- Registry inventory was extracted and grouped, but exhaustive service/router/repository mutation and read/report consumer coverage per entity remains pending; therefore this is a coverage baseline, not a complete CRUD/dependency matrix.
- Some domain entries may have distinct creation/setup/seed paths, logical deletion or lifecycle states, and may not be reachable from every public route. Those distinctions require individual source tracing.
- An observed naming anomaly/overlapping sheet prefix (`MATERIAL_NEEDS` and `PROJECT_STATUSES` both reference `18_...`) is recorded as a registry observation to validate against actual deployed setup behavior and source history; it is not treated as a production defect or schema decision.

### Gap implications
- G-01 remains BLOCKER until all inventory entries are mapped to confirmed lifecycle/read/report/caller paths and coverage gaps are explicitly resolved or accepted.
- G-10 remains BLOCKER until all direct/indirect links and tenant-safe constraint candidates are traced.
- G-12 remains OPEN/HIGH for configuration scope, revision, lifecycle and dependency evidence.

No live data, SQL/DDL, migration, DB provisioning, tests, production changes or deployment occurred.


## Increment 12 — Partial entity-to-service/repository lifecycle coverage (2026-11-22)

### Read-only source evidence sampled
- User-role assignment: UserRoleService exposes tenant-validated listing and assignment pathways; UserRoleRepository is a separate persistence adapter. Coverage of unassignment/reassignment route callers and lifecycle invariants remains partial.
- Role: RoleService has tenant-scoped listing/get and create path evidence; full route, update/deactivate/delete and association dependency coverage remains unverified in this increment.
- Permission catalog: PermissionService read/list/get-by-code paths and PermissionRepository read adapter observed. Catalog write lifecycle appears separate/limited in sampled service; provisioning/seed and global-vs-tenant scope need verification.
- Role-permission association: RolePermissionService and repository sampled in prior increments; repository contains bulk association operations. Transactional atomicity and full tenant alignment constraints are not established.
- Workflow step event rules: repository includes schema ensure, version-scoped lookup and clone-version operation; validator checks rule arrays and bounded enum-like values. Tenant ownership, rule deletion/restore, published-version immutability, event-type alignment and all router callers still need complete tracing.
- Workflow step-department association: repository has insert/insertMany and lookup paths; complete update/deactivate/delete/cascade mapping remains pending.
- Project Status: service-level permission/context and sheet lifecycle operations sampled; history/transition consumers, deletion impact and concurrency remain unresolved.
- Reports and Telegram are consumers across multiple domains; this sampling confirms they exist but does not inventory all entities read, report projections or notification triggers.

### Coverage status for this increment
- CONFIRMED (bounded): selected tenant-aware service/repository existence and selected read/association/versioning operations.
- PARTIAL: lifecycle and API route coverage for user-role, role-permission, role, permission, workflow rules, step-department and project-status entities.
- UNVERIFIED: exhaustive operation matrix, dependency constraints, atomicity, indirect tenant links, historical retention and consumer projections.

G-01 and G-10 remain BLOCKERS; G-12 remains OPEN/HIGH. This increment does not claim exhaustive CRUD coverage.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment occurred.


## Increment 13 — Router/API reachability evidence (read-only, 2026-11-22)

Router.js sampled directly in the read-only production-reference repository:
- `/users/{id}/roles` path is permission-gated with USER_EDIT and dispatches to UserRoleService.listByUser in the inspected branch. Assignment endpoint/mutation route completeness not established from this limited trace.
- `/roles/available-permissions` requires ROLE_MANAGE and dispatches PermissionService.list.
- `/roles` list is gated by ROLE_MANAGE or USER_EDIT and dispatches RoleService.list.
- `/roles/{...}` route family exists, but complete operation/permission mapping requires further branch tracing.
- `/workflow-management/bootstrap` requires WORKFLOW_MANAGE and dispatches WorkflowService.managementBootstrap.
- `/workflows` has a requireAny gate (WORKFLOW_MANAGE, PROJECT_CREATE, SYSTEM_SETTINGS) and dispatches WorkflowService.list.
- `/project-statuses` list/create and `/project-statuses/{id}` update are observed under PROJECT_STATUS_MANAGE; full deletion and route semantics not established.
- `/auth/me/permissions` returns permission codes, with admin and user-permission branches.

Coverage: CONFIRMED for the specific route/permission/dispatch snippets above; PARTIAL for endpoint families and mutation lifecycle; UNVERIFIED for exhaustive route coverage, indirect tenant enforcement, transaction/constraint semantics, delete/restore, and caller graph. G-01 and G-10 remain blockers; G-12 remains open/high.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


## Increment 14 — Procurement Lifecycle Evidence Audit (bounded read-only, 2026-11-22)

### Source inspected
ProcurementService.js, ProcurementRepository.js, MaterialPlanningService.js, ProcurementEventIntegrationService.js. Repository is a tenant-filtered sheet adapter for NEEDS, ORDERS, ITEMS, ALLOCATIONS and RECEIPTS; this is not DB-enforced integrity.

### Evidence findings
- Order creation: script lock; tenant context; optional IdempotencyKey replay lookup; item list required; existing active allocations considered when calculating need ordering; status exclusions include CANCELLED/VOID/DELETED/NEEDS_REVIEW; per-item allocations are normalized and inserted alongside order/items. Audit entry observed.
- Allocation: records connect purchase-order item to project and optional material need; quantities validated and tenant-scoped project/need checks in inspected create path. General orders/items can exist without one project, with allocations potentially linking multiple projects/needs. Constraints on duplicate allocations, atomic write failures and FK integrity remain unproven.
- Single receipt: script lock and IdempotencyKey replay; SourceEventID + item replay check excluding VOID receipts; project allocation checks and remaining allocation quantity validation; receipt includes item, project, quantity, ReceivedAt, warehouse department, SourceEventID, notes, received-by and key. Writes receipt and recalculates order status from receipt totals. Write + status update are separate sheet operations, consistency gap possible.
- Batch receipt: script lock; validates entries; replay by receipt IdempotencyKey, duplicate keys within request rejected; checks item/order/allocations and project-specific received/planned amounts; insertMany receipt rows and order status recalculation. Not proven transactionally atomic with Sheets.
- Quantity aggregation: receipt totals are summed by item and project; order status can become ACTIVE, PARTIALLY_RECEIVED or RECEIVED, with NEEDS_REVIEW protected from status overwrite in inspected code. VOID/cancelled receipt handling in aggregate/report consumers needs exhaustive verification; a VOID marker exclusion in one replay path is not a full reversal contract.
- Cancellation/reversal: no confirmed dedicated purchase-order cancellation, receipt correction/reversal or receipt-void workflow in this increment. Status exclusions are evidence of filtering, not proof of operation semantics.
- Event integration: receipt APIs appear independent from event integration; SourceEventID validation/linkage exists. Procurement event integration and material calculations remain adjacent pathways and need contract alignment.

### Readiness conclusions
CONFIRMED (bounded): order and single/batch receipt creation, replay/idempotency checks, allocation linkage, quantity/status calculation pathways and audit writes.
PARTIAL: multi-project allocations, general orders, replay behavior, batch/single parity and tenant validation.
UNVERIFIED/BLOCKER: correction/reversal/void semantics, cancellation operations, transactional atomicity, duplicate allocation contract, precision/rounding, full historical receipts inventory, concurrency beyond script lock, reconciliation/recovery after partial writes.
G-02 remains BLOCKER. G-01/G-10 remain BLOCKERS. This is evidence only; it does not define V2 business rules.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


## Increment 15 — Attachment & File Ownership Audit (bounded read-only, 2026-11-22)

### Direct source evidence
- Attachment validator accepts EntityType PROJECT/EVENT only; EntityID required; validates filename, MIME, extension, allowed MIME list, base64 size ceiling and AttachmentType format. PROJECT image type can default to PROFILE; AttachmentType is constrained to PROJECT attachment path.
- AttachmentService upload resolves Project or Event, validates tenant and project visibility. Project-linked events additionally require parent-project visibility. Metadata records TenantID, EntityType, EntityID, FileName, MimeType, FileSize, StorageProvider=GOOGLE_DRIVE, StorageKey, UploadedBy, UploadedAt, AttachmentType (PROJECT only) and ACTIVE status.
- Attachment visibility is resolved through owner entity: PROJECT or EVENT. Event with ProjectID requires parent project access; independent event visibility pathway exists. No other owner types confirmed in inspected upload/validator path.
- AttachmentRepository list/get/update uses sheet-backed metadata; no relational FK evidence.
- StorageRepository delegates upload/get/copy/delete to GoogleDriveStorage. Provider metadata indicates GOOGLE_DRIVE; tenant folder path exists in storage service, but audited upload path was observed calling uploadBase64 (not proof of consistent tenant-folder usage across all paths).
- Delete pathway validates tenant and visibility, then deletes physical storage first and updates metadata to DELETED, then logs audit. This sequence has potential consistency gap if metadata update/audit fails after physical delete or storage deletion fails.
- ProjectDeletionService is dependency-aware and includes attachments in project-related deletion context; exact full caller/recovery semantics are not established here. Project/event owner records are polymorphic EntityType/EntityID.
- SourceAttachmentID exists for copied project images (schema column ensure path); copy image only allows PROFILE/INFORMATION project images in inspected pathway.

### Readiness assessment
CONFIRMED (bounded): PROJECT/EVENT attachment ownership in sampled upload/validation/visibility paths; storage metadata contract; MIME/extension/size validation; copy-image lineage field.
PARTIAL: historical owner inventory, all routes/callers, tenant-folder behavior, deletion cascade and file metadata status transitions.
UNVERIFIED/BLOCKER: full historic owner types, attachment-to-form or other entities, typed relational FK model, orphan detection/reconciliation, recovery after partial storage/metadata writes, checksums, malware scanning, retention, restore/purge, migration completeness and object-storage provider contract.
G-04 remains BLOCKER; G-10 tenant-safe polymorphic linkage remains BLOCKER; G-11 storage migration remains OPEN/HIGH.

No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


## Increment 16 — Event data and form-response persistence cross-check (read-only, 2026-11-22)

### Source files re-read
- `EventDataService.js` (read-only reference backend)
- `FormService.js` (read-only reference backend)

### Bounded findings
- EventDataService resolves the event, enforces tenant equality and parent-project visibility for project-linked events; independent events follow a separate visibility path. Missing stored event data returns null-success. Stored DataJSON is parsed; malformed JSON or non-object/array payloads are rejected. The observed read path is event-ID scoped and does not itself demonstrate broader payload query/search/filter capabilities.
- FormService sampled persistence stores EventID, FormID, FormVersion, serialized ResponseJSON, serialized DefinitionSnapshotJSON, creator and timestamp in a form-response record; the inspected write path updates an existing record when found, otherwise inserts. Thus this bounded evidence shows snapshot-bearing response persistence but does not establish immutable response history, amendment/version retention, full event/form payload query indexes, complete report/search requirements, or transactional synchronization with central project information.
- A bounded amendment validation snippet links the amendment event to the same project and recognizes explicit configuration plus legacy name-marker fallback; the V2 design should not treat event-name heuristics as the authoritative contract.

### Gap implications
- G-03 remains BLOCKER: complete payload/query/report/search workload, response revision and historical immutability contract, event/form lifecycle matrix and amendment behavior are not established.
- G-01/G-10 remain BLOCKERS: this cross-check does not provide exhaustive mutation or tenant-safe relational coverage.

No tests were executed. No SQL/DDL, migration, DB provisioning, live production data, production writes, deployment or VPS action occurred.


## Increment 17 — Project mutation and route-dispatch cross-check (read-only, 2026-11-22)

### Reference files re-read
- Production backend Router.js
- ProjectService.js
- ProjectDeletionService.js
- ProjectInformationService.js
- EventService.js
- FormService.js

### Bounded route evidence
- POST /projects is routed through PROJECT_CREATE to ProjectService.create.
- Project update route dispatches to ProjectService.update; the inspected branch applies PROJECT_EDIT, with a draft/creator exception and PROJECT_CREATE fallback behavior in the relevant branch (route semantics are not exhaustive from this snippet).
- Project deletion preview and deletion dispatch to ProjectDeletionService.preview/remove with admin-only checks in the inspected Router branches.
- Project information record removal/deactivation dispatches to ProjectInformationService.deactivate under PROJECT_EDIT.
- POST /events is routed under EVENT_CREATE to EventService.create; event void is routed under EVENT_EDIT to EventService.void.
- Event form-response PUT route dispatches to FormService.updateEventFormResponse under EVENT_EDIT.
- Form create/update and form-field update/removal routes are permission-gated under SYSTEM_SETTINGS in the sampled Router branches.

### Interpretation and remaining coverage
This cross-check confirms selected API-to-service mutation reachability and permission gates only. It does not establish a complete route matrix, every nested write, repository operation, indirect tenant-bound constraint, transaction/recovery behavior, nor all create/update/delete/archive/reversal pathways. These observations must not be treated as a security certification or relational schema declarations.

G-01 and G-10 remain BLOCKERS. The next bounded increment should continue systematic route-to-service mapping and repository-level mutation/dependency coverage across the remaining registry domains, with evidence tiers and unresolved items retained.

No live data, tests, SQL/DDL, migration, DB provisioning, production changes, deployment or VPS action occurred.


## Increment 18 — Configuration/workflow domain mutation and route cross-check (read-only, 2026-11-22)

### Reference files sampled
Router.js, WorkflowService.js, WorkflowStepDepartmentService.js, WorkflowStepDepartmentRepository.js, WorkflowRepository.js, WorkflowStepEventRuleRepository.js, EventTypeService.js, EventTypeRepository.js, DepartmentService.js, DepartmentRepository.js, ProjectStatusService.js, InformationTypeService.js and InformationTypeRepository.js.

### Bounded operation inventory
- WorkflowService exposes management bootstrap/data, list/get, create/update, step operations, step event-rule retrieval/save and publish. WorkflowStepDepartmentService exposes listing, assign/assignMany and remove. Repositories provide insert/update, association lookups and workflow-version rule cloning/replacement.
- EventTypeService exposes list/get/create/update and validates references including department, project status and amendment forms in sampled code; EventTypeRepository has insert/update/delete operations.
- DepartmentService exposes list/get/create/update; DepartmentRepository exposes tenant-oriented lookup and insert/update.
- ProjectStatusService exposes list/create/update in the sampled service. A dedicated repository file was not located under the guessed filename; the service appears to access sheet-level operations directly in sampled code.
- InformationTypeService exposes list/get/create/update/deactivate and column list/create/update/deactivate operations; InformationTypeRepository contains type and column persistence methods.
- Router samples confirm selected read and mutation gates: workflow management routes are gated by WORKFLOW_MANAGE (read list also permits PROJECT_CREATE/SYSTEM_SETTINGS); department create/update gates use DEPARTMENT_MANAGE; event type creation/update gates use EVENT_TYPE_MANAGE; information type creation and column operations use SYSTEM_SETTINGS in inspected branches; project status listing/create use PROJECT_STATUS_MANAGE.

### Evidence limitations and gap impact
This is a bounded method/route sample, not a full CRUD/consumer matrix. It does not establish all deletion/deactivation paths, route conditions, repository callers, published workflow immutability, cross-tenant constraint enforcement, association cascade/restore, transaction semantics, historical consumers or database constraints. Repository file names not found are not treated as proof that operations are absent.
- G-01 remains BLOCKER: full registry-to-route/service/repository/read/report lifecycle mapping is not complete.
- G-10 remains BLOCKER: indirect tenant-alignment and relational-constraint matrix remains incomplete.
- G-12 remains OPEN/HIGH: configuration scope, revision/publish/deactivate lifecycle and historical consumer contracts remain unresolved.

No live data, SQL/DDL, migration, database provisioning, tests, production modification, deployment or VPS action occurred.


## Increment 19 — Identity, role-assignment, audit, report and print-template cross-check (read-only, 2026-11-22)

### Reference files sampled
UserService.js, UserRoleService.js, RoleService.js, RolePermissionService.js (the fetched file contains repository-level implementation in this source snapshot), AuditService.js, ReportService.js, PrintTemplateService.js, PrintTemplateRepository.js and selected Router.js dispatch branches.

### Bounded evidence
- UserService list/get/create/update: tenant-filtered listing and per-user tenant checks; create validates department tenant, generates ID, hashes submitted password, and logs sanitized user data. Update validates tenant and department, prevents TenantID mutation, hashes changed password and logs sanitized snapshots. Username duplicate checking is global in the sampled paths, consistent with a login username uniqueness requirement, but exact database constraint and concurrency guarantee remain unverified.
- User-role assignment/removal: validates target user and tenant; assignment validates active role, role tenant and user/role tenant equality, checks duplicate, writes association and audits. Removal validates association tenant and deletes association then audits. Atomicity and race-safe uniqueness are not proven.
- Role service: tenant-scoped list/get/create/update observed; creation checks tenant-local Code duplicate, update checks duplicate and limits patch fields. Role/permission association lifecycle is sampled but full route-to-service/repository and referential integrity coverage remains incomplete.
- AuditService records tenant, actor, action, entity type/id, serialized old/new data, reason and timestamp. Read paths include tenant list, entity lookup and user lookup with tenant checks. Audit write atomicity with business mutations, immutability/tamper protection, retention and privacy/redaction policy remain unverified.
- ReportService project-report path verifies tenant and project availability, aggregates non-void/cancelled events and project-specific procurement needs/orders/calculations; dashboard reads multiple domain collections and builds tenant-visible aggregates. Report projections and historical semantics are consumers that must be captured in target design; the source audit does not establish all report variants or query performance requirements.
- PrintTemplateRepository defines template fields including tenant, code/name/status/version, DefinitionJSON, creator/updater timestamps, PDF metadata and image-assets JSON; it additively ensures sheet/header presence and supports get/insert/update. This indicates dynamic template definitions and stored PDF/image metadata, not a confirmed relational schema.
- PrintTemplateService selected operations include list/create/update/deactivate, preview, resolve/resolveMany, project-binding updates and image/PDF upload/download paths. The sampled Router branches gate list/get/source/image/PDF/preview/resolve paths with SYSTEM_SETTINGS/PROJECT_VIEW combinations and create/update/deactivate with SYSTEM_SETTINGS; updateProjectBinding dispatch is direct in Router but service-level checks were observed for project-field/central-information bindings (PROJECT_EDIT) and event-related bindings (EVENT_EDIT). Full permission/tenant path review remains pending.

### Readiness and limitations
CONFIRMED (bounded): selected identity and role-assignment validation, audit record shape/read paths, project report and dashboard aggregation inputs, print-template definition and asset metadata, and selected route dispatch/gates.
PARTIAL/UNVERIFIED: full user disable/delete/session effects; username uniqueness constraint under concurrency; role/permission uniqueness and lifecycle; atomic audit coupling; report/export consumer completeness; template revision immutability, asset ownership, tenant scope and file lifecycle; complete route authorization and relation matrix.
- G-01 remains BLOCKER (complete lifecycle and consumer matrix).
- G-10 remains BLOCKER (tenant-safe relational constraints).
- G-08 remains HIGH/open (identity migration and username/password/session policy).
- G-09 remains HIGH/open (audit/history retention and recovery).
- G-11 remains HIGH/open (PDF/image asset storage migration).
- G-12 remains HIGH/open (configuration lifecycle/versioning; template semantics included).

Source-only review. No live data, credentials, tests, SQL/DDL, migration, database provisioning, production modification, deployment or VPS action occurred.


## Increment 20 — Procurement lifecycle, project cascade deletion and attachment/storage cross-check (read-only, 2026-11-22)

### Reference files sampled
ProcurementService.js, ProcurementRepository.js, ProjectDeletionService.js, AttachmentService.js, AttachmentRepository.js and relevant route-dispatch references previously sampled.

### Bounded evidence
- Procurement data collections are tenant-scoped across material needs, purchase orders, order items, allocations and receipts (repository collection names 18_MaterialNeeds, 19_PurchaseOrders, 20_PurchaseOrderItems, 21_PurchaseAllocations, 22_MaterialReceipts).
- ProcurementService sampled methods include createOrder (script-lock wrapper and internal creation flow), createReceipt, batch receipt creation, needs/orders listing, workspace, dashboard and reports. Order creation supports an idempotency key and project/event validation helpers. Numerical validation is based on Number conversion and positive/nonnegative bounds in sampled helper; exact precision policy and atomicity across records still unresolved.
- Receipt/quantity aggregation is computed from receipt records; dashboards cap received quantity at ordered quantity while surplus and pending delivery are separately computed. This projection must not be confused with ledger/accounting semantics.
- ProjectDeletionService implements an admin-only dependency-aware cascade. Its preview collects events/event data, material needs, order items, allocations, receipts, material specs/calculations and attachments. Shared orders/items allocated to other projects are selectively preserved. Delete operations acquire a script lock, soft-delete selected children, void events, trash files after attachment record updates, then physically remove the project row when no earlier failures occur. Audit logs outcome counts and partial failures. This is not a database transaction and the physical project delete plus child processing may be partially completed on failure.
- AttachmentService supports PROJECT and EVENT entity attachments, tenant and parent-project visibility checks, upload/download/thumbnail/delete and image-gallery paths. Attachment rows carry tenant/entity type/entity ID, storage provider/key, uploader, timestamps, status and attachment type; project-image copying retains source-attachment linkage. Upload stores file before record/audit; delete removes storage before updating row/audit, so failure/compensation and transaction ordering are important target design issues. Storage observed as Google Drive in sampled paths.
- AttachmentRepository supports entity lookup and update/insert; sampled entity lookup excludes DELETED but status handling and VOID consistency need full audit.

### Readiness and limitations
- G-01 remains BLOCKER: complete mutation/consumer and deletion/restoration lifecycle matrix is still not complete.
- G-02 remains BLOCKER: procurement receipt correction/reversal, allocation and shared-order lifecycle semantics, uniqueness/idempotency concurrency and transaction design remain unresolved.
- G-04 remains BLOCKER: complete attachment ownership and relationship coverage, including orphan detection, not established.
- G-06 remains HIGH: precision, unit conversion, rounding and numerical field policy not established.
- G-09 remains HIGH: retention/recovery and project cascade restoration strategy remain unresolved.
- G-10 remains BLOCKER: tenant and FK enforcement, shared-order association semantics and constraints remain incomplete.
- G-11 remains HIGH: storage provider/key migration, compensating failure strategy and object lifecycle remain open.

Source-only inspection. No live data, test execution, SQL/DDL, migration, database provisioning, production modification, deployment or VPS action.


## Increment 21 — Event void reversal, historical snapshot and recovery semantics (read-only, 2026-11-22)

### Reference files sampled
EventService.js (void method and related helper evidence), EventRepository.js, ProjectRepository.js, ProjectService.js and Router.js dispatch excerpts.

### Bounded evidence
- Event void is a status change to VOID rather than deletion. The sampled service validates tenant and project visibility, event type and AllowVoid configuration, actor department/admin status and downstream events in other departments.
- Before voiding, the service compares expected project state changes against current state and checks a prior snapshot. When conditions match, it restores prior project step/status/available-department fields, then marks the event VOID and audits project restoration and event void.
- After void, it recalculates current-step completion based on workflow-rule progress and invokes MaterialPlanningService.markEventVoided. Material-review failure is handled as a reported/reconciliation failure and separately audited, rather than preventing the void from returning.
- Event records carry prior-state snapshot fields and idempotency processing/completion fields in sampled paths. This indicates recovery/replay and event lifecycle semantics that must be preserved, but does not prove full immutability, retry safety or completeness of snapshots.
- The sequence includes multiple separate sheet writes (project restore, event status, recalculation, audit and material review); no atomic transaction guarantee is established. Partial completion and compensation/reconciliation are significant migration requirements.
- Router exposes event void through a dispatch path; this review does not claim exhaustive route coverage.

### Findings / design implications
- Event void must remain an auditable lifecycle operation, not physical deletion.
- PostgreSQL design should preserve event's prior-state snapshot/transition provenance and model status transition rules, with transactions for database-side mutations and outbox/reconciliation handling for external or independently failing integrations.
- Material-planning changes after event void need a deterministic reconciliation/replay strategy.
- Restoration is conditional on current project state matching the event's expected changes; this protects against blind overwrites and should be preserved in target behavior.

### Readiness and limitations
- G-01 remains BLOCKER (full lifecycle/consumer coverage).
- G-03 remains BLOCKER (complete historical snapshot, form response/amendment and event transition semantics not established).
- G-02 remains BLOCKER (procurement/material-plan reversal and reconciliation semantics).
- G-09 remains HIGH/open (recovery and retention).
- G-10 remains BLOCKER (tenant-safe relationship constraints).

Read-only source review only. No live data, tests, SQL/DDL, migration, database provisioning, production modification, deployment or VPS action.


## Increment 22 — Material planning specifications, calculation ledger and void reconciliation audit (read-only, 2026-11-22)

### Source files sampled
MaterialPlanningService.js (specification create/revise, calculation recording, project recalculation, void handling), ProjectInformationService.js and repositories sampled previously for linkage context.

### Bounded evidence
- Project material specifications are tenant/project-scoped; a SpecKey groups revisions. Revision creates a new record and marks the prior row SUPERSEDED, while latest revision selection is based on Revision. The multi-write sequence has no transaction guarantee established.
- Calculations use a CalculationKey for idempotent replay and are recorded with source event/version, specification, basis quantity, consumption, wastage, computed quantity, previous quantity, variance and type/status metadata. Ledger is append-oriented in intent, but sampled service later updates calculation records to link MaterialNeedID and on void to set SOURCE_EVENT_VOIDED; strictly immutable semantics are not established.
- Recalculation identifies active specifications by latest revision, locates confirmed/estimated needs, supersedes prior needs, records calculations, creates/updates needs and links calculation to need. Multiple independent writes plus audit may partially complete.
- Void handling marks source calculations SOURCE_EVENT_VOIDED and confirmed needs NEEDS_REVIEW, and logs aggregate counts. This is a review state, not a full reversal of quantities or procurement lifecycle.
- Calculation rounding is observed to 4 decimal places after ceiling in the sampled path; unit precision and domain-wide rounding policy are not established.
- CalculationKey tuple and replay checks exist in sampled paths; unique constraint/race-safe enforcement is not proven.
- ProjectInformationService validates typed scalar/table values and attachment ownership, and has revision/history/approval/reference-sync paths; this increment focuses on material-plan linkage.

### Target architecture implications
- Preserve specification revision identity and status history; define concurrency-safe unique keys and revision constraints in PostgreSQL.
- Represent calculations and needs with explicit relationship constraints and transactional orchestration. Decide whether ledger entries are immutable or permit controlled transitions; legacy does not establish immutability.
- Void reconciliation should explicitly track NEEDS_REVIEW and calculation invalidation and must not silently imply purchase order/receipt reversal.
- Establish quantity/decimal precision policy across quantities, wastage, variance and units; G-06 remains open/high.
- Preserve source-event provenance and event-version association for calculations.

### Readiness status
G-01 BLOCKER, G-02 BLOCKER, G-03 BLOCKER, G-06 HIGH/open, G-09 HIGH/open, G-10 BLOCKER.

Read-only review only. No tests, live data, SQL/DDL, migration, DB provisioning, production changes, deployment or VPS action.
