# PMS V2 — Roadmap & Task Status

> Status values: NOT STARTED / IN PROGRESS / IMPLEMENTED / TESTED / ACCEPTED / COMPLETE. A task is COMPLETE only after successful verification and explicit Product Owner acceptance.

## Phase 0 — Discovery & Repository Preparation

### Task 0.1 — Verify V2 repositories and establish project governance
- [x] Verify Infra repository availability and access.
- [x] Verify all four V2 repositories and access permissions.
- [x] Establish initial README, TODO, and ARCHITECTURE documents.
- [x] Verify initial documentation files by reading them back from GitHub.

**Status:** COMPLETE  
**Notes:** All four repositories are verified accessible with admin/push permissions. The three implementation repositories have been confirmed Private.

### Task 0.2 — READ-ONLY audit of current PMS
- [x] Preliminary frontend repository structure and selected modules retrieved.
- [x] Backend route surface and selected service files retrieved.
- [x] Inventory database sheet definitions and headers from DatabaseSetup.js.
- [x] Preliminary audit of tenant-scoping patterns, workflow-version/rule context, event/form version snapshot references, and procurement idempotency paths.
- [x] Extract selected workflow-event sequencing, repeat-project/reference copy behavior, and file lifecycle rules (selected paths).
- [ ] Extract relationship/business rules in detail; audit workflows and role/permission enforcement. (Router POST/PUT/DELETE dispatch inventory recorded; service-level verification remains.)
- [x] Preliminary inventory of Drive storage and attachment lifecycle.
- [x] Inventory integrations (Telegram and other external services) at source level; live configuration remains undiscovered.
- [x] Inventory frontend and backend test-file names and CI workflow files (inventory only; tests not run).
- [x] Consolidate critical risks, performance constraints, migration dependencies, and test/deployment coverage caveats.
- [x] Record detailed findings and unknowns in ARCHITECTURE.md.

**Status:** IN PROGRESS

### Task 0.3 — Discovery report and target boundaries
- [x] Draft high-level current-system map from read-only source audit evidence.
- [x] Draft candidate migration scope, platform exclusions/replacement handling, domain boundaries and compatibility requirements.
- [x] Record unknowns and items requiring live-data discovery or business confirmation.
- [x] Product Owner review and acceptance.

**Status:** COMPLETE — Product Owner accepted on 2026-11-22. This closes Task 0.3 only; Phase 0 repository preparation remains IN PROGRESS.

### Task 0.3 acceptance (2026-11-22)
- Product Owner accepted discovery report and provisional domain boundaries.
- No schema implementation, migration or legacy-system changes authorized by this acceptance.
- Remaining Phase 0 work: verify/create the three independent V2 repositories and continue any approved preparation tasks.


---

## Phase 1 — Foundation & Architecture
- [ ] Establish repository collaboration conventions and CI baseline.
- [ ] Define architecture, API, security, logging, and error-handling standards.
- [ ] Define initial domain boundaries and database migration strategy.
- [ ] Establish testing strategy and quality gates.

## Phase 2 — Database Design & Migration Framework
- [ ] Design PostgreSQL schema based on verified existing data/workflows.
- [ ] Implement versioned migrations.
- [ ] Implement migration tooling, validation, reports, and rollback/recovery planning.
- [ ] Test migration against sanitized/test data only.

## Phase 3 — Backend Foundation
- [ ] Build Node.js/TypeScript backend foundation.
- [ ] Implement authentication, authorization, tenant boundaries, audit, and API validation.
- [ ] Implement core domain services and tests.

## Phase 4 — Frontend Foundation
- [ ] Adapt existing UX and workflows to API architecture.
- [ ] Establish responsive application shell, routing, state, error/loading patterns.
- [ ] Integrate authentication and API contracts.

## Phase 5 — Core PMS Workflows
- [ ] Design & sampling.
- [ ] Production project/workflow management.
- [ ] Cutting.
- [ ] Sewing.
- [ ] Packaging.
- [ ] Warehouse-related functions within confirmed existing scope.
- [ ] Procurement and supplies.
- [ ] Reporting and printing.

## Phase 6 — Integrations, Files & Automation
- [ ] Define and implement file-storage integration.
- [ ] Reassess notifications/integrations based on verified requirements.
- [ ] Implement reliable, permission-aware integrations and tests.

## Phase 7 — QA, Performance, Security & UAT
- [ ] Automated functional, regression, API, permission, data-integrity, and security testing.
- [ ] Performance/load testing and remediation.
- [ ] User acceptance testing.
- [ ] Record Product Owner approvals.

## Phase 8 — Deployment Readiness
- [ ] Infrastructure, Docker Compose, Nginx, deployment automation.
- [ ] Backups, monitoring, restore test, and operational runbooks.
- [ ] VPS selection/purchase only when justified and approved.
- [ ] Security and release readiness review.

## Phase 9 — Controlled Migration & Cutover
- [ ] Migration rehearsal and validation.
- [ ] Explicit Product Owner approval for real-data migration.
- [ ] Controlled cutover and rollback plan.
- [ ] Preserve old PMS read-only for an approved retention period.

## Operating Rules
- Work on one major task at a time.
- Read this file and ARCHITECTURE.md before each task.
- Do not duplicate or repeat accepted work.
- Mark IN PROGRESS only after work starts.
- IMPLEMENTED is not equivalent to TESTED or ACCEPTED.
- Task COMPLETE requires successful verification and explicit Product Owner acceptance.
- Report blockers, dependencies, test results, changes, and risks.
- Existing production repositories remain read-only; do not commit, branch, or PR against them.

### Task 0.2 incremental notes (2026-11-21)
- Selected project creation and reference-copy paths examined: tenant-derived IDs, repeat-workflow source checks, policy-driven initial copy/live sync/independent, copy-plan state, profile-image copy and sync-log behavior.
- Selected event workflow-rule logic examined: required/reference/not-required modes, STRICT/GROUPED sequencing, allow-repeat, manual step completion checks and void restoration behavior.
- Selected attachment lifecycle examined: PROJECT/EVENT attachment associations, parent project visibility for events, upload/download/thumbnail validation, Google Drive provider and deletion behavior.
- Still pending: deeper route/permission coverage map, event amendment details, project deletion impact, integration inventory, CI/test execution-gate review, performance/migration dependency synthesis.


### Task 0.2 incremental notes (2026-11-21 continued)
- Selected ProjectInformationService paths: ExpectedRevision stale-write guard, revision/history and approval records, attachment validation, cardinality/data-type validation, LIVE_SYNC conflict detection and explicit conflict-resolution path.
- Selected event void path: AllowVoid configuration and later-step constraints; restore prior project/step status, mark event VOID, audit, refresh workflow progress and potentially flag material planning.
- Selected project deletion path: tenant-scoped administrator check, dependency preview and exact-code confirmation; protection for shared procurement items/orders, dependency handling, file trashing, audit/failure reporting. V2 deletion/recovery policy remains an open business decision.


### Task 0.2 incremental notes (2026-11-21 continued)
- Read backend-check and PMS agent-suite workflow definitions; recorded test/syntax/manifest/domain-contract/schema safety checks.
- Read frontend-check, frontend agent suite, and cross-repo QA definitions; recorded syntax, regression tests, asset/CSS checks, isolated staging E2E fail-closed guard, and cross-repo validation.
- Workflow definitions only; no CI run was executed or status evaluated.


### Repository availability re-check (2026-11-21)
- [x] Infra V2 repository accessible.
- [x] Rechecked the other three intended repositories via GitHub API; 404/not found returned. Existence is not confirmed; creation is still pending.


### Task 0.2 incremental notes (2026-11-21 continued)
- Continued read-only integration audit: TelegramBotService uses Script Properties for token/secret and tenant-to-chat mapping, supports registration flow and polling trigger (one-minute trigger), message sending, and update offset persistence. Telegram diagnostics are also present. Secrets are not retrieved or exposed.
- Procurement event integration source confirms a limited link: event-type setting can recalculate confirmed material needs based on a single numeric central-information type, with validation and audit. Purchase orders/receipts are stated to remain independent from this event integration.
- Additional route/source tracing and exact route permission map remain pending. No live Telegram configuration, tokens, tenant chat IDs or user data accessed.

### Task 0.2 incremental notes (2026-11-21)
- Extracted all declared sheet header arrays from DatabaseSetup.js (schema inventory, not data). Key domains include tenant/security, project/workflow, event/form snapshots, central information revisions, attachments/audit, reference-sync logs, material specs/calculations, purchase orders/items, allocations and receipts.
- Enumerated literal Router route families and EventService method names as a preliminary route/domain map; this does not yet constitute a complete route permission matrix.
- Further EventService inspection confirmed create-request idempotency key handling with user-scoped locking and replay lookup. Void handling is tenant/project-scoped, permission/department constrained and checks event type AllowVoid and later-event conditions (full behavior still needs tracing).
- Relationship extraction and exhaustive amendment/revision tracing remain in progress. No production writes or data access; tests not run.


### Task 0.2 incremental notes (2026-11-21 continued)
- Router GET/POST/PUT/DELETE branches inspected further. Route-level permissions cover project/event/form/workflow/template/user/role/audit/attachment/procurement domains, with draft-project creator exceptions and admin-only project deletion/session management.
- Route permission coverage is still a source-level preliminary map: endpoint branches and service-level checks need systematic reconciliation; no claim of complete authorization audit.
- Event form response amendment path is routed through PUT `/events/:eventId/forms/:formId` and FormService.updateEventFormResponse under EVENT_EDIT; event void through PUT `/events/:id/void`, also EVENT_EDIT. Event-form response snapshots/versioning and amendment edge cases still need service-level trace.
- PermissionService resolves tenant-bound active roles and role-permission mappings; request caches are keyed by tenant/user. Router's generic enforceRouteAccess hook itself does not enforce permissions; route-specific enforcement occurs in Router branches and services. This architecture is an important V2 risk/requirement: centralize authorization and fail closed.
- Route review notes are preliminary and do not represent a live penetration/security test. Tests were not run.


### Task 0.2 — additional verified form-amendment path findings (2026-11-21)
- Event form amendment path requires a same-tenant, same-project amendment event; amendment event type is accepted if explicitly configured as form amendment or if legacy code/name marker matches EDIT/UPDATE/AMEND/ویرایش/اصلاح. Explicit configuration restricts amendable form IDs.
- Form response amendment validates form version, validates values against current form fields and visibility/required rules, checks tenant/project availability, and serializes updates under script lock. It supports idempotent replay keyed by LastAmendmentEventID and conflict checks on repeated differing values.
- Response amendment also synchronizes central ProjectInformation, deactivates emptied values, handles project code specially, and writes an audit record on non-replay. This multi-step Sheets operation is not a DB transaction; partial-failure recovery remains a migration risk.
- Event form response storage is 35_EventFormData; response fields include EventID/FormID/FormVersion/ResponseJSON/DefinitionSnapshotJSON/CreatedBy/CreatedAt (and amendment tracking fields such as LastAmendmentEventID/UpdatedAt inferred from selected update paths; schema-header verification for all fields still pending).
- FormRepository relationships are repository-filtered by FormID and EventID/FormID; these are logical relationships without enforced database foreign keys in Sheets. Event-to-project relation uses Event.ProjectID; Event data is separate 14_EventData keyed by EventID.


### Task 0.2 incremental notes (2026-11-21 — procurement operation and void recovery trace)
- Read-only review of ProcurementService and ProcurementRepository paths confirms order creation validates the full order/line/allocation graph before writes, tenant/project/need visibility, positive quantities, allocation totals versus order quantity, and prevents project-specific needs from being allocated to unrelated projects. Existing active allocations and unallocated order items are considered in outstanding-need calculations; surplus is separately tracked.
- Order creation uses an idempotency key and script-level lock. Since Sheets cannot commit order header, items and allocations atomically, partial failures leave the order marked NEEDS_REVIEW with recovery context rather than silently deleting it. PostgreSQL V2 should use a transaction and durable idempotency constraints while preserving the reconciliation behavior where operationally useful.
- Receipt paths include lock/idempotency and batch processing logic; detailed receipt edge-case and allocation-consumption trace remains incomplete in this audit increment.
- Event void path source rechecked: prior project state is restored only if the current state still matches the event's expected configured changes and a prior snapshot exists; otherwise it refuses unsafe restoration. It recalculates step progress, logs, and attempts material-needs reconciliation; failures are explicitly surfaced/audited. Sheets operations remain non-atomic.
- Source audit only; no tests, live data, or operational system access.


### Task 0.2 incremental notes (2026-11-21 — receipt allocation and deletion dependency trace)
- Batch receipts validate tenant ownership of order item/order, project and optional source event, positive quantities, cumulative receipts against ordered quantity, and project receipt totals against allocation limits. Unallocated surplus can be received into general stock only within the unallocated portion; project receipts require a matching allocation. Batch writes receipt rows in bulk, then recalculates purchase-order status (`ACTIVE`, `PARTIALLY_RECEIVED`, `RECEIVED`) unless the order is already `NEEDS_REVIEW`; sheet-level writes remain non-transactional.
- Single receipt path additionally validates allocations and project-specific cumulative receipt limits and uses source-event/item replay checks; batch and single pathways have some differing validation/replay behavior, which should be harmonized in V2 rather than assumed equivalent.
- ProjectDeletionService dependency preview collects project events/event data, needs, specs/calculations, attachments, receipts, allocations, related order items and orders. Orders are only cascade-deleted when all active items are selected and no other-project allocation remains; shared/general order data is intentionally protected. Admin and tenant checks plus exact project-code confirmation and a script lock are enforced.
- Deletion handles legacy child rows lacking Status by ensuring the column before soft deletion; event rows use VOID. Attachment records are soft-deleted before attempting storage trash. The project row is physically deleted only when dependency handling reports no failures. Partial failure can leave mixed state; V2 should replace this with transactional deletion/archive state, dependency-aware restrictions and durable storage cleanup job/outbox, without destructive cascade to shared procurement records.
- Audit remains source-only, no runtime tests or live records accessed.


### Task 0.2 incremental notes (2026-11-21 — repository-backed relationship map)
- Confirmed repository-level logical links: Projects.ID -> Events.ProjectID; Events.ID -> EventData.EventID; Events.ID -> EventFormData.EventID; EventType.ID -> EventTypeForms.EventTypeID -> Forms.ID; Forms.ID -> FormFields.FormID; InformationTypes.ID -> FormFields.InformationTypeID and ProjectInformation.InformationTypeID; Projects.ID -> ProjectInformation.ProjectID; ProjectInformation.ID -> History.ProjectInformationID; Project.ID pair and InformationTypeID -> ReferenceSyncLog; Workflow.ID -> WorkflowSteps.WorkflowID, WorkflowStepDepartments.WorkflowStepID, WorkflowStepEventRules.WorkflowID/WorkflowStepID with version context; WorkflowStep rules also EventTypeID; Events carry workflow/step/type/user/department references; Attachments and AuditLogs are polymorphic entity pointers; procurement graph Need -> PurchaseOrderItems.MaterialNeedID, PurchaseOrder -> Items.PurchaseOrderID, Items -> Allocations/Receipts via PurchaseOrderItemID, allocations/receipts -> Project and MaterialNeed; calculations connect Project, Spec, Need, SourceEvent.
- Repository source confirms relationships are logical and largely query-filtered rather than enforced FK. Some links are versioned (workflow and event/form snapshots) and must retain historical context. Polymorphic attachment/audit pointers require deliberate V2 modeling.
- Relationship map is provisional: schema edge cases, deleted/inactive semantics, unique cardinality, and full tenant-aware constraints require further discovery.


### Task 0.2 incremental notes (2026-11-21 — authorization route review)
- Re-read Router GET/POST/PUT/DELETE branches and AccessControlService/PermissionService. Source confirms route-specific authorization checks across major route families; admin bypass is based on tenant-scoped active role codes SYS_ADMIN/ADMIN; permission resolution filters tenant role assignments and active tenant roles, and caches within request context.
- Preliminary coverage observations: draft project GET/PUT and project material-spec/reference-copy POST paths include creator/tenant/draft exceptions after PROJECT_VIEW/PROJECT_EDIT failure; admin project deletion and session operations are explicit admin-only. Event-form amendments require EVENT_EDIT; event void requires EVENT_EDIT; attachment operations use separate view/upload/delete permissions; procurement writes require PROCUREMENT_MANAGE.
- Candidate for deeper verification: PUT /print-templates/:id/project-data dispatches directly to PrintTemplateService.updateProjectBinding in Router without an explicit AccessControlService.require in the examined Router branch. Whether service-level validation/auth is sufficient is unknown; flag for targeted source review, not a confirmed vulnerability.
- AccessControlService.enforceRouteAccess itself returns true for authenticated users (except public auth paths) and explicitly relies on Router/service checks. This makes systematic route-to-service authorization verification and fail-closed centralized enforcement a V2 requirement; no vulnerability claim based solely on this generic hook.
- Source-level review only. No runtime tests, penetration tests, production data or changes to production repos.


### Task 0.2 incremental notes (2026-11-21 — print-template direct binding permission verification)
- Rechecked the previously flagged print-template project-data binding update route against `PrintTemplateService.updateProjectBinding`. The service explicitly enforces `PROJECT_EDIT` for project-field and central-information updates, and `EVENT_EDIT` for event-form-field and event-field updates. This narrows the earlier concern: Router-level permission check is not visible in that branch, but service-level authorization is present for supported editable source types. This is source-path confirmation only; no runtime authorization test was performed.
- Other route/service authorization reconciliation remains incomplete.


### Task 0.2 incremental notes (2026-11-21 — direct event-field update audit)
- READ-ONLY source review of `EventService.updateFieldFromTemplate`: allowed fields are restricted to `OccurredAt` and `Description`; it verifies tenant ownership, event validity/status, project tenant and user visibility, event type tenant, and department ownership (or admin). Date edits are blocked when later cross-department events exist. Optimistic concurrency compares `UpdatedAt`/`CreatedAt` against the expected version; the update is locked and an audit record is written. The code states this direct edit does not rerun workflow.
- Source review only; runtime tests remain pending. Broader event mutation and history/version paths still need discovery.


### Task 0.2 incremental notes (2026-11-21 — event void, form revisions and central-information integrity)
- Event void source path validates tenant/project access, event state, event-type AllowVoid configuration, department ownership or admin, and blocks voiding when a later cross-department event exists; audit/workflow impact paths remain under review. Idempotency-key handling in event creation is user- and tenant-scoped under a lock and detects PROCESSING replays, but legacy calls without keys retain original behavior.
- FormService create/update/addField/removeField source paths validate tenant-scoped form/type relationships, reject duplicate codes/fields, validate ordered visibility references, soft-delete form fields, bump form versions, invalidate definition cache and write audit logs. Definition cache is optimization-only and short-lived; configuration changes invalidate via version key. Full response persistence and route-level authorization reconciliation remain pending.
- ProjectInformationService save validates project/type/event association, data types and attachments, table rows/cardinality, limits payload size, supports ExpectedRevision conflict detection, increments revisions, and retains SourceEventID. Approval records a revision in history and triggers reference synchronization. saveWithLock serializes the write path. Attachment validation is project/tenant/entity/type/status scoped. This is source-only; runtime tests not run.
- V2 implications: event create should be idempotent; event void should be a reversible/audited state transition with dependency checks; form definitions need versioned immutable snapshots; central-information writes need transactional revision/audit/approval and explicit reference-sync behavior. These findings do not constitute runtime or security test results.

### Task 0.2 incremental notes (2026-11-21 — project lifecycle and workflow mutation review)
- ProjectService create path derives TenantID from the authenticated user (not request input), validates workflow and first active step/version when activating a project, and supports draft creation with audit. Reference-copy operation ID supports replay lookup and returns the prior project/plan when found; source-path inspection only.
- Project update path tenant-scopes and checks user project visibility; active projects cannot revert to draft, and workflow changes are rejected after any project event (including voided events). Before changing workflow, it validates active/published tenant workflow and selects the first eligible step within its published version. It validates department/user access lists against active tenant records and status values against active tenant project statuses (with a compatibility fallback).
- Project code changes use a lock and tenant-scoped case-insensitive duplicate check; update then logs audit. The observed lock is limited to code uniqueness, not the entire project update. This is source inspection; no runtime or concurrency test.
- V2 design should preserve draft/activation semantics, immutable workflow after event history starts, published workflow-version context and initial-step selection, explicit tenant/user access lists, and status validation. Transactionally combine project edits, workflow assignment and audit; enforce project-code uniqueness in PostgreSQL with a tenant-scoped constraint.


### Task 0.2 incremental notes (2026-11-21 — attachment visibility, audit records and workflow completion rules)
- READ-ONLY AttachmentService review: attachment visibility is tenant-scoped and checked against project visibility (for project files) or event/project visibility (for event files). Project-image listing restricts to eligible active image records and the visible projects; the project gallery excludes void/cancelled events. Metadata endpoints intentionally avoid exposing storage keys/file bytes. Reference-project image copy validates tenant, source/target visibility, image type, active status and allowed attachment types (PROFILE/INFORMATION), checks for existing copies and audits the copy; cleanup attempts to delete copied storage if repository insertion fails.
- AuditService creates AUD-prefixed IDs, serializes old/new data, records action/entity/user/tenant/reason/time and offers tenant-filtered list, entity and user queries. The source does not establish audit immutability, append-only enforcement or runtime authorization testing; those remain discovery items.
- EventService workflow rule progress reads rules for the project's workflow step/version, filters tenant/workflow/version/status and evaluates project events excluding void records; the completion/transition path depends on configured event rules and event-type transition settings (including target step/status/department). Detailed finalization and project deletion dependency behavior remain pending.
- V2 implications: separate attachment metadata from binary storage, enforce authorization at entity-level for download/list/copy, use storage cleanup/outbox patterns for failures, implement immutable append-only audit records with transactional writes, preserve versioned step-rule completion and void-event exclusion, and explicitly map project/event dependency-safe deletion/archive behavior. Source review only; no runtime tests.


### Task 0.2 incremental notes (2026-11-21 — project dependency-aware deletion and file lifecycle)
- READ-ONLY review of ProjectDeletionService: deletion is admin-only and tenant-scoped; preview enumerates dependency counts and requires the project code as confirmation. Collection includes project events/event data, material needs, purchase order lines/orders (preserving shared orders/items allocated elsewhere), allocations, receipts, specifications, material calculations and attachments. It soft-deletes dependencies where supported, marks events VOID, trashes Drive files after attachment records are handled, and physically removes the project row only if earlier steps report no failure. It records a cascade-delete audit entry including counts and failure count; failure paths report partial outcomes rather than claiming atomic rollback. Locking and revalidation are used, but the sequence is not a transactional cascade and can partially complete.
- File lifecycle source review: allowed MIME/extensions are aligned, filenames and payload size are validated, PDF signature is checked in the storage upload path, and Google Drive trashing is used rather than permanent deletion. Tenant-specific folder upload path exists. Service code still relies on Google Drive; file storage operations and DB metadata changes are not one atomic transaction.
- V2 implications: prefer soft deletion/archive and explicit retention policies; use FK/dependency policies and transactions for relational rows, preview/confirmation workflow for privileged cascade actions, durable job/outbox for external file deletion, retries and reconciliation for partial failures. Shared procurement records require dependency-aware preservation. Source inspection only; no runtime tests.


### Task 0.2 incremental notes (2026-11-21 — material planning, procurement, workflow rules and reporting)
- READ-ONLY MaterialPlanningService review: project material specifications are revisioned by SpecKey, previous revisions are superseded, and calculations are an append-only ledger keyed for idempotent replay. Recalculation ties confirmed quantities to a source event and event-version context; calculation records preserve basis quantity, consumption, wastage, prior calculated quantity and variance. No runtime verification.
- READ-ONLY ProcurementService review: procurement records are tenant-scoped and validate project/event references and project visibility. Order creation uses a lock and optional idempotency key; order items/allocations track needs across project(s), and shared orders must not be incorrectly treated as belonging to one project. Receipts are idempotency-aware, validate allocated/project context and reject quantities exceeding ordered amounts. Source inspection only; detailed edge cases and permissions remain to audit.
- READ-ONLY workflow rule-validator review: each workflow step uses one sequence mode (STRICT, FREE or GROUPED); rule requirement modes REQUIRED/OPTIONAL/REFERENCE/NOT_REQUIRED align with data source NEW_EVENT/SOURCE_PROJECT/NONE. STRICT requires unique event order, FREE requires unique display order, GROUPED requires group key/order. Rule event types must be active and tenant-valid at save. Workflow rules are versioned with draft/published context. Runtime progression remains unverified.
- READ-ONLY reporting review: project reports validate tenant and project visibility, omit VOID/CANCELLED events and filter procurement needs/calculations/orders by project, including items allocated to projects within shared orders. Dashboard aggregation uses tenant-scoped entities and visibility-filtered projects, with indexed event grouping to avoid repeated project scans. EventDataService reads legacy event data with tenant/project visibility checks. Source-only review; runtime and report-level permissions/tests pending.
- V2 implications: model material specs and calculation snapshots as versioned records; preserve calculation idempotency keys and source-event/version links; represent purchase orders separately from project allocations/receipts (many-to-many allocation) and enforce quantity constraints transactionally; implement workflow rule modes and versioning explicitly; reports must query only tenant-visible project/event data and correctly exclude void states where business rules do.


### Repository availability checkpoint (2026-11-21)
- Checked GitHub API availability for the planned repositories `PRSPRO2-V2`, `PRSPRO2BACKEND-V2`, and `PRSPRO2DATABASE-V2`; each returned 404 (not confirmed accessible/existing in this connection). No creation was attempted. The Infra repository remains the only confirmed V2 repository at this checkpoint. This is a blocker to starting repository-based implementation, but Phase 0 source audit/documentation continues.


### Task 0.2 incremental notes (2026-11-21 — repository-level relationship and sheet map)
- READ-ONLY repository review confirms the current storage is organized as Google Sheets repositories, not a relational database with enforceable foreign keys. Confirmed sheet identifiers: projects `08_Projects`; events `13_Events`; event payloads `14_EventData`; procurement needs `18_MaterialNeeds`; purchase orders `19_PurchaseOrders`; order items `20_PurchaseOrderItems`; allocations `21_PurchaseAllocations`; receipts `22_MaterialReceipts`; material specs `24_ProjectMaterialSpecs`; material calculations `25_MaterialNeedCalculations`; project information values `30_ProjectInformation`; project information history `31_ProjectInformationHistory`; workflow step event rules `37_WorkflowStepEventRules`; project-reference synchronization log `38_ProjectReferenceSyncLog`.
- Confirmed repository relationships from source: EventData is looked up by EventID; events by ProjectID/EventTypeID/PerformedBy/DepartmentID; project information by ProjectID + InformationTypeID and history by ProjectInformationID; workflow rules by WorkflowID + WorkflowStepID + WorkflowVersion + EventTypeID; procurement order items by PurchaseOrderID, allocations by PurchaseOrderItemID + MaterialNeedID + ProjectID, receipts by PurchaseOrderItemID and optional ProjectID/SourceEventID; material calculations refer to ProjectID, MaterialSpecID, MaterialNeedID and SourceEventID/SourceEventVersion.
- ProcurementRepository is tenant-filtered at read boundaries. Its sheet mapping explicitly separates need, order header, order item, project allocation and receipt records. This separation is essential: one order item can have allocations across projects, while receipt quantity must be reconciled against total ordered and project-allocated/unallocated portions.
- Important limitation: repository methods generally load/filter sheet rows in application code; the source reviewed does not prove relational FK/unique constraints, referential integrity enforcement, or atomic multi-sheet transactions. Treat the above as a verified logical relationship map, not proof of database constraints. Confirm exact column sets, optionality, deletion semantics and cardinalities during full schema inventory.
- V2 implication: implement explicit PostgreSQL foreign keys and tenant-safe composite constraints after full field inventory; model purchase order header/item/allocation/receipt separately; index EventData.EventID, Events.ProjectID and key tenant references; use transactions for event payload, material calculations, allocations, receipts and audit writes.


### Task 0.2 increment — core identity, authorization and schema inventory (2026-11-21)
- Read-only DatabaseSetup inventory verified core tables and headers: tenants, users (tenant, department, password hash), departments, roles, permissions, user-role joins, role-permission joins, projects, workflows/steps, event types/fields, events/event payloads, attachments/audit, workflow-step departments, project statuses, information types/columns, project information/history, forms/fields, event-type-form joins, event-form snapshots, print templates, workflow event rules, reference sync logs, procurement event settings, material needs/specifications/calculations, purchase orders/items/allocations/receipts.
- Verified identity/authorization logical joins: User -> Tenant and Department; UserRole -> User and Role; RolePermission -> Role and Permission. Permissions appear global catalog while role/assignment records include tenant scope; confirm exact constraints and admin bypass/permission evaluation in full AccessControlService/PermissionService review before schema finalization.
- IDs use prefixed string conventions in Constants (examples prefixes TEN, USR, DEP, ROL, PER, PRJ, WF, STEP, ET, EF, EVT, ATT, AUD, SES). Preserve identifiers as text rather than converting to numeric keys.
- DatabaseSetup includes potentially destructive full reset routine; this was source-reviewed only and NOT executed. Production DB remains untouched.
- Backend tests directory was catalogued (41 test files observed) covering schema contracts, auth caches, attachments, dashboard/performance, forms/cache, idempotent events, material planning, procurement, project deletion/references, reports, print templates, reusable forms, Telegram, and workflow rules. File listing is not test execution and no pass claim is made.
- PostgreSQL design direction: tenant-aware unique keys and composite FK patterns; separate user-role and role-permission junctions; global permission catalog; workflow/project status customization; event/form definition version snapshots; retain append-only audit and reference synchronization history. Explicit schema discovery still pending for precise field types/nullability and auth edge cases.


### Task 0.2 increment — authorization and user-management path audit (2026-11-21)
- Read-only review of AccessControlService, PermissionService, UserService, UserValidator and Router. Authentication is token-based through AuthService.me; request-scoped caches exist for authenticated user, admin determination, roles and permissions and are reset per request (reset lifecycle to be separately verified).
- Admin determination uses active tenant roles with code SYS_ADMIN or ADMIN. User permission resolution joins tenant-scoped UserRoles, active tenant Roles, tenant-scoped RolePermissions and global Permission catalog; permissions cached per request.
- Router guards are authoritative entry-path checks, with explicit `require` and `requireAny` patterns. Admin shortcut is applied by access-control functions. Service methods also perform tenant/resource validation in reviewed user operations; don't rely on route guard alone in V2—apply authorization in service/domain layer.
- User management: list/get are tenant-filtered; create derives TenantID from authenticated user, validates department tenant, enforces global case-insensitive username uniqueness and hashes passwords (minimum 8 chars enforced in validator); update blocks TenantID changes, validates department tenant and logs sanitized audit data. Username uniqueness currently appears global, not tenant-local, consistent with comments about login not selecting tenant. Preserve this unless PO approves change.
- Router read-path samples: USER_VIEW for `/users`, USER_EDIT for user-role list/assign and update, USER_CREATE for create; ROLE_MANAGE for permission operations; role listing may allow ROLE_MANAGE or USER_EDIT; department listing uses multiple operational permissions. These are route-source findings only; full route matrix and runtime tests pending.
- Security caveats/pending verification: confirm authentication/token storage and revocation, permission endpoint exposure semantics, complete permission matrix for all CRUD routes, admin bypass semantics, cache reset on each request, and enforce global username uniqueness in PostgreSQL with a unique index (case-insensitive). Password hash algorithm/cost, session security and migration compatibility remain pending.
- No production changes or runtime tests; source inspection only.


### Task 0.2 incremental notes (2026-11-21 — authentication and session lifecycle)
- READ-ONLY AuthService review: login resolves username matches and fails closed unless exactly one user matches; this current implementation/commentary is inconsistent with an earlier user-management source observation that usernames are globally unique and tenant-scoped records exist, so uniqueness behavior needs reconciliation. Password verification uses deterministic SHA-256 of the raw password with no salt or work factor visible in this implementation; this is a significant V2 security migration concern, and current hashes should not be carried forward as a password-storage design. Require modern password hashing (Argon2id or bcrypt), secure migration/reset strategy, and no exposure of credential data.
- Sessions use UUID-derived opaque tokens stored in Script Properties, active status, configurable expiry and last-seen writes throttled to one minute. Logout deletes token storage; admin session list/revoke is tenant/admin scoped, session IDs are hashed for display and current-session revocation is blocked. Session records and Script Properties are not a scalable/relational security store for V2; implement durable session records, revocation, secure cookie/token transport, rotation and expiry.
- WebApp.js confirms request caches are reset at both GET and POST entry points before parsing/dispatch (PermissionService, AccessControlService, DepartmentRepository and GoogleSheetsRepository). This verifies request cache lifecycle in the examined entrypoint paths.
- Authentication/session source review only. No production execution, runtime test, credentials or session records accessed. Open issues: reconcile username uniqueness observations, inspect token transport risks (query parameter usage is supported), login throttling/lockout, password hash migration, session expiry config and full route authorization matrix.


### Task 0.2 incremental notes (2026-11-21 — username uniqueness reconciliation and login abuse controls)
- Reconciled username uniqueness source paths: UserService create and update explicitly enforce case-insensitive GLOBAL uniqueness across all tenants, and UserRepository findAllByUsername supports matching. AuthService login fails closed unless exactly one case-insensitive username match. Earlier apparent tenant-local/global ambiguity is resolved at source level: global uniqueness is the intended current rule, though storage-level atomic uniqueness is not enforced by Sheets. V2 should preserve global uniqueness initially with a database unique index on normalized username.
- No login throttling, rate limiting, lockout, progressive delay or explicit abuse-monitoring mechanism was found in the reviewed AuthService/SessionManager/UserValidator paths. This is a bounded source review, not a comprehensive absence proof; V2 must include rate limiting, monitoring and recovery safeguards.
- Auth token can be accepted via query parameters (GET parsing and AuthService token extraction), creating exposure risk through URLs/logging/browser history depending on transport context. V2 should avoid URL token transport and use secure, deliberate authorization headers or secure cookie strategy.
- Password SHA-256 implementation and login path confirmed; current password migration needs a secure reset or controlled migration design, not reuse of this hash scheme.
- Session expiry is configurable and sessions are stored in Script Properties. No runtime verification of config values or security controls was performed.
- Audit increment only; no production modifications, runtime tests, login attempts, or credentials accessed.


### Task 0.2 incremental notes (2026-11-21 — Router authorization audit)
- READ-ONLY route audit of Router.js and AccessControlService.js across GET, POST, PUT and DELETE dispatch. Most sensitive route branches explicitly require permission codes or admin checks; admin bypass is centralized in AccessControlService.require and Router.requireAny. Domain services additionally validate tenant/resource ownership in previously examined flows. This source review did not establish a confirmed authorization bypass.
- AccessControlService.enforceRouteAccess is primarily an authentication gate (allowing login/logout/me exceptions and admin shortcut); operational permission enforcement is deliberately deferred to Router/service dispatch. V2 should implement centralized route policy plus service/domain authorization, with deny-by-default and automated route coverage.
- Route groups sampled: projects/drafts and information, events/forms/attachments, workflow management, templates, forms and information types, event types, users/roles/permissions, audit, procurement, reports, admin sessions and project deletion. There are many ad hoc permission combinations; V2 needs a documented matrix, explicit resource-scope checks and automated tests per route and method.
- Special admin-only endpoints reviewed in Router: deletion-preview, project deletion and session listing/revocation. Admin status based on active tenant role codes SYS_ADMIN/ADMIN.
- Source-only review. No runtime testing or production changes. Task 0.2 remains open pending deeper endpoint/service coverage, production frontend integration audit and final Discovery synthesis.


### Task 0.2 incremental discovery (2026-11-21 — API transport/error handling and frontend inventory)
- Reviewed production backend RequestParser.js and WebApp.js read-only. GET accepts token in query params; POST/PUT/DELETE parser accepts token in query params or JSON body, with method override via params/body. V2 should use Authorization header or secure cookie, strict method/path handling, no URL token exposure, and avoid carrying auth token in payload semantics.
- WebApp serializes unknown/internal exceptions with error.message in generic INTERNAL_ERROR responses. This is a potential information disclosure concern to validate in runtime/log scenarios (source observation only); V2 should return sanitized client errors and log correlated details server-side.
- Frontend production repository top-level inventory confirms large maintained documentation/architecture and task logs, but source-file tree inventory and endpoint-to-UI coverage remain pending. Production repositories remain READ-ONLY.
- No runtime testing, no production changes. Task 0.2 remains IN PROGRESS.


### Task 0.2 incremental discovery (2026-11-21 — frontend API client and module inventory)
- Read-only frontend tree inventory: modular SPA with shared core API/session/cache/router/permissions/theme, UI components, and domain modules for projects, project-detail/images, events, workflows, event-types, forms, information-types, material-planning, procurement, reports, attachments, users/roles/permissions/departments, statuses, audit, settings/admin, print-designer. Pages are dynamically loaded through Router and index.html.
- API client (js/core/api.js) sends auth token as URL query parameter for all requests; GET and POST-overridden PUT/DELETE share URL construction. This exposes token risk through URL/log/history surfaces. V2 frontend should use header/cookie auth and shared API client.
- Session token persisted in localStorage (pms_api_token and pms_session); V2 should prefer secure HttpOnly SameSite cookies where architecture allows, minimize JS-accessible token storage, and define CSRF protections if cookie-based.
- Frontend API supports GET request deduplication, retry on GET network/429/5xx, sync status events, and cache-assisted reads/background refresh. Preserve equivalent UX/performance behavior with a robust server/API cache strategy; ensure retry/idempotency rules for writes.
- Frontend has client route-permission gating from /auth/me/permissions; this is UX-level and must never replace backend authorization.
- Frontend modules and index.html inventory only; exhaustive endpoint usage mapping, pages source audit, build/test dependency audit, and API call inventory remain outstanding. Production repo is READ-ONLY; no runtime tests or changes performed.


### Task 0.2 incremental discovery (2026-11-21 — frontend pages and API module mapping)
- Confirmed production frontend page inventory: 22 HTML pages (login, dashboard, projects/project-detail, events, workflows, statuses, event-types, information-types, forms, procurement, attachments, reports, users, departments, roles, permissions, audit, settings, admin, print-designer, print-output). Read-only source tree inspection.
- Sampled and extracted direct Api.get/post/put/delete calls from modules projects, events, procurement, material-planning, forms, reports, project-detail, attachments, workflows, event-types, information-types, users, roles, permissions, departments, project-statuses, audit, settings, print-designer and admin. API usage spans projects/events/attachments, material specs, procurement orders/receipts, reusable forms and event-type configuration, workflow, print templates, reporting, user/role administration and admin sessions/deletion. This mapping is partial: dynamic routes, indirect calls, helper wrappers and full request/response payload semantics remain to be examined.
- UI modules are relatively decoupled behind shared Api client, supporting a migration strategy that preserves modules while replacing the API client/contracts. However, exact API contracts and behavioral parity require full endpoint and payload audit before implementation.
- Source-only; no runtime or production changes. Task 0.2 remains open.


### Task 0.2 — API usage patterns and Google-specific coupling (2026-11-21)
- Expanded frontend direct-call extraction across procurement, material planning, events, forms, print designer. Patterns include procurement workspace/dashboard/needs/orders and batch receipts; material-spec update and project-scoped planning; event creation and amendment; reusable forms and event-type form linking; print template revisions, sources, preview, resolve, save/update/delete, project/attachment references.
- Source scan of these frontend modules found no direct Google Apps Script APIs (SpreadsheetApp, DriveApp, PropertiesService, google.script.run, ScriptApp, CacheService, LockService); they use shared Api client, consistent with backend-mediated access.
- Backend repository source scan confirms GoogleSheetsRepository directly uses SpreadsheetApp and LockService, while TelegramBotService uses PropertiesService, UrlFetchApp and ScriptApp. StorageRepository, AttachmentService and PrintTemplateService sampled did not directly show those identifiers; this does not establish their full dependency freedom because collaborators and runtime configuration remain to be audited.
- V2 replacement boundaries: persistence adapter/transaction layer replacing GoogleSheetsRepository; file-storage adapter and lifecycle service; notification adapter replacing GAS Telegram transport/webhook; configuration/session/cache/logging infrastructure independent of Script Properties. Preserve external Telegram capability only after requirements and secret handling are documented.
- Remaining: detailed endpoint payload/response contract extraction, complete backend integration dependency graph, all frontend module dynamic/indirect calls, GAS config/deployment dependencies, webhook setup, external file references, and test/environment behavior. READ-ONLY; no production changes.


### Task 0.2 — Projects & Events Router-to-Service mapping (2026-11-21)
- Read-only Router.js inspection mapped core GET paths: /projects (ProjectService.list), /project-form/lookups, /projects/:id, project information list/item/history, project events, workflow-progress, material specifications/calculation history, project details/page, project attachments, /events (EventService.list), /events/:id (EventService.get), /events/:id/forms (FormService.getEventResponses), /events/:id/data (EventDataService.getByEventId), /events/:id/attachments (AttachmentService.listByEntity), project image-gallery/counts and project-card-images.
- POST mapping: /projects (ProjectService.create), /events (EventService.create), /projects/:id/material-specifications (MaterialPlanningService.createSpecification), /projects/:id/reference-copy/:informationType (ProjectService.copyReferenceInformationType), /projects/:id/complete-step (EventService.completeStep), event/project attachment upload paths, and event form response writes. Additional routes are present but not fully mapped.
- PUT mapping: /projects/:id (ProjectService.update), /material-specifications/:id (MaterialPlanningService.reviseSpecification), /events/:id/forms/:formId (FormService.updateEventFormResponse); other event edits route through specific branches. DELETE/void and other methods still need detailed contract mapping.
- Permission and draft exceptions are route-specific (e.g. draft owner/tenant checks with PROJECT_CREATE fallback) and must be reproduced deliberately, not generalized.
- Services/repositories sampled show LockService references in ProjectService and EventService; repositories themselves generally abstract storage and don't expose SpreadsheetApp directly in sampled Project/Event repository files. Locking must be translated to transactional/concurrency primitives in V2.
- Scope limitations: mapping is route-to-service, not exhaustive payload schemas, validations, downstream calls, authorization matrix, or runtime tests. Task remains open, read-only.


### Task 0.2 — Projects & Events API route/service map (2026-11-21)
- READ-ONLY Router audit mapped GET routes: `/projects`, project by ID, project information collection/item/history, project events, workflow-progress, material specifications/calculations, project details and attachments, `/events`, event by ID, event forms/data/attachments, plus gallery/card image endpoints and project-form lookups.
- POST routes sampled: project create, event create, project material-spec creation, reference-information copy, project/event attachment upload, workflow-step completion. PUT routes sampled: project update, material-spec revision, event-form response update and related routes; DELETE/void semantics are covered by earlier source audits and require fuller route inventory.
- Permission gate patterns observed: PROJECT_VIEW/PROJECT_CREATE fallback for owner-created drafts in tenant scope; EVENT_VIEW for project events/details; ATTACHMENT_VIEW for gallery/attachments; PROJECT_EDIT with limited created-draft PROJECT_CREATE fallback for material specs and project updates; EVENT_CREATE for event creation and workflow completion; EVENT_EDIT for event-form amendment and void. Source review, not runtime security validation.
- Project/Event domain dependencies are routed through services (ProjectService, ProjectInformationService, MaterialPlanningService, EventService, EventDataService, FormService, AttachmentService) and repositories; V2 migration should preserve service-level business boundaries, tenant scope, draft exceptions, workflow side effects and concurrency/idempotency.
- Contract audit is partial: exact schemas, validation branches, response envelopes, pagination/filter semantics and cross-service transaction boundaries remain pending. Production untouched.


### Task 0.2 — Projects/Events route-to-service audit (2026-11-21)
- READ-ONLY Router.js inspection mapped key GET routes: project list/form lookups, project by ID, project information/history, project events, workflow progress, material specs/calculations, project details/pages and attachments; events list, event by ID, forms/data/attachments; project image-gallery counts and images.
- POST mapping sampled: project create, material specification create, reference-information copy, event create, complete workflow step, project/event attachment upload. PUT mapping sampled: project update, material-specification revision, event-form response update, workflow event rules, workflows/forms/form-fields/event-types. DELETE void-event route sampled; more delete routes require mapping.
- Router permissions identified in inspected branches include PROJECT_VIEW/CREATE/EDIT, EVENT_VIEW/CREATE/EDIT, ATTACHMENT_VIEW/UPLOAD, WORKFLOW_MANAGE, SYSTEM_SETTINGS, EVENT_TYPE_MANAGE. Draft-project exceptions are creator/tenant/state scoped in inspected branches. This is source observation, not runtime security validation.
- Project/Event services and repositories sampled for functions and direct GAS identifiers: ProjectService/EventService show LockService usage; ProjectRepository/EventRepository/EventDataRepository sampled had no direct GAS identifiers. Locks are GAS-specific orchestration and need a concurrency-control replacement (transactions/advisory locks/unique constraints) in V2.
- Route contract audit is partial; payload schemas, all CRUD routes, request-parser normalization, service-level validation, and complete authorization edge cases remain pending. Task 0.2 remains IN PROGRESS.


### Task 0.2 — Project/Event domain service behavior (2026-11-21, source-only)
- Project creation obtains TenantID from authenticated user rather than trusting client-supplied tenant; invokes ProjectValidator. Draft path exists. Reference-copy operation ID supports replay lookup scoped to tenant; prior record visibility is checked. Project updates enforce tenant and project availability, draft activation behavior, prohibit workflow changes on non-draft, and protect reference policy from client edits. Project listing filters tenant and project visibility; non-PROJECT_VIEW users in sampled logic are restricted to their own drafts.
- Event creation supports IdempotencyKey, user+tenant scoping, lock-based duplicate detection, and PROCESSING-state protection; legacy clients without keys retain unlocked path. Event list filters tenant and only exposes project-linked events for visible projects, while projectless events remain included (notably integrations such as procurement). Batch enrichment reduces repeated repository reads.
- Event-by-project listing validates project tenant/availability; workflow-step completion requires assigned responsibility/admin, workflow configuration, manual completion setting, required-event satisfaction and handles repeat completion idempotently.
- EventData access validates event tenant and project visibility; data JSON parse failures return controlled errors. Repository writes still delegate to GoogleSheetsRepository.
- V2 requirements: transactional project creation/update; draft visibility policy; globally/tenant-consistent idempotency keys with unique indexes; stateful retry/recovery instead of lock-only markers; workflow transition invariants; event payload schema validation and relational ownership; tenant-scoped batch reads; audit in same transaction.
- Source review only; validation/runtime tests not executed. Audit remains incomplete.


### Task 0.2 — Workflow/EventType/Form domain audit (2026-11-21)
- WorkflowService uses draft/published workflow versioning, clones rules on draft creation from published version, publishes a selected version, and reads/writes step event rules tied to version. Rules encode sequence modes (STRICT/FREE/GROUPED) and information/event requirements; repeat reference policies validate active tenant information types. The rule repository/service file names searched yielded WorkflowStepEventRuleRepository and Validator elsewhere; exact mapping needs continued verification.
- EventTypeService normalizes JSON arrays and boolean configuration, validates tenant-scoped department/status references and amendable form references (active, tenant-owned forms), and exposes optional form-definition prefetch. Event types can be versioned and configured with amendment behavior.
- FormService: tenant-scoped reusable forms; definition snapshots include form version, field order, required state, visibility rule, grouped labels/help and embedded InformationType definitions/columns. Visibility rules only reference an earlier active field in the same form. Multiple-cardinality information types require active columns; duplicate information types in a form disallowed. Form and field updates increment version, invalidate definition cache, and audit; removal is soft-status based in inspected methods. Cache is tenant/event-type scoped, TTL 60 sec, version-based invalidation; GAS CacheService/LockService dependencies.
- V2 requirements: explicit workflow draft/publish immutable versions, version-bound rules; relational event-type/form mappings; versioned form-definition snapshots; visibility-rule validation; support cardinality and columns; tenant-scoped cache invalidation; soft deletion; audited changes. Form definition cache is optimization only.
- Source inspection only. Workflow service runtime and complete route mapping, dynamic form payload schemas and full validation remain pending. Task 0.2 remains IN PROGRESS.


### Task 0.2 — Material planning and procurement audit (2026-11-21)
- MaterialPlanningService maintains versioned project specifications (revision supersedes prior active spec), tenant/project validation, audited changes, and calculation history. CalculationKey replay detection makes recorded calculations idempotent. Calculations capture basis quantity, source event/version, consumption, wastage, prior quantity and variance.
- Confirmed-quantity recalculation is lock-guarded; it selects active project specs, recalculates per spec, records calculation history, and creates/updates MaterialNeeds keyed to spec revision/source/basis. Prior active needs are superseded when recalculated. Source-event voiding marks calculation history SOURCE_EVENT_VOIDED and affected confirmed needs NEEDS_REVIEW with audit; it does not automatically erase procurement history.
- ProcurementService uses tenant/project visibility filtering and request-level context reuse. Order creation uses ScriptLock, optional tenant/user-provided IdempotencyKey replay and validates the complete order/allocation graph before writes. Allocations are constrained by order-item quantity, tenant, project and need references; existing allocations/orders are considered when deriving remaining need and legacy surplus. Shared orders and surplus quantities are explicitly handled.
- Receipt creation and batch receipt routes exist in service (source names inspected), but detailed receipt constraints, batch atomicity and partial failure handling require deeper review. The observed dashboard aggregates receipt quantity, caps received display at ordered quantity, computes pending quantity and surplus metrics.
- V2 requirements: spec revision history; immutable calculation snapshots and durable idempotency; transaction/locking strategy; explicit allocation constraints and shared procurement; surplus distinction; receipt ledger and over-receipt/duplicate protections; NEEDS_REVIEW reconciliation after voided source; audit/outbox; tenant/resource visibility.
- Source-only review. GAS ScriptLock and GoogleSheetsRepository writes are non-transactional; the audit does not establish atomicity under failure. Task 0.2 remains IN PROGRESS.


### Task 0.2 — Reporting, Dashboard, Attachment storage and Integration audit (2026-11-21)
- ReportService provides tenant-scoped project lists and project reports; project report composes procurement payload where available. Dashboard aggregation builds a visible-project index and filters events/attachments/users/departments/workflows/event types by tenant; aggregation optimized to avoid repeated project scans. Full report payload contract/export and route matrix remain pending.
- AttachmentService checks tenant and project/event visibility; gallery/count endpoints return bounded safe metadata and exclude deleted/void records. Storage keys and binary internals are intentionally not exposed in metadata responses. Upload derives uploader identity from authenticated user, stores provider/key metadata, audits, and attempts cleanup if metadata write fails. Copy/delete use Drive adapter. Metadata and Drive lifecycle are non-atomic; V2 requires storage abstraction, transactional metadata/audit, outbox cleanup, retries/reconciliation, access-controlled signed URLs/streaming, MIME/size validation.
- StorageRepository is a thin adapter to GoogleDriveStorage; current provider recorded as GOOGLE_DRIVE. V2 must isolate provider choice from domain entities and use private storage.
- TelegramBotService uses Script Properties for secrets/config, Apps Script polling/webhook machinery, tenant chat mapping, time-limited pairing, tenant-scoped notification formatting and attachment filtering. Notifications are integration concerns; V2 should use a separate adapter, secret management, job queue/outbox, retry/backoff, dead-letter observability and no secret exposure.
- AuditService writes tenant/user/action/entity metadata with old/new data as applicable and supports tenant-scoped reads by entity/user. Append-only enforcement and atomic coupling to business operations are not proven in source-only audit. V2 transactional audit plus immutable retention controls are requirements.
- Source-only review, not runtime/security tests. Task 0.2 remains IN PROGRESS.


### Task 0.2 — Additional repository/test inventory (2026-11-21)
- Recursive repository tree snapshots confirmed 187 frontend entries and 153 backend entries; identified feature-test inventories spanning UI workflows, contracts, access control, storage, procurement, reports, workflow and integrations.
- Existing production CI/automation workflows were inventoried for context only; none were run or modified as part of the read-only audit.
- Test filenames were inventoried only; no tests were executed. No production writes, PRs, deployments, spreadsheet/Drive access or secrets access.
- Remaining gaps: complete route/permission and request-response mapping; exhaustive relationship/unique-key mapping; remaining receipt/report/print edge cases; migration validation and rollback blueprint. Task 0.2 remains IN PROGRESS.

### Task 0.2 — Route authorization sampling (2026-11-21)
- Read-only extraction of Router permission branches confirmed a mixture of single-permission checks, any-of permission sets, and admin-only checks across major API families.
- Sampled GET and mutation route families were inventoried. This is not a complete route-to-service permission matrix; service-level checks, route overlap/order, request contracts and tenant boundary regression tests remain to be reconciled.
- Architecture risk recorded: branch-based authorization policy is difficult to exhaustively verify; V2 should use declarative centralized policy plus domain/service checks and automated route coverage. Source review only; no test or runtime security verification.


### Task 0.2 — Response contract and schema header cross-check (2026-11-21)
- Read shared response/error helpers and cross-checked database sheet header definitions. Response contract is an envelope with success/data/error/meta; known coded errors are surfaced and unknown errors generic, but possible message leakage in WebApp-level handling requires further reconciliation.
- Schema header inventory reconfirmed all major domains including information history, event-form snapshots, workflow rules, reference sync, material specifications/calculations, purchase allocations/receipts and procurement-event settings.
- Constraints, uniqueness, foreign keys, idempotency and transaction semantics remain a discovery/design exercise; source/header review only. No live sheet data or reset functions touched.


### Task 0.2 — material planning, procurement and workflow source audit (2026-11-21)
- Reviewed service source for MaterialPlanningService, ProcurementService, ProcurementEventIntegrationService and WorkflowService.
- Findings: revisioned material specs and calculations with idempotency keys; recalc source event/version coupling; procurement order creation locking/idempotency replay and shared-item allocation/surplus; event integration requires confirmed quantity central info linked to same event; workflow version/draft-publish semantics.
- V2 design implication: transaction-backed versioning, explicit source linkage, durable idempotency, DB constraints for allocation/receipt quantities, concurrency-safe recalc, immutable published workflow versions. Read-only source review; no runtime tests or production changes.


### Task 0.2 — project information, deletion, reporting and print-template audit (2026-11-21)
- ProjectInformation: ExpectedRevision, revision/history, event-linked idempotent behavior, approvals bound to current revision, and source/target revision checks for reference conflict resolution.
- Project deletion: dependency preview and explicit confirmation, tenant/admin scope, preserve shared PO/item dependencies, soft-delete/VOID/physical deletion mixed semantics, Drive trash and partial failure reporting. V2 must avoid non-atomic cascades with transaction/outbox and reconciliation.
- Reports: tenant-visible project index, exclusion of VOID/CANCELLED events and tenant-filtered enrichments.
- Print templates: tenant-scoped, validated definitions, version increment, dynamic resolution, asset cleanup/reference checks, soft delete.
- Project statuses configurable and tenant-scoped. Prefix numbering in Sheets is not a reliable key convention; V2 uses explicit table identifiers.
- Source review only; no runtime tests, no production changes.


### Task 0.2 — validator and repository audit update (2026-11-21)
- Project/Event/Form/Information Type/Workflow/Workflow-step-event rule validators reviewed. Extracted limits, enum rules, code patterns, project quantity/department constraints, dynamic field validation, PROJECT_CODE special invariant, and rule sequencing/group uniqueness.
- Repositories inspected: ProjectRepository, EventRepository, ProcurementRepository; thin repository functions and tenant filtering observed, while integrity depends primarily on service layer due Sheets lacking FK/unique/check constraints.
- V2 migration candidates: tenant-scoped unique indexes, relational FK, check/enum constraints, composite indexes, explicit cascade/restrict rules and transactions.
- Source-only; no runtime testing and no production changes.


### Task 0.2 — API route inventory (2026-11-21)
- Extracted Router route families covering auth, admin, projects/central information/material planning, workflows, templates, forms, information/event types, events, attachments, users/roles/permissions, audit, dashboard, procurement and reports.
- WebApp request handling: per-request cache reset, route-access guard, Router dispatch for GET/POST/PUT/DELETE, method override support, token can be parsed from query/body. V2 should use header/cookie authentication, explicit methods and sanitized generic errors.
- Standard response envelope confirmed. Generic WebApp error response may leak error message; verification pending.
- Permission-route matrix and full endpoint contract still in progress; no runtime tests/no production changes.


### Task 0.2 incremental notes (2026-11-21 — report/dashboard and print-template deeper trace)
- Project report composition is tenant/visibility scoped, excludes void/cancelled events, and assembles project snapshot, event history and selected-project procurement needs/calculations/orders.
- Dashboard source uses visible-project indexing and grouped activity aggregation. Procurement workspace/reporting models shared order-item project associations through allocations and receipts; orders with inaccessible linked projects are filtered as a whole.
- Print-template service provides tenant-scoped library/revision/source/resolve/preview and versioned definitions/assets; exact endpoint payload/permission map remains pending.
- Remaining discovery priorities: exhaustive route-permission-contract reconciliation, schema/header/identifier constraints dictionary, edge-case matrix, and migration/validation blueprint. Source-only; no runtime tests or production changes.


### Task 0.2 — Router permission and mutation trace update (2026-11-21)
- Re-fetched Router.js and sampled GET/POST/PUT/DELETE dispatch. Identified mixed permission patterns: direct guards, any-of sets, admin-only and draft-project creator exception with tenant/state/creator validation.
- Permission codes sampled include ATTACHMENT_DELETE/UPLOAD/VIEW, DEPARTMENT_MANAGE, EVENT_CREATE/EDIT/TYPE_MANAGE/VIEW, PERMISSION_MANAGE, PROCUREMENT_MANAGE/VIEW, PROJECT_CREATE/EDIT/STATUS_MANAGE/VIEW, REPORT_VIEW, ROLE_MANAGE, SYSTEM_SETTINGS, USER_CREATE/EDIT/VIEW, WORKFLOW_MANAGE. This is not yet exhaustive mapping.
- Mutation route families and soft-delete/void/admin-confirmation semantics recorded. Potentially sensitive print-template project-data binding PUT branch requires service-level authorization review; no bypass claim made.
- V2 policy design: declarative route/action policies, explicit any-of/all-of/admin semantics, domain-level tenant/creator checks, exhaustive endpoint permission coverage and IDOR regression tests. Source-only, no runtime tests/no production changes.


### Task 0.2 — Data Dictionary and preliminary migration blueprint checkpoint (2026-11-21)
- DatabaseSetup.js header inventory re-read and cataloged across tenant/auth, projects/workflows/events, forms/central information, templates/files/audit, material planning and procurement.
- Identifiers are prefixed strings; JSON payload/configuration and revision/version/idempotency metadata are central migration concerns. Schema sheet headers are not a substitute for checking actual data drift.
- No DB-native FK/unique/check constraints found in the source storage design; V2 should establish these explicitly.
- Preliminary migration principles recorded: preserve IDs as text, stage immutable exports, dependency-order loads, validate counts/relations/JSON/revisions/procurement totals, storage checksums and controlled cutover/rollback; no production data access or transfer performed.
- Exact field type/nullability, actual data counts, orphan/duplicate and legacy drift analysis remain pending safe discovery/export and V2 migration design.


### Task 0.2 — relationship edge-case matrix checkpoint (2026-11-21)
- Recorded relationship and migration risk matrix for project/event history, workflow versioning, event form snapshots, central-information revisions/reference synchronization, attachments and storage reconciliation, material specification/calculation/need lifecycle, shared procurement allocations/receipts/surplus, and audit chronology.
- Validation gates include tenant-bound relation checks, orphan/duplicate detection, version/snapshot consistency, cardinality/revision validation, file checksum reconciliation, material quantity recomputation, shared procurement totals, and preservation of void/inactive/archive semantics.
- This matrix is a planning artifact based on source audit; data-level validation, runtime tests and migration dry-run are pending. Production remains untouched.


### Task 0.2 — authorization end-to-end trace checkpoint (2026-11-21)
- Investigated PUT print-template project-data binding route: Router dispatch lacks a direct permission call, but service/domain branch gates were confirmed. Project fields/central-information mutations require PROJECT_EDIT; event-form/event-field mutations require EVENT_EDIT. Service also tenant-scopes template/project, active template, validates binding ID/source and rejects non-LATEST selection; optimistic source conflict is checked.
- Conclusion: no bypass claim; the matrix must capture source-specific service-level permission requirements rather than Router-only guards.
- AccessControlService admin role bypass and tenant-scoped active role permission resolution/request caching reviewed at source level. No runtime/penetration testing.
- Next: continue exhaustive endpoint/service permission matrix and security test planning, including cross-tenant IDOR, visibility, token/session and file-access tests.


### Task 0.2 incremental notes (2026-11-21 — authentication/session/attachment lifecycle trace)
- AuthService source review confirms login searches matching username globally and fails closed unless exactly one matching user exists; status is checked; password verification uses SHA-256 of the raw password with no salt/work-factor visible in this implementation. User creation/update also hashes via the same routine. This is a critical V2 migration concern: implement modern password hashing (Argon2id or bcrypt), and plan credential migration/reset without exposing or reusing legacy hashes.
- SessionManager generates opaque UUID-derived tokens and stores session records in Script Properties. Session expiry is configuration-driven; expired sessions are removed when checked. Last-seen persistence is throttled (60 seconds). Admin session listing/revocation is tenant-scoped and uses hashed session IDs for display/revoke lookup; current session revocation is blocked in that admin path. V2 should use durable session storage, revocation, secure token transport and lifecycle policy.
- RequestParser accepts token fields via query/body/Authorization-like parameter, and method override via query/body. WebApp's request lifecycle resets several request caches and routes through shared access guard before Router. Query token transport risks URL-history/log exposure; V2 should use headers/cookies and standard methods only.
- UserService source validates tenant and department ownership on creation/update, derives TenantID from current user, and sanitizes list/detail responses. Username uniqueness is checked in service but not enforced by a DB constraint; legacy login fails closed on ambiguity. V2 must enforce normalized unique indexes and validate department/user references transactionally.
- AttachmentService source trace: upload validates entity and tenant/project visibility, derives uploader from current user, stores file before metadata and audit; failure windows exist between storage, metadata and audit. Download/thumbnail checks tenant, visibility and active status. Delete calls storage deletion before metadata status update/audit, creating a non-atomic failure window. V2 should use durable metadata transactions plus storage outbox/reconciliation, private object storage and access-controlled download.
- AuditService serializes old/new payloads into audit records and provides tenant-scoped reads; source does not establish append-only enforcement or transactional coupling. Sensitive fields in audit payloads require explicit redaction/retention policy in V2.
- Source-only trace; no passwords, tokens, session records, user data, runtime tests or operational configuration accessed. Production repositories remain unchanged.


### Task 0.2 incremental notes (2026-11-21 — department, role, assignment and event-data service review)
- EventDataService historical-data retrieval checks authenticated user, event tenant, and project availability for project-linked events before reading payload; malformed JSON/non-object payload is rejected with a generic data-read error. This path appears read-only and tenant/visibility scoped in the inspected code.
- DepartmentService list/get/create/update derives tenant from current user and tenant-scopes read/update; create/update checks tenant-scoped case-insensitive department-code duplicates at service level, validates input and audits. No database-level uniqueness is available; update audit follows the write and therefore is not atomic.
- RoleService list/get/create/update similarly tenant-scopes reads and mutations and checks case-insensitive tenant-scoped role-code duplicates at service level; service source inspected did not show its own permission guard in these methods, so Router authorization must be verified for each route rather than treating service access alone as proof of protection.
- UserRoleService assign/remove/listByUser validates user and role tenant alignment, active role for assignment, duplicate assignments and tenant scope; it records audit after mutation. Authorization guard visibility is mixed/not demonstrated in these service methods and remains a Router-to-service reconciliation item. RolePermissionRepository batch operations use script lock for insert/deleteMany and invalidate cache; relation writes themselves are not database-transactional with audit/permission-cache state.
- ProjectStatusService uses admin bypass or PROJECT_STATUS_MANAGE for context; reads are tenant-filtered and active duplicate names are checked, but direct sheet append/update paths and audit remain non-transactional. Route policy reconciliation and race-safe uniqueness remain V2 requirements.
- EventTypeService list/get filters event types by tenant, status, department and allowed users; management operations require EVENT_TYPE_MANAGE and validate referenced users/departments/workflow steps/forms/statuses. Duplicate event-type codes are checked within tenant, though storage constraint and concurrency enforcement are absent. Validation of project status reads directly from sheet 18_ProjectStatuses; V2 should replace direct storage access with repository/domain validation.
- These findings are source-level observations only; route guards, full method contracts, runtime tests and security tests are not exhaustively reconciled. No production modifications or live data access.


### Task 0.2 incremental notes (2026-11-21 — workflow policy and router/service reconciliation)
- Router trace confirms workflow-management bootstrap requires WORKFLOW_MANAGE; workflow list allows any of WORKFLOW_MANAGE, PROJECT_CREATE, SYSTEM_SETTINGS; workflow read/management-data/rules routes have differentiated guards, including WORKFLOW_MANAGE for management data and rules, and PROJECT_VIEW or WORKFLOW_MANAGE for step reads. Workflow detail path is guarded by WORKFLOW_MANAGE. Service methods also tenant-check workflow, step, rule and department references.
- WorkflowService source checks TenantID for workflows and versions/steps/rules. Publish validates active steps and active event type references against tenant. Version cloning and draft/published state logic present. Several create/update/step mutation methods do not visibly log Audit in inspected snippets; publication and rule updates do. This requires exhaustive mutation-to-audit mapping, not a blanket conclusion.
- WorkflowStepDepartmentService selected paths tenant-check workflow and active departments/steps, and validates assignment batches (limit 100). Route guard reconciliation remains underway; service source itself does not show a direct permission guard for selected assignment operations.
- AccessControlService isAdmin resolves active tenant roles with SYS_ADMIN/ADMIN codes; require bypasses permissions for admin otherwise delegates PermissionService. Permission resolution uses tenant-scoped user-role and active roles and caches per request. Router enforceRouteAccess is a shared authentication gate but returns true after authentication; specific permissions are applied in Router route branches. Thus route-specific guard coverage is critical. PermissionService.list/get source authenticates but does not visibly require PERMISSION_MANAGE; Router branches shown apply guards on selected routes.
- No runtime authorization tests executed; inspected route/service excerpts only. No production changes.


### Task 0.2 incremental notes (2026-11-21 — user/role permission mutation route audit)
- Router trace: GET /users requires USER_VIEW; GET /users/:id/roles requires USER_EDIT; GET /roles/available-permissions and GET /roles/:id/permissions require ROLE_MANAGE; GET /roles permits ROLE_MANAGE or USER_EDIT; GET /permissions requires PERMISSION_MANAGE.
- POST /users requires USER_CREATE; POST /users/:id/roles requires USER_EDIT; POST /roles/:id/permissions requires ROLE_MANAGE. PUT /roles/:id and DELETE role-permission relationship require ROLE_MANAGE; PUT /users/:id and DELETE /users/:id/roles/:roleId require USER_EDIT.
- RoleService create/update tenant-scope roles, validate tenant duplicate codes and audit mutations; create/update service source has no internal permission guard (Router guards selected mutations). UserService tenant scopes get/update, derives tenant on create, validates department ownership and sanitizes response, audits mutations. Username uniqueness is service-level and global. Password hashes stored, but algorithm source remains SHA-256 as previously noted.
- UserRoleService validates target user/role tenant match, active role and duplicate assignment; list/removal tenant-scoped and mutations audit. RolePermissionService implementation located in repository-style file; bulk insert/delete uses ScriptLock, sheet operations and cache invalidation; detailed service-level authorization/tenant validation for every relationship operation still needs further trace. Router mutation guard is ROLE_MANAGE.
- Router mappings were source inspected; no live auth tests were performed. Production repositories remain read-only.


### Task 0.2 incremental notes (2026-11-21 — projects/events/attachments lifecycle audit)
- ProjectService source reviewed: project reads and access are tenant-scoped plus visibility lists (admin/creator/allowed department/user); drafts are creator-scoped in list logic. Create validates tenant workflow published state, steps, departments, allowed users, statuses and repeat-project source visibility. Project-code duplicate check is lock-guarded in create. Project update route has a draft-creator exception requiring PROJECT_CREATE after PROJECT_EDIT fails. Full endpoint map remains pending.
- ProjectDeletionService: admin-only, tenant-scoped, preview collects dependent events/event data, needs, purchase items/orders/allocations/receipts, specs/calculations and attachments. Shared orders/items are protected against deleting other-project allocation dependencies. Deletion uses per-row soft-delete/void with partial-failure tracking, trashes Drive files, but after dependency success physically deletes project row. Audit occurs after multi-step mutation; not atomic. Partial failure can leave a mixed state; recovery/reconciliation needs explicit design in V2.
- EventService void: tenant and project-visibility checks, event-type AllowVoid, department restriction unless admin, later cross-department event protection, snapshot-based state restoration guard, project restoration and event VOID plus audit; material needs reconciliation attempted and failures separately audited. Mutations not transactionally atomic in Sheets, potential intermediate inconsistency.
- AttachmentService: tenant/entity visibility and project visibility enforced for project/event attachments; upload validates and stores file before metadata insert/audit (non-atomic); download checks tenant, visibility and deleted status; thumbnail checks deleted/void and image MIME; delete storage removal precedes metadata update/audit (failure/partial-state risk). Storage keys are internal to metadata endpoints reviewed; V2 should use signed/streamed delivery and transactional metadata/outbox cleanup.
- Router trace sampled: POST /events EVENT_CREATE; event/project attachment uploads ATTACHMENT_UPLOAD; GET attachments ATTACHMENT_VIEW; DELETE attachment ATTACHMENT_DELETE; event void PUT route EVENT_EDIT; admin project deletion ADMIN check. No runtime testing, no production modifications.


### Task 0.2 incremental notes (2026-11-22 — project/event/attachment route map)
- Expanded Router source mapping: GET `/projects` uses PROJECT_VIEW or PROJECT_CREATE; GET project by ID uses PROJECT_VIEW with a narrow creator-owned draft exception (same tenant, RecordState DRAFT, CreatedBy match, then PROJECT_CREATE); project information/history routes use PROJECT_VIEW; project event/details routes additionally EVENT_VIEW; project attachments ATTACHMENT_VIEW; specs/calculations reads allow PROJECT_VIEW or PROJECT_EDIT. Project gallery/counts combine project/event visibility with ATTACHMENT_VIEW. Project form lookups require PROJECT_CREATE.
- Event read paths: GET `/events` EVENT_VIEW; `/events/:id` and forms/data paths share EVENT_VIEW guard; event attachment listing requires ATTACHMENT_VIEW. Service checks should still validate tenant/project visibility, as described earlier.
- Attachment retrieval: GET `/attachments` and `/attachments/:id/{thumbnail|download}` require ATTACHMENT_VIEW; Service additionally validates tenant, entity visibility, status. DELETE `/attachments/:id` requires ATTACHMENT_DELETE and Service visibility. Project/event uploads require ATTACHMENT_UPLOAD and Service validates entity/project visibility.
- Project deletion: GET admin deletion-preview path and DELETE `/admin/projects/:id` rely on Admin resolution; deletion confirmation code is passed into ProjectDeletionService. Admin sessions are separately admin guarded.
- Route mapping is partial/source-based; all API methods, unknown fallthroughs, query parser, and service authorization/audit coverage still need exhaustive reconciliation. No runtime or penetration tests.


### Task 0.2 incremental notes (2026-11-22 — migration source/repository audit)
- Read-only inspection of DatabaseSetup.js and ProjectRepository, EventRepository, AttachmentRepository plus GoogleSheetsRepository confirms repositories are thin wrappers over Google Sheets and do not provide relational constraints/transactions. EventRepository finder methods load broad/all rows then filter/sort in memory, illustrating migration extraction/performance considerations (not a verified runtime bottleneck).
- DatabaseSetup headers show IDs and relationships are stored as string fields across sheets. Project record has workflow version, creator, quantity, current step/status, department/user access lists, SourceProjectID, RecordState, reference-copy policy/status/operation fields. Event record retains workflow and event-type versions, Performer/Creator/Department, OccurredAt/CreatedAt, state transition snapshots, variant details and idempotency markers. Attachment record retains provider/key metadata, status/type and SourceAttachmentID.
- Migration inventory implications: extract headers as schema metadata, retain raw JSON fields and source IDs, preserve status/history/idempotency snapshots and source attachment metadata; reconcile header drift and row-level anomalies. Source files inspected only; no live spreadsheet data or counts accessed. No production changes.


### Task 0.2 incremental notes (2026-11-22 — schema header consistency and status refresh)
- Further source inspection of DatabaseSetup definitions confirms material/procurement entities, workflow step-department assignments, event-type procurement integration settings, workflow-step event rules and project-reference sync log are represented in the source schema. Raw JSON and revision/idempotency fields are migration-critical.
- A naming collision in sheet numbering is observed: both MaterialNeeds and ProjectStatuses are defined as `18_...` sheet names. They are distinct literal sheet names (`18_MaterialNeeds` and `18_ProjectStatuses`), so not a runtime sheet-name collision, but numeric prefix conventions cannot be treated as unique ordering/identity metadata.
- Router source snippets further confirm guarded reads for workflows/forms/information-types/event-types/events/attachments/users/roles/audit/print templates/procurement, but this remains a sampled route audit. Mutation mapping, parser/fallback handling and exhaustive authorization review still open. No runtime tests or live-data inspection.


### Task 0.2 incremental notes (2026-11-22 — HTTP ingress/parser and error handling)
- Read-only review of RequestParser.js and WebApp.js: request cache reset, parser/dispatch flow, opt-in admin GET diagnostics, event-creation diagnostic response behavior, token query/body handling, method override, path normalization, JSON object validation, and unsupported-method handling documented in ARCHITECTURE.md.
- Error response source currently exposes error.message even for unknown exceptions (with INTERNAL_ERROR code); V2 must sanitize unknown errors and retain secure structured logs. Parser-level size/content-type enforcement not established from inspected files and remains pending verification.
- No runtime tests, live records, secrets, or production changes. Route matrix and test coverage remain incomplete.


### Task 0.2 incremental notes (2026-11-22 — mutation route inventory)
- Source inventory of Router POST/PUT/DELETE branches was expanded and categorized in ARCHITECTURE.md: print templates, project/status, departments, roles/permissions, procurement/orders/receipts, materials/specifications, workflows/steps/department assignments/rules, forms/information types, event types/events/event void/form responses, attachments, users/user-role assignments, project information and admin-only project/session actions.
- The inventory records guard categories but is explicitly partial reconciliation: resource/service authorization and audit coverage remain to be checked; it is not runtime/security proof. No production changes or tests.


### Task 0.2 incremental notes (2026-11-22 — audit-log consistency and mutation write patterns)
- Read-only AuditService review: stores tenant/user/action/entity/reason/time and JSON OldData/NewData; list paths tenant-filter results. Append-only enforcement, redaction and retention are not established.
- Selected department, event-type and project-status CRUD traces show business writes followed by audit writes, without transaction coupling in Sheets. Audit-write failure may leave unaudited mutation; broader audit coverage remains unverified.
- Read-then-write duplicate checks for tenant-scoped codes/names remain race-prone without database unique constraints. V2 should enforce constraints and transactions.
- Documented requirements: append-only audit, explicit sensitive-field redaction, retention/access policy, transaction coupling, correlation IDs and outbox/reconciliation for external side effects. Source-only; tests not run and production untouched.


### Task 0.2 incremental notes (2026-11-22 — configuration, workflow assignment and multi-record service review)
- FormService and InformationTypeService: tenant context/resource checks and sampled CRUD audit calls; form field/mapping mutations update related version/cache metadata and are multi-write sequences. Information-type structure changes/deactivation are guarded against active use. Permission reconciliation remains route-dependent/incomplete.
- WorkflowStepDepartmentService: assignment validates tenant/workflow/active steps/departments and batch size/duplicates; remove soft-deactivates. Explicit service-level permission and audit calls were not evident in inspected methods (route guards may apply); batch fallback inserts can partially succeed.
- UserRoleService: tenant alignment, active-role and duplicate checks; write-then-audit pattern, not atomic in Sheets.
- ProjectInformationService: revisions/history/audit and reference sync conflict logs; multi-row sync/resolution needs transactions/idempotency/recovery.
- MaterialPlanningService and ProcurementService: version/idempotency patterns, project/tenant visibility and multi-record mutation/reconciliation needs documented; order creation uses a script lock but does not provide transactional guarantees.
- Source-only, sampled inspection; no tests or production changes. Route-level permissions, exhaustive audit coverage and runtime verification remain pending.


### Task 0.2 incremental notes (2026-11-22 — event/workflow execution trace)
- Selected EventService transition helpers traced: target-step resolution validates tenant/workflow and active step-department assignment; strict paths reject invalid mappings. Draft projects are exempt from configured project lifecycle changes in the inspected helper.
- Event later-record/void checks use tenant/project scope, timestamps, and exclude VOID records. Restoration is conditional on expected current-state/snapshot consistency rather than unconditional overwrite.
- WorkflowService management bootstrap and management-data paths tenant-filter workflows, departments, active project event types, active information types, assignments and workflow-version-scoped steps.
- This incremental source trace does not complete exhaustive route/service authorization, event mutation, sequencing, integration or performance audit.

### Task 0.2 checkpoint limitations (2026-11-22)
- The audit remains source-only: no live Google Sheets/Drive data, user records, deployed configuration or runtime telemetry were accessed.
- Pending major areas: exhaustive route-to-service authorization matrix; full integration inventory beyond Telegram and the sampled procurement-event integration; test inventory-to-execution and coverage mapping; baseline data volumes/performance; migration anomaly and cutover dependencies; complete event/project/workflow relationship and mutation coverage.
- No tests were run; production repositories and production systems remain untouched.


### Task 0.2 incremental notes (2026-11-22 — selected GET route permission trace)
- Mapped sampled GET router gates for project, workflow, form/information-type, event, attachment, user/role/permission, audit/report and procurement branches. Detailed permission categories and nested access gates recorded in ARCHITECTURE.md section 42.
- Router gate review is not proof of service-level authorization/resource ownership or exhaustive coverage. Reconciliation of all route methods and nested paths remains pending.
- V2 authorization design requirement: consistent policy enforcement with tenant/resource scope, explicit any-of/admin/Draft exceptions, and automated tests; do not trust frontend checks.


### Task 0.2 incremental notes (2026-11-22 — selected mutation route trace)
- Reviewed selected POST/PUT/DELETE branches in production Router read-only. POST categories include project/event creation, workflow configuration/publish/assignments, project-information mutations, forms/catalogue, procurement receipts/orders, attachment upload, users/roles and template media. PUT includes configuration revisions, project update (Draft creator exception), event form amendments/void and user updates. DELETE includes soft-deactivation, relationship removals, attachment removal, admin project deletion and session revocation.
- Permission categories and notable Draft/admin/resource-scoping behavior are recorded in ARCHITECTURE.md section 44.
- No production changes or tests. Endpoint exhaustiveness, underlying service/resource authorization, mutation atomicity and audit coverage remain pending verification.


### Task 0.2 incremental notes (2026-11-22 — audit/service review)
- Read-only inspection of AuditService.log/list/listByEntity/listByUser and selected mutation service files (FormService, WorkflowService, ProjectInformationService, ProcurementService, AttachmentService, EventService, PrintTemplateService).
- Audit log creation requires tenant/action/entity fields, serializes OldData/NewData and writes through AuditRepository; sampled list operations filter by tenant. Redaction, append-only enforcement, payload bounds, retention, indexes and transaction coupling were not established.
- Service-level source has multi-step workflows and audit/locking markers, but their existence is not proof of complete coverage or atomicity. This remains source-review-only, no tests.


## Phase 0 — Task 0.2 Discovery Audit: consolidated readiness (2026-11-22)
- Status: READY FOR PRODUCT OWNER REVIEW; awaiting explicit acceptance before marking COMPLETE.
- Consolidated source audit, route permission traces, service/Audit observations, integration dependencies, test directories and historical CI verification evidence into ARCHITECTURE.md section 46.
- CI evidence checked (historical GitHub Actions runs): backend validate SUCCESS on 2026-11-19 commit 4a131ae; frontend-check SUCCESS on 2026-11-19 commit f1b667f. This does not constitute fresh test execution or acceptance.
- V2 repo readiness: Infra repo confirmed; frontend/backend/database V2 repos were not found in the checks and remain pending.
- Safety: production repositories unchanged; no live data, credentials, migration or VPS work.
- Product Owner explicitly accepted the consolidated discovery audit on 2026-11-22; Task 0.2 is now COMPLETE. This acceptance closes the audit task only, not Phase 0 or repository preparation.


### Task 0.2 acceptance (2026-11-22)
- Product Owner explicitly accepted the consolidated READ-ONLY audit and findings.
- Task 0.2 status changed to COMPLETE. Phase 0 remains IN PROGRESS because V2 repository preparation and Task 0.3 discovery boundaries/report remain outstanding.
- No production changes, live data access, tests, migration or deployment.

### Task 0.3 — Discovery report and target boundaries
- [x] Draft high-level current-system map from read-only source audit evidence.
- [x] Draft candidate migration scope, platform exclusions/replacement handling, domain boundaries and compatibility requirements.
- [x] Record unknowns and items requiring live-data discovery or business confirmation.
- [ ] Product Owner review and acceptance.

**Status:** IN PROGRESS — draft prepared for Product Owner review. No schema or implementation work authorized.

### Task 0.3 incremental note (2026-11-22)
- Draft discovery report added to ARCHITECTURE.md section 47.
- Report includes high-level system map, candidate scope, platform exclusions, provisional domain boundaries, compatibility/migration guardrails and pending discovery questions.
- This is a draft, not an accepted architecture or migration decision.

### Repository-preparation blocker update (2026-11-22)
- Rechecked repository availability; the three V2 repos remain unconfirmed.
- Available GitHub integration does not expose a repository-creation action in this session.
- Created Issue #1 to track the required user-side creation of private independent repositories: https://github.com/Saeidesl/PRSPRO2-INFRA-V2/issues/1
- Required names: PRSPRO2-V2, PRSPRO2BACKEND-V2, PRSPRO2DATABASE-V2. No forks of production repos; no secrets or production data.
- Phase 0.1 remains blocked pending creation/access confirmation.


### Repository access verification (2026-11-22)
- [x] All four repositories accessible under Saeidesl; Admin/Push confirmed.
- Initial public visibility finding for the three implementation repositories was superseded by the privacy verification below; all three now Private.


### Repository privacy verification (2026-11-22)
- [x] Rechecked GitHub metadata for PRSPRO2-V2 — Private.
- [x] Rechecked GitHub metadata for PRSPRO2BACKEND-V2 — Private.
- [x] Rechecked GitHub metadata for PRSPRO2DATABASE-V2 — Private.
- Infra repository remains public; it contains governance documentation only at this stage.
- Verified metadata, no source or secrets inspected/added in the privacy-check action.


### Repository privacy verification (2026-11-22)
- [x] PRSPRO2-V2 — Private.
- [x] PRSPRO2BACKEND-V2 — Private.
- [x] PRSPRO2DATABASE-V2 — Private.
- Infra repository remains public; it currently contains governance documentation only.


### Task 0.2 audit continuation (2026-11-22 — frontend auth/API transport)
- Re-read the legacy frontend shared API client and backend Router source in READ-ONLY mode. The frontend API client places the session token in the query string for all requests, including GET and POST-overridden mutations; it also uses method override parameters for PUT/DELETE. The backend parser accepts token query transport and method override semantics, confirming this is an end-to-end legacy contract rather than an isolated frontend detail.
- V2 API contract must avoid URL token transport; prefer secure HttpOnly/SameSite cookie architecture with CSRF controls if cookie-based, or Authorization headers where appropriate. Query-string secrets must not be logged, stored in browser history, or included in analytics. Method override should not be carried forward unless explicitly justified and hardened.
- The frontend client currently centralizes retries for GET/HEAD, GET deduplication, sync-state notification and diagnostics. Preserve appropriate UX behaviors in V2, but constrain retries to safe/idempotent operations and ensure diagnostics never expose sensitive payloads.
- This was a source-level slice only; no runtime/security testing or production changes. Task 0.2 remains READY FOR PO REVIEW but not COMPLETE until explicit acceptance.


### Task 0.2 audit continuation (2026-11-22 — sessions and authorization resolution)
- Source inspection verified tenant-bound role resolution, admin derivation from active tenant roles (SYS_ADMIN/ADMIN), request-scoped permission caching and service/Router permission checks. Generic route-access hook authenticates but intentionally does not itself enforce domain permissions; fail-closed centralized policies and resource-level scope validation remain a V2 requirement.
- Session source inspection verified Script Properties token-keyed storage, expiry/status checks, tenant/user consistency, and tenant-scoped admin listing/revocation (including protection from revoking the current session through that path). No tokens or live session records were accessed.
- Added V2 requirements for secure session storage, revocation/rotation, tenant-bound policies, cache invalidation and automated route authorization matrix tests. No runtime test or production change. Task 0.2 remains IN PROGRESS.


### Task 0.2 incremental notes (2026-11-22 — notification and storage recovery)
- Rechecked Telegram notification and attachment reference-copy source paths read-only; findings and V2 outbox/retry/idempotency, file metadata/storage reconciliation requirements are recorded in ARCHITECTURE.md.
- Telegram update offset persistence is not a durable notification delivery ledger; sampled attachment copy path has best-effort cleanup that can fail silently. Treat these as resilience/recovery requirements, not proof of a production incident.
- No secrets, live integrations, files, production data, runtime tests or writes to legacy repos were accessed.


## Phase 0 — Discovery & Repository Preparation
### Task 0.2 — READ-ONLY legacy PMS audit
- **Status:** AUDIT WORK CONCLUDED — awaiting Product Owner acceptance (not marked COMPLETE under protocol until acceptance).
- Reviewed and consolidated source findings covering architecture/repository relationships, workflow/event/form and snapshots, procurement/material planning, attachment and Drive lifecycle, Telegram integration, audit/logging, sessions/authentication/permissions, Router/API transport, service mutation paths, CI/test inventory and migration/recovery constraints.
- Consolidated route families and permission categories from Router.js; this is a coverage catalogue, not an exhaustively runtime-verified permission matrix.
- Documented risks/requirements and unknowns in ARCHITECTURE.md. Audit was read-only; no production data, secrets or live settings accessed; no production repository modified; no runtime tests or migration executed.
- Outstanding discovery items for later design/validation (not blockers to closing source-audit task): actual data-volume/capacity baseline; complete runtime authorization matrix; full integration credential/operations inventory; recovery objectives; migration rehearsal and validation.
- **Next:** present consolidated audit for PO acceptance. Do not proceed to implementation/schema/migration/deployment until the PO accepts the audit and roadmap step.


## STATUS RECONCILIATION — authoritative update (2026-11-22)

This section supersedes conflicting older checkpoint notes above and below it.

- **Task 0.1 — COMPLETE:** all four repositories verified accessible; three implementation repositories verified Private.
- **Task 0.2 — COMPLETE and accepted:** READ-ONLY source audit concluded to the documented scope; audit limitations remain follow-up QA/discovery items, not blockers to closure.
- **Task 0.3 — COMPLETE and accepted:** discovery report and provisional boundaries accepted by Product Owner.
- **Phase 0 — COMPLETE:** repository preparation, audit and discovery boundary tasks are closed. This does not authorize production modification, migration, deployment or real-data access.

### Phase 1 — Foundation & Architecture

#### Task 1.1 — Repository collaboration conventions
- **Status: IN PROGRESS** (work started 2026-11-22).
- Define repository separation, branching/PR, commit, review, CI, security and change-scope conventions in CONTRIBUTING.md.
- Establish CI baseline remains subject to actual repository/workflow inspection and validation; no tests are claimed here.
- **Acceptance required:** Product Owner review after document is verified on GitHub.

No application code, schema, migration, deployment or production system changes have been made.


## PHASE 1 STATUS RECONCILIATION — 2026-11-22 (authoritative)

This update supersedes earlier conflicting task-status notes in this historical file:
- Task 1.1 — Repository collaboration conventions: COMPLETE and accepted.
- Task 1.2 — CI baseline inventory/readiness review: COMPLETE based on read-only inspection and documentation; Product Owner directed continuation on 2026-11-22, treated as acceptance of the review. CI pipelines themselves are NOT implemented or tested.
- Task 1.3 — Architecture, API, security, logging and error-handling standards: IN PROGRESS. Draft ARCHITECTURE-STANDARDS.md created and verified pending Product Owner review.
- CI baseline documented in CI-BASELINE.md. Existing production workflows are READ-ONLY references.
- No application implementation, schema, migration, live data, deployment, VPS acquisition, or production changes.


## PHASE 1 STATUS RECONCILIATION — 2026-11-22 (authoritative)
- Task 1.1 — Repository collaboration conventions: COMPLETE and accepted.
- Task 1.2 — CI baseline inventory/readiness review: COMPLETE and accepted for inventory scope; pipelines not implemented.
- Task 1.3 — Architecture/API/security/logging/error standards: COMPLETE and accepted. Draft ARCHITECTURE-STANDARDS.md accepted as standards baseline; implementation is not implied.
- Task 1.4 — Domain boundaries and database migration strategy: IN PROGRESS. Draft DOMAIN-MIGRATION-STRATEGY.md created, pending review and acceptance.
- No schema, implementation, live-data access/export, migration, deployment, VPS acquisition or production-system change.


## PHASE 1 STATUS RECONCILIATION — 2026-11-22 (authoritative)
- Tasks 1.1–1.3: COMPLETE and accepted.
- Task 1.4 — Domain boundaries and database migration strategy: COMPLETE and accepted. Draft accepted as planning baseline only; no schema/migration implementation.
- Task 1.5 — Testing strategy and quality gates: IN PROGRESS. TESTING-STRATEGY.md draft created and pending review/acceptance.
- No application implementation, schema, live data access/export, migration, deployment, VPS acquisition or production-system change.


## PHASE 1 CLOSURE / PHASE 2 STATUS — 2026-11-22 (authoritative)
- Tasks 1.1–1.5: COMPLETE and accepted. Phase 1 Foundation & Architecture is closed as a planning/documentation phase; implementation of CI or architecture is not implied.
- Phase 2 Task 2.1 — Conceptual entity inventory and relationship consolidation: IN PROGRESS. DATA-MODEL-DRAFT.md created for review.
- This task is conceptual only. No SQL/schema, migration scripts, live data, deployment, VPS or production-system change.


### Task 2.1 incremental checkpoint — relationship matrix (2026-11-22)
- Added an evidence-aware conceptual relationship matrix to DATA-MODEL-DRAFT.md, distinguishing observed source links from design candidates and unknown cardinality.
- Added candidate transaction boundaries (hypotheses only) and a relationship-validation checklist for source-to-target mapping.
- Task 2.1 remains IN PROGRESS pending Product Owner acceptance. No executable schema or migration was created; no tests were run.


### Task 2.1 incremental checkpoint — Project/Event/Form focused model (2026-11-22)
- Expanded DATA-MODEL-DRAFT.md with focused conceptual aggregate notes for Projects, Events, Forms and workflow context; documented candidate constraints and explicit open questions.
- No columns, cardinality, schema constraints or behaviors were invented as final; details remain subject to validation against implementation.
- Task 2.1 remains IN PROGRESS and awaiting PO acceptance. No SQL, tests, live data or migration executed.


### Phase 2 / Task 2.1 checkpoint (2026-11-22)
- [x] Add conceptual identity/tenant/role/permission and session requirements to DATA-MODEL-DRAFT.md (provisional, source-backed where noted).
- [x] Add procurement allocation/receipt quantity invariants and unresolved policy questions.
- [x] Add file-storage metadata/association and audit lifecycle requirements.
- [ ] Consolidate conceptual inventory/relationship matrix and open questions for Product Owner review.
- Status: IN PROGRESS; no SQL schema or migration tooling.


### Phase 2 / Task 2.1 consolidation checkpoint (2026-11-22)
- [x] Consolidate conceptual domain dependency sequence.
- [x] Document candidate transaction boundaries and external side-effect/recovery patterns.
- [x] Consolidate cross-domain integrity invariants and schema design validation gate.
- [ ] Validate detailed source-to-target IDs/fields/cardinality and uniqueness scope.
- [ ] Resolve business policy questions and confirm workload/query baseline.
- [ ] Present consolidated draft to Product Owner for acceptance.
- Status: IN PROGRESS; executable schema is not started or authorized.


### Phase 2 / Task 2.1 — Source mapping checkpoint (2026-11-22)
- [x] Extract read-only legacy sheet headers for Project/Workflow/Event/Form domains from DatabaseSetup.js.
- [x] Add provisional source-to-target mapping and caveats to DATA-MODEL-DRAFT.md.
- [ ] Validate complete Event headers and service semantics, including snapshots, amendments and project-info history.
- [ ] Map procurement/material, identity/security, files/audit and print-template domains.
- Status: IN PROGRESS. Header-level mapping only; no live data or executable schema.


### Phase 2 / Task 2.1 — Material Planning & Procurement mapping (2026-11-22)
- [x] Extract source headers for material needs/specifications/calculations, procurement settings, purchase orders/items, allocations and receipts.
- [x] Targeted read-only review of ProcurementService paths for create-order, single/batch receipt and needs accounting.
- [x] Document provisional mappings, transaction/integrity candidates and unresolved policy questions.
- [ ] Confirm precision/unit conversion, surplus, returns, corrections, reallocation, status rules and uniqueness scopes.
- Status: IN PROGRESS. No executable schema/migration.


### Phase 2 / Task 2.1 — Identity and Authorization mapping (2026-11-22)
- [x] Extract identity, tenant, department, role, permission and assignment table headers.
- [x] Review targeted legacy authentication, access-control, permission-resolution and Router authorization excerpts.
- [x] Document V2 security design candidates, risks and unresolved tenant/identity policies.
- [ ] Resolve tenant membership, department scope, session migration, privilege model and resource authorization matrix.
- Status: IN PROGRESS. Mapping and security design are provisional; no implementation/schema.


### Phase 2 / Task 2.1 — Attachments, Audit, Print and Integration mapping (2026-11-22)
- [x] Extract header-level source mapping for Attachments, Audit Logs and Print Templates from DatabaseSetup.js.
- [x] Record configured legacy attachment entity/provider values and relevant permission catalog excerpts.
- [x] Document V2 candidate requirements and explicit unresolved behavior/policy questions.
- [ ] Validate dedicated service paths, attachment lifecycle and access, audit coverage/redaction, template definition semantics and integration delivery behavior.
- Status: IN PROGRESS. Mapping is header/config-level; no SQL, schema implementation, live data, secrets or migration.


### Phase 2 / Task 2.1 — Event, workflow, project-information and form mapping checkpoint (2026-11-22)
- [x] Retrieve and verify the complete event header inventory in read-only source.
- [x] Inspect targeted EventService, ProjectInformationService, FormService and WorkflowService excerpts.
- [x] Record source mapping observations and unresolved semantics in SOURCE-MAPPING-CHECKPOINT-2.1.md.
- [ ] Complete audit of event mutation/amendment/void, form snapshots, workflow publish/version, and project-information revision semantics.
- Status: IN PROGRESS; no schema or migrations.


### Phase 2 / Task 2.1 — Lifecycle semantics checkpoint (2026-11-22)
- [x] Review targeted event-void lifecycle and conditional project restoration.
- [x] Review project-information logical deactivation, revision history and reference-sync logging excerpts.
- [x] Review form-amendment replay/conflict handling and project-information synchronization excerpts.
- [x] Review workflow publication validation/version state and audit excerpts.
- [x] Record evidence and design implications in LIFECYCLE-SEMANTICS-CHECKPOINT-2.1.md.
- [ ] Validate complete registration/void/amendment/publish paths and recovery semantics.
- Status: IN PROGRESS; no schema/migration or tests.


### Phase 2 / Task 2.1 — Project/reference, workflow-version, form, and material-planning audit (2026-11-22)
- [x] Inspect project creation, workflow-version selection and repeat-project policies.
- [x] Inspect workflow draft-version cloning and version-specific rule edits.
- [x] Inspect form versioning, soft-delete, response validation and snapshot references.
- [x] Inspect material specification revisions, calculation history and event-void reconciliation.
- [x] Record observations in PROJECT-WORKFLOW-FORM-MATERIAL-CHECKPOINT-2.1.md.
- [ ] Complete source paths for copy recovery, form snapshot guarantees and material reconciliation resolution.
- Status: IN PROGRESS; no executable schema or migration.


### Phase 2 / Task 2.1 — Procurement, attachment, and reference-sync checkpoint (2026-11-22)
- [x] Inspect purchase-order creation, allocation validation, idempotency and partial-operation recovery.
- [x] Inspect receipt/batch receipt lock and idempotency behavior.
- [x] Inspect attachment validation, metadata, storage key abstraction and copy/delete paths.
- [x] Inspect project-information revisions, approval history, LIVE_SYNC conflict/idempotency logic.
- [x] Record evidence in PROCUREMENT-ATTACHMENT-REFERENCE-SYNC-CHECKPOINT-2.1.md.
- [ ] Continue source audit of remaining transitions/recovery and permission/data constraints.
- Status: IN PROGRESS; conceptual model still not presented for approval.


### Phase 2 / Task 2.1 — Identity, permissions and tenant isolation checkpoint (2026-11-22)
- [x] Inspect authentication/current-user and session lifecycle paths.
- [x] Inspect tenant-scoped role assignment and role-permission resolution.
- [x] Inspect route-level permission enforcement patterns and identify route-coverage audit as pending.
- [x] Inspect repository scoping patterns and tenant/project visibility considerations.
- [x] Record findings in IDENTITY-PERMISSIONS-TENANT-CHECKPOINT-2.1.md.
- [ ] Complete route-by-route permission coverage and remaining table/relationship audit.
- Status: IN PROGRESS.


- [x] Inspect GoogleSheetsRepository abstraction and core project/event/workflow/form/project-information/delete service paths at function-level.
- [x] Record repository/lifecycle findings in REPOSITORY-LIFECYCLE-AUDIT-CHECKPOINT-2.1.md.
- [ ] Exhaustively verify relation cardinalities, mutation idempotency, project copy recovery, delete/retention semantics and remaining table mapping.


- [x] Extract table registry from DatabaseSetup source and document structural naming/numbering concerns.
- [ ] Verify exact entity relationships and tenant/global ownership from service behavior, not just table headers.
- [ ] Investigate auxiliary backup sheet semantics before migration scope decisions.


- [x] Audit cascade deletion, procurement partial-failure recovery, form amendment replay/concurrency, and attachment deletion semantics at source level; document findings.
- [ ] Continue audit of procurement cancellation and receipt-adjustment/return paths, complete dependency inventory and retry semantics.


- [x] Inspect procurement state transitions, cancellation checks, receipt aggregate recalculation, and project-delete protection for shared procurement items; record missing explicit cancellation/adjustment paths as discovery items.
- [ ] Finish domain dependency and API contract inventory; prepare conceptual model and unresolved decision list.


- [x] Document provisional entity relationship map and API audit scope/permissions spot-check; capture remaining endpoint-contract and cardinality questions.
- [ ] Complete API endpoint and data-contract inventory, print-template resolver mapping and remaining relationship constraints before preparing the conceptual model review.


- [x] Audit print-template resolver source bindings, tenant-scoped resolution, asset lifecycle, and Router route families (partial mutation/API inventory); checkpoint PRINT-RESOLVER-API-CHECKPOINT-2.1.md.
- [ ] Complete remaining resolver source/output assembly and full HTTP route/mutation contract matrix.

- [x] Read-only audit of reference-project copy plan/replay semantics, dependency-safe project deletion, attachment access and audit-record shape; checkpoint REFERENCE-COPY-DELETION-AUDIT-CHECKPOINT-2.1.md.
- [ ] Complete reference-copy retry/rollback and attachment recovery/retention paths, audit coverage, auxiliary sheet, and API mutation matrix.


### Phase 2 — Task 2.1 final consolidation checkpoint (2026-11-22)
- Consolidated the conceptual entity inventory, relationship evidence, transaction boundaries, reliability requirements and unresolved policy gates in DATA-MODEL-DRAFT.md.
- The draft is prepared for Product Owner review; it is not an accepted schema blueprint.
- Task 2.1 remains IN PROGRESS until explicit acceptance. No schema/migration code or tests started.


## Phase 2 — Task 2.1 acceptance (2026-11-22)
- Product Owner explicitly accepted the consolidated conceptual model and review package.
- Task 2.1 is accepted/closed. Proceed to Task 2.2: detailed source-to-target field mapping and constraint matrix; this next task is read-only discovery and must not create schema/migrations until its review/approval gate.


## Phase 2 — Task 2.2 IN PROGRESS (2026-11-22)
- Began read-only field/constraint mapping using source registry and previously audited service behavior.
- Added a partial header-evidence matrix and explicitly marked unknowns in SOURCE-TO-TARGET-MAPPING-2.2.md. Further source verification is required; this is not final mapping or schema design.
- No SQL/migrations or production data access.


## Phase 2 — Task 2.2 final review checkpoint (2026-11-22)
- [x] Consolidate mapping by major domain/field families and source evidence.
- [x] Review logical relationships, tenant isolation, snapshots/versioning, idempotency and mutation boundaries.
- [x] Document source-supported constraints and explicit unknowns/policy gates.
- [x] Review receipt-path divergence, partial failure, attachment cleanup, deletion/reversal and authorization limitations.
- [x] Perform comprehensive consistency/safety review and add checklist/findings to SOURCE-TO-TARGET-MAPPING-2.2.md.
- [x] Keep SQL types/nullability/precision, exact DDL uniqueness/FK decisions, live-sheet drift, full permission audit and workload measurements gated for later validated design/QA.
- Status: IMPLEMENTED — READY FOR PRODUCT OWNER REVIEW. Task is not COMPLETE until acceptance.
- No schema, SQL, migrations, production data, secrets, tests, production modifications, deployment or VPS action.


## Phase 2 — Task 2.2 Product Owner acceptance (2026-11-22)
- Product Owner explicitly accepted the Task 2.2 comprehensive source-to-target mapping and review package.
- Task 2.2 is COMPLETE and accepted. Do not repeat it.
- Next task may begin only according to the Phase 2 roadmap and protocol.
- Acceptance does not approve schema/migrations, production data access, deployment or production changes.


## Phase 2 — Task 2.3: Field semantics & constraint validation (2026-11-22)
- Started read-only source validation of API field semantics, optional/required behavior, enums, JSON boundaries, tenant relationships and constraint candidates.
- Created `FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md` with task scope, work plan, initial evidence from EventValidator, InformationTypeValidator, FormValidator, EventTypeValidator, DatabaseSetup and selected MaterialPlanningService excerpt.
- Initial confirmed findings include optional Event.ProjectID (independent events), API-validator versus database-nullability distinction, configured information-type values/cardinality, visibility-rule shape, and event-type connection restrictions.
- Full domain coverage, persistence normalization, precision/rounding, unique/FK scopes, complete state transitions and evidence-backed review remain outstanding.
- Status: IN PROGRESS (partial evidence checkpoint). No SQL/schema/migrations, tests, live-data inspection, deployment, VPS or production modifications.

- [x] Inspect shared Validator.js semantics and selected GoogleSheetsRepository row-to-object persistence boundary; append findings to Task 2.3 checkpoint. (2026-11-22)

- [x] Inspect selected project, workflow/rule, user/role/department, attachment/audit and print-template validators; document additional evidence and caveats in Task 2.3 checkpoint. (2026-11-22)

- [x] Extend Task 2.3 read-only review into repository/service semantics: lookup/uniqueness candidates, event-data cardinality assumption, polymorphic attachments, audit JSON snapshots, active/history semantics for project information, and procurement numeric/integrity concerns. Findings recorded in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md. (2026-11-22)

- [x] Inspect material-planning specification lifecycle (latest revision resolution, key uniqueness check, revision guard, numeric ranges/defaults); document evidence and concurrency/precision caveats. (2026-11-22)

- [x] Inspect dependency-aware project deletion, event workflow/status transition logic and user-role assignment/removal semantics; record shared-dependency, fail-closed, draft transition and association-race evidence. (2026-11-22)

- [x] Inspect selected ProcurementService project visibility/numeric helper, ProjectInformationService attachment-reference and approval-revision logic, AttachmentService visibility/copy checks, and EventDataService historical read validation; document caveats and unresolved integrity boundaries. (2026-11-22)

- [x] Inspect procurement order and receipt write paths (locking, idempotency, allocation/receipt ceilings, batch normalization, status updates and partial-write recovery); record V2 transactional requirements and unresolved edge cases. (2026-11-22)

- [x] Inspect procurement allocation lifecycle visibility in service methods, allocation aggregation and current named tests; record the bounded absence of mutation/void methods in inspected service, VOID aggregate uncertainty, and unexecuted test-coverage gaps. (2026-11-22)

- [x] Inspect procurement route authorization and material-planning revision, recalculation, source-event-void, and CalculationKey replay behavior; record concurrency and reconciliation implications. (2026-11-22)

- [x] Reinspect receipt aggregate paths in ProcurementService: record the conditional VOID exclusion inconsistency across single receipt, batch receipt and needs-report aggregation (no VOID mutation path yet found). (2026-11-22)

- [x] Audit material-spec revision write ordering/partial-failure risk, calculation record fields/idempotency race, and event integration validation/apply boundaries (receipt integration remains inert). (2026-11-22)

- [x] Audit procurement service/router lifecycle method inventory and declared repository headers; document bounded absence of order/receipt/allocation mutation routes in inspected source and candidate V2 uniqueness/FK constraints. (2026-11-22)


## Phase 2 — Task 2.3 final review checkpoint (2026-11-22)
- [x] Complete bounded source validation across field semantics, validators, service/repository behaviors, tenant scopes, persistence representation, relationships, status/reversal behavior and constraint candidates.
- [x] Consolidate precision, temporal, JSON, polymorphic links, history, idempotency, transaction and uniqueness candidates/risks.
- [x] Cross-review findings against accepted Task 2.2 mapping and conceptual model; preserve unknowns and avoid unsupported assumptions.
- [x] Complete comprehensive consistency/safety review and document scope limitations and decision gates.
- Status: IMPLEMENTED — READY FOR PRODUCT OWNER REVIEW. Task 2.3 is not COMPLETE until explicit acceptance.
- No tests executed, live data inspected, SQL/schema/migrations created, production repository modified, deployment or VPS action.
- Await Product Owner acceptance before advancing to Phase 2 Task 2.4/schema design.


## Phase 2 — Task 2.3 Product Owner acceptance (2026-11-22)
- Product Owner explicitly accepted the comprehensive Task 2.3 audit package.
- Task 2.3 is COMPLETE and accepted. Do not repeat this task.
- Next: begin Task 2.4 only after reviewing roadmap and current architecture/documentation; schema-design scope and approval gates remain to be established in the next task.


## Phase 2 — Task 2.4: PostgreSQL Schema Blueprint (design-only)
- Status: IN PROGRESS.
- Scope established: logical tables/domain groups, tenant-aware key/FK strategy, uniqueness and association constraints, historical/snapshot/soft-delete/polymorphic/storage models, transaction/idempotency/concurrency strategy, and unresolved decision register.
- Inputs: accepted Task 2.1, 2.2 and 2.3 artifacts; do not repeat these audits.
- Guardrails: no DDL/SQL/migration implementation, database provisioning, live data, production changes, deployment or VPS work.
- Work packages: 2.4-A logical inventory; 2.4-B keys/relationships; 2.4-C history/snapshot/audit/storage; 2.4-D transaction/concurrency/decision register.
- [ ] Complete blueprint and decision register with evidence levels and unresolved business choices.
- [ ] Cross-review consistency against accepted discovery artifacts and target architecture.
- [ ] Read back and verify document updates.
- Stop for Product Owner review/acceptance before schema implementation.


## Task 2.4 progress checkpoint — 2026-11-22
- Work packages 2.4-A through 2.4-D have been drafted in POSTGRESQL-SCHEMA-BLUEPRINT-2.4.md: logical domain/table inventory; key/FK/tenant isolation and uniqueness candidates; lifecycle/history/snapshot/file/audit; transactions/idempotency/concurrency/outbox and migration reliability.
- This remains a design proposal; no executable SQL/DDL, migrations, database setup, live-data access, production changes or deployment.
- [x] Draft A: domain inventory and ownership boundaries.
- [x] Draft B: tenant-aware key/FK and constraint candidates.
- [x] Draft C: history, snapshots, deletion, file and audit lifecycle.
- [x] Draft D: transaction boundaries, idempotency, concurrency and reliability.
- [ ] Cross-review against accepted Task 2.1–2.3 source evidence and architecture.
- [ ] Verify read-back of documentation changes.
- [ ] Product Owner review and acceptance (required to close Task 2.4).


## Task 2.4 — Cross-review checkpoint
- [x] Cross-reviewed blueprint against accepted conceptual model, mapping and field-semantic evidence (2.1–2.3).
- [x] Recorded alignment matrix and limitations in POSTGRESQL-SCHEMA-BLUEPRINT-2.4.md.
- [x] Work packages A–D drafted; documentation read-back verification underway.
- Status: READY FOR PRODUCT OWNER REVIEW; not closed until explicit acceptance. No schema implementation authorized.


## Phase 2 — Task 2.4 Product Owner acceptance (2026-11-22)
- Product Owner explicitly accepted the Task 2.4 PostgreSQL logical schema blueprint and cross-review package.
- Task 2.4 is COMPLETE and accepted as a design artifact; it is not a migration-ready specification and no SQL/DDL, schema migration or database provisioning has been authorized or executed.
- Important remaining dependencies: source mapping completeness, payload/search requirements, procurement correction/reversal contract, attachment owner inventory, precision/timezone, identifier migration, retention, RLS, identity migration.
- Next roadmap step: prepare a safe detailed schema/migration implementation readiness task, first closing the source-evidence gaps and design decisions before executable migrations.


## Phase 2 — Task 2.5: Schema implementation readiness & evidence gap closure (IN PROGRESS — 2026-11-22)
- Dependency: accepted Task 2.4 logical blueprint; do not repeat accepted 2.1–2.4 work.
- Scope: read-only evidence audit and decision inventory to determine whether source mapping and write-path evidence are sufficiently complete for safe migration/schema implementation.
- Focus areas: table/entity coverage, mutations and lifecycle paths, uniqueness/FK evidence, event response/payload search requirements, procurement reversal/correction contract evidence, attachment owner types, identifiers, precision/timezone, identity migration and retention.
- Deliverable: readiness-gap register, evidence-backed updates, prioritized resolution plan and review package.
- Guardrail: no executable SQL/DDL, migrations, DB provisioning, live production data access, production changes, deployment or VPS action during this task.
- Task remains IN PROGRESS; no acceptance/completion claim until review and Product Owner acceptance.


### Task 2.5 incremental evidence audit (2026-11-22 — increment 1)
- Re-read source-only backend files DatabaseSetup.js, ProcurementService.js, AttachmentService.js, EventService.js, ProjectInformationService.js and FormService.js; registry, lifecycle operation families and key evidence gaps recorded in `TASK-2.5-READINESS-GAP-REGISTER.md`.
- Confirmed this increment remains read-only and limited: no live data, tests, SQL/DDL, migration, provisioning, production modification or deployment.
- Task 2.5 remains IN PROGRESS; next focus is exhaustive mutation-path/tenant relationship trace, followed by procurement and event/form payload semantics.


### Task 2.5 incremental audit — mutation/tenant boundary sample (2026-11-22)
- Expanded read-only review to Router, Project, User, Workflow, EventType, InformationType, ProjectDeletion, Storage/Attachment/Event repositories and EventData service.
- Evidence and bounded observations recorded in `TASK-2.5-READINESS-GAP-REGISTER.md`; G-01/G-10 remain blockers. Service validation, router authorization and DB relational enforcement are distinct layers.
- No live data, tests, executable schema, SQL, migration, provisioning, production modification or deployment.


### Task 2.5 increment 3 — procurement/material and attachment evidence (2026-11-22)
- Sampled single/batch receipt and order idempotency paths, material calculation/source-event void, procurement event integration, attachment owner/visibility/upload/delete, and project deletion preservation of shared procurement records.
- Evidence is recorded in `TASK-2.5-READINESS-GAP-REGISTER.md`. G-02 remains blocker; G-04 has bounded observed owners PROJECT/EVENT but exhaustive coverage is still pending; G-03/G-09/G-11 remain open.
- Read-only only: no DB/DDL, migration, live data, test, production write or deployment.


### Task 2.5 increment 4 — Event/Form snapshot, amendment and central-information evidence (2026-11-22)
- Read-only sampled event idempotency/replay, event void, form-response validation/persistence and definition snapshots, amendment event linkage, central-information history/approval and live-reference conflict handling.
- Findings recorded in `TASK-2.5-READINESS-GAP-REGISTER.md`; G-03/G-01/G-10 remain blockers and G-12 remains open.
- No SQL/DDL, migration, DB provisioning, live data, tests, production change or deployment.


### Task 2.5 increment 5 — payload shape and form-definition evidence (2026-11-22)
- Reviewed read-only legacy EventData JSON parsing and reusable-form definition/snapshot logic, field ordering/visibility/requiredness/cardinality helper behavior and separate central-information history/sync repositories.
- Evidence recorded in `TASK-2.5-READINESS-GAP-REGISTER.md`; G-03 remains blocker and G-01/G-10/G-12 remain open.
- No SQL/DDL, migration, live data, tests, database provisioning, production change or deployment.


### Task 2.5 increment 6 — information type, columns and reports (2026-11-22)
- Read-only validator/service review confirmed supported data-type list, SINGLE/MULTIPLE, column metadata, validation-rule shapes, revision and structural-change restrictions, logical column deletion/cache invalidation, bounded attachment validator contract and report/dashboard aggregation needs.
- Evidence and gap implications recorded in TASK-2.5-READINESS-GAP-REGISTER.md; G-03/G-04/G-10 remain blockers; G-06 high; G-12 open.
- No tests executed; no SQL/DDL, migration, DB provisioning, live data, production changes or deployment.


### Task 2.5 incremental audit — identifier/tenant relationship evidence increment 7 (2026-11-22)
- Read-only sampling of ID conventions, authentication/session, project validation, event validation, registry and project deletion dependency collection.
- Recorded mixed ID-generation evidence, username ambiguity/login fail-closed behavior, tenant-scoped entity/association registry and application-side dependency deletion observations in the readiness register.
- G-05 remains HIGH; G-10 and G-01 remain BLOCKERS; G-08 remains HIGH. No schema, SQL, migration, live data, tests or production writes.


### Task 2.5 incremental audit — numeric and temporal semantics increment 8 (2026-11-22)
- Read-only sampling of project quantity validation, procurement/material numeric validation, event occurrence-date parsing and timestamp handling.
- Recorded bounded integer-vs-number observations and unresolved precision/unit/timezone policy in the readiness register.
- G-06/G-07 remain HIGH; G-01 remains BLOCKER. No schema, SQL, migration, live data, tests or production writes.


### Task 2.5 incremental audit — identity, audit, deletion and file lifecycle increment 9 (2026-11-22)
- Read-only sampling of user lifecycle and audit paths, user repository delete capability, role-permission bulk deletion, dependency-aware project deletion and Google Drive file trashing.
- Recorded bounded identity/audit/deletion/storage evidence and unresolved migration, restore, purge, retention and reconciliation policies in the readiness register.
- G-08/G-09/G-11 remain HIGH; G-01 remains BLOCKER. No SQL/DDL, migration, DB provisioning, live data, tests or production writes.


### Task 2.5 incremental audit — configuration and dependency graph sampling increment 10 (2026-11-22)
- Read-only review of workflow/step-department, department, project-status, print-template and legacy event-field pathways.
- Recorded bounded relationship graph additions and lifecycle/constraint gaps in the readiness register.
- G-01/G-10 remain BLOCKERS; G-12 remains open; historical payload and file/template lifecycle gaps persist. No SQL/DDL, migration, DB provisioning, live data, tests or production writes.


### Task 2.5 incremental audit — registry entity coverage baseline increment 11 (2026-11-22)
- Extracted and grouped the 38 configuration registry definitions from DatabaseSetup.js (read-only source; no live sheet/data access).
- Recorded a baseline inventory and highlighted the need for per-entity caller/lifecycle/constraint mapping. The inventory is not yet a completed mutation matrix.
- G-01/G-10 remain BLOCKERS; G-12 remains open. No SQL/DDL, migration, DB provisioning, live data, tests or production writes.


### Task 2.5 incremental audit — partial service/repository lifecycle mapping increment 12 (2026-11-22)
- Sampled user-role assignment, role/permission catalog, workflow-step-event-rule versioning, workflow-step-department associations and project-status lifecycle source paths read-only.
- Recorded bounded CONFIRMED/PARTIAL/UNVERIFIED coverage states; no exhaustive operation matrix claim.
- G-01/G-10 remain BLOCKERS and G-12 open. No SQL/DDL, migrations, DB provisioning, live data, tests or production writes.


### Task 2.5 — Increment 13 (2026-11-22)
- Read-only Router.js evidence for selected user-role, role, permissions, workflow, project-status and permission introspection paths added to readiness register.
- Confirmed only bounded route/permission/dispatch snippets; full endpoint family lifecycle coverage remains incomplete. G-01/G-10 blockers and G-12 open/high.
- No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


### Task 2.5 — Increment 14 (2026-11-22)
- Bounded read-only Procurement Lifecycle Evidence Audit: order creation, allocations, single/batch receipt creation, idempotency/replay, quantity totals and status recomputation documented.
- Cancellation, correction/reversal, atomicity, precision, reconciliation and full VOID semantics remain unresolved; G-02 blocker.
- No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


### Task 2.5 — Increment 15 (2026-11-22)
- Read-only Attachment/File Ownership audit documented: PROJECT/EVENT owner types, validator limits, metadata, visibility, storage delegation, copy-image lineage and deletion sequence.
- Owner inventory, polymorphic FK design, orphan/reconciliation/recovery, storage migration, retention and full historical completeness remain unresolved; G-04 blocker, G-10 blocker and G-11 open/high.
- No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


### Task 2.5 — Increment 16 (2026-11-22)
- Re-read EventDataService.js and FormService.js read-only: tenant/parent visibility, independent-event access path, DataJSON parsing and response-record upsert with serialized ResponseJSON/DefinitionSnapshotJSON were cross-checked.
- This is bounded evidence only; full immutable response history, query/report/search workload and amendment semantics remain unresolved (G-03 blocker). G-01/G-10 remain blockers.
- No tests, SQL/DDL, migration, DB provisioning, live data or production changes/deployment.


### Task 2.5 increment 17 — Project mutation and route-dispatch cross-check (2026-11-22)
- Re-read Router.js and selected Project, ProjectDeletion, ProjectInformation, Event and Form services in the READ-ONLY production-reference repository.
- Confirmed bounded API dispatch/permission snippets for project create/update/delete-preview/delete, project-information deactivation, event create/void, event-form-response update, and form/form-field management. Coverage remains partial, not exhaustive.
- Recorded evidence and limitations in TASK-2.5-READINESS-GAP-REGISTER.md. G-01/G-10 remain blockers.
- No live data, tests, SQL/DDL, migration, database provisioning, production changes, deployment or VPS action.


### Task 2.5 increment 18 — Configuration/workflow domain mutation and route cross-check (2026-11-22)
- Sampled Router and workflow, workflow-step-department, event-type, department, project-status and information-type services/repositories in the READ-ONLY backend reference.
- Recorded bounded mutation methods, selected route permission gates, and remaining lifecycle/tenant/constraint gaps in TASK-2.5-READINESS-GAP-REGISTER.md.
- G-01/G-10 remain blockers; G-12 remains open/high. No exhaustive coverage claim.
- No live data, tests, SQL/DDL, migration, database provisioning, production changes, deployment or VPS action.


### Task 2.5 increment 19 — Identity, role, audit, report and print-template cross-check (2026-11-22)
- Read-only sampled UserService, UserRoleService, RoleService, RolePermissionService (fetched implementation content), AuditService, ReportService, PrintTemplateService, PrintTemplateRepository and selected Router branches.
- Documented bounded tenant checks, user/password and role-assignment paths, audit record shape, report/dashboard consumers, print-template definition/asset metadata and selected dispatch/permission gates in TASK-2.5-READINESS-GAP-REGISTER.md.
- G-01/G-10 remain blockers; G-08/G-09/G-11/G-12 remain open/high. This is not a complete lifecycle, security or relational-constraint audit.
- No live data, tests, SQL/DDL, migration, DB provisioning, production changes, deployment or VPS action.


### Task 2.5 increment 20 — Procurement, project cascade-delete and attachment/storage audit (2026-11-22)
- Read-only sampling of ProcurementService/Repository, ProjectDeletionService, AttachmentService/Repository and relevant route evidence.
- Documented tenant-scoped procurement datasets, idempotency and receipt paths, project cascade dependency graph, shared order preservation, partial-failure behavior, attachment ownership/access checks and Drive storage operations in TASK-2.5-READINESS-GAP-REGISTER.md.
- G-01/G-02/G-04/G-10 remain blockers; G-06/G-09/G-11 remain open/high. No exhaustive coverage claim.
- No live data, tests, SQL/DDL, migration, DB provisioning, production changes, deployment or VPS action.


### Task 2.5 increment 21 — Event void reversal and recovery audit (2026-11-22)
- Read-only sampling of EventService void path, EventRepository, ProjectRepository, ProjectService and Router excerpts.
- Documented conditional project-state restoration, event VOID status, workflow-progress recomputation, material-plan review integration, prior-state snapshots and multi-write partial-failure risks in TASK-2.5-READINESS-GAP-REGISTER.md.
- G-01/G-02/G-03/G-10 remain blockers; G-09 remains open/high. No full lifecycle or transaction guarantees claimed.
- No live data, tests, SQL/DDL, migration, DB provisioning, production modifications, deployment or VPS action.


### Task 2.5 increment 22 — Material-planning revisions, calculation ledger and reconciliation audit (2026-11-22)
- Read-only sampled MaterialPlanningService specification revision, calculation-key replay, calculation records, recalculation and event-void review paths.
- Documented multi-write partial completion, revision and calculation provenance, status mutation (not strictly immutable ledger), NEEDS_REVIEW on void, and observed four-decimal ceiling rounding in TASK-2.5-READINESS-GAP-REGISTER.md.
- G-01/G-02/G-03/G-10 remain blockers; G-06/G-09 remain open/high. No exhaustive audit or transaction guarantee claimed.
- No tests, live data, SQL/DDL, migration, DB provisioning, production modifications, deployment or VPS action.


### Task 2.5 increment 23 — Event data and project-information reference dependencies (2026-11-22)
- Read-only sampled EventData service/repository, ProjectInformation repository, project deletion collector, and workflow relationship repositories.
- Documented parent-event access enforcement, potentially omitted ProjectInformation/history/reference-sync dependencies in deletion collector, cross-project reference-sync links and workflow version relationship constraints in readiness register.
- G-01/G-03/G-09/G-10/G-12 remain open at blocker/high levels. No comprehensive dependency or retention policy claim.
- No tests, live data, SQL/DDL, migration, DB provisioning, production modification, deployment or VPS action.


### Task 2.5 — READ-ONLY discovery/readiness audit package closure (2026-11-22)
- Completed and documented Increment 24 bounded audit of authentication/session, tenant boundary, notification secrets/integration and legacy repository/config/storage semantics.
- Closed Task 2.5 as a discovery deliverable only; comprehensive source coverage is not claimed and schema implementation is NOT READY.
- Blockers G-01/G-02/G-03/G-04/G-10 and remaining high/open gaps are retained in TASK-2.5-READINESS-GAP-REGISTER.md; next task must resolve blockers before schema design.
- No tests, live data, SQL/DDL, migration, DB provisioning, production modification, deployment or VPS action.


## Task 2.6 — Resolve discovery blockers before Schema Design
- Status: IN PROGRESS.
- Increment 1: bounded Procurement order/receipt/event-integration review and proposed correction/reversal contract documented in TASK-2.6-BLOCKER-RESOLUTION.md.
- G-02 remains BLOCKER pending further source coverage and explicit contract closure; do not implement physical schema yet.
- Next increment: inspect receipt void/adjustment, allocation mutation, order status-transition and deletion/report dependency paths; document lifecycle matrix and evidence.
- Read-only reference review only. No tests, live data, SQL/DDL, migration, DB provisioning, production changes or deployment.


### Task 2.6 Increment 2 — Procurement projections, reports and deletion dependencies
- Reviewed workspace/dashboard/report calculations and project-deletion procurement dependency logic.
- Recorded receipt eligibility/status semantics, shared order visibility, shared allocation/receipt dependencies and project-delete retention implications in TASK-2.6-BLOCKER-RESOLUTION.md.
- G-02 remains BLOCKER. Further increment must inspect the remaining receipt batch write logic, order/line normalization and source API/router exposure, then produce lifecycle matrix.


### Task 2.6 Increment 3 — batch receipt invariants
- Reviewed batch receipt normalization, quantity/allocation caps, idempotency, order status recalculation, router read-surface and existing tests (tests not executed).
- Evidence recorded in TASK-2.6-BLOCKER-RESOLUTION.md. Single/batch parity and atomicity are not proven; G-02 remains BLOCKER.


### Task 2.6 Increment 4 — order recovery and receipt parity
- Reviewed order write sequence/partial recovery marker, single receipt sequence and Router procurement write service inventory.
- Documented NEEDS_REVIEW compensation semantics, multi-write non-atomicity and single/batch receipt policy parity needs. G-02 remains BLOCKER.


### Task 2.6 Increment 5 — Procurement lifecycle inventory
- Inspected ProcurementService public-method inventory, Repository API, Event integration service and relevant test-source assertions (not executed).
- Provisional order/receipt/allocation/surplus/NEEDS_REVIEW lifecycle matrix recorded. No dedicated allocation mutation, order cancellation, receipt reversal or adjustment public API was found in the sampled service inventory.
- G-02 remains BLOCKER; matrix is provisional and pending cross-service/report/business-rule coverage.


### Task 2.6 Increment 6 — cross-service and project-retention semantics
- Reviewed MaterialPlanningService, ProcurementEventIntegrationService and project deletion collector dependencies.
- Recorded separation of need recalculation from receipts and retention-safe project archive/deletion implications. G-02 remains BLOCKER pending final cross-consumer/source coverage.


### Task 2.6 Increment 7 — configuration lifecycle
- Reviewed workflow publication/versioning, information-type logical deactivation, event-type reference validation, project-status lifecycle and permission service.
- Provisional V2 configuration lifecycle decisions recorded. G-01/G-10 remain BLOCKERS pending dependency coverage and relationship matrix.


### Task 2.6 Increment 8 — identity/authorization relational integrity
- Reviewed user-role, role-permission, role/permission repository and request-cache/authorization paths.
- Provisional V2 tenant-compatible FK, unique association, deny-by-default and centralized authorization decisions documented. G-01/G-10 remain BLOCKERS.


### Task 2.6 Increment 9 — attachment/storage lifecycle
- Reviewed attachment service/repository/validator, Google Drive storage adapter and StorageRepository boundary.
- Provisional V2 metadata/object-storage lifecycle, staged recovery, retention purge and tenant-parent relationship constraints documented. G-04/G-11/G-10 remain BLOCKERS.


### Task 2.6 Increment 10 — audit/report and legacy ID relationship semantics
- Reviewed audit/report repository/service and EventData, ProjectInformation and project repository relationships.
- Provisional append-only audit, tenant-safe report, historical-retention and legacy-ID mapping decisions documented. G-01/G-05/G-09/G-10 remain OPEN/BLOCKERS as applicable.


### Task 2.6 Increment 11 — cross-cutting technical baseline
- Consolidated recommended exact-decimal/unit, UTC/local-time, tenant-compatible relational constraints and retention-safe relationship policies.
- These are provisional V2 technical decisions; G-06/G-07/G-10 remain OPEN/BLOCKERS pending source/data-field discovery and business precision/time policy.


### Task 2.6 Increment 12 — procurement projection parity audit
- Read-only review of ProcurementService need/order/workspace/report/dashboard projection paths.
- Recorded projection divergence: status filtering, allocation proportional receipt distribution, surplus handling, quantity caps and whole-order project visibility in TASK-2.6-BLOCKER-RESOLUTION.md.
- Recommended a canonical net receipt projection, explicit lifecycle/reconciliation, allocation accounting and shared report/dashboard projection. G-02 remains BLOCKER; no schema/migration readiness.
- No tests, live data, SQL/DDL, migration, DB provisioning, production changes or deployment.


### Task 2.6 Increment 13 — single/batch receipt parity and replay audit
- Read-only audit of single/batch receipt service implementations and Router dispatch.
- Documented validation differences, source-event/idempotency replay divergence, broad receipt aggregation, multi-write partial completion and absence of a verified reversal/correction API in the sampled public method inventory.
- Recommended unified receipt command, tenant-scoped idempotency constraint, explicit postings/reversals, allocation/general-stock parity and transactional writes with reconciliation. G-02 remains BLOCKER.
- No tests, live data, SQL/DDL, migration, DB provisioning, production changes or deployment.


### Task 2.6 Increment 14 and audit closure assessment
- Bounded read-only audit of order lifecycle/status/countability, cancel-related code search, receipt eligibility and partial-write recovery.
- Recorded cancellation-after-receipt/allocation policy gaps and recommendations for state machine, history preservation, release/reversal controls, transactional state-transition audit and legacy quarantine.
- Task 2.6 audit marked AUDIT COMPLETE / DESIGN READINESS NOT APPROVED. Blockers remain explicitly open; physical schema/SQL/migration not authorized.
- No tests, live data, SQL/DDL, migration, DB provisioning, production changes or deployment.


### Task 2.7 — Blocker Resolution & Evidence Closure kickoff (2026-11-22)
- Status: IN PROGRESS — discovery/evidence review only.
- Scope: traceability matrix; G-03 event/form history; G-04 attachment lifecycle/ownership; G-10 tenant-safe relationship coverage; G-01 registry/service/route/dependency reconciliation; closure gate assessment.
- Task plan: TASK-2.7-BLOCKER-CLOSURE.md.
- Schema design readiness remains NOT APPROVED; no SQL/DDL, migrations, live data, tests, provisioning, deployment or production modifications authorized/performed.


### Task 2.7 Increment 1 — bounded event/form and attachment cross-check (2026-11-22)
- Read-only review of EventService, EventData, FormService, AttachmentService, ProjectInformationService and Router source paths.
- Documented evidence for void validation, form amendment/snapshot, event-data access, attachment polymorphic ownership, upload visibility, storage deletion ordering, image-copy linkage and candidate tenant relationships.
- G-03/G-04/G-10/G-01 remain open. Coverage bounded; no runtime tests, live data, SQL/DDL, migrations, DB provisioning or production changes.


### Task 2.7 Increment 2 — header and router inventory (2026-11-22)
- Sampled DatabaseSetup.js and Router.js in read-only legacy backend.
- Reconciled 35+ legacy structures across security, project/workflow, event/form, information/template, attachment/audit, procurement domains and bounded route inventory.
- Identified naming/numbering anomalies and legacy backup structure; no V2 entity assumptions. G-01/G-10 remain open; inventory extraction is not exhaustive.


### Task 2.7 Increment 3 — service-operation inventory (2026-11-22)
- Read-only method inventory reconciled across ProjectService, EventService, FormService, AttachmentService, ProcurementService, AuditService and Router.
- Recorded lifecycle/projection cross-dependencies and high-risk delete/void/amend/copy paths; bounded coverage only, no exhaustive handler/dependency matrix or runtime tests.
- G-01/G-03/G-04/G-10 remain open; G-02 remains unresolved. Schema readiness NOT APPROVED; no SQL/DDL, live data, migrations, provisioning, deployment or production modification.


### Task 2.7 Increment 4 — Project deletion dependency collector (2026-11-22)
- Read-only review of ProjectDeletionService and related Project/Event/Attachment/Storage repositories plus Router delete path.
- Collector evidence maps event/data, needs, procurement allocations/items/orders/receipts, specs/calculations and project/event attachments; cross-project allocation safeguards noted.
- Deletion is multi-step/non-atomic, soft-delete/void child handling followed by file trash and physical project row removal if no prior failures; recovery/rollback unknown. This is legacy evidence, not a V2 design decision.
- G-01/G-04/G-10 remain open; G-02 open. No tests, live data, SQL/DDL, migration, provisioning, deployment or production modification.


### Task 2.7 Increment 5 — Definition/history mutation review (2026-11-22)
- Read-only sampled WorkflowService/Repository, EventTypeService, InformationTypeService, ProjectInformationService and FormService/Repository.
- Evidence recorded: tenant-scoped lookup/management, soft-delete and audit paths for information types/columns, project information history, form field/mapping deletion, and workflow/event type update/version/configuration.
- Dependent effect enforcement, complete version lineage, retention and all mutation paths remain unproven; bounded review only. G-01/G-03/G-04/G-10 open; G-02 open. No live data/tests/DDL/migrations/provisioning/deployment/production write.


### Task 2.7 Increment 6 — Tenant relationship integrity evidence (2026-11-22)
- Read-only cross-check of generic repositories and tenant validation patterns in project/event/event-data/form/information/attachment/procurement/access-control services.
- Generic repository contracts often remain global-sheet/ID based; tenant scoping is largely service-layer, while procurement read helpers filter by tenant. Polymorphic attachment ownership and nullable independent events remain physical integrity cases.
- Recorded relationship matrix and constraint gaps. G-10 remains OPEN; G-01/G-03/G-04/G-02 open. No live data, runtime tests, DDL/migrations, provisioning/deployment or production writes.


### Task 2.7 Increment 7 — Event/form history evidence (2026-11-22)
- Reviewed bounded event-create, form-response persistence/snapshot, amendment, void, audit and report projection paths. Evidence includes multi-step writes and service-level replay/optimistic checks; atomicity, immutable history, complete projection parity and retention remain unproven.
- G-03 remains OPEN. No tests/live data/SQL/DDL/migrations/provisioning/deployment or production changes.


### Task 2.7 Increment 8 — Attachment lifecycle failure boundaries (2026-11-22)
- Reviewed upload/storage/metadata/audit sequencing, access/download, delete storage-first ordering, copy linkage and polymorphic ownership. Compensation, restore, reconciliation, retention and purge remain unproven; G-04 OPEN.
- No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.7 — Blocker Resolution & Evidence Closure — AUDIT DELIVERABLE COMPLETE (2026-11-22)
- Completed nine documented evidence increments: bounded event/form/attachment review; header/router inventory; service-operation inventory; project deletion collector; workflow/form/information mutations; tenant relationship integrity; event/form history; attachment failure boundaries; final coverage/blocker register and closure assessment.
- Task 2.7 audit deliverable complete pending Product Owner acceptance. This is NOT underlying blocker closure or schema readiness.
- G-01, G-02, G-03, G-04 and G-10 remain OPEN with documented closure conditions. Schema Readiness NOT APPROVED.
- No runtime tests, live data, SQL/DDL, migrations, DB provisioning, deployment, VPS setup or production writes. Production repos remain read-only.


### Task 2.8 — Remaining Blocker Policy & Evidence Decision Pack (2026-11-22)
- IN PROGRESS. Drafted policy recommendations for G-01/G-02/G-03/G-04/G-10, separating technical proposals from business decisions and evidence requirements.
- Document: TASK-2.8-BLOCKER-POLICY-DECISION-PACK.md. No schema readiness, SQL/DDL, migration, provisioning, live data, production write or deployment.
### Task 2.8 — Recommended Policy Defaults Register (2026-11-22)
- Continued Task 2.8 by translating open G-02/G-03/G-04/G-10/G-01 questions into a concrete, conservative defaults decision register.
- New document: TASK-2.8-RECOMMENDED-POLICY-DEFAULTS.md. Defaults remain proposals requiring Product Owner acceptance; they are not implementation authorization.
- No tests, SQL/DDL, migrations, live data, DB provisioning, deployment or production changes.

### Task 2.8 — Lifecycle & Transaction Contracts (2026-11-22)
- Added draft lifecycle/state transition, invariant, transaction/concurrency and candidate acceptance-test contracts for procurement, event/form history, attachment lifecycle and tenant integrity.
- Document: TASK-2.8-LIFECYCLE-TRANSACTION-CONTRACTS.md. Design only; not implementation or test evidence. Business-policy decisions and exhaustive source coverage remain open.


### Task 2.8 — G-01 Coverage Increment 1 (2026-11-22)
- Retrieved recursive read-only trees: frontend reference 187 entries; backend reference 153 entries.
- Added TASK-2.8-G01-SOURCE-COVERAGE-INCREMENT-1.md with verified tree-level page/service/repository surfaces and explicit PARTIAL classifications.
- G-01 remains OPEN; tree inventory is not exhaustive route, caller, permission or dependency reconciliation. No tests/runtime execution or production modifications.


### Task 2.8 — G-01 Router surface increment 2 (2026-11-22)
- Read-only fetched complete Router.js plus Frontend router/API core. Recorded GET/POST/PUT/DELETE route families and client route-permission map in TASK-2.8-G01-ROUTER-SURFACE-INCREMENT-2.md.
- Not exhaustive pattern/handler/caller/permission matrix; explicit gaps and next reconciliation steps recorded. G-01 remains OPEN; no runtime tests or legacy modifications.


### Task 2.8 — G-01 Service/Test sampling increment 3 (2026-11-22)
- Read-only sampled AccessControl, Event, Procurement, Attachment, ProjectDeletion, Workflow and ProjectInformation service sources; inventory backend test filenames from tree.
- Record: TASK-2.8-G01-SERVICE-TEST-SAMPLING-INCREMENT-3.md. Sampling only, not test execution or exhaustive mapping. G-01 remains OPEN.


### Task 2.8 — Mutation Risk Evidence Increment 4 (2026-11-22)
- Source-level evidence recorded for ProjectDeletion, AttachmentService.delete and procurement receipt single/batch paths: TASK-2.8-MUTATION-RISK-EVIDENCE-INCREMENT-4.md.
- Key migration incompatibilities/risks: multi-sheet deletion is non-transactional; receipt records included in project cascade; attachment storage bytes deleted before metadata/audit; receipt insertion/order-status/audit not atomic; replay payload equivalence not evidenced.
- G-01 and blockers remain open. No tests executed.


### Task 2.8 — Event/Form Lifecycle Evidence Increment 5 (2026-11-22)
- Read-only source review of EventService and FormService event creation, void, metadata edits, response snapshots and amendment flows recorded in TASK-2.8-EVENT-FORM-LIFECYCLE-EVIDENCE-INCREMENT-5.md.
- Findings: void and project restoration/material-planning reconciliation are multi-operation; form response stores definition snapshot but direct edits/amendments update the current response record; immutable revision chain not demonstrated; concurrency/version checks and amendment event governance exist in inspected paths.
- V2 design implications recorded; G-01 remains open; tests not executed.


### Task 2.8 — G-01 Increment 6: registry/auxiliary lifecycle and reset-safety evidence (2026-11-22)
- Read-only review of DatabaseSetup.js registry/header definitions plus selected frontend API and feature-module excerpts recorded in `TASK-2.8-SCHEMA-REGISTRY-AUXILIARY-EVIDENCE-INCREMENT-6.md`.
- Confirmed numeric prefix reuse across distinct sheet names (`18_MaterialNeeds` and `18_ProjectStatuses`), JSON/list and polymorphic source-field categories, and presence of a destructive legacy reset utility (not executed).
- V2 implications and bounded limitations recorded; G-01/G-10 remain open. No tests, live data, SQL/DDL, migration, provisioning, production writes or deployment.


### Task 2.8 — G-01 Increment 7: Frontend/API caller sampling (2026-11-22)
- Read-only sampled production frontend API callers in core API/router/permission files and event, form, procurement, project and attachment modules; endpoint-family observations and reconciliation gaps documented in `TASK-2.8-FRONTEND-API-CALLER-SAMPLING-INCREMENT-7.md`.
- Observed compatibility/fallback procurement caller surface and cross-domain endpoint families; client permission gates are not server enforcement evidence. Coverage is sampled only; G-01 remains OPEN. No tests, live data or production writes.


### Task 2.8 — G-01 Increment 8: sampled frontend/backend Router cross-check (2026-11-22)
- Targeted read-only route-string cross-check of sampled caller families against legacy backend Router.js recorded in `TASK-2.8-BACKEND-ROUTER-CROSSCHECK-INCREMENT-8.md`.
- Most sampled route-family strings were found; literal `workflow-progress` was not found in Router.js, recorded as an unresolved discrepancy (not a confirmed missing route). Literal search is not proof of handler/permission coverage. G-01 remains OPEN.
- No tests, live data, DDL/migration, provisioning, production writes or deployment.


### Task 2.8 — Route dispatch/caller method evidence Increment 9 (2026-11-22)
- Reconciled frontend API wrapper verb helpers with verb-specific Router.js branches for sampled route families; resolved prior workflow-progress literal-search discrepancy: the GET project-prefix dispatch exists and calls EventService.workflowProgress behind PROJECT_VIEW.
- Evidence recorded in `TASK-2.8-ROUTE-DISPATCH-CALLER-METHOD-EVIDENCE-INCREMENT-9.md`. Bounded source comparison only; not exhaustive handler, tenant, permission or test coverage. G-01 remains OPEN; no tests or production changes.


### Task 2.8 — Targeted tenant service validation Increment 10 (2026-11-22)
- Read-only inspection of EventService.workflowProgress and selected ProjectService/ProcurementService tenant-visibility helpers recorded in `TASK-2.8-TARGETED-TENANT-SERVICE-VALIDATION-INCREMENT-10.md`. Observed project tenant and availability validation in these inspected paths; coverage is bounded, not exhaustive. G-10/G-01 remain OPEN; no tests/live data/production changes.


### Task 2.8 — Test surface inventory Increment 11 (2026-11-22)
- Read-only recursive test path inventory from legacy frontend/backend recorded in `TASK-2.8-TEST-SURFACE-INVENTORY-INCREMENT-11.md`. Test names provide surface inventory only, not execution or coverage proof; test-to-source traceability remains unresolved. G-01 OPEN; no tests executed or production changes.


### Task 2.8 — Source-to-test assertion trace sampling Increment 12 (2026-11-22)
- Read-only sampled assertion trace across workflow enforcement, procurement, attachment, project deletion, schema contract and event idempotency test files recorded in `TASK-2.8-SOURCE-TO-TEST-ASSERTION-TRACE-INCREMENT-12.md`. Not exhaustive and tests not executed; G-01/G-10 remain OPEN.


### Task 2.8 — Consolidated Gap Register Increment 13 (2026-11-22)
- Consolidated increments 1–12 into domain-level evidence/gap register in `TASK-2.8-CONSOLIDATED-GAP-REGISTER-INCREMENT-13.md`, separating partial evidence from closure. G-01 through G-04 and G-10 remain OPEN; schema readiness NOT APPROVED. No tests or production changes.


### Task 2.8 — Structured Entity Manifest Increment 14 (2026-11-22)
- Extracted 37 declared legacy sheet registry entries and grouped candidate domain/relationship notes in `TASK-2.8-STRUCTURED-ENTITY-MANIFEST-INCREMENT-14.md`. Registry evidence only; candidate edges are inferred and remain to be validated against source consumers. No live data/tests/schema/migration or production changes.


### Task 2.8 — Registry-to-Code Surface Crosswalk Increment 15 (2026-11-22)
- Crosswalked registry domains against read-only frontend/backend tree paths in `TASK-2.8-REGISTRY-CODE-SURFACE-CROSSWALK-INCREMENT-15.md`. Path correspondence only; exact consumers and field operations remain partial. No tests or production changes.


### Task 2.8 — High-Risk Lifecycle Relationship Trace Increment 16 (2026-11-22)
- Targeted read-only trace across event/form, attachment, procurement, project deletion and reference-copy source paths recorded in `TASK-2.8-HIGH-RISK-LIFECYCLE-TRACE-INCREMENT-16.md`. Partial positive evidence and open gaps separated; all blockers remain open. No tests or production changes.


## Phase 2 — Task 2.8 Increment 17 (2026-11-22)
- Bounded read-only relationship edge and tenant enforcement matrix recorded in `TASK-2.8-RELATIONSHIP-TENANT-ENFORCEMENT-MATRIX-INCREMENT-17.md`, covering tenant/project/event/form/information/workflow/attachment/audit/procurement and deletion dependencies.
- Identified repository-level broad reads vs service-level tenant checks, polymorphic owner edges, and candidate tenant-qualified relational constraint strategy. These are recommendations, not approved schema decisions.
- G-01/G-10 remain OPEN; G-02/G-03/G-04 remain OPEN. Schema readiness NOT APPROVED. No tests, live data, SQL/DDL, migration, provisioning or production changes.


## Phase 2 — Task 2.8 Increment 18 (2026-11-22)
- Header-grounded inventory of nullable, polymorphic and versioned relationship edges documented in `TASK-2.8-NULLABLE-POLYMORPHIC-VERSIONED-EDGE-INVENTORY-INCREMENT-18.md`.
- Recorded tenant derivation for EventData, repeated parent references in history/sync, optional procurement edge combinations, attachment/audit polymorphism and version-sensitive rule relationships. Nullability remains unconfirmed without full source/data validation.
- G-01/G-10/G-02/G-03/G-04 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 19 (2026-11-22)
- Read-only reconciliation of project cascade deletion dependency collector and source-event-void material planning path recorded in `TASK-2.8-DELETION-SOURCE-EVENT-DEPENDENCY-RECONCILIATION-INCREMENT-19.md`.
- Evidence confirms collector protects shared order lines/cross-project allocations but mutation is sequential and non-transactional; project row physical deletion is gated on child failures, and audit recovery is not established. Source-event void moves calculations/needs to review but does not directly reverse receipts/allocations in the inspected path.
- G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 20 (2026-11-22)
- Targeted read-only AttachmentService failure-boundary review documented in `TASK-2.8-ATTACHMENT-FAILURE-BOUNDARY-RECONCILIATION-INCREMENT-20.md`.
- Normal upload stores object before metadata/audit without demonstrated compensation; delete removes object before metadata/audit; copyProjectImage has bounded compensating cleanup on error. Recommendations recorded for staged/outbox storage operations and reconciliation.
- G-04/G-10/G-01 remain OPEN; schema readiness NOT APPROVED. No tests, live files/data, DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 21 (2026-11-22)
- Read-only AuditService/AuditRepository and ProcurementService batch receipt/order failure-boundary trace recorded in `TASK-2.8-PROCUREMENT-RECEIPT-BATCH-AUDIT-FAILURE-BOUNDARY-INCREMENT-21.md`.
- Batch receipts validate and normalize before one receipt-sheet insertMany, then update order status sequentially and write audits separately. No cross-sheet transaction is demonstrated; replay keys do not prove payload-equivalence or uniqueness enforcement. Audit rows are tenant-filtered at service reads but immutable/transactional durability not established.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 22 (2026-11-22)
- Read-only trace of Audit GET route permission gates, tenant filtering, validator/service distinction and test-tree inventory documented in `TASK-2.8-AUDIT-ROUTE-PERMISSION-TEST-TRACE-INCREMENT-22.md`.
- Router gates `/audit`, `/audit/entity/*`, `/audit/user/*` with REPORT_VIEW; service tenant-filters results and validates same-tenant target user. AuditRepository scans broad sheet then filtering occurs in service. No dedicated audit test filename was identified in test tree; filename absence is not assertion coverage proof.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 23 (2026-11-22)
- Read-only crosswalk of procurement mutation POST routes, permission gates, service defense and test-tree evidence recorded in `TASK-2.8-PROCUREMENT-MUTATION-ROUTE-PERMISSION-TEST-CROSSWALK-INCREMENT-23.md`.
- Order, single receipt and batch receipt POST routes require PROCUREMENT_MANAGE; relevant service checks/locks/validation are documented, while multi-sheet transaction boundaries remain unresolved. Procurement test filenames found but assertion mapping and execution are not yet proven.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 24 (2026-11-22)
- Actual assertions in procurement-service, procurement-event-integration and material-planning test files were read and mapped in `TASK-2.8-PROCUREMENT-MATERIAL-PLANNING-ASSERTION-COVERAGE-INCREMENT-24.md`.
- Source assertions cover allocation cap/surplus, request-level read reuse, event-source-bound confirmed quantity, event integration restrictions, replay, source void history, revision and cross-tenant/invisible project rejection. They were not executed.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 25 (2026-11-22)
- Actual assertion crosswalk for project deletion, event idempotency and workflow enforcement tests documented in `TASK-2.8-PROJECT-DELETION-EVENT-IDEMPOTENCY-WORKFLOW-ASSERTION-CROSSWALK-INCREMENT-25.md`.
- Assertions observed include shared-order dependency preservation, tenant/project scoped deletion collection, event replay/PROCESSING/COMPLETED safeguards and workflow rule revalidation under lock. Tests were not executed; production behavior and complete recovery coverage remain unverified.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 26 (2026-11-22)
- Assertion crosswalk for attachment visibility, repeat-reference copy and schema-contract tests documented in `TASK-2.8-ATTACHMENT-REFERENCE-COPY-SCHEMA-CONTRACT-ASSERTION-CROSSWALK-INCREMENT-26.md`.
- Positive assertions cover attachment event/project visibility, reference copy operation plan/history/live-sync/idempotency/policy and source-level schema/deploy guard contracts. Attachment storage failure recovery, runtime execution and PostgreSQL migration correctness remain unverified.
- G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 27 (2026-11-22)
- Read-only assertion crosswalk for access-control caches, user validation, reusable forms and form API contract documented in `TASK-2.8-ACCESS-CONTROL-USER-VALIDATION-REUSABLE-FORM-ASSERTION-CROSSWALK-INCREMENT-27.md`.
- Assertions cover request-scoped caches, password validation, reusable form snapshots/visibility/requiredness/idempotency and amendment permission/concurrency/replay. Runtime route authorization, auth/session security and DB-level tenant constraints remain unverified. Tests not run.
- G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 28 (2026-11-22)
- Read-only selected frontend assertion crosswalk documented in `TASK-2.8-FRONTEND-BACKEND-CONTRACT-ASSERTION-CROSSWALK-INCREMENT-28.md`; production frontend repo remains unmodified.
- Tests assert dossier/revision history, event submit progress/retry/accessibility, five-second success close, repeat-reference sync/conflict/retry and receipt form loading/error behavior. Tests not executed and are not API/database integrity evidence.
- G-01 remains OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 29 (2026-11-22)
- Consolidated blocker evidence classifications and measurable closure criteria across increments 1–28 recorded in `TASK-2.8-CONSOLIDATED-EVIDENCE-COVERAGE-BLOCKER-CLOSURE-CRITERIA-INCREMENT-29.md`.
- Evidence currently E1/E2 only (source and test assertion inspections); E3 runtime test evidence and E4 live-data/database evidence are absent by scope. Critical closure criteria for G-01/G-02/G-03/G-04/G-10 and cross-cutting audit/transaction/migration/auth risks are recorded. Schema readiness remains NOT APPROVED; Task 2.8 not complete.


## Phase 2 — Task 2.8 Increment 30 (2026-11-22)
- Read-only literal route-family inventory from backend Router.js recorded in `TASK-2.8-ROUTER-ROUTE-FAMILY-INVENTORY-INCREMENT-30.md` (51 dispatch literal matches in source extraction; duplicates represent method-specific route occurrences). Dynamic path families explicitly flagged as requiring separate trace.
- Route inventory is a checklist, not an exhaustive authorization or tenant audit. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 31 (2026-11-22)
- Dynamic/prefix Router.js pattern inventory recorded in `TASK-2.8-DYNAMIC-ROUTER-PATH-PATTERN-INVENTORY-INCREMENT-31.md`, including GET, POST, update/delete and admin/destructive families.
- Inventory remains a dispatch-pattern extraction; route-method combinations, permissions, payload validation and service effects are not exhaustively traced. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 32 (2026-11-22)
- Focused read-only Router.js trace for admin project deletion preview/remove, admin session listing/revocation and attachment list/download/upload gates recorded in `TASK-2.8-HIGH-RISK-ADMIN-DELETION-ATTACHMENT-ROUTE-TRACE-INCREMENT-32.md`.
- Explicit admin checks and ATTACHMENT_VIEW/ATTACHMENT_UPLOAD gates observed in sampled routes; project attachment ownership is path-bound. Runtime authorization, exhaustive variants and tests remain unverified.
- G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 33 (2026-11-22)
- Read-only Router crosswalk for central project information save/resolve/approve/deactivate, reusable form definition/assignment, event form amendment, event void, workflow update and workflow event-rule mutation recorded in `TASK-2.8-PROJECT-INFORMATION-FORM-EVENT-WORKFLOW-MUTATION-PERMISSION-TRACE-INCREMENT-33.md`.
- Explicit gates observed: PROJECT_EDIT, SYSTEM_SETTINGS, EVENT_TYPE_MANAGE (or SYSTEM_SETTINGS for assignment), EVENT_EDIT, EVENT_CREATE and WORKFLOW_MANAGE. Exhaustive service/runtime/tenant test coverage remains unverified.
- G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 34 (2026-11-22)
- Read-only Router trace for user/role/permission, department and workflow GET route gates documented in `TASK-2.8-RBAC-DEPARTMENT-WORKFLOW-ROUTE-PERMISSION-TRACE-INCREMENT-34.md`.
- Explicit gates observed, including broad requireAny permission lists for department and workflow read routes. Least-privilege business suitability, service tenant checks and runtime tests remain unverified.
- G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increments 35–36 (2026-11-22)
- Core project read/mutation route crosswalk documented in `TASK-2.8-CORE-PROJECT-READ-MUTATION-ROUTE-CROSSWALK-INCREMENT-35.md`; route families and sampled gates catalogued.
- Follow-up on PUT print-template project-data binding documented in `TASK-2.8-PRINT-TEMPLATE-PROJECT-BINDING-GATE-VERIFICATION-INCREMENT-36.md`. Router branch lacks visible explicit gate, but PrintTemplateService enforces source-specific PROJECT_EDIT/EVENT_EDIT, tenant-scoped template lookup, active template, latest-binding and stale-source checks; no vulnerability conclusion.
- G-01/G-10 remain OPEN; schema readiness NOT APPROVED. Tests not executed; no live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 37 (2026-11-22)
- GET route permission/domain crosswalk for project nested reads, event/form/data, workflow, reusable forms/information types/event types, procurement read and reporting routes recorded in `TASK-2.8-GET-ROUTE-PERMISSION-DOMAIN-CROSSWALK-INCREMENT-37.md`.
- Source gates and dispatch are recorded; exhaustive callers, service tenant validation and runtime test evidence remain unverified. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 38 (2026-11-22)
- POST mutation route crosswalk recorded in `TASK-2.8-POST-ROUTE-MUTATION-PERMISSION-CROSSWALK-INCREMENT-38.md`, covering project/draft fallback, reference copy, workflow, config/forms, events/attachments and RBAC.
- Special same-tenant own-DRAFT fallback and route-bound attachment ownership highlighted for test coverage; runtime tests and exhaustive service validation remain unverified. G-01/G-10 OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 39 (2026-11-22)
- PUT/DELETE route crosswalk recorded in `TASK-2.8-PUT-DELETE-ROUTE-CROSSWALK-INCREMENT-39.md`, covering update/deactivate/remove, admin delete/session, attachment and workflow/form/RBAC families.
- Dispatch gates documented; service-level tenant guards, atomicity, retention/recovery and runtime test evidence remain unverified. G-01/G-03/G-04/G-10 OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 40 (2026-11-22)
- Draft V2 transaction/integrity/test matrix across tenant/RBAC, project, central info/history, event/form, workflow, material planning, procurement/allocation/receipt, attachments, deletion and audit documented in `TASK-2.8-V2-TRANSACTION-INTEGRITY-TEST-MATRIX-DRAFT-INCREMENT-40.md`.
- Matrix and transaction strategy are evidence-derived proposals only, not approved business policy or schema. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No implementation, tests, live data, DDL/migrations, provisioning or deployment.


## Phase 2 — Task 2.8 Increment 41 (2026-11-22)
- Remaining technical defaults vs unresolved business decisions and evidence boundary recorded in `TASK-2.8-REMAINING-POLICY-DECISIONS-EVIDENCE-BOUNDARY-INCREMENT-41.md`.
- Source evidence pack is bounded and documented, but business-policy items remain unconfirmed, and no E3 runtime tests or E4 live-data evidence exist. Task 2.8 discovery/decision pack cannot declare schema readiness. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 42 (2026-11-22)
- Focused tenant header/repository boundary trace documented in `TASK-2.8-TENANT-INTEGRITY-HEADER-REPOSITORY-BOUNDARY-TRACE-INCREMENT-42.md`. Source observations: tenant-qualified User/Department/Role/UserRole/RolePermission/Project headers, global-looking Permission header, tenant/user cache keying in AccessControlService, and ID-only generic repository wrappers requiring service-layer ownership enforcement.
- This source-only increment does not close G-10/G-01; uniqueness and global/shared policy decisions remain open. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 43 (2026-11-22)
- Authenticated tenant-context/RBAC resolution trace documented in `TASK-2.8-AUTHENTICATED-TENANT-CONTEXT-RBAC-RESOLUTION-TRACE-INCREMENT-43.md`. Source observations include session-user TenantID match, tenant-filtered role and permission associations, tenant-scoped admin resolution, global permission catalog reads and Router-specific authorization reliance.
- E1 only; route coverage, runtime security, and database enforcement unverified. G-10/G-01 remain OPEN; no tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 44 (2026-11-22)
- Reconciled exact frontend/backend test-tree inventories (43 test files each) with thematic mapping recorded in `TASK-2.8-G01-FRONTEND-BACKEND-TEST-TREE-RECONCILIATION-INCREMENT-44.md`. Tree counts are filename-level only and do not prove execution/assertion coverage.
- G-01 remains OPEN pending operation-to-assertion mapping and test execution; other blockers remain open. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 45 (2026-11-22)
- Assertion-level source inspection for five selected backend test files documented in `TASK-2.8-SELECTED-LEGACY-TEST-ASSERTION-CROSSWALK-INCREMENT-45.md`. Identified bounded assertions/mock boundaries for request cache, user validation, deletion dependency collection, procurement read reuse and attachment project access.
- E2 source assertion inspection only; no test execution. Cross-tenant runtime coverage, atomicity and exhaustive G-01/G-10 remain open. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 46 (2026-11-22)
- Assertion-source crosswalk for event idempotency/recovery, schema contract and workflow enforcement/rules tests recorded in `TASK-2.8-EVENT-IDEMPOTENCY-SCHEMA-WORKFLOW-ASSERTION-CROSSWALK-INCREMENT-46.md`.
- E2 source inspection only; harness and source-text assertion boundaries documented. Runtime behavior, concurrency, cross-tenant constraints and lifecycle integrity remain unverified. G-01/G-03/G-10 remain open. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 47 (2026-11-22)
- Frontend assertion-source crosswalk for router base path, draft project flow, procurement receipt loading, reference conflict resolution and project dossier documented in `TASK-2.8-FRONTEND-ASSERTION-CROSSWALK-INCREMENT-47.md`.
- E2 only; source-pattern assertion limitations noted. No runtime tests or browser execution claimed. G-01 remains OPEN. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 48 (2026-11-22)
- Assertion-source crosswalk for form API contract, material planning, procurement integration, draft project lifecycle and reusable forms recorded in `TASK-2.8-FORM-MATERIAL-PROCUREMENT-DRAFT-ASSERTION-CROSSWALK-INCREMENT-48.md`.
- E2 only; mocked repository and source-pattern boundaries noted. G-01/G-02/G-03 remain open. No runtime tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 49 (2026-11-22)
- Assertion-source crosswalk for report, print-template batch, reference-copy, project-image gallery and thumbnail tests documented in `TASK-2.8-REPORT-PRINT-REFERENCE-ATTACHMENT-ASSERTION-CROSSWALK-INCREMENT-49.md`.
- E2 only; mock/storage boundaries noted. G-01/G-04/G-10 remain open. No runtime tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 50 (2026-11-22)
- Consolidated blocker/evidence matrix and closure dependency review recorded in `TASK-2.8-CONSOLIDATED-EVIDENCE-MATRIX-CLOSURE-DEPENDENCIES-INCREMENT-50.md`, summarizing E1/E2, G-01/G-02/G-03/G-04/G-10, seven business-policy decisions and safe evidence execution boundary.
- Schema readiness remains NOT APPROVED. Task 2.8 cannot be marked complete until decisions/closure criteria are accepted or dispositioned; current GitHub-only access cannot execute isolated legacy runtime tests and production mutation is prohibited.


### Task 2.8 Increment 51 (2026-11-22)
- Read-only schema-header tenant ownership classification documented in `TASK-2.8-SCHEMA-HEADER-TENANT-OWNERSHIP-CLASSIFICATION-INCREMENT-51.md`. Explicit TenantID headers catalogued; Permissions appears shared/global; EventData lacks direct TenantID and relies on parent Event linkage.
- E1 only; header presence does not prove DB integrity or live schema. G-10 remains OPEN. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 52 (2026-11-22)
- Header-derived tenant-qualified relationship crosswalk recorded in `TASK-2.8-TENANT-QUALIFIED-RELATIONSHIP-HEADER-CROSSWALK-INCREMENT-52.md`, mapping candidate tenant-consistent FK edges and special cases (EventData, global-looking Permissions, polymorphic Attachments/AuditLogs, nullable/independent events).
- E1 structural evidence only; this is not live schema or runtime integrity proof. G-10 remains OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 53 (2026-11-22)
- Bounded read-only EventService/AttachmentService trace documented in `TASK-2.8-INDEPENDENT-EVENT-ATTACHMENT-OWNERSHIP-TRACE-INCREMENT-53.md`: independent-event listing/retrieval behavior, parent-project checks for project-linked events and attachment ownership/visibility/copy checks.
- Source evidence only; no runtime tests. G-10/G-04/G-01 remain open; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 54 (2026-11-22)
- Bounded repository lookup/filter boundary review documented in `TASK-2.8-REPOSITORY-LOOKUP-TENANT-FILTER-BOUNDARY-TRACE-INCREMENT-54.md`: generic ID lookup and selected relation repository filters are not themselves tenant-qualified; sampled service callers apply additional tenant/access checks.
- E1 source inspection only; exhaustive caller review/runtime negative tests remain pending. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 55 (2026-11-22)
- Bounded identity/RBAC repository tenant-boundary trace recorded in `TASK-2.8-IDENTITY-RBAC-REPOSITORY-TENANT-BOUNDARY-TRACE-INCREMENT-55.md`: selected ID lookup and association repository methods are not tenant-qualified themselves; sampled service-level role validation/filtering is layered above.
- E1 only; uniqueness scopes and exhaustive mutation/caller/runtime testing remain unresolved. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 56 (2026-11-22)
- Bounded UserRoleService/RolePermissionService/PermissionService trace documented in `TASK-2.8-USER-ROLE-MUTATION-AUDIT-BOUNDARY-TRACE-INCREMENT-56.md`: assignment tenant/role checks, duplicate guard and separate audit write sequence; tenant-based active-role resolution and global permission-catalog read sampled.
- E1 only; runtime behavior and complete mutation coverage unverified. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 57 (2026-11-22)
- Bounded procurement/material-planning lifecycle trace recorded in `TASK-2.8-PROCUREMENT-MATERIAL-PLANNING-LIFECYCLE-TRACE-INCREMENT-57.md`: tenant-scoped repository reads, locked/idempotent order and receipt paths, compensating NEEDS_REVIEW behavior for partial order writes, calculation/spec revision lifecycle, source-event void handling and separate audit writes.
- E1 source review only; no runtime testing. G-02/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 58 (2026-11-22)
- Event void and ProjectInformation revision/sync trace recorded in `TASK-2.8-EVENT-VOID-PROJECT-INFORMATION-REVISION-SYNC-TRACE-INCREMENT-58.md`: sequential void restoration/cascade/audit writes and recoverable material reconciliation failure, plus information current/history/audit/reference-sync write boundaries and sampled Form/EventData controls.
- E1 only; runtime tests not executed. G-03/G-10/G-01 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 59 (2026-11-22)
- Attachment storage/delete lifecycle trace recorded in `TASK-2.8-ATTACHMENT-STORAGE-DELETE-LIFECYCLE-TRACE-INCREMENT-59.md`: Google Drive storage abstraction, sequential file/metadata/audit writes and delete-to-trash/soft-delete boundary; recovery/retention/backup policies not established in inspected source.
- E1 source review only; runtime testing not executed. G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 60 (2026-11-22)
- Audit/project cascade deletion integrity trace recorded in `TASK-2.8-AUDIT-PROJECT-CASCADE-DELETION-INTEGRITY-TRACE-INCREMENT-60.md`: audit service/repository boundaries and sequential dependency soft deletion, storage trashing and physical project row deletion with partial failure reporting.
- E1 source review only; runtime tests not executed. G-04/G-10/G-01 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 61 (2026-11-22)
- Bounded Router/API permission boundary trace recorded in `TASK-2.8-ROUTER-API-PERMISSION-BOUNDARY-TRACE-INCREMENT-61.md`: sampled procurement, project material planning, information/history, attachment and audit routes with route permission layers.
- E1 only; full endpoint matrix and route tests pending. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 62 (2026-11-22)
- Workflow version/event rule lifecycle trace recorded in `TASK-2.8-WORKFLOW-VERSION-EVENT-RULE-LIFECYCLE-TRACE-INCREMENT-62.md`: versioned rule fields, clone/replace behavior under lock and bounded workflow/event type version validation.
- E1 only; runtime tests and full aggregate publish path review pending. G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 63 (2026-11-22)
- Legacy test inventory and V2 verification gap map recorded in `TASK-2.8-LEGACY-TEST-INVENTORY-VERIFICATION-GAP-MAP-INCREMENT-63.md`. Verified 40 test files in the reference backend tests tree; read representative test names for attachment access, project deletion, procurement, workflow rules, event idempotency and material planning.
- Test execution/pass status is not claimed. PostgreSQL/transaction/RLS/route matrix/storage recovery coverage remains unverified. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No production changes.


### Task 2.8 Increment 64 (2026-11-23)
- Consolidated increments 52–63 into `TASK-2.8-CONSOLIDATED-COVERAGE-EVIDENCE-READINESS-MATRIX-INCREMENT-64.md`, mapping domains to source evidence, test-source evidence, V2 integrity/transaction requirements and unresolved validation.
- G-01 and G-10 remain OPEN/BLOCKERS; G-02/G-03/G-04 remain OPEN/HIGH. No E3/runtime evidence established; schema/migration readiness remains NOT APPROVED. No production changes.


### Task 2.8 Increment 65 (2026-11-23)
- Bounded read-only inventory of Router.js GET-dispatch branches and permission patterns recorded in `TASK-2.8-ROUTER-GET-DISPATCH-BRANCH-INVENTORY-INCREMENT-65.md`.
- This does not cover mutation routes or exhaustive authorization. G-01 remains OPEN/BLOCKER; no tests executed or production changes.


### Task 0.2 / Task 2.8 Increment 68 (2026-11-23)
- Bounded tenant/identity/role mutation trace recorded in `TASK-2.8-TENANT-BOUNDARY-ROLE-MUTATION-TRACE-INCREMENT-68.md`: UserRole assignment/removal checks, RoleService tenant/code checks, sampled PermissionService role resolution, WorkflowService tenant filtering, and repository lookup boundaries.
- E1 source evidence only; no runtime cross-tenant tests or exhaustive mutation coverage. G-01/G-10 remain OPEN/BLOCKER; G-02/G-03/G-04 remain OPEN/HIGH. No production changes.


### Task 0.2 / Task 2.8 Increment 69 (2026-11-23)
- Bounded procurement mutation and project cascade-delete transaction-gap trace recorded in `TASK-2.8-PROCUREMENT-AND-PROJECT-DELETE-TRANSACTIONAL-GAP-TRACE-INCREMENT-69.md`: lock/idempotency/prevalidation and compensation in procurement; sequential dependency deletion/storage trashing/project deletion.
- E1 source evidence only; no runtime concurrency, transaction or recovery tests. G-02/G-03/G-04/G-10 remain OPEN/HIGH or BLOCKER; G-01 remains OPEN. No production changes.


### Task 0.2 / Task 2.8 Increment 70 (2026-11-23)
- Bounded Project/Event/EventData/ProjectInformation/material-planning/Attachment/AuditService trace recorded in `TASK-2.8-PROJECT-EVENT-INFORMATION-MATERIAL-ATTACHMENT-BOUNDARY-TRACE-INCREMENT-70.md`: tenant checks, optimistic revision/idempotency/lock patterns and sequential multi-write/storage boundaries.
- E1 source evidence only; no runtime, concurrency or recovery tests. G-01/G-10 remain OPEN/BLOCKER; G-02/G-03/G-04 remain OPEN/HIGH. No production changes.


### Task 0.2 / Task 2.8 Increment 71 (2026-11-23)
- Current tests directory listing returned 41 test files (previous increment reported 40; snapshot difference noted). Six representative test sources reviewed for test intent: material planning, procurement, project deletion, workflow event enforcement, attachment access and idempotency schema recovery.
- Review of test sources only; tests not executed. Mock/source-contract coverage does not establish PostgreSQL/runtime validation. G-01/G-10 remain OPEN/BLOCKER; G-02/G-03/G-04 remain OPEN/HIGH. No production changes.


### Task 0.2 / Task 2.8 Increment 72 (2026-11-23)
- Recorded bounded logical relationship and candidate constraint crosswalk in `TASK-2.8-RELATIONSHIP-CONSTRAINT-CROSSWALK-INCREMENT-72.md` based on DatabaseSetup headers and sampled repositories. Includes tenant-qualified references, project/event/form/information/history/material/procurement links and polymorphic attachment/audit risk.
- Candidate constraints are recommendations only; cardinality, independent-event policy, lifecycle/retention and cross-domain semantics remain pending discovery. MaterialPlanningRepository fetch failed (NOT_FOUND). E1 only; no runtime tests, DDL, migration, live data or production changes. G-01/G-10 remain BLOCKER; G-02/G-03/G-04 open/high; schema readiness NOT APPROVED.


### Task 0.2 / Task 2.8 Increment 73 (2026-11-23)
- Cross-operation dependency/failure boundary audit recorded in `TASK-2.8-CROSS-OPERATION-DEPENDENCY-FAILURE-BOUNDARY-INCREMENT-73.md`: project deletion re-collection/shared procurement protections/sequential partial-state boundaries; event void and project-information locks; project template allow-list/optimistic check; attachment storage→metadata→audit sequence.
- E1 only; no runtime tests. Reconciliation, transactions and fault-injection test requirements documented. G-01/G-10 remain BLOCKER; G-02/G-03/G-04 OPEN/HIGH; schema readiness NOT APPROVED. No production changes.


### Task 0.2 / Task 2.8 Increment 74 (2026-11-23)
- Recorded deletion coverage gap in `TASK-2.8-PROJECT-DELETION-COVERAGE-GAP-INCREMENT-74.md` after inspection of the collector and FormRepository: collector's explicit child inventory does not include ProjectInformation/history, EventFormData responses or reference-sync logs. This is a source-level coverage gap, not runtime proof of orphaning.
- Noted audit-write-after-project-row-delete boundary. V2 requires dependency registry, explicit disposition policies, durable deletion ledger and transactional audit/outbox. E1 only; G-01/G-10 remain blockers; no production changes.


### Task 0.2 / Task 2.8 Increment 75 (2026-11-23)
- Recorded Event void/reference-copy reconciliation trace in `TASK-2.8-EVENT-VOID-REFERENCE-COPY-RECONCILIATION-INCREMENT-75.md`: void guards and restoration/reconciliation sequencing, material-review failure handling, reference-copy operation replay and image-copy validation.
- E1 only; aggregate atomicity/recovery unverified. No runtime tests or production changes. G-01/G-10 remain blockers; G-02/G-03/G-04 remain open/high.


### Task 0.2 / Task 2.8 Increment 76 (2026-11-23)
- Bounded FormService/event-response source review documented in `TASK-2.8-FORM-SNAPSHOT-AND-WORKFLOW-DRAFT-PUBLISH-BOUNDARY-INCREMENT-76.md`: tenant/mapping/version/visibility validation, DefinitionSnapshot capture and response persistence; form/field version bumps and cache invalidation; published workflow draft cloning/version-qualified rule replacement.
- Atomicity, immutable response lineage, complete publish rollback and runtime concurrency/recovery remain unverified. E1 only; no tests executed or production changes. G-01/G-03/G-10 remain open; G-02/G-04 remain open; schema readiness NOT APPROVED.


### Task 0.2 / Task 2.8 Increment 77 (2026-11-23)
- Recorded `TASK-2.8-INDEPENDENT-EVENT-AND-PROCUREMENT-TIMELINE-PROJECTION-INCREMENT-77.md`: bounded EventService list evidence includes tenant-visible independent events (ProjectID absent) and project-linked visibility filtering; procurement order/receipt timeline is synthesized from source procurement records, not duplicate event rows.
- Full independent-event write lifecycle and projection consistency remain unverified; E1 only, no tests executed. G-01/G-10 blockers and G-02/G-03/G-04 open. No production changes.


### Task 0.2 / Task 2.8 Increment 78 (2026-11-23)
- Recorded `TASK-2.8-REFERENCE-COPY-OPERATION-LEDGER-AND-WORKFLOW-EDIT-FAILURE-BOUNDARY-INCREMENT-78.md`: project reference-copy operation replay lookup, persisted item-level copy plan/checkpoint/failure status, per-type retry path and image-copy validation; WorkflowService published-rule clone and draft transition/version replacement sequence.
- Dedicated durable operation ledger, DB aggregate atomicity, storage rollback/outbox and workflow clone/publish rollback/concurrency remain unverified. E1 only; no runtime tests. G-01/G-10 blockers, G-02/G-03/G-04 open; no production changes.


### Task 0.2 / Task 2.8 Increment 79 (2026-11-23)
- Recorded `TASK-2.8-ATTACHMENT-ACCESS-AND-INFORMATION-HISTORY-BOUNDARY-INCREMENT-79.md`: attachment visibility tenant/entity/project gates, image-copy ownership/category/idempotent duplicate lookup and compensating cleanup; project information revision/ExpectedRevision, event replay, history/audit and reference-sync sequencing.
- Storage reconciliation and transactional current/history/audit/sync remain unverified. E1 only; no runtime tests. G-01/G-10 blockers and G-03/G-04 open; no production changes.


### Task 0.2 / Task 2.8 Increment 80 (2026-11-23)
- Recorded `TASK-2.8-GET-ROUTE-SERVICE-AUTHORIZATION-CROSSCHECK-INCREMENT-80.md`: bounded Router GET checks for project information, attachments, galleries, event responses/EventData and procurement read routes; distinguishes explicit route permission checks from reliance on service validation.
- No confirmed authorization defect; method-level and runtime coverage incomplete. E1 only, no tests. G-01/G-10 blockers remain; no production changes.


### Task 0.2 / Task 2.8 Increment 81 (2026-11-23)
- Recorded `TASK-2.8-MUTATION-ROUTE-PERMISSION-CROSSCHECK-INCREMENT-81.md`: bounded Router mutation checks for procurement orders/receipts, project information save/approval, event form response update and attachment upload/delete; notes service-validation reliance for sampled EventData and form-response reads.
- Full route/service permission coverage and runtime negative tests remain pending. E1 only; G-01/G-10 blockers; no production changes.


### Task 0.2 / Task 2.8 Increment 82 (2026-11-23)
- Recorded `TASK-2.8-FRONTEND-API-CALLER-TRACE-INCREMENT-82.md`: read-only frontend source trace of index API URL configuration, API request/dedup/retry diagnostics and caller endpoint families in events/forms/attachments/projects modules.
- Caller/route/service/permission crosswalk remains partial; no confirmed mismatch from sampled excerpts. E1 only; no tests or production changes. G-01/G-10 blockers remain.


### Task 0.2 / Task 2.8 Increment 83 (2026-11-23)
- Recorded `TASK-2.8-ADDITIONAL-FRONTEND-CALLER-FAMILIES-INCREMENT-83.md`: read-only endpoint-family extraction from material planning, project detail, workflows, event types, reports and admin frontend modules.
- Route/caller/permission reconciliation remains partial; no confirmed mismatch. E1 only; no tests or production changes; G-01 blocker remains.


### Task 0.2 / Task 2.8 Increment 84 (2026-11-23)
- Recorded `TASK-2.8-PROCUREMENT-FRONTEND-INTEGRATION-CALLER-BOUNDARY-INCREMENT-84.md`: procurement integration caller snippets across event submission failure handling, project timeline projection filtering and report payload needs/calculations/orders; no standalone procurement module in the sampled index asset inventory.
- Full frontend/backend contract remains incomplete. E1 only; no tests or production changes. G-01 blocker and G-02 open/high remain.


### Task 0.2 / Task 2.8 Increment 85 (2026-11-23)
- Recorded `TASK-2.8-FRONTEND-SESSION-PERMISSION-AND-IMAGE-CALLER-TRACE-INCREMENT-85.md`: frontend auth/session/permissions endpoint-family and information-type/project-image caller trace; recommends central session/permission and attachment abstraction in V2.
- Complete session lifecycle/cache invalidation and permission contract remain unverified. E1 only; no tests or production changes. G-01 blocker remains.


### Task 0.2 / Task 2.8 Increment 86 (2026-11-23)
- Recorded `TASK-2.8-ADMINISTRATIVE-FRONTEND-CALLER-TRACE-INCREMENT-86.md`: read-only caller-family trace for users, roles, available permissions, departments, project statuses, permission catalogue, audit and workflow settings modules.
- Administrative route/payload/permission reconciliation remains partial. E1 only; no tests or production changes. G-01/G-10 blockers remain.


### Task 0.2 / Task 2.8 Increment 87 (2026-11-23)
- Recorded `TASK-2.8-FRONTEND-BACKEND-CROSSWALK-ROLLUP-INCREMENT-87.md`: consolidated observed frontend caller families and backend counterparts and explicitly bounded coverage status.
- The route-to-caller-to-service-to-repository matrix is not exhaustive and route negative/runtime tests remain unexecuted. G-01/G-10 blockers remain; E1 only, no production changes.


### Task 0.2 / Task 2.8 Increment 88 (2026-11-23)
- Recorded `TASK-2.8-EVENTDATA-AND-ATTACHMENT-DELETE-BOUNDARY-INCREMENT-88.md`: EventData read parent-event tenant/project visibility validation; attachment delete ordering storage -> metadata DELETED -> audit; ProjectDeletion dependency collector omission of project information/history, event form responses and reference-sync log in visible source.
- E1 only; no runtime tests or production changes. G-01/G-10 blockers; G-03/G-04 remain open.


### Task 0.2 / Task 2.8 Increment 89 (2026-11-23)
- Recorded `TASK-2.8-PROJECT-CASCADE-DELETE-RETRY-AND-SHARED-ORDER-BOUNDARY-INCREMENT-89.md`: cascade delete sequence, per-item failure capture, project physical deletion gating, shared/general procurement safeguards, partial retry boundary and audit-after-project-row-delete risk.
- No runtime proof. G-01/G-02/G-03/G-04/G-10 remain open/blocking as applicable. No production changes.


### Task 0.2 / Task 2.8 Increment 90 (2026-11-23)
- Recorded `TASK-2.8-PROCUREMENT-ORDER-RECEIPT-ATOMICITY-AND-ALLOCATION-INCREMENT-90.md`: tenant/project/event validation, order/receipt idempotency, allocation and receipt quantity ceilings, order NEEDS_REVIEW partial-write compensation, receipt batch insert followed by order status/audit sequencing.
- Source-only evidence; no runtime tests. G-01/G-02/G-03/G-10 remain open. No production changes.


### Task 0.2 / Task 2.8 Increment 91 (2026-11-23)
- Recorded `TASK-2.8-EVENT-VOID-LIFECYCLE-RESTORE-AUDIT-AND-MATERIAL-RECONCILIATION-INCREMENT-91.md`: event void eligibility, snapshot guarded restore, lifecycle/status/audit sequencing, workflow recomputation and caught material reconciliation failure.
- Source-only evidence; no runtime tests. G-01/G-03/G-10 remain open. No production changes.


### Task 0.2 / Task 2.8 Increment 92 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-PUBLISH-VERSION-SCOPE-AND-CONCURRENCY-INCREMENT-92.md`: publish version-scoped rules, active step membership validation scope concern, sequential publish/audit, missing visible publish lock/CAS and concurrency risk.
- E1 observation; potential scope issue pending validation, not a confirmed exploitable defect. No runtime tests. G-01/G-03/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 93 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-PUBLISH-SCOPE-CHECK-REFINEMENT-INCREMENT-93.md`: verified version-scoped rule repository lookup and locked replace operation; narrowed publish step-version scope concern pending verification because step repository source path could not be fetched.
- Potential gap remains unconfirmed; E1 only, no runtime tests. G-01/G-03/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 94 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-STEP-CREATE-DRAFT-CLONE-AND-UNIQUENESS-INCREMENT-94.md`: createStep code uniqueness scope, all-version step ordering, clone→workflow draft update→step insert sequential failure/concurrency boundaries.
- Business rule and runtime validation pending. E1 only; G-01/G-03/G-10 open.


### Task 0.2 / Task 2.8 Increment 95 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-DEPARTMENT-ASSIGNMENT-VERSION-SCOPE-INCREMENT-95.md`: assignment scope semantics for versioned steps, duplicate/concurrency boundary and batch atomicity/fallback writes.
- Business semantics remain unresolved; E1 only, no runtime tests. G-01/G-03/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 96 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-RULE-SAVE-STEP-VERSION-MISMATCH-INCREMENT-96.md`: saveStepEventRules validates workflow/step tenant relationship but does not explicitly bind selected step version to draft target version; credible mismatch path, runtime impact unverified. Sequential clone/replace/metadata/audit boundary.
- E1 source evidence only. No runtime tests; G-01/G-03/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 97 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-ASSIGNMENT-REPOSITORY-TENANT-AND-UNIQUENESS-INCREMENT-97.md`: repository helper lookup tenant scoping relies on callers, service filtering and storage-level uniqueness/concurrency constraints not demonstrated.
- E1 only; caller inventory and runtime tests pending. G-01/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 98 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-UPDATE-MASS-ASSIGNMENT-OF-MUTABLE-FIELDS-INCREMENT-98.md`: WorkflowService.update generic copy-through permits unexcluded lifecycle/version fields at service layer; route-level filtering/authorization pending.
- Potential mass-assignment boundary, not confirmed exploit. E1 only; no tests. G-01/G-03/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 99 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-ROUTE-PERMISSION-CROSSCHECK-INCREMENT-99.md`: route crosscheck confirms sampled workflow mutation endpoints require WORKFLOW_MANAGE; narrows Increment 98 reachability, but does not replace explicit mutable-field allowlisting.
- E1 only; route inventory remains partial and runtime tests pending. G-01/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 100 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-SHEET-RELATIONSHIP-AND-KEY-CONSTRAINT-CROSSWALK-INCREMENT-100.md`: crosswalk of Workflow/Step/Department assignment/Rule sheet fields and required relational constraints for V2.
- Header/source evidence only; Sheets headers do not prove constraints. No tests. G-01/G-10 remain open.


### Task 0.2 / Task 2.8 Increment 101 (2026-11-23)
- Recorded `TASK-2.8-WORKFLOW-TEST-INTENT-COVERAGE-CROSSCHECK-INCREMENT-101.md`: inspected existing workflow tests and identified untested version mismatch, protected-field, concurrency, fault and assignment uniqueness boundaries.
- Test source inspected only; tests NOT run. G-01/G-03/G-10 remain open.


### Task 2.8 Increment 102 (2026-11-23)
- Recorded `docs/audit/TASK-2.8-WORKFLOW-SERVICE-PUBLISH-AND-RULE-VALIDATION-TRACE-INCREMENT-102.md`: read-only source trace of `WorkflowService.createStep`, `getStepEventRules`, `saveStepEventRules`, and `publish`.
- Evidence: step creation checks tenant/workflow and duplicate Code, but uses sequential version-clone/draft-update/step-insert; rule saving validates the target draft version yet step membership is not explicitly checked against that same workflow version; publishing validates active-step membership for active rules and writes workflow publication state followed by audit.
- These are source-level boundaries, not confirmed runtime defects. No tests executed and no production changes. G-01/G-03/G-10 remain open.


### Task 2.8 Increment 103 (2026-11-23)
- Recorded `docs/audit/TASK-2.8-ATTACHMENT-UPLOAD-DELETE-RECOVERY-BOUNDARY-INCREMENT-103.md`: source trace of attachment upload persistence/audit sequencing and delete storage-first sequencing.
- Upload object precedes metadata insert and audit without visible compensating cleanup; delete removes storage before metadata soft-delete and audit. These create recoverability/orphan and metadata-to-storage inconsistency boundaries, not runtime-confirmed failures. E1 only, no tests. G-04/G-09/G-11 remain open; G-01/G-10 remain blockers.


### Task 2.8 Increment 104 (2026-11-23)
- Recorded `docs/audit/TASK-2.8-ATTACHMENT-VISIBILITY-COPY-IDEMPOTENCY-TRACE-INCREMENT-104.md`: visibility helper/list status handling, project-image listing, copy restrictions, copy idempotency/cleanup compensation and potential concurrency constraint gap.
- E1 only; no runtime tests. Status policy and concurrent deduplication remain unverified. G-01/G-04/G-10/G-11 remain open.


## Task 2.8 Increment 105 — Material-planning source retrieval limitation
- Recorded `docs/audit/TASK-2.8-MATERIAL-PLANNING-SOURCE-RETRIEVAL-LIMITATION-INCREMENT-105.md`: targeted backend repository code search for material planning did not return indexed source, so this increment makes no implementation claims. Source retrieval/tree access is required before a material-planning trace can continue.
- E0 for this increment. No tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED.


## Task 2.8 Increment 106 — Auth/session/user lifecycle boundary
- Recorded `docs/audit/TASK-2.8-AUTH-SESSION-USER-LIFECYCLE-TRACE-INCREMENT-106.md`: source inspection of auth, session, user update, permissions and selected Router paths. Observed unsalted SHA-256 password hash helper, globally unique username/login policy, token/session storage in Script Properties, configured expiry behavior, admin tenant-scoped session controls and sampled user-update route permissions. Session invalidation on account/password change remains unverified.
- E1 only; no runtime tests, live users, secrets or production changes. G-01/G-10 remain blockers; schema readiness NOT APPROVED.


## Task 2.8 Increment 107 — Role/user-role mutation boundary (2026-11-23)
- Recorded read-only role lifecycle and role/user-role mutation trace in `docs/audit/TASK-2.8-ROLE-USER-ROLE-MUTATION-BOUNDARY-INCREMENT-107.md`: tenant-scoped role/user validation, role code duplicate-check race boundary, sequential mutation/audit, Router role/user-role permissions and repository bulk locking/write semantics. Role-permission service orchestration remains unavailable via indexed source retrieval; no claims made about it.
- E1 only; no runtime tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 108 — Department lifecycle boundary (2026-11-23)
- Recorded read-only DepartmentService/Repository/Validator trace: tenant-scoped list/get/create/update, service-level tenant code duplicate checks, explicit update allowlist, request cache reset on writes, and sequential mutation/audit boundaries.
- E1 only; no runtime tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 109 — Project-status lifecycle boundary (2026-11-23)
- Recorded read-only ProjectStatusService trace: tenant/admin permission context, tenant filtering, create active-name duplicate validation, update mutable-field protections and audit sequence. Candidate consistency gap: reactivation to ACTIVE without supplying Name bypasses the visible duplicate-active-name check; runtime impact unverified. Create/update uniqueness checks and writes are not visibly atomic.
- E1 only; no tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 110 — Information type/column lifecycle boundary (2026-11-23)
- Recorded read-only InformationTypeService/Repository/Validator trace: tenant-scoped type checks, structural-change guard for active usage, revision updates, type/column soft-deactivation and code uniqueness/validation boundaries. Column deactivation dependency checks, revision CAS, storage uniqueness and transactional audit are not demonstrated.
- E1 only; no tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 111 — Form definition/field lifecycle (2026-11-23)
- Recorded read-only form/field lifecycle trace: tenant ownership, type and visibility validation, form version increments, field add/update/soft-delete dependency checks, 60-second ScriptCache invalidation and sequential mutation/audit boundaries. Composite DB constraints, transactional configuration writes and CAS are not demonstrated.
- E1 only; no runtime tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 112 — Event lifecycle boundary (2026-11-23)
- Recorded read-only EventService trace: event type/project/tenant/workflow/department validation, independent events, form and procurement integration sequencing, quantity variants, user-scoped idempotency and processing marker, void guards and timeline projection/pagination boundaries.
- Event, response, procurement integration, project mutation and audit are sequential; atomicity and PROCESSING recovery/DB uniqueness are not demonstrated. E1 only; no tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 113 — Event form response/amendment sync boundary (2026-11-23)
- Recorded read-only FormService response trace: required mappings/form version/field validation, response definition snapshots, response save and central-information sync, direct template update concurrency checks, amendment event gating/replay and project-code reconciliation. Response+central projection+audit atomicity remains unproven.
- E1 only; no tests or production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.


## Task 2.8 Increment 114 — Project lifecycle/repeat-copy boundary (2026-11-23)
- Read-only trace recorded for draft create/completion, tenant-scoped active project creation, workflow immutability after event history, repeat project source validation and configured INITIAL_COPY/LIVE_SYNC/INDEPENDENT policies, staged copy plan/retry state, profile/information image copying, operation IDs, and code uniqueness checks.
- DB transaction/uniqueness guarantees and cross-storage recovery remain unproven. E1 only, no tests/production changes. Task 2.8 IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 open.


## Task 2.8 Increment 115 — Project information revision and reference sync boundary (2026-11-23)
- Audited value validation (including attachment ownership, multi-row bounds), revision/history, approval and LIVE_SYNC fan-out, idempotency logs, conflict detection/resolution and local preservation. Sequential value/history/audit/sync writes and durable transactional recovery are not proven.
- E1 only, no tests or production changes; Task 2.8 IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain.
