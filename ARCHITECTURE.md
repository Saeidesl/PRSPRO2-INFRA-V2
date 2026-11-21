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


## Discovery notes — material planning, procurement, workflow rules and reports (2026-11-21)


### Task 0.2 incremental notes (2026-11-21 — material planning, procurement, workflow rules and reporting)
- READ-ONLY MaterialPlanningService review: project material specifications are revisioned by SpecKey, previous revisions are superseded, and calculations are an append-only ledger keyed for idempotent replay. Recalculation ties confirmed quantities to a source event and event-version context; calculation records preserve basis quantity, consumption, wastage, prior calculated quantity and variance. No runtime verification.
- READ-ONLY ProcurementService review: procurement records are tenant-scoped and validate project/event references and project visibility. Order creation uses a lock and optional idempotency key; order items/allocations track needs across project(s), and shared orders must not be incorrectly treated as belonging to one project. Receipts are idempotency-aware, validate allocated/project context and reject quantities exceeding ordered amounts. Source inspection only; detailed edge cases and permissions remain to audit.
- READ-ONLY workflow rule-validator review: each workflow step uses one sequence mode (STRICT, FREE or GROUPED); rule requirement modes REQUIRED/OPTIONAL/REFERENCE/NOT_REQUIRED align with data source NEW_EVENT/SOURCE_PROJECT/NONE. STRICT requires unique event order, FREE requires unique display order, GROUPED requires group key/order. Rule event types must be active and tenant-valid at save. Workflow rules are versioned with draft/published context. Runtime progression remains unverified.
- READ-ONLY reporting review: project reports validate tenant and project visibility, omit VOID/CANCELLED events and filter procurement needs/calculations/orders by project, including items allocated to projects within shared orders. Dashboard aggregation uses tenant-scoped entities and visibility-filtered projects, with indexed event grouping to avoid repeated project scans. EventDataService reads legacy event data with tenant/project visibility checks. Source-only review; runtime and report-level permissions/tests pending.
- V2 implications: model material specs and calculation snapshots as versioned records; preserve calculation idempotency keys and source-event/version links; represent purchase orders separately from project allocations/receipts (many-to-many allocation) and enforce quantity constraints transactionally; implement workflow rule modes and versioning explicitly; reports must query only tenant-visible project/event data and correctly exclude void states where business rules do.


### Repository availability checkpoint (2026-11-21)
- Checked GitHub API availability for the planned repositories `PRSPRO2-V2`, `PRSPRO2BACKEND-V2`, and `PRSPRO2DATABASE-V2`; each returned 404 (not confirmed accessible/existing in this connection). No creation was attempted. The Infra repository remains the only confirmed V2 repository at this checkpoint. This is a blocker to starting repository-based implementation, but Phase 0 source audit/documentation continues.


## Verified logical data relationships — source repository audit (2026-11-21)


### Task 0.2 incremental notes (2026-11-21 — repository-level relationship and sheet map)
- READ-ONLY repository review confirms the current storage is organized as Google Sheets repositories, not a relational database with enforceable foreign keys. Confirmed sheet identifiers: projects `08_Projects`; events `13_Events`; event payloads `14_EventData`; procurement needs `18_MaterialNeeds`; purchase orders `19_PurchaseOrders`; order items `20_PurchaseOrderItems`; allocations `21_PurchaseAllocations`; receipts `22_MaterialReceipts`; material specs `24_ProjectMaterialSpecs`; material calculations `25_MaterialNeedCalculations`; project information values `30_ProjectInformation`; project information history `31_ProjectInformationHistory`; workflow step event rules `37_WorkflowStepEventRules`; project-reference synchronization log `38_ProjectReferenceSyncLog`.
- Confirmed repository relationships from source: EventData is looked up by EventID; events by ProjectID/EventTypeID/PerformedBy/DepartmentID; project information by ProjectID + InformationTypeID and history by ProjectInformationID; workflow rules by WorkflowID + WorkflowStepID + WorkflowVersion + EventTypeID; procurement order items by PurchaseOrderID, allocations by PurchaseOrderItemID + MaterialNeedID + ProjectID, receipts by PurchaseOrderItemID and optional ProjectID/SourceEventID; material calculations refer to ProjectID, MaterialSpecID, MaterialNeedID and SourceEventID/SourceEventVersion.
- ProcurementRepository is tenant-filtered at read boundaries. Its sheet mapping explicitly separates need, order header, order item, project allocation and receipt records. This separation is essential: one order item can have allocations across projects, while receipt quantity must be reconciled against total ordered and project-allocated/unallocated portions.
- Important limitation: repository methods generally load/filter sheet rows in application code; the source reviewed does not prove relational FK/unique constraints, referential integrity enforcement, or atomic multi-sheet transactions. Treat the above as a verified logical relationship map, not proof of database constraints. Confirm exact column sets, optionality, deletion semantics and cardinalities during full schema inventory.
- V2 implication: implement explicit PostgreSQL foreign keys and tenant-safe composite constraints after full field inventory; model purchase order header/item/allocation/receipt separately; index EventData.EventID, Events.ProjectID and key tenant references; use transactions for event payload, material calculations, allocations, receipts and audit writes.


## Core schema, identity and authorization inventory


### Task 0.2 increment — core identity, authorization and schema inventory (2026-11-21)
- Read-only DatabaseSetup inventory verified core tables and headers: tenants, users (tenant, department, password hash), departments, roles, permissions, user-role joins, role-permission joins, projects, workflows/steps, event types/fields, events/event payloads, attachments/audit, workflow-step departments, project statuses, information types/columns, project information/history, forms/fields, event-type-form joins, event-form snapshots, print templates, workflow event rules, reference sync logs, procurement event settings, material needs/specifications/calculations, purchase orders/items/allocations/receipts.
- Verified identity/authorization logical joins: User -> Tenant and Department; UserRole -> User and Role; RolePermission -> Role and Permission. Permissions appear global catalog while role/assignment records include tenant scope; confirm exact constraints and admin bypass/permission evaluation in full AccessControlService/PermissionService review before schema finalization.
- IDs use prefixed string conventions in Constants (examples prefixes TEN, USR, DEP, ROL, PER, PRJ, WF, STEP, ET, EF, EVT, ATT, AUD, SES). Preserve identifiers as text rather than converting to numeric keys.
- DatabaseSetup includes potentially destructive full reset routine; this was source-reviewed only and NOT executed. Production DB remains untouched.
- Backend tests directory was catalogued (41 test files observed) covering schema contracts, auth caches, attachments, dashboard/performance, forms/cache, idempotent events, material planning, procurement, project deletion/references, reports, print templates, reusable forms, Telegram, and workflow rules. File listing is not test execution and no pass claim is made.
- PostgreSQL design direction: tenant-aware unique keys and composite FK patterns; separate user-role and role-permission junctions; global permission catalog; workflow/project status customization; event/form definition version snapshots; retain append-only audit and reference synchronization history. Explicit schema discovery still pending for precise field types/nullability and auth edge cases.


## Authorization and user-management audit findings


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
