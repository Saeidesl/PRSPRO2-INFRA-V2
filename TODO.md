# PMS V2 — Roadmap & Task Status

> Status values: NOT STARTED / IN PROGRESS / IMPLEMENTED / TESTED / ACCEPTED / COMPLETE. A task is COMPLETE only after successful verification and explicit Product Owner acceptance.

## Phase 0 — Discovery & Repository Preparation

### Task 0.1 — Verify V2 repositories and establish project governance
- [x] Verify Infra repository availability and access.
- [x] Search for the three remaining V2 repositories; not found, creation pending.
- [x] Establish initial README, TODO, and ARCHITECTURE documents.
- [x] Verify initial documentation files by reading them back from GitHub.

**Status:** IN PROGRESS  
**Notes:** Infra repository and documentation verified. Searches did not find PRSPRO2-V2, PRSPRO2BACKEND-V2, or PRSPRO2DATABASE-V2; creation/access remains pending.

### Task 0.2 — READ-ONLY audit of current PMS
- [x] Preliminary frontend repository structure and selected modules retrieved.
- [x] Backend route surface and selected service files retrieved.
- [x] Inventory database sheet definitions and headers from DatabaseSetup.js.
- [x] Preliminary audit of tenant-scoping patterns, workflow-version/rule context, event/form version snapshot references, and procurement idempotency paths.
- [x] Extract selected workflow-event sequencing, repeat-project/reference copy behavior, and file lifecycle rules (selected paths).
- [ ] Extract relationship/business rules in detail; audit workflows and role/permission enforcement.
- [x] Preliminary inventory of Drive storage and attachment lifecycle.
- [ ] Inventory integrations (Telegram and other external services) in detail.
- [x] Inventory frontend and backend test-file names and CI workflow files (inventory only; tests not run).
- [ ] Identify critical risks, performance constraints, migration dependencies, and test/deployment coverage.
- [ ] Record detailed findings and unknowns in ARCHITECTURE.md.

**Status:** IN PROGRESS

### Task 0.3 — Discovery report and target boundaries
- [ ] Produce a verified current-system map.
- [ ] Identify migration candidates, exclusions, and compatibility requirements.
- [ ] Draft V2 domain boundaries and migration strategy (no schema implementation yet).
- [ ] Present findings for Product Owner review.

**Status:** NOT STARTED

**Phase 0 exit criteria:** Repository access verified; audit and architecture baseline documented with unknowns clearly identified; Product Owner accepts the discovery report.

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
