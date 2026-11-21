# PMS V2 — Architecture & Discovery Record

## 1. Objective
Develop a standalone garment-production management system, independent of Google Apps Script and Google Sheets, while preserving verified existing business rules, workflows, UX, identifiers, and data relationships where practical and explicitly approved.

## 2. Target Stack (approved project direction)
- Backend: Node.js + TypeScript.
- Database: PostgreSQL.
- Frontend: existing HTML/CSS/JavaScript, adapted to the independent API.
- Infrastructure: Ubuntu, Docker, Docker Compose, Nginx.
- CI/CD: GitHub Actions.
- File storage: independent storage; exact solution pending discovery.

These are target choices, not claims of implementation.

## 3. Repository Governance
- Production frontend reference: PRSPRO2-PRODUCTION (read-only).
- Production backend reference: PRSPRO2BACKEND-PRODUCTION (read-only).
- V2 repositories planned:
  - PRSPRO2-V2 — frontend.
  - PRSPRO2BACKEND-V2 — backend.
  - PRSPRO2DATABASE-V2 — schema/migrations.
  - PRSPRO2-INFRA-V2 — infrastructure, CI/CD, governance and documentation.
- Do not modify production repositories or migrate live data during discovery/development.
- No secrets or real user data in GitHub.
- No deployment or VPS acquisition until deployment is justified and approved.

## 4. Verified Discovery Context (initial, based on existing repository documentation)
The following are documented in the read-only production repositories and are not yet independently verified by a full source-code audit:
- Current backend stack: Google Apps Script V8 and Google Sheets.
- Current frontend: HTML, CSS, JavaScript.
- Existing workflow stages documented as Design, Cutting, Sewing, Packaging, Warehouse; workflow configuration may assign departments.
- Existing system includes projects, event registration/forms, dynamic information types, attachments, procurement, reports, templates/printing, permissions, and notification/integration components.
- Tenant/project scope and permission enforcement are described in backend architecture documentation.
- Attachment handling and Drive storage are described in backend architecture documentation.
- Production data and the existing PMS remain operational and must be protected.

## 5. Discovery Findings vs Unknowns
### Findings from documentation review
- Read the production frontend TODO.md and ARCHITECTURE.md.
- Read the production backend TODO.md and ARCHITECTURE.md.
- Documentation indicates active development of template/printing functionality and procurement improvements; actual implementation, tests, and behavior require source audit.

### Pending independent verification
- Actual source tree and dependency inventory.
- API route inventory and payload contracts.
- Full business rules and workflow transitions.
- Sheet inventory, headers, identifiers, constraints, and relationships.
- Authentication, permission and tenant-boundary enforcement.
- Data consistency, concurrency, and transaction-like operations.
- Performance measurements and bottlenecks.
- Integrations, file lifecycle, and storage limits.
- Automated test coverage and deployment process.
- Migration feasibility, validation and rollback requirements.

## 6. Migration Principles
- Audit actual structures and business rules before designing schema.
- Preserve stable identifiers and relationships where possible.
- Use repeatable, observable migration scripts and validation.
- Test only with sanitized or synthetic data before approval.
- No uncontrolled dual-writing.
- Plan backups, recovery, controlled cutover, rollback and old-system read-only retention.

## 7. Decisions Log
- D-001: V2 is independent of GAS and Sheets.
- D-002: Production repositories are read-only references.
- D-003: GitHub is the primary development and project-management environment.
- D-004: No VPS purchase/deployment before readiness and approval.
- D-005: Real user/data migration requires successful validation and explicit approval.

## 8. Audit Log
- 2026-11-21: V2 repository search found none of the four repositories before the Infra repository was created.
- 2026-11-21: Infra repository Saeidesl/PRSPRO2-INFRA-V2 verified accessible.
- 2026-11-21: Initial documentation review performed on the production frontend and backend TODO.md and ARCHITECTURE.md. This was documentation discovery only; full source audit is pending.
- 2026-11-21: No production code or repository was modified.

## 9. Source Audit — Preliminary Inventory (2026-11-21)

### Repository tree inventory
- Frontend tree contains 187 entries; notable structure: core API/cache/router/session/permissions; feature modules for projects, events, procurement, workflows, reports, forms, attachments, print designer; page files and a broad test directory; GitHub Actions workflows include frontend checks, staging deployment, QA and UI/performance-related workflows.
- Backend tree contains 153 entries; notable services/repositories include Router, DatabaseSetup, GoogleSheetsRepository, authentication/access control, projects, events, forms, workflow, procurement, attachments/storage, audit, reports, print templates and Telegram; tests cover service/API contracts, caching, schema, integration and performance diagnostics.

### Source files fetched for read-only review
Frontend: js/core/api.js, js/core/cache.js, js/core/router.js, js/modules/projects.js, js/modules/events.js, js/modules/procurement.js.
Backend: Router.js, DatabaseSetup.js, GoogleSheetsRepository.js, AuthService.js, AccessControlService.js, EventService.js, ProcurementService.js.

### Audit scope status
- Repository trees and selected source files retrieved successfully. This is a preliminary audit; detailed extraction of API contracts, table schemas, workflow rules, permission coverage and test execution remains pending.
- The audit is performed through read-only GitHub retrieval; no production repository writes have been performed.

## 10. Next Work
Phase 0, Task 0.1: verify access/availability of the three remaining V2 repositories.
Phase 0, Task 0.2: perform read-only audit of the production frontend and backend source, database structure and workflows.

## 11. Source Audit — Database and API Surface (2026-11-21)

### 11.1 Verified sheet definitions from backend DatabaseSetup.js
The source file declares these sheet-backed entities (table names are the configured sheet names; this is an inventory, not yet a relational schema design):
- Tenant and access: 01_Tenants, 02_Users, 03_Departments, 04_Roles, 05_Permissions, 06_UserRoles, 07_RolePermissions.
- Projects/workflows: 08_Projects, 09_Workflows, 10_WorkflowSteps, 17_WorkflowStepDepartments, 37_WorkflowStepEventRules, 18_ProjectStatuses.
- Events/forms: 11_EventTypes, 12_EventFields, 13_Events, 14_EventData, 32_Forms, 33_FormFields, 34_EventTypeForms, 35_EventFormData.
- Information model: 28_InformationTypes, 29_InformationTypeColumns, 30_ProjectInformation, 31_ProjectInformationHistory.
- Files/audit/templates: 15_Attachments, 16_AuditLogs, 36_PrintTemplates, 38_ProjectReferenceSyncLog.
- Procurement/material planning: 18_MaterialNeeds, 23_EventTypeProcurementSettings, 24_ProjectMaterialSpecs, 25_MaterialNeedCalculations, 19_PurchaseOrders, 20_PurchaseOrderItems, 21_PurchaseAllocations, 22_MaterialReceipts.

**Important discovery:** the configured names contain overlapping numeric prefixes (e.g. MaterialNeeds and ProjectStatuses both use prefix 18). Prefixes must not be treated as unique identifiers; exact sheet names and header definitions are authoritative. Table relationships, constraints, and data semantics still require service/repository-level verification.

### 11.2 API route families observed in Router.js
Route families include authentication/session, projects and project information/history/reference copy, project image galleries, departments and statuses, workflow management and step event rules, event types/forms, event registration and amendments, information types and columns, print templates and image/PDF endpoints, attachments, users/roles/permissions, audit, dashboards/reports, and procurement needs/orders/receipts/workspace/reporting.

This list is a route-family inventory only. Exact request/response contracts, HTTP method semantics, and route authorization are still being extracted.

### 11.3 Selected frontend architecture observations
- js/core/api.js requires an explicitly configured API base URL and avoids fallback to Development/Staging deployments; it also contains in-flight GET deduplication and diagnostics functionality.
- js/core/cache.js stores cache data in browser localStorage and uses a cache-version mechanism and optional TTL.
- js/core/router.js maintains route definitions and route-permission metadata.
- The selected feature modules are substantial: projects.js (~168 KB), events.js (~219 KB), and procurement.js (~73 KB); module boundaries and behavior will require careful mapping rather than a superficial rewrite.

### 11.4 Audit limits and pending risks
- Sheet header definitions are verified from source, but no real sheet data or production spreadsheet was accessed.
- No exhaustive foreign-key/relationship analysis or migration mapping has been performed.
- Route families are inventoried; exact endpoint contracts and authorization checks require continued review.
- No tests were executed and no runtime or performance claims are made.
- All retrieval remains read-only; production repositories have not been written to.


## 12. Source Audit — Security, Workflow, Projects, and Storage (2026-11-21)

### 12.1 Permission and tenant scoping — preliminary code observations
- PermissionService uses user/tenant-aware permission lookup and caches permission results with a tenant/user key pattern.
- WorkflowService and ProjectService repeatedly scope records to the current user's TenantID; workflow source includes version-aware selection (draft/published versions).
- Router has route-specific permission checks and admin-only paths. These patterns are promising but are not a complete security assessment; authorization paths, bypass resistance and cross-tenant tests remain to be audited.

### 12.2 Workflow and project behavior
- Workflow source has version/lifecycle concepts and tenant-scoped workflow/step/event-rule handling.
- ProjectService contains locking-related logic (LockService), validation and audit/notification-related behavior. Detailed transition rules, side effects and compatibility constraints remain pending.
- MaterialPlanningService uses script locks for certain operations and has calculation/specification revision concepts. These are important migration requirements to investigate, not a blueprint for the new DB design.

### 12.3 File-storage dependency
- AttachmentService is tenant-scoped and references Google Drive storage provider metadata.
- GoogleDriveStorage.js directly uses DriveApp operations, confirming a GAS/Google Drive dependency in the current attachment storage implementation.
- V2 storage design must preserve attachment associations and lifecycle semantics, while replacing provider-specific operations; exact metadata, upload, download, copying and authorization flows remain pending audit.

### 12.4 Reliability/migration risks identified for deeper assessment
- Google Sheets multi-sheet operations are not equivalent to database transactions. Source procurement code explicitly prevalidates graphs before writes and comments on the lack of atomic multi-sheet commits; the V2 migration should model transactional operations where applicable.
- LockService-based concurrency controls and idempotency mechanisms appear in selected project/material/procurement paths. Their business guarantees must be mapped and tested before migration.
- Tenant isolation, workflow versioning, file access controls, dynamic forms and project-reference synchronization are high-priority audit areas.

These are preliminary observations from selected source files; no live runtime, production spreadsheet or Drive data was accessed, and no test suite was executed.


## 13. Source Audit — Event/Form contracts, Procurement Idempotency, and Test Inventory (2026-11-21)

### 13.1 Event and form data/versioning observations
Selected EventService, EventRepository, EventDataRepository, EventTypeRepository, FormRepository and WorkflowStepEventRuleRepository sources were retrieved. The observed data model carries tenant identifiers, workflow version/step context, event-type references, and form-version/snapshot concepts (including EventFormData definition snapshots). Event service logic filters workflow rule context by project workflow version and current step. Detailed behavioral mapping, amendment handling, and full request/response contracts remain pending.

### 13.2 Workflow enforcement and event sequencing
WorkflowStepEventRuleRepository supports workflow-version-specific rule retrieval and cloning rules between versions. EventService applies version and step filters to rule context. The repository contains test files specifically for workflow-event enforcement and event rules, indicating these behaviors are covered by contract/regression tests, but tests were not run in this audit.

### 13.3 Procurement operation reliability
ProcurementService source shows idempotency keys for purchase order creation and receipts, replay checks, project/need/order validation, allocations and receipt processing. Source code references audit logging for order operations. This must be translated into database constraints and transaction-safe idempotency in V2; exact invariants require a deeper operation-level mapping.

### 13.4 Existing test inventory — backend
The backend tree includes tests for access control, attachment service, dashboard performance contracts, event form-definition batching/caching, event idempotency, form API contracts, Google Drive storage, Sheets cache invalidation, information cardinality and file types, material planning, print templates, procurement integration/service, project deletion/draft-information/reference-copy/image gallery, reports, reusable forms, schema contracts, Telegram integrations, workflow enforcement and workflow rules.

### 13.5 Existing test inventory — frontend
The frontend tree includes tests for event filtering/history/forms/image previews/submission latency and progress, event type loading, project entry and picker, information types and files, Jalali calendar/date handling, print designer/output, project information, reference synchronization/conflicts, reports, reusable forms, router deployment path, shared page cache, workflow management and modal accessibility. GitHub Actions workflow files were inventoried for frontend checks, staging, cross-repository QA, UI performance, regression and agent suite.

**Verification status:** test file inventory only. No tests were executed; these files and workflow names do not establish that current tests pass or that coverage is complete.

### 13.6 Pending audit
- Trace major API handlers to service functions and exact permission codes/HTTP contracts.
- Detail workflow transition, event amendment, reference-copy/synchronization, attachment lifecycle and material receipt invariants.
- Review tests and CI definitions to understand actual execution commands, gates and deployment boundaries.
- Finish migration-risk assessment and discovery report.


## 14. Source Audit — Reference Projects, Event Sequencing, and Lifecycle (2026-11-21)

### 14.1 Repeat-project/reference-copy behavior (verified selected ProjectService paths)
- Repeat workflows require a source project within the same tenant; the source must be an active/non-draft project and accessible to the requesting user. Client-provided reference-policy overrides are not accepted as authoritative; the workflow policy is used.
- Reference information policies observed: `INITIAL_COPY`, `LIVE_SYNC`, and `INDEPENDENT`. A per-type copy plan is persisted in project reference-copy status JSON, and a request operation ID is used to recover/replay project creation requests.
- Reference-copy plan can include the source profile image. File-based information without image fields may be skipped. Copy processing is individually tracked; source image attachment validation is tenant/project scoped and copied images receive separate attachment records/storage objects.
- Selected copy path creates project-information records/history and, for LIVE_SYNC, a sync-log record with source revision and idempotency key. Conflict resolution and broader synchronization behavior still need further tracing.

### 14.2 Workflow-event sequencing and manual completion
- EventService selected source applies configured step rules. Requirements include REQUIRED, REFERENCE, and NOT_REQUIRED semantics, repeat allowances, sequence modes (STRICT and GROUPED), ordering/group ordering, and step-specific event-type eligibility.
- Under strict sequence, earlier incomplete required events block later events. Under grouped sequence, earlier incomplete required groups block later groups.
- Manual step completion is permitted to system administrators or users in a department assigned to the current workflow step; completion requires all required events satisfied and a workflow configuration requiring manual completion. Completed manual steps reject further events in that step.
- Event voiding can restore captured prior project status/current-step state and then refresh workflow-rule progress; material planning may be notified to review affected needs. Exact edge cases remain pending.

### 14.3 Project creation and access boundaries (selected source)
- Tenant identity is derived from the authenticated user rather than client input. Draft projects are handled separately from active workflow projects.
- Active project creation requires a published/active workflow, a valid first active step, tenant-valid departments/users, and a tenant-defined active project status (or legacy/default status behavior).
- Repeat-project source validation and duplicate project-code validation are tenant-scoped; create operations use a lock around critical insertion/validation paths.

### 14.4 File lifecycle and access (selected AttachmentService/GoogleDriveStorage)
- Attachments are linked to PROJECT or EVENT entities, tenant-scoped, and project/event visibility checks are applied. Event attachment visibility inherits parent-project visibility.
- Upload validates file metadata/content and stores a provider key; project files have canonical server-side names. Download/thumbnail paths validate attachment tenant/visibility/status. Delete currently deletes/trashes the storage object and marks the attachment record DELETED, then audit-logs.
- Current provider is Google Drive (DriveApp); tenant folder and root-folder behavior are present. V2 needs provider-neutral storage identifiers, safe download authorization, soft-delete/recovery policy decisions, and migration mapping. Selected code is not a full file-security audit.

### 14.5 Audit limitations
- These findings are from selected code paths only. API route/permission mapping is not exhaustive; no tests were executed and no live data, spreadsheet, or Drive contents were accessed.


## 15. Source Audit — Information Revision, Live-Sync Conflict, and Project Deletion (2026-11-21)

### 15.1 Project-information revision and approval
- Selected ProjectInformationService source validates information types/columns and attachment ownership/type/status, supports single/multiple cardinality and data-type validation, and records revisions/history on save.
- Saves can use ExpectedRevision to detect stale writes; repeated event/value writes can be idempotently recognized. Approval history is revision-specific, and approvals trigger reference-sync processing.
- Live reference sync is revision/idempotency-key aware. It checks target state against the prior synchronization record; local edits or locally-kept conflict states can prevent overwriting and create conflict records. Conflict resolution requires revision checks and a decision; a source revision mismatch is treated as stale.

### 15.2 Event voiding (selected EventService path)
- Event voiding is subject to tenant/project visibility and event-type AllowVoid configuration, and rejects voiding when later-step activity constrains the event timestamp/change.
- For qualifying events, void operation restores stored prior project status and current-step status, marks the event VOID, logs the operation, recalculates workflow-rule progress and updates step status if necessary. Material planning may be notified to review voided-event impact.
- This is a source-path observation, not a complete event amend/revision audit.

### 15.3 Project deletion (selected ProjectDeletionService and Router)
- Admin-only, tenant-scoped project deletion has a preview endpoint and requires exact project-code confirmation. It collects dependencies across events/event data, material needs, specifications/calculations, allocations, receipts, order items/orders, and attachments.
- The collector protects shared/general procurement orders and order lines used by other project allocations. Deletion uses soft-deletion/VOID markers for related rows where supported, trashes attached Drive objects, then physically deletes the project row. Errors are collected and surfaced; audit logging records dependency counts and failures.
- V2 should not replicate destructive cascade semantics blindly. Prefer transaction-backed deletion policy, dependency preview, confirmation, referential integrity, retained audit, shared procurement protection and recoverability. Whether V2 project deletion should be soft-delete-only is a pending PO/business policy decision.

### 15.4 Audit limits
- Findings are based on selected code paths only. No operational data was accessed and no tests were run.


## 16. Source Audit — CI/QA Gates (workflow definitions read-only; 2026-11-21)

### Backend gates observed
- Backend-check workflow uses Node 20, runs `node --test tests/*.test.js`, validates Apps Script manifest JSON and syntax of top-level JS files, rejects duplicate target names and checks a minimum source-file count.
- PMS agent suite additionally runs a read-only domain contract audit script, tests, source/manifest validation and schema-contract safety test.

### Frontend gates observed
- Frontend-check workflow uses Node 20, runs syntax checks across JS files, regression tests, local asset existence and CSS brace-balance checks, and an E2E safety guard that expects write-enabled staging E2E to fail closed when isolated staging API credentials/config are absent.
- Frontend agent suite performs read-only contract/diff review and asset/CSS checks.
- Cross-repository QA checks frontend syntax/assets and runs backend tests and source/manifest checks against the backend main reference; it also checks presence of key event/material-planning components.

### Important limitation
- Workflow definitions were read, not executed. No CI statuses/runs were evaluated in this audit session. These workflows provide static/test gates, but actual CI outcomes and test adequacy require separate verification. Tests that create records must remain isolated from production.


## 17. V2 Repository Availability Check (2026-11-21)
- GitHub API lookups returned 404/not found for `Saeidesl/PRSPRO2-V2`, `Saeidesl/PRSPRO2BACKEND-V2`, and `Saeidesl/PRSPRO2DATABASE-V2` during this check. They are not confirmed as existing repositories; repository creation remains pending and should be performed only after Phase 0 audit and the documented preparation step.
- `PRSPRO2-INFRA-V2` remains verified and accessible.


## 18. Source Audit — Telegram and Procurement Event Integration (2026-11-21)

### 18.1 Telegram integration (selected TelegramBotService and diagnostics source)
- Telegram token and webhook secret are read from Apps Script Script Properties; tenant-to-chat recipients are configured through a property mapping. No secret values were accessed.
- The bot supports `/start` and `/help` registration messaging, `/chatid`, a pending tenant registration window, and validation of active tenant state before registering a chat.
- Polling mode removes any prior webhook, creates a one-minute time-based trigger and uses a persisted update offset. Message sending uses Telegram's `sendMessage` API. A diagnostics utility reports configuration and webhook state without exposing token values.
- V2 should treat notifications as an optional integration adapter, externalize credentials, define secure tenant recipient registration, retry/backoff and delivery logs, and avoid making core production operations depend on notification delivery. Exact notification event triggers and opt-in scope need further audit.

### 18.2 Procurement/event integration
- `ProcurementEventIntegrationService` supports a narrowly scoped event-type setting to recalculate confirmed material needs from a numeric, single-cardinality central-information type.
- The configured quantity must be supplied by exactly one connected form response, positive, and persisted centrally from the same event; project access and tenant scope are validated. Setting changes are audit logged.
- Source comments explicitly keep purchase orders and receipts independent from this event integration; legacy receipt mappings are inert.
- This is a selected service audit, not a complete procurement domain mapping.

### 18.3 Audit limits
- Source reviewed read-only. No Telegram properties, token, recipient mapping, real production notification, external API action, or live business data was accessed. No test executed.

## 19. Additional schema and event-operation discovery (2026-11-21)

- Extracted the exact declared column/header arrays from `DatabaseSetup.js`; this is metadata only. It confirms the schema areas and key fields for tenant/user/role relationships; project/workflow versions; event type, event, event-data; information-type columns, project information and revision history; forms, form fields, event-form associations and form response snapshots; attachments/audit; workflow event rules; reference sync; and procurement needs/specifications/calculations/orders/items/allocations/receipts.
- Preliminary route families extracted from literal Router path branches include authentication, projects, statuses, departments, project images, workflow management, templates, forms, central information, event types/events, attachments, users/roles/permissions, audit, dashboards/reports and procurement. Duplicate route branches and dynamic routes require manual mapping before this becomes a route-permission matrix.
- EventService `create` has an optional idempotency key, validates its length, uses a user-scoped lock and tenant/user-scoped replay lookup; replay returns the existing event/context rather than creating a duplicate. If a prior request remains PROCESSING, it blocks replay and requires investigation.
- Event void path validates tenant and project access, already-void state, event type configuration (`AllowVoid`), actor admin/department eligibility, and downstream/later-event constraints. Exact rollback behavior is not fully traced in this increment.
- These findings are source review only. No test execution or operational data access.


## 20. Source Audit — Router & Authorization Path (2026-11-21)

### 20.1 Router authorization observations
- Explicit route checks observed across projects, events, forms, workflow management, print templates, users/roles, audit, attachments, procurement, and reporting.
- Admin-only operations observed: project deletion preview/removal and session listing/revocation.
- Draft-project exception: selected project GET/PUT and material-spec creation/reference-copy paths allow a creator with PROJECT_CREATE where ordinary PROJECT_VIEW/PROJECT_EDIT is unavailable, subject to tenant, draft state and creator checks in examined branches.
- Generic AccessControlService.enforceRouteAccess returns true after authentication for non-admin users; route-specific checks occur in Router/services. It is not itself a security barrier. V2 should centralize authorization, fail closed, and test tenant-aware access.
- PermissionService resolves tenant-bound active roles and role-permission mappings and caches permissions per request/user. Source observations only; not a security test.

### 20.2 Amendment and void routing
- Event form response update: PUT `/events/:eventId/forms/:formId` -> EVENT_EDIT -> FormService.updateEventFormResponse.
- Event void: PUT `/events/:eventId/void` -> EVENT_EDIT -> EventService.void.
- Full amendment revision/snapshot and void rollback dependency trace remains incomplete.

### 20.3 Audit limitations
- No exhaustive route-by-route permission matrix reconciled with service checks.
- No tests, runtime probes, penetration tests, or data access performed.


## 21. Source Audit — Form Amendment and Logical Relationships (2026-11-21)

### 21.1 Form amendment path
- `FormService.updateEventFormResponse` validates the original event and project within tenant scope, project availability, and the amendment event as same-tenant and same-project.
- Amendment type qualifies when explicitly marked `IsFormAmendment` or when a legacy name/code marker matches EDIT/UPDATE/AMEND/ویرایش/اصلاح. Explicitly configured amendment types restrict the allowed form IDs through `AmendableFormIDs`.
- The operation validates form version, input shape, field visibility and required rules, field data types, and field membership. It locks and rereads response data before writing; replay through the same amendment event is idempotent if payload matches and conflicts if it differs.
- Form response changes synchronize ProjectInformation values/deactivations and special project-code handling, then log an audit record for non-replay. The response and information sync are separate Sheets operations and therefore are not atomic; partial-failure recovery is a key V2 requirement.

### 21.2 Logical data relationships observed (not FK constraints)
- Project (`08_Projects`) logically parents events (`13_Events`) via ProjectID; events may also carry WorkflowID/WorkflowStepID/EventTypeID and actor/department fields.
- Event payload data (`14_EventData`) is associated by EventID.
- Form definitions: `32_Forms` -> `33_FormFields` by FormID; field references `InformationTypeID`.
- Event-type/form associations: `34_EventTypeForms` links EventTypeID and FormID.
- Event form responses: `35_EventFormData` associated by EventID/FormID, with response/snapshot JSON and version metadata.
- Central information: `30_ProjectInformation` links ProjectID and InformationTypeID; `31_ProjectInformationHistory` links ProjectInformationID and records revisions. Reference sync logs (`38_ProjectReferenceSyncLog`) link source/target project and information type.
- Attachments (`15_Attachments`) use polymorphic EntityType/EntityID rather than one dedicated FK; audit logs (`16_AuditLogs`) also use entity type/ID.
- These are logical relations established from selected source paths and headers; they are not enforced PostgreSQL-style foreign-key constraints in the existing Sheets system. Complete relationship inventory remains pending.


## 22. Source Audit — Procurement Operation and Event Void Recovery (2026-11-21)

### 22.1 Purchase-order creation
- Order creation validates tenant/project/need scope, line name/unit and positive quantity, optional unit price, project-need consistency, allocation scope and quantity, and total allocations not exceeding the ordered quantity.
- Outstanding quantity calculations account for prior allocations and eligible order items; surplus quantity/cost is tracked distinctly. Shared/general order scenarios and protected need states are handled in validation paths.
- An idempotency key and script lock protect order creation. The order header, items and allocation records are multiple Sheets writes; if a later write fails, the order is marked `NEEDS_REVIEW` and recovery context is added rather than pretending the operation completed.
- V2 should implement transactional order/line/allocation creation, idempotency uniqueness, constrained quantity arithmetic, reconciliation records, and audit logging.

### 22.2 Receipt processing — pending deeper trace
- Receipt creation and batch processing are lock/idempotency guarded. The exact validation edge cases, allocation accounting, and receipt-to-need recalculation effects require further source tracing before migration rules are finalized.

### 22.3 Event void restoration safety
- Event void restoration checks that the project still matches the event's expected configured changes and that a prior state snapshot is available. If not, it rejects restoration to avoid overwriting intervening project changes.
- On successful void, it restores prior project fields, marks the event VOID, refreshes workflow-rule progress and step state, audits, and attempts material-needs review/reconciliation. Reconciliation failure is surfaced and logged rather than represented as successful.
- This is still non-transactional in Sheets; V2 should coordinate event status, project-state restoration, workflow progress and material-plan reconciliation in one transaction where possible, with durable reconciliation/outbox handling for external effects.

### 22.4 Audit limitations
- Selected source paths only. Receipt details, complete route/permission matrix and runtime/test verification remain pending.


## 23. Source Audit — Receipt Allocation and Project Deletion Dependencies (2026-11-21)

### 23.1 Receipt rules
- Batch receipt processing checks tenant ownership, active order status, project/source-event validation, positive quantities, total receipts against ordered quantity, and cumulative project receipts against allocation amounts.
- For allocated items, project receipts require an allocation to that project and cannot exceed its allocated quantity. General-stock receipt of unallocated surplus is permitted only within the unallocated order portion. Items with no allocations validate expected project linkage.
- Batch records are inserted in bulk; order status is subsequently recomputed (`ACTIVE`, `PARTIALLY_RECEIVED`, `RECEIVED`) while `NEEDS_REVIEW` orders are protected from this status rewrite. Audit rows are emitted per receipt.
- Single receipt processing includes source-event/item replay checks and allocation/project cumulative limits. The single and batch paths have differing validation/replay details; V2 should converge them on a shared domain rule and transactional service.

### 23.2 Project dependency-aware deletion
- Admin and tenant checks, explicit project-code confirmation, and a script lock guard deletion. Preview exposes dependency counts before deletion.
- Dependencies collected include project events, event data, needs, material specs/calculations, attachments, receipts, allocations, order items, and eligible purchase orders.
- Shared/general procurement protection: a purchase order is only selected for deletion when all its active items are included and none has other-project allocations. Items linked to other project allocations are protected.
- Legacy child sheets without Status columns are prepared for soft deletion; events are marked VOID. Storage trash occurs after attachment record deletion. The project row is physically removed only if dependency processing has no failures; failures leave potentially partial state and are reported.
- V2 should use a transactional project archive/deletion state, dependency preview and explicit safeguards, retain audit/recovery, protect shared procurement records, and queue external storage cleanup through durable outbox/jobs.

### 23.3 Audit caveats
- The source has separate single and batch receipt validation paths and non-transactional status updates. This was not tested in runtime. No production data was read or modified.


## 24. Repository-Backed Relationship Map (provisional, 2026-11-21)

### Core production graph
- Project (`08_Projects.ID`) -> Events (`13_Events.ProjectID`) -> EventData (`14_EventData.EventID`). Event also references workflow/version, workflow step, event type/version, performer, creator and department.
- EventType -> EventTypeForms (`34_EventTypeForms.EventTypeID`) -> Form (`32_Forms.ID`); Form -> FormFields (`33_FormFields.FormID`). Event responses (`35_EventFormData`) associate EventID/FormID and include form-version and definition snapshot context.
- InformationTypes -> InformationTypeColumns; FormFields reference InformationTypeID; ProjectInformation connects ProjectID and InformationTypeID; ProjectInformationHistory references ProjectInformationID; reference-sync log associates source/target projects and information type.
- Workflow -> WorkflowSteps; WorkflowStepDepartments associates steps/departments; WorkflowStepEventRules associates WorkflowID, WorkflowVersion, WorkflowStepID and EventTypeID. Preserve version-context keys and rule snapshots/context.
- Attachments and AuditLogs use polymorphic EntityType/EntityID pointers; V2 must decide between explicit link tables, scoped polymorphic models, or separate attachment/audit relation structures with integrity enforcement.

### Procurement graph
- Project -> MaterialNeeds and MaterialSpecs; MaterialNeed -> calculations and may link SourceEventID and MaterialSpecID.
- PurchaseOrder -> PurchaseOrderItems; item references MaterialNeed (optional) and PurchaseOrder; PurchaseAllocations and MaterialReceipts link through PurchaseOrderItemID, and also carry project/need context.
- Calculations reference project, spec, need, and source event context. Shared orders/items may serve multiple projects through allocations; delete and migration logic must not assume exclusive ownership.

### Relationship and schema design implications
- Current Sheets data access provides logical relationships rather than DB-enforced foreign keys. V2 needs explicit FK constraints, tenant-aware integrity, indexes on relationship/tenant/version fields, and clear cardinality/uniqueness rules after source/data discovery.
- Preserve event/form and workflow snapshots/version context to support historic records when definitions change. Do not silently apply current definitions to old events.
- Relationship map is provisional; complete uniqueness, cardinality, lifecycle/status, and tenant constraint audit remains pending. No runtime test or data-level validation performed.


## 25. Source Audit — Preliminary Route Authorization Review (2026-11-21)
- Router GET/POST/PUT/DELETE branches and AccessControlService/PermissionService were reread. Route-specific checks are distributed across Router and service calls. Tenant-scoped active admin roles (`SYS_ADMIN`/`ADMIN`) bypass individual permission checks. Permission lookup uses tenant-scoped role assignments, active tenant roles and tenant-scoped role-permission rows, with request caches.
- Reviewed examples include project/event/form/workflow/attachment/user/role/permission/report/procurement paths; admin-only project deletion and session-management routes; creator exceptions for certain draft project operations; EVENT_EDIT for amendment/void; ATTACHMENT_VIEW/UPLOAD/DELETE separation; PROCUREMENT_MANAGE for order/receipt mutations.
- `AccessControlService.enforceRouteAccess` generically authenticates and returns true, delegating operational checks to Router/service code. V2 should centralize route policy and fail closed, with an exhaustive endpoint matrix and automated authorization tests.
- Candidate targeted review: Router PUT `/print-templates/:id/project-data` branch calls `PrintTemplateService.updateProjectBinding` without an explicit Router-level permission check in the examined branch. Service-level authorization may exist; this is unverified and not asserted as a vulnerability.
- This review is preliminary/source-only. Complete route reconciliation, service checks, runtime authorization testing and penetration testing remain outstanding.
