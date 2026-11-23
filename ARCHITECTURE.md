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


## 14. Source Audit — Repository/Test Landscape and Remaining Discovery (2026-11-21)

### 14.1 Read-only repository structure confirmation
- Recursive tree retrieval confirmed frontend production repository has 187 tree entries and backend has 153 tree entries at audited main snapshots.
- Frontend test inventory covers event registration/history/context, forms/cache, images/gallery, project workflows/drafts, procurement UI, printing/report builder, calendars, accessibility, router and shared caching.
- Backend test inventory covers access-control caches, attachments/storage, dashboard/performance, event idempotency/form definitions, information cardinality/file types, material planning, print templates, procurement/integration, project deletion/drafts/images/reference-copy, reports, schema contracts, Telegram and workflow rules.
- These are inventories only. No tests were executed.

### 14.2 Repository automation context
- Frontend workflows include frontend checks, staging Pages, cross-repository QA, UI/performance audit, print regression and E2E diagnostics.
- Backend workflows include backend checks, agent suite, procurement diagnostics and Apps Script synchronization.
- V2 will have independent CI; no production deployment or GAS synchronization workflow will be reused.

### 14.3 Discovery gaps still open
- Complete route-to-permission and request/response contract mapping.
- Exhaustive relational model and identifier/unique-index mapping.
- Remaining project/event/report/print/procurement receipt edge cases and integration contracts.
- Migration data dictionary, synthetic migration fixtures, validation and rollback design.
- Runtime testing/performance measurements belong to later safe synthetic/test environments.

### 14.4 Safety status
- Production repos remain read-only. No code, branches, PRs, deployments, live sheet data, Drive files, secrets or users were changed/accessed.
- Source-level audit only; tests were not run.

## 15. Source Audit — Route Permission Matrix Sampling (2026-11-21)

### 15.1 Route-level permission patterns confirmed by Router source
- Router has a helper requiring any of a permission set, with admin bypass via AccessControlService; single-permission routes use AccessControlService.require.
- GET route families observed: project status, departments, project and image/galleries, project information/history, workflows and rules, print-template library/source/image/PDF, forms, information types, event types/events, attachments, users/roles/permissions, audit, dashboards/reports and procurement workspace/reports/dashboard/needs/orders.
- Mutation families observed: project/information/material specifications/reference-copy, workflow and rules, forms/information types/event types, event creation/amendment/step completion, attachments, users/roles/permissions/departments/statuses, print template assets, procurement orders and receipts, and admin project deletion/session revocation.
- Several route branches use alternatives (e.g., PROJECT_VIEW or PROJECT_CREATE; WORKFLOW_MANAGE or PROJECT_CREATE or SYSTEM_SETTINGS; SYSTEM_SETTINGS or EVENT_TYPE_MANAGE). The route map is not yet reconciled with every service-level guard or frontend expectation.
- Admin-only branches include project deletion preview/deletion and session management. Sensitive paths also validate tenant/project visibility and/or department ownership in service layers.

### 15.2 Security architecture risk
- Router authorization is branch-specific rather than a single declarative route policy; omission risk and maintenance complexity remain. V2 should adopt centralized declarative route policy, service-level domain authorization, tenant scoping, fail-closed behavior, and automated route coverage tests.
- This is a source-level sampling, not an exhaustive authorization matrix, bypass assessment, or runtime penetration test. No tests were run.


## 16. Source Audit — API Response, Error Handling and Data Dictionary Cross-Check (2026-11-21)

### 16.1 Response contract
- Shared response envelope in `Response.js`: success responses carry `success:true`, `data`, `error:null`, `meta`; error responses carry `success:false`, `data:null`, `error:{code,message,details}`, and empty meta.
- `ErrorHandler` logs server-side stack/error; recognized coded errors return their code/message/details, unknown errors return INTERNAL_ERROR with a generic Persian message. Earlier WebApp review noted some internal error paths may expose message text; needs reconciliation and tests.
- V2 should standardize envelope (or decide on typed REST errors), correlation IDs, sanitized public error messages, validation details, and server-side structured logs; avoid exposing stack traces or sensitive details.

### 16.2 Schema cross-check
- Read-only header inventory reconfirmed domain table/sheet definitions for tenant/user/access control, projects/workflows, event types/fields/events/data, attachments/audit, information types/columns/project info/history, forms/fields/event-form mapping/form snapshots, print templates, workflow rules/step departments, reference sync logs, material specs/calculations/needs, purchase orders/items/allocations/receipts and event-procurement settings.
- Important normalization/constraint candidates for V2: unique tenant-scoped codes and relations, workflow/event/form version snapshots, idempotency keys, project-information revision history, purchase allocation quantity constraints, receipt idempotency, shared PO item allocations, soft-delete/status semantics.
- Sheets do not provide relational FK/unique/transaction guarantees comparable to PostgreSQL; actual constraints must be derived and validated before schema design.

### 16.3 Audit boundaries
- No spreadsheet records were accessed; headers and source code only. No reset/maintenance functions invoked. Production remains unchanged.


## 17. Read-only audit — material planning, procurement and workflow services (2026-11-21)

- `MaterialPlanningService.js`: lazy/additive schema handling uses script lock with timeout and a long-lived schema-ready cache. Project specifications are revisioned by SpecKey; latest revision is authoritative. Calculations identify retries with a key derived from source event/spec revision/basis tuple; calculation records capture basis, consumption, wastage, calculated/previous/variance quantity, type and source event version. Recalculation updates material needs and supersession state. GAS lock semantics must be replaced with DB transactions and concurrency controls.
- `ProcurementService.js`: tenant-scoped validation of projects/events, lock-protected order creation, optional idempotency replay by key, shared purchase-order items, allocations against needs and surplus handling. Receipt and allocation flows need relational constraints/transactions and clear shared-order accounting; legacy quantities are handled defensively.
- `ProcurementEventIntegrationService.js`: event type configuration maps confirmed quantity to an active tenant-owned information type; positive quantity validation; recalculation is gated on the value being saved to central project information and originating from the same source event. V2 should preserve source linkage and prevent duplicated side effects.
- `WorkflowService.js`: tenant-scoped management/bootstrap and version-aware rule/step retrieval. Draft/published versions and repeat-reference policy snapshots are present. V2 should keep published definitions immutable and bind project/event execution to explicit versions.
- Source review only, not runtime test; production repositories and data unchanged.


## 18. Read-only audit — project information, deletion, reports and print templates (2026-11-21)

- ProjectInformationService: tenant/project/type and attachment context validation; revisions with ExpectedRevision optimistic concurrency; event-linked idempotent replay; history and audit entries; approval tied to exact current revision; reference-copy conflict resolution validates both source and target revisions. V2 should transact current value, history, approval, audit and reference-sync/conflict operations; preserve revision semantics.
- ProjectDeletionService: admin/tenant-scoped dependency-aware cascade, preview and confirmation, handles project-specific dependencies while preserving shared purchase orders/items; events are voided using allowed status; other rows are soft-deleted or physically deleted where no status exists; Drive objects trashed after metadata marking; project row may be physically removed; partial failures are collected and reported. V2 should use archive/soft delete, dependency-aware preview, transaction and outbox/reconciliation for storage, retain audit and shared records.
- ReportService: tenant/project visibility index, excludes void/cancelled events, filters scoped attachments/users/departments/workflows/event types, optimizes batch filtering. Full report field contract still pending discovery.
- PrintTemplateService: tenant-scoped template management, version increments on updates, validator-backed definitions, resolution of dynamic project/form/event/info data, asset storage and reference-aware cleanup, deleted templates soft-marked. V2 needs template versioning, snapshot-safe outputs and asset lifecycle integrity.
- ProjectStatusService: tenant-scoped configurable statuses and updates. Confirmed header name collision in DatabaseSetup inventory: ProjectStatuses is named `18_ProjectStatuses` while MaterialNeeds also is `18_MaterialNeeds` (not same name but numbering prefix duplicate); sheet prefix numbering is descriptive rather than a unique logical constraint. Migration should use explicit stable table names, not numeric-prefix assumptions.
- Source review only, no runtime tests and no production changes.


## 19. Validator and repository integrity audit (2026-11-21)

- ProjectValidator enforces title/code/workflow, quantity as positive integer, at least one available department, bounded department/user lists and notification settings; update supports lifecycle/status fields, active tenant-configurable project status validated at service level.
- EventValidator validates project linkage when supplied, required event fields and update status enum (ACTIVE/VOID); key tenant/project/type checks and lifecycle rules are in EventService.
- FormValidator: name length limit, Code pattern (`^[A-Za-z][A-Za-z0-9_]{1,59}$`-style actual form code expression appears max 60), descriptions bounded, status ACTIVE/INACTIVE; field definitions validate ordering, required flags, display conditions and condition operators/values.
- InformationTypeValidator: JSON validation rules (numeric ranges, lengths, pattern bounds), names and codes bounded and code pattern, cardinality/data type enums, options capped, special PROJECT_CODE invariant (single text value), column code/type/options/order/required/status validation.
- WorkflowValidator: required name/code, workflow type/status validation; deeper step/version integrity belongs to WorkflowService/repositories.
- WorkflowStepEventRuleValidator validates modes and data-source combinations, strict/free/grouped sequencing, duplicate order/display-order/group/event-type constraints.
- Repository layer is generally thin Sheets access (ProjectRepository code lookup, EventRepository project lookup/inserts, ProcurementRepository tenant-filtered collections). Service validations cannot replace DB unique/FK/check constraints; V2 must enforce tenant-scoped unique indexes, FK, enum/check constraints and indexes.
- Source inspection only; no runtime or mutation tests.


## 20. API route and transport inventory audit (2026-11-21)

- Router path inventory extracted from production Router.js (READ-ONLY). Route families include auth/me and permissions; admin project deletion preview/session administration; project statuses/departments; project listing/detail/image gallery/card images/form lookups/information/history/material specifications/calculations; workflows/bootstrap and workflow management; print-template library/revision/sources/resolve/preview; forms/fields; information types/columns; event types/forms; events; attachments; users; roles/available permissions; permissions; audit/entity/user; project-dashboard lookups; procurement workspace/reports/dashboard/needs/orders/receipts; report projects/project; dashboard.
- WebApp GET/POST handlers parse requests, reset per-request caches, invoke route-access enforcement before Router dispatch, then dispatch GET/POST/PUT/DELETE. POST may use method override; token parsing allows query and body transfer. This confirms API transport migration concern: avoid query-token transport and method override in V2; prefer standard HTTP methods and header/cookie auth.
- Response envelope observed in Response.js: success `{success:true,data,error:null,meta}` and error `{success:false,data:null,error:{code,message,details},meta}`. WebApp's generic error path appears to include raw error.message in INTERNAL_ERROR response; needs runtime/security verification.
- Router path inventory captured, but permission-by-route matrix and full route contract (schemas, side effects, response shapes) remain incomplete. Source-only, no runtime tests or production changes.


## 21. Read-only audit — report/dashboard and print-template deeper trace (2026-11-21)

- ReportService project-report contract assembles a tenant/visibility-checked project snapshot, excludes VOID/CANCELLED events, and scopes procurement needs/calculations/orders to the selected project. Report output includes project/workflow/status/quantity/color-size metadata, event list and procurement sections, with generated timestamp. Report generation uses service composition rather than a separately persisted report artifact.
- Dashboard service uses tenant-visible project indexes, grouped event aggregation and scoped enrichments; source shows linearized project activity aggregation rather than repeated project scans. Dashboard and report source still require response-schema, access regression and performance tests in a synthetic environment.
- Procurement reporting/workspace computes shared order-item project associations through allocations, receipt records and need links; visibility filtering excludes an order if any linked project is not visible. It derives project labels and material spec/calculation context. V2 must preserve shared-item privacy, allocation attribution and surplus accounting, and test ambiguous legacy associations.
- Print-template service exposes tenant-scoped library, revision digest, dynamic sources, template definition resolution, assets and versioned updates. Templates are data-driven rather than embedding project data; resolved outputs must retain source/version context. Exact full resolve/preview/asset endpoint payloads and permission mapping remain pending.
- Discovery priority now: (1) exhaustive route-permission-contract reconciliation, (2) exact schema header/ID/unique/cardinality dictionary and migration rules, (3) event/project/form/print/procurement report edge-case matrix, (4) integration/storage/notification requirements and migration/cutover validation strategy. No runtime tests executed; source only; production repositories/data untouched.


## 22. Read-only audit — Router permission and mutation route trace (2026-11-21)

- Re-fetched current Router.js and traced GET, POST, PUT, DELETE dispatch sections. The API mixes direct single permission guards, any-of guards, and admin-only conditions; draft-project exceptions permit creator access under PROJECT_CREATE when PROJECT_VIEW/PROJECT_EDIT is unavailable, with tenant/state/creator checks in the Router branch.
- Major route families reviewed include project status/department, project and central information, material specifications/calculations, workflow management and steps/rules, print-template library/source/resolve/preview/assets, forms and fields, information types/columns, event types/forms, events/void, attachments, users/roles/permissions, audit, reports/dashboard and procurement.
- Permission identifiers sampled/extracted include ATTACHMENT_DELETE/UPLOAD/VIEW, DEPARTMENT_MANAGE, EVENT_CREATE/EDIT/TYPE_MANAGE/VIEW, PERMISSION_MANAGE, PROCUREMENT_MANAGE/VIEW, PROJECT_CREATE/EDIT/STATUS_MANAGE/VIEW, REPORT_VIEW, ROLE_MANAGE, SYSTEM_SETTINGS, USER_CREATE/EDIT/VIEW, WORKFLOW_MANAGE. Route behavior also has any-of sets and ADMIN/SYS_ADMIN bypass handled by shared access service; this is not yet a complete matrix.
- Notable policy/coverage risks for reconciliation: print-template project-data PUT route appears to dispatch without an explicit Router permission check (service-level validation must be inspected); procurement GET routes use any-of permissions and diagnostics; route ordering/pattern overlap and service-level authorization still need a complete cross-check. No bypass is asserted without service audit/tests.
- Mutation trace: POST creates/templates, project specs, order/receipts, and other domain records; PUT includes project/role/dept/status/workflow/form/event-type/project-data/template/information-type/event form and void paths; DELETE includes central-information deactivation, template/type/column deactivation, admin project removal, session revoke, role/user-role/department-step assignments, form field removal, attachment deletion. Mixed soft-delete/void/admin-confirmation semantics require preservation.
- This extraction is source-only and non-exhaustive. No runtime permission tests, no production data access, no modifications to production repositories.

## 23. V2 authorization migration design implications

- Model policies as route/action declarations plus domain-level guards; do not rely on frontend gating.
- For multi-permission routes encode explicit any-of vs all-of requirements, with admin policy explicit and testable.
- Project draft exceptions must be domain policies with creator, tenant and draft-state assertions.
- Define permission coverage tests for every endpoint and tests for tenant/project visibility, IDOR, admin paths, and mutations; specifically review print-template project-data binding update before mapping its authorization.
- Keep audit events for privileged mutations and sensitive permission/session/attachment operations.


## 24. Data Dictionary — schema-header inventory checkpoint (source audit, 2026-11-21)

- Re-read the DatabaseSetup.js schema definitions. Confirmed inventory includes core tenancy/auth/authorization (Tenant, User, Department, Role, Permission, UserRole, RolePermission), projects/workflows/steps/event types/fields/events/event payload, central-information types/columns/project-information/history, forms/fields/event-type-form links/event-form snapshots, print templates, attachments/audit, workflow step departments/rules, project-reference synchronization, event-type procurement settings, material needs/specs/calculations, purchase orders/items/allocations/receipts, and project statuses.
- Schema definitions use string/prefixed identifiers and JSON columns for several evolving payload/configuration structures. Header definitions are source-of-truth candidates but require data-level checks to establish actual sheet content/order and legacy drift; do not assume all populated rows match headers.
- The definitions show no database-native FK, unique, or check constraints. Their equivalent must be designed for PostgreSQL, including tenant-scoped uniqueness, relation consistency, enums/checks, versioning and idempotency.
- Observed tables (header definitions) with important migration semantics: project RecordState/reference-copy operation metadata; workflow lifecycle/published/draft versions and repeat policies; event versions/idempotency/previous-state snapshot; central information revision/history/reference sync; form definition snapshots; material spec/calculation revision/key and need status; purchase allocation and receipt idempotency; attachments provider/key metadata; audit old/new/reason.
- This is a header inventory checkpoint, not a final data dictionary. Exact field types/nullability/validity, row counts, duplicate-key findings, and relation orphan counts require safe export/analysis not performed against production data.

## 25. Data migration blueprint — preliminary principles

1. Preserve existing prefixed identifiers as PostgreSQL text; no forced integer conversion.
2. Stage source exports into a non-production migration environment; retain immutable raw copies and checksums.
3. Load in dependency order: tenant/auth/catalogs → workflow definitions → projects → event types/forms/information types → events and snapshots → central information/history/reference sync → attachments metadata → material specifications/calculations/needs → purchase headers/items/allocations/receipts → audit and remaining references (adjust exact order after FK map).
4. Reconcile counts, IDs, tenant ownership, FK/orphan/duplicate candidates, enums/statuses, JSON validity, revisions, procurement totals and shared-order allocation totals.
5. Never migrate binary content without a validated storage inventory and permissions; transfer through a staged private storage process and reconcile checksums/metadata.
6. Cutover requires approved backup, freeze/read-only strategy, repeatable delta strategy, validation report, explicit PO approval and rollback window. No dual-write by default.
7. Migration dry-run/rollback and real data transfer remain out of scope until V2 implementation and test gates are approved.


## 26. Relationship and migration edge-case matrix checkpoint

| Domain | Relationship/edge cases to preserve | Migration validation |
|---|---|---|
| Projects/events | Event history linked to project; events may carry workflow/step/version, previous-state snapshots, variant quantities, idempotency state; void/cancelled history retained | orphan event/project; version references; duplicate idempotency; status/snapshot consistency |
| Workflows | Projects/events may refer to versioned workflows/steps; publish/draft lifecycle; event rule ordering/grouping and repeat policies | version/step existence; published-version consistency; rule uniqueness/order; stale references |
| Event forms | Event-type↔form associations, version and definition snapshot in event-form responses; historical snapshots must remain stable if definitions change | association and event links; snapshot completeness; historical version consistency |
| Central information | Information-type/cardinality, JSON values, revision, source event, actor history; reference-copy policy/conflicts/idempotency | cardinality; revision monotonicity; history coverage; source links; reference-sync replay collisions |
| Attachments | entity ownership and tenant; attachment type/profile/information; source-copy relationship; metadata separate from binary provider | inaccessible/orphan metadata; checksum and MIME validation; source-link visibility; staged storage reconciliation |
| Materials | project spec key/revision; calculations linked to specs, source event/version and need; superseded/review states | key/revision uniqueness; source-event void handling; calculation↔need links; quantity recomputation comparison |
| Procurement | shared order items may be allocated across projects/needs; surplus; receipts attributed; voided events affect reports | order-item quantity vs allocation; tenant/project/need relation; receipt attribution; shared-item totals; surplus accounting |
| Audit | actor/tenant/entity; old/new data and reason; chronology | missing actor/entity links where expected; chronology; completeness for privileged mutations; immutable archive |

**Cross-cutting rules:** preserve soft-deleted/voided history where required; do not treat inactive as deleted; avoid destructive cascade where shared procurement or history exists; validate all tenant boundaries; use repeatable idempotent migration operations; report—not silently repair—ambiguous or conflicting source data.


## 27. Authorization trace — print-template project data binding path

- Re-read Router.js PUT branch for /print-templates/:id/project-data. It dispatches to PrintTemplateService.updateProjectBinding without a direct Router permission declaration.
- Service trace confirmed authentication and tenant-scoped template lookup; template must be active; binding ID must exist; only LATEST selection accepted; project resolution enforces tenant and project availability. The mutation branches require PROJECT_EDIT for PROJECT_FIELD and PROJECT_INFORMATION, EVENT_EDIT for EVENT_FORM_FIELD and EVENT_FIELD, and unsupported source types fail closed.
- Therefore this route is not evidence of a permission bypass based on the inspected service code. It illustrates why endpoint authorization must be evaluated end-to-end (Router + service + domain checks) and why route matrix should record permission gates by mutation source type.
- Additional review of AccessControlService showed ADMIN/SYS_ADMIN role bypass is centralized; non-admin permission resolution is tenant-scoped, active-role filtered and request-cache reset. Runtime penetration/security tests remain unperformed.
- Security testing backlog: systematically map all route branches and service gates; test cross-tenant IDs, project visibility, unauthorized binding types, stale ExpectedSource/revision conflicts, admin bypass and attachment visibility; verify token transport/log exposure and session revocation/expiry.
- READ-ONLY; no production changes or runtime tests.


## 28. Read-only security trace — authentication, session and attachment failure windows (2026-11-21)

- AuthService login requires a single global username match (ambiguous duplicate usernames fail closed), active user status and SHA-256(raw password) match. The hash implementation shown is unsalted and lacks an adjustable work factor. V2 must use Argon2id/bcrypt, normalized username uniqueness, and a safe legacy credential migration/reset strategy.
- SessionManager stores opaque UUID-derived session tokens and session JSON in Script Properties; expiry is configuration-driven and checked on access, last-seen is throttled at 60 seconds. Admin session list/revoke is tenant scoped, exposes hashed session IDs, validates user status and refuses admin-path revocation of the current session. V2 should use durable session storage, revocation, secure transport, explicit expiry and audit.
- RequestParser supports token via query/body and method override via query/body; WebApp resets request caches and dispatches to Router after shared access handling. Query-string tokens are a transport/log/history risk. V2 should use Authorization headers or secure cookies, strict HTTP methods, CSRF controls where cookie-based, sanitized logs and correlation IDs.
- UserService creation/update derives tenant from current user and validates department tenant. It checks global username uniqueness at service level but no storage-level unique constraint is present in Sheets. V2 needs normalized unique indexes and transactional validation.
- AttachmentService upload/download/thumbnail/delete source paths enforce tenant and project visibility. Upload sequence is storage upload → metadata insert → audit; delete sequence is storage delete → metadata status update → audit. Failure between these operations may leave orphaned or inconsistent records/files. V2 needs transaction-bound metadata/audit and outbox/reconciliation for object-storage operations.
- AuditService serializes old/new values to JSON and stores tenant/action/entity/user/reason/time. Source does not prove append-only enforcement or atomic business-operation coupling; sensitive data redaction and retention rules remain to be defined.
- This is a source-level audit, not runtime testing or penetration testing. No credentials, sessions, live data, storage objects or operational settings accessed.


## 29. Service-level access and integrity review — selected catalog/assignment domains (2026-11-21)

- EventDataService read path validates authenticated user, tenant ownership of event and project visibility when linked to a project; stored JSON must parse as a non-array object. This is a selected source trace, not a full route audit.
- DepartmentService tenant scopes list/get/update/create; code duplicates are checked case-insensitively within tenant in service logic. RoleService follows similar tenant-scoped CRUD and code duplicate checks. These CRUD methods did not visibly contain permission guards in the inspected snippets; Router guard reconciliation is required before any vulnerability conclusion.
- UserRoleService validates target user and role tenant alignment, active role on assignment and duplicate assignment; removal/listing are tenant-scoped. Auditing occurs after mutation. Permission relationship batch repository operations use a script lock, but Sheets operations and audit/cache invalidation are not transactionally coupled.
- ProjectStatusService requires admin or PROJECT_STATUS_MANAGE in its context, tenant-scopes records and checks active-name duplicates. Direct sheet writes and subsequent audit are non-atomic. EventTypeService applies tenant/status/department/allowed-user visibility to reads, EVENT_TYPE_MANAGE for mutations, and validates references including tenant-scoped forms, users, departments, steps and project statuses. Project status reference validation directly reads Sheets, a persistence boundary to remove in V2.
- Across these catalog and assignment domains, service-level duplicate checks are vulnerable to concurrent race conditions absent DB unique constraints. V2 must use tenant-scoped uniqueness constraints, transactions and centralized policy mapping. This source review does not establish a live authorization vulnerability.
- No runtime tests, live records, production configuration, or production changes.


## 30. Workflow authorization and Router/Service reconciliation (2026-11-21)

- Router requires WORKFLOW_MANAGE for management bootstrap, workflow detail/management-data/rule management; workflow list uses any-of WORKFLOW_MANAGE, PROJECT_CREATE or SYSTEM_SETTINGS. Step reads use any-of WORKFLOW_MANAGE or PROJECT_VIEW. The workflow path is permission-guarded before service detail. Selected service methods independently tenant-check workflows, steps, rule references and assignments.
- WorkflowService validates tenant ownership and versions, active steps and active event type references on publish; version cloning/draft-published lifecycle is implemented in source. Publication and rule-update operations visibly audit; some create/update/step methods reviewed did not visibly audit, so a complete mutation/audit matrix remains necessary.
- WorkflowStepDepartmentService selected assignment operations validate tenant and active workflow/steps/departments and limit batches to 100. Selected methods did not show direct permission guard, so route coverage must be verified for every mutation endpoint.
- AccessControlService admin resolution uses active tenant roles and SYS_ADMIN/ADMIN codes. Its require helper grants admin bypass and otherwise delegates to PermissionService. Permission resolution is tenant-scoped, filters active roles and caches per request. The shared enforceRouteAccess authenticates then returns true; actual authorization is route-branch-specific. Therefore full route coverage—not this shared gate alone—is security-critical. PermissionService selected list/get methods authenticate but do not visibly require a specific permission internally; Router guard reconciliation remains essential.
- This review does not establish a live vulnerability. No runtime/penetration testing, live user data, or production modifications.


## 31. Router audit — user, role and permission relationship routes (2026-11-21)

- Read routes: `/users` → USER_VIEW; `/users/:id/roles` → USER_EDIT; `/roles/available-permissions` and `/roles/:id/permissions` → ROLE_MANAGE; `/roles` → ROLE_MANAGE or USER_EDIT; `/permissions` → PERMISSION_MANAGE.
- Mutation routes: POST `/users` → USER_CREATE; POST `/users/:id/roles` → USER_EDIT; POST `/roles/:id/permissions` → ROLE_MANAGE; PUT `/roles/:id` → ROLE_MANAGE; DELETE `/roles/:id/permissions/:permissionId` → ROLE_MANAGE; PUT `/users/:id` and DELETE `/users/:id/roles/:roleId` → USER_EDIT.
- RoleService tenant-scopes reads and mutations, validates tenant duplicate codes and audits create/update. UserService tenant-scopes target records, derives tenant at creation, validates department ownership, sanitizes outputs and audits. Username uniqueness is service-level/global; SHA-256 password hashing remains a legacy security concern already noted.
- UserRoleService validates tenant alignment, active role for assignment and duplicates; tenant-scopes list/removal and audits. RolePermissionRepository-style batch insert/delete uses ScriptLock but not transactional DB semantics. Relationship service exhaustive validation and audit trace remains pending.
- Route excerpts and selected source only; no live runtime or security tests. Production remains unchanged.


## 32. Project, event-void, attachment and deletion lifecycle findings (2026-11-21)

- ProjectService visibility is tenant-scoped and additionally applies project access lists (creator, allowed departments/users; admin bypass). Draft list access is creator-specific when broader project viewing is unavailable. Create validates published workflow and tenant references, repeat-project source visibility, active departments/users and statuses. Project-code duplicate check uses a ScriptLock. Project update route includes a draft creator exception that requires PROJECT_CREATE when PROJECT_EDIT is denied.
- ProjectDeletionService is admin-only and tenant scoped; dependency preview includes event/data, procurement relationships, needs, specifications/calculations and attachments. Shared procurement records are guarded against deleting other-project allocations. Cascade mutation is multi-step, row-level soft delete/void and storage trash, with failure collection; project record is physically deleted only after dependency actions succeed. Audit is not a database transaction and partial failure can leave mixed lifecycle states.
- Event void checks tenant, parent project visibility, AllowVoid, departmental ownership (unless admin), later events in other departments, and snapshot/current-state consistency before restoration. Event is VOID, project state restored, audit records emitted, and material-need reconciliation attempted; reconciliation failures are reported and audited. Sheets operations cannot ensure atomic rollback across this sequence.
- AttachmentService checks tenant/entity/project visibility. Upload validates data, uploads binary before metadata/audit insertion (non-atomic). Download checks visibility and deleted status; thumbnail also excludes VOID and image types are checked. Delete removes storage before metadata/audit update, introducing partial-failure risk. V2 storage should separate delivery/access and use transactions/outbox reconciliation for lifecycle.
- Router sampled routes: EVENT_CREATE for event creation, ATTACHMENT_UPLOAD for upload, ATTACHMENT_VIEW for retrieval, ATTACHMENT_DELETE for deletion, EVENT_EDIT for void, admin check for project deletion. Audit is source-only; no runtime tests or production changes.


## 33. Project/event/attachment route map extension (2026-11-22)
- GET `/projects` route guard: PROJECT_VIEW or PROJECT_CREATE. GET `/projects/:id`: PROJECT_VIEW, with creator-owned same-tenant Draft exception requiring PROJECT_CREATE. Project information/history reads require PROJECT_VIEW. Project event/details paths also require EVENT_VIEW; project attachment listing requires ATTACHMENT_VIEW. Material specifications/calculations reads allow PROJECT_VIEW or PROJECT_EDIT. Gallery/count routes combine project/event visibility with ATTACHMENT_VIEW; project form lookups require PROJECT_CREATE.
- Event read routes: GET `/events` and `/events/:id` (including forms/data subpaths) guarded by EVENT_VIEW; event attachments listing additionally ATTACHMENT_VIEW. Service visibility checks are part of defense-in-depth.
- Attachment retrieval routes GET `/attachments` and `/attachments/:id/thumbnail|download` require ATTACHMENT_VIEW; AttachmentService validates Tenant and entity/project visibility and lifecycle status. Delete route requires ATTACHMENT_DELETE and service visibility. Project/event uploads require ATTACHMENT_UPLOAD and Service-level validation.
- Project deletion preview and DELETE `/admin/projects/:id` are Admin-only; confirmation code is passed to deletion service. Admin session revocation also Admin-only.
- This is a partial route mapping/source audit, not exhaustive API coverage or runtime testing. Remaining: all route methods/path combinations, parser/transport validation, unknown-route behavior, resource ownership reconciliation, mutation audit coverage, migration data map, and runtime/security tests.


## 34. Migration source and repository boundary audit (2026-11-22)
- DatabaseSetup and repository source confirms ProjectRepository, EventRepository and AttachmentRepository are thin persistence wrappers over GoogleSheetsRepository. No native relational integrity or multi-entity transactions in this persistence boundary. EventRepository's query helpers load the broad collection then filter/sort in memory; this is a design observation, not an asserted runtime bottleneck.
- Project source schema includes string IDs, TenantID, workflow/version references, current step/status, Creator, quantity, access lists, SourceProjectID, RecordState and reference-copy policy/status/operation markers. Event schema retains workflow/event type versions, actor/department, occurrence/creation timestamps, transition snapshot fields, variant details and idempotency markers. Attachment schema includes entity references, storage provider/key metadata, lifecycle status/type and source attachment reference.
- Migration design must preserve identifiers, version/snapshot/idempotency fields, raw JSON semantics and source attachment metadata; establish source-schema/header drift reconciliation and row-level anomaly reporting. Live data/counts were not accessed. No production changes.


## 35. Source schema naming and migration metadata audit (2026-11-22)
- Source schema additionally confirms procurement integration settings, workflow step department assignments, workflow-step event rules, project-reference sync logs, project status records and material specifications/calculation history. Preserve JSON payloads, revisions, status and idempotency keys during migration.
- `18_MaterialNeeds` and `18_ProjectStatuses` share a numeric prefix, but their literal sheet names differ. Prefix-based sheet classification/order must not assume unique numbering; use canonical source names and explicit mapping.
- Additional sampled Router paths include forms, information types, event types, event/form data, attachment access, users/roles/permissions, audit, print-template reads and procurement workspace/report reads. Full route/authorization mutation reconciliation and tests remain pending.


## 36. Read-only HTTP ingress, parser, and error-response audit (2026-11-22)

- WebApp ingress resets per-request permission/access-control/department/sheet caches, parses request, calls shared authentication enforcement, then dispatches to Router. Cache reset is request-scoped; performance diagnostics are reset per request. GET diagnostics are opt-in and admin-only. Event-creation POST responses include diagnostics by current implementation; this diagnostic surface requires review/redaction in V2.
- RequestParser accepts token via query parameter for GET and via query/body for POST-family requests; method override is accepted from query parameters, body _method, or body method, restricted to POST/PUT/DELETE. Path normalization only ensures leading slash and removes trailing slash. V2 should use explicit HTTP methods, Authorization headers or secure-cookie design, strict path parsing, CSRF policy if cookies, and avoid credential-bearing URLs.
- POST parser validates JSON syntax and requires a non-array object; it rejects unsupported method override values. It does not show explicit request-size/content-type enforcement in this parser; limits and edge-case validation remain unknown at source audit level and need deployment/API tests.
- WebApp error response returns error.message even for unrecognized errors, assigning INTERNAL_ERROR code but potentially exposing internal exception text. ErrorHandler.handle logs stack/error; WebApp catch path also serializes message for unknown errors. V2 should sanitize client-facing unknown errors, retain structured internal logs with correlation IDs, and avoid sensitive details.
- Router has explicit route-specific branches and unknown paths return NOT_FOUND. Method-specific route coverage is extensive but not yet systematically reconciled; no runtime tests were executed. This source trace is not a penetration test or vulnerability confirmation.
- No production modifications, live data or credentials accessed.


## 37. Router mutation-route inventory (source-only, partial reconciliation; 2026-11-22)

### POST routes and guard categories inspected
- Authentication: `/auth/login`, `/auth/logout`.
- Print templates: create, resolve/preview, image/PDF upload; SYSTEM_SETTINGS or PROJECT_VIEW guards as route-specific.
- Project status and department creation: PROJECT_STATUS_MANAGE / DEPARTMENT_MANAGE.
- Roles and permissions: ROLE_MANAGE; bulk assign/remove permission operations.
- Procurement: orders and receipt/batch receipt creation; PROCUREMENT_MANAGE.
- Project/material: create project PROJECT_CREATE; material specification create with PROJECT_EDIT and draft-creator exception; reference-copy with same draft exception; workflow create/step/publish/department assignment WORKFLOW_MANAGE; information types/columns/forms and form fields SYSTEM_SETTINGS; event type and procurement integration EVENT_TYPE_MANAGE; project information save/approve/reference conflict resolve PROJECT_EDIT; event create EVENT_CREATE; attachment upload ATTACHMENT_UPLOAD; user creation USER_CREATE; user-role assignment USER_EDIT.

### PUT routes and guard categories inspected
- Project status, role, department, information-type/column, print template, material specification, project update, workflow/event rules and workflow updates, form/form-field, event type, event form response, event void, user update. Guards include respective management settings, PROJECT_EDIT (with draft creator exception for project update), EVENT_EDIT, USER_EDIT. Print-template project binding delegates its resource-specific authorization to service.

### DELETE routes and guard categories inspected
- Project information deactivation PROJECT_EDIT; information-type column/template/type deactivation SYSTEM_SETTINGS; project deletion and session revocation Admin-only; role permission/user-role removal respective ROLE_MANAGE/USER_EDIT; workflow-step department assignment removal WORKFLOW_MANAGE with assignment/workflow match; form-field removal SYSTEM_SETTINGS; event-type/form unassignment EVENT_TYPE_MANAGE or SYSTEM_SETTINGS; attachment delete ATTACHMENT_DELETE.

### Reconciliation caveats
- This is a source-derived inventory of Router branches, not an exhaustive route test matrix. Route pre-guard, service-level tenant/resource checks, audit coverage and parser/route edge cases still need systematic verification. Several routes rely on service authorization (e.g. print-template project binding) and require inspection of each service method. The route table must not be interpreted as proof of endpoint security.
- Source-only review; no runtime tests, production changes or data access.


## 38. Audit-log and mutation consistency review (2026-11-22)

- Read-only source inspection of AuditService confirms audit entries are serialized as OldData/NewData JSON, with tenant, user, action, entity type/ID, reason and timestamp. Audit list and entity/user list paths apply tenant filtering. This source alone does not establish append-only storage, retention, sensitive-field redaction, or immutability guarantees.
- Selected CRUD service traces show a recurring write-then-audit sequence rather than a transaction. DepartmentService creates/updates department rows then logs audit; EventTypeService creates/updates and then logs; ProjectStatusService writes directly to its sheet then logs. Audit failure after the business write could leave the business mutation without a corresponding audit record. Conversely, audit payloads may capture broad old/new objects; V2 must define field-level redaction and audit retention.
- Selected services use tenant context and validate tenant ownership for resources; authorization is sometimes route-level and sometimes service-level. This pattern must be reconciled endpoint-by-endpoint rather than assuming a generic shared gate enforces permissions.
- Concurrency risk remains where duplicate-code/name checks are read-then-write operations without database constraints (e.g. departments/event types/project statuses). V2 should use tenant-scoped unique indexes and transactions.
- V2 audit design requirement: immutable/append-only audit table with actor and tenant identity, structured diff or event payload, explicit redaction, retention/access controls, correlation IDs, and transactional coupling to business writes; integration/storage side effects should use outbox/reconciliation.
- Source-level audit only. No runtime tests, penetration tests, production changes, or live data access.

### Audit coverage status
- Confirmed in sampled traces: department CRUD, event-type CRUD, project-status CRUD, user/role assignments, role CRUD, selected workflow publish/rule changes, event form amendment, project information mutation, event void, attachment lifecycle, procurement operations and project deletion have audit-related behavior in at least selected paths.
- Not established: exhaustive mutation-to-audit coverage, consistent action taxonomy, append-only enforcement, payload redaction, audit-write failure recovery, and all workflow/form/catalog mutation paths. This is a pending verification matrix, not an assertion that uncovered paths are unaudited.


## 39. Selected configuration and workflow service audit (2026-11-22)

- FormService source trace: tenant context is obtained through AccessControlService.getCurrentUser; resource helpers enforce form/event-type tenant ownership and active state. Sampled form, form-field and event-type/form mapping mutations write business records, bump form versions/cache invalidation, then call AuditService.log. These sequences are multi-write and not transactionally coupled in Sheets; audit and cache invalidation consistency must be designed in V2. This service inspection is not an exhaustive permission audit; route guards remain part of the legacy authorization model.
- InformationTypeService trace: methods use current-user context and tenant-scoped resource lookups; CRUD and column mutations audit after repository writes. Structure changes are restricted when type is used by project information or active forms, and deactivation checks form use. Revision increments and cache invalidation are observed. Service-level permission checks are not consistently visible in this file and route authorization must be reconciled.
- WorkflowStepDepartmentService trace: assignMany validates tenant/workflow/step/department status and batch limits, avoids duplicate department assignments, and inserts batch (or fallback individual writes); remove soft-deactivates. In the inspected service methods, explicit permission checks and AuditService.log calls were not evident. Router guards may authorize these routes, but service-level defense-in-depth and audit coverage remain open questions; batch fallback can also produce partial writes if an insert fails midway.
- UserRoleService trace: validates same-tenant user/role, active role and duplicate assignment; writes then logs audit. Removal deletes the assignment then audits. These two-step sequences are not transactionally atomic and require constraint/transaction handling in V2.
- ProjectInformationService trace: tenant and project visibility validation, type/event matching, history/revision records and audit are present in sampled write paths. Reference sync/conflict resolution can involve multiple project-information rows, history and sync-log writes. These multi-record sequences require transactional handling, unique idempotency keys and reconciliation in V2.
- MaterialPlanningService trace: validates tenant/project visibility; specification revisions and calculations use keyed history/idempotency patterns and AuditService in sampled paths. Recalculation may update material needs and calculations across multiple rows; V2 must preserve revision/basis snapshots and transact these changes.
- ProcurementService trace: tenant-scoped reads/validation, script lock for order creation and idempotency replay logic; order creation inserts order, items and allocations, then audit. Receipt paths similarly touch multiple records and audit. The script lock is a legacy concurrency control but does not establish atomicity or DB constraint equivalence.
- Source-only review, sampled methods. No runtime tests, penetration tests, production modification or live data access. Permission and audit matrix remains incomplete.


## 40. Event and workflow execution invariants — additional source trace (2026-11-22)

- EventService source trace confirms transition target resolution depends on tenant-owned workflow, active step-department assignments, and matching workflow-step records; strict paths reject invalid/missing mappings rather than silently advancing. Events for Draft projects do not apply configured project lifecycle changes in the examined transition helper.
- Event ordering/void safety uses occurrence/creation timestamps, tenant/project scoping and excludes VOID records when evaluating later events in another department. Event void restoration checks expected current-state/snapshot conditions before restoring, rather than blindly overwriting a project state.
- WorkflowService management bootstrap and management-data reads filter workflow, department, active event-type and information-type data by tenant and workflow version. The trace reinforces that workflow versioning and assignment scope must be explicit in V2 contracts and queries.
- These are selected source traces; comprehensive event mutation, sequencing, repeat-project, and route/service authorization reconciliation remains pending. No runtime tests were executed.

## 41. Discovery audit scope and limitations — current checkpoint (2026-11-22)

- Audit is a source-code review of selected legacy repository files and declared schemas/routes; it has not queried live Google Sheets/Drive, user records, production telemetry, or deployed configuration.
- Findings are therefore design and migration evidence, not performance measurements, operational guarantees, or confirmed exploitability. A complete route-to-service authorization matrix, external integration inventory, test coverage/run status, data-volume and performance baseline, and migration anomaly assessment remain outstanding.
- Major V2 migration requirements emerging from source: tenant-scoped relational constraints; preserved legacy identifiers, version/snapshot and idempotency semantics; transactional multi-record operations; secure authentication and authorization; append-only/redacted audit; independently managed files; repeatable migration with reconciliation; and test evidence before cutover.


## 42. Additional Router read-route authorization matrix trace (2026-11-22)

Selected GET Router branches were traced against permission gates and service destinations:
- Projects and project detail: list permits PROJECT_VIEW or PROJECT_CREATE; detail requires PROJECT_VIEW with narrow creator-owned tenant Draft fallback to PROJECT_CREATE. Project-information/history reads require PROJECT_VIEW. Project event/details paths additionally require EVENT_VIEW. Project attachments additionally require ATTACHMENT_VIEW. Material specifications/calculations permit PROJECT_VIEW or PROJECT_EDIT at the router branch.
- Workflow reads: list allows WORKFLOW_MANAGE, PROJECT_CREATE or SYSTEM_SETTINGS; steps allow WORKFLOW_MANAGE or PROJECT_VIEW; management bootstrap/data and event rules require WORKFLOW_MANAGE.
- Forms/information types: list permits SYSTEM_SETTINGS or EVENT_TYPE_MANAGE; detail and nested paths require SYSTEM_SETTINGS. Event type list permits EVENT_TYPE_MANAGE, EVENT_CREATE or SYSTEM_SETTINGS; event-type details/forms read branches permit EVENT_TYPE_MANAGE or EVENT_CREATE.
- Event reads: list/detail require EVENT_VIEW; form responses/data remain under EVENT_VIEW; event attachments additionally require ATTACHMENT_VIEW.
- Roles/users: user list USER_VIEW; user role list USER_EDIT; role list ROLE_MANAGE or USER_EDIT; role permissions and available permissions ROLE_MANAGE; permission catalogue PERMISSION_MANAGE.
- Audit/report reads: audit list/entity/user and dashboard/project reports require REPORT_VIEW.
- Procurement workspace, dashboard, needs and orders permit PROCUREMENT_VIEW or PROCUREMENT_MANAGE; procurement reports permit REPORT_VIEW or procurement permissions.
- These router gates do not alone establish service-level resource authorization or complete route coverage. The matrix remains sampled and must be reconciled across all methods and nested paths.

## 43. Authorization audit — caveats and design requirements

- Router branch gates differ in purpose and are not a substitute for resource-level tenant/ownership checks. V2 should use consistent authentication, permission policy and resource-scope enforcement in middleware/service boundaries, with no UI trust.
- Any-of permission gates and admin bypasses should be explicit policy constructs and tested. Draft creator exceptions should be narrowly defined and covered by tests.
- No security test or runtime validation was performed in this source trace.


## 44. Selected mutation route authorization and effects trace (2026-11-22)

### POST (sampled Router branches)
- Creates/changes: print templates and media (SYSTEM_SETTINGS); project statuses (PROJECT_STATUS_MANAGE); departments (DEPARTMENT_MANAGE); roles and permission assignments (ROLE_MANAGE); procurement orders/receipts (PROCUREMENT_MANAGE); projects (PROJECT_CREATE); workflow create/publish/step and department assignment (WORKFLOW_MANAGE); information types/forms (SYSTEM_SETTINGS); project-information save/approve/reference conflict resolution (PROJECT_EDIT); event types and procurement integration (EVENT_TYPE_MANAGE); event creation (EVENT_CREATE); attachments (ATTACHMENT_UPLOAD); users (USER_CREATE) and user-role assignment (USER_EDIT).
- Material specifications and reference-information copy paths include narrowly scoped creator-owned project Draft exception requiring PROJECT_CREATE after PROJECT_EDIT check fails.

### PUT (sampled Router branches)
- Project statuses, departments, roles, information types/columns, templates, workflow/rules, forms/fields, event types, event form responses and user updates are guarded by their corresponding management/edit permissions.
- Project update has a creator-owned tenant Draft exception with PROJECT_CREATE after PROJECT_EDIT failure.
- Print-template project-data binding delegates resource-specific authorization to its service; do not infer bypass from router branch alone.
- Event void uses EVENT_EDIT; material-spec revision uses PROJECT_EDIT.

### DELETE (sampled Router branches)
- Project information deactivation and material/catalog/form/template deactivation are permission-gated; role-permission/user-role removal and workflow assignment removal are management-gated; attachment removal uses ATTACHMENT_DELETE.
- Project removal is admin-only and uses confirmation code plus ProjectDeletionService. Session revocation is admin-only and revokes a selected session via SessionManager.
- Workflow department assignment removal verifies assignment belongs to specified workflow before service call.

### Risk and limitations
- These are selected Router paths, not exhaustive endpoint inventories or tests. Method/path normalization, overlapping prefix handlers, resource authorization in services, audit completeness and mutation atomicity remain open validation items.
- V2 should model writes as explicit command handlers with validated input, tenant/resource scope, permission policy, transaction, audit/outbox and idempotency where appropriate; soft deactivation vs deletion semantics must be deliberately preserved.


## 45. Mutation service and audit implementation observations (READ-ONLY, 2026-11-22)

### AuditService behavior inspected
- AuditService.log validates object and required TenantID/Action/EntityType/EntityID, creates AUD-prefixed UUID ID, serializes OldData/NewData to JSON strings, stores Reason and timestamp, and delegates to AuditRepository.insert.
- The inspected implementation has no visible redaction/size guard, immutable storage enforcement, or transaction coordination itself. These may exist elsewhere but were not established here.
- list/listByEntity/listByUser retrieve records then filter TenantID in service code; tenant scoping is present in these sampled methods. Bulk-query/index/retention guarantees are not established.

### Selected service mutation review (scope-limited)
- Fetch/read-only review of FormService, WorkflowService, ProjectInformationService, ProcurementService, AttachmentService, EventService and PrintTemplateService source confirmed these services are substantial multi-operation workflows and contain audit/locking markers in selected files. This marker presence alone does not establish audit coverage or atomicity for every mutation.
- Earlier inspected behavior and these source patterns leave a cross-cutting risk: business mutation and audit/storage/cache/reference side effects can be separate operations in a Sheets environment. Partial failures may leave inconsistent state unless explicitly reconciled.
- V2 requirements: audit payload redaction and bounded size; append-only policy and retention; transactional audit coupling for database mutations; outbox for external/storage effects; retry-safe idempotency; observable reconciliation; policy-level tests for tenant/resource scope.

### Verification status
- Source inspection only. No runtime, concurrency, failure-injection, security, or integration tests were run. Audit completeness and service-level resource authorization remain pending.


## 46. Discovery audit consolidation — Phase 0 Task 0.2 (2026-11-22)

### Repository readiness
- Infra V2 repository exists and is accessible.
- The three other planned repositories (frontend, backend, database V2) were not found in the GitHub repository checks at this review; creation/access remains a separate prerequisite and is not claimed complete.
- Legacy production frontend/backend repositories remain read-only throughout this audit.

### Test infrastructure and recent verification evidence
- Backend tests directory enumerated: 41 test files (scope includes access control/request cache, attachment, performance contracts/diagnostics, event idempotency and form/cache/prefetch, schema recovery, material planning, procurement, projects/drafts/deletion/gallery, print templates, reusable forms, Telegram, validators, workflow enforcement/rules and related contracts).
- Frontend tests directory enumerated: 45 test files (scope includes event form/cache/performance, project picker/history/cards/gallery/report/print, accessibility, reusable forms, workflow, procurement loading, Jalali calendar, router base-path, cache and related UI contracts).
- Latest inspected backend source-check run: 2026-11-19, commit 4a131ae, job validate SUCCESS. Other backend suite/performance workflows on same push were previously observed successful in the runs list.
- Latest inspected frontend-check run: 2026-11-19, commit f1b667f, job frontend-check SUCCESS. Associated frontend QA/performance/deployment runs were successful in the runs list.
- These are repository CI evidence, not a freshly executed local suite, exhaustive feature verification, or production smoke test. The deployment workflow success observed is historical evidence only.

### Integration/dependency inventory
- Backend is Apps Script V8 and depends directly on Google Sheets persistence, Google Drive/file storage, Script Properties for secrets/session/integration configuration, Apps Script web-app ingress and deployment. Telegram is a documented integration via Bot API webhook/polling, tenant-chat mapping and notifications.
- Frontend is a GitHub repository SPA served via GitHub Pages/deployment workflow, API integration with legacy GAS endpoint, browser local/cache mechanisms and assets/fonts; no direct V2 database dependency.
- Procurement/material planning, project-information reference synchronization, workflow enforcement, print-template resolution and file/attachment storage are internal cross-service dependencies and migration-sensitive.
- Google Apps Script runtime/services, spreadsheet identity/headers, Drive file IDs/permissions, Properties, deployment URL and Telegram credentials/configuration are platform-specific dependencies requiring replacement or migration plan in V2.
- Exact secret values, production data, live integrations and operational counts were not accessed.

### Discovery risks and migration guardrails (consolidated)
1. Sheets lacks relational transactions/constraints: preserve IDs/history and validate tenant/FK/unique rules; use PostgreSQL constraints and transactional command handling.
2. Multi-step mutation, audit, file storage and cache/reference side effects can partially fail: transaction + outbox/idempotency + reconciliation.
3. Authorization is distributed between Router/service; map and test policies including tenant/resource scope, admin and draft exceptions.
4. Legacy password/session mechanics and token transport need secure migration/reset; no credential transfer assumption.
5. File migration must preserve metadata, links, visibility and access control; reconcile object migration and orphan/duplicate handling.
6. Telegram/configuration and other Apps Script integrations need independent adapters and secret management.
7. No source audit can establish actual data volumes, runtime behavior, concurrency limits, security posture, performance baselines or complete route/test coverage; these are pending discovery/test/deployment phases.
8. Migration must be staged, repeatable, validated, backed up, rollbackable and approved; legacy system stays operational and read-only at cutover. Avoid dual-write.

### Task 0.2 readiness conclusion
- READ-ONLY discovery and source audit findings have been consolidated to the best available source/document/CI evidence.
- No legacy production repo was modified, no production data was accessed, no secrets were retrieved, no VPS was purchased/configured and no migration was executed.
- Task is READY FOR PRODUCT OWNER REVIEW; under project protocol it is not marked COMPLETE until explicit acceptance.


## 47. Phase 0 — Task 0.3 discovery report and target boundaries (draft, 2026-11-22)

### Status
- Task 0.3 is IN PROGRESS. This is a source-informed draft for Product Owner review; it is not accepted and does not authorize implementation, migration, or changes to the legacy system.

### Verified current-system map (high-level)
- Frontend: HTML/CSS/JavaScript SPA hosted via GitHub Pages/deployment workflow; consumes the legacy GAS API and uses browser-side caching.
- Backend: Google Apps Script V8 web-app, with Router, service/repository layers, authorization and validation, and platform-bound runtime services.
- Persistence: Google Sheets tables/headers and logical relationships; tenant and domain scoping are generally implemented in code rather than database-enforced foreign keys.
- Files: Google Drive-based storage and attachment metadata/lifecycle paths.
- Integration/configuration: Telegram Bot API integration is documented; tenant mappings and sensitive configuration are held in platform configuration mechanisms. No secret values were accessed.
- Cross-domain dependencies: project/workflow/event/form snapshots, central information/reference synchronization, material planning, procurement/allocation/receipts, print-template binding, attachment and audit lifecycle.
- Existing frontend/backend production repositories and deployed systems remain READ-ONLY.

### Candidate migration scope (subject to PO review and detailed validation)
- Tenant/user/role/permission model and authentication/session replacement.
- Project and lifecycle/status management; workflow definitions, versions, steps, departments and event rules.
- Event records, event-form submissions and definition/version snapshots, amendments and void history.
- Central project information, revisions/history, approval/reference-sync/conflict behavior.
- Material specifications, calculations, material needs, procurement orders/items/allocations/receipts.
- Attachments/files and metadata, audit history, print-template definitions/bindings.
- Confirmed integrations and configuration, with secrets re-provisioned rather than copied.

### Candidate exclusions or replacement handling (not finalized)
- Google Apps Script runtime, Google Sheets storage, GAS deployment URLs, Apps Script locks/cache, Script Properties, and Google Drive storage are platform components to replace, not target V2 dependencies.
- Legacy authentication/session tokens and secret values are not presumed portable; security review and user re-authentication/reset strategy are required.
- Operational data, files, and configuration migration are deferred until a sanitized migration rehearsal and explicit approval.

### Target domain boundaries — provisional
1. Identity & tenancy: tenant, user, role, permission, session/authentication, tenant-scoped policy enforcement.
2. Production projects: project identity, status/lifecycle, project information and revision history.
3. Workflow configuration: versioned workflow, steps, department assignments, event rules and publication.
4. Events & forms: event records, event type, form definitions/versions, snapshots, responses, amendment and void semantics.
5. Materials & planning: specifications, calculations, needs, revision/basis snapshots.
6. Procurement & stock receipt: orders, items, allocations, receipts, cumulative limits and reconciliation.
7. Files & media: storage abstraction, attachment associations, access policies, upload/download lifecycle.
8. Reporting & templates: report queries, print templates, resolved project/event/form data and rendering.
9. Audit & operations: append-only audit design, observability, correlation, reconciliation and retention.
10. Integration adapters: notification and external-service adapters (initial inventory is incomplete).

### Compatibility and migration requirements
- Preserve legacy IDs where feasible and map all identities explicitly; never rely on inferred relationships during migration.
- Preserve historical workflow/form definition snapshots and revision context.
- Enforce tenant-scoped FK/unique constraints and resource authorization at API/service boundaries.
- Use database transactions for multi-record operations; idempotency constraints for retry-sensitive operations; transactional audit and outbox/reconciliation for side effects.
- Define file migration checksums, metadata, visibility and access-control validation.
- Migration must be repeatable, report discrepancies, support recovery/rollback, and be rehearsed with sanitized/test data.
- No dual-writing and no cutover before explicit PO approval.

### Outstanding discovery questions
- Exhaustive route-to-service/resource authorization matrix and security test strategy.
- Full integration inventory and operational dependency details.
- Data volumes, tenant counts, identifier/duplicate anomalies and performance baseline (not available from source audit).
- File inventory, sizes, orphan/duplicate candidates and Drive permission behavior (no live inspection).
- Complete audit/event history retention and business rules requiring confirmation.
- Final storage provider, deployment topology and authentication design decisions.

### Phase 0.3 deliverable status
- High-level current-system map, provisional migration scope/exclusions, domain boundaries, compatibility requirements and unknowns drafted.
- Awaiting Product Owner review; this draft does not approve or implement a schema.


## 48. Task 0.3 acceptance (2026-11-22)
- Product Owner explicitly accepted the discovery report and provisional domain boundaries in section 47.
- Task 0.3 status is COMPLETE; Phase 0 remains IN PROGRESS because independent repository preparation remains outstanding.
- Acceptance does not authorize schema implementation, real-data access/migration, production changes or deployment.


## 49. Repository verification (2026-11-22)
- Verified access to all four V2 repositories under Saeidesl with admin/push permissions via GitHub repository metadata.
- The three newly created repositories are empty and currently public, not private as required. This is a risk to resolve before source code, sensitive architecture details or credentials are added. No secret or production data was added.
- Repository creation/access blocker is cleared; privacy setting remains an external account action.


## 50. Repository privacy verification (2026-11-22)
- GitHub metadata recheck confirmed the three implementation repositories are Private and accessible with admin/push permissions.
- Infra repository remains Public and currently contains governance documentation; reconsider privacy before storing sensitive security or operational details.


## 51. Repository privacy state supersedes earlier notes (2026-11-22)
- Sections 49 and earlier repository notes describe the initial state and are historical.
- Current verified state: the three implementation repositories are Private; access is confirmed.
- Infra repo remains public, with no code/secrets/production data added in this task.


## 52. Task 0.2 audit increment — frontend/backend authentication transport and shared API contract (2026-11-22)

### Source evidence (READ-ONLY)
- Re-read production `Router.js` and frontend `js/core/api.js`. The shared frontend client places the session token in the query string (`token`) for requests, and uses `_method` parameters with POST override for PUT/DELETE. Backend route/parser support was previously confirmed for query-token transport and method overrides.
- This confirms the transport behavior is an end-to-end legacy compatibility contract. V2 should not reproduce it by default: use an Authorization-header or secure HttpOnly/SameSite cookie approach selected during architecture design. If cookie-based auth is chosen, define CSRF protections; ensure URL secrets are absent from logs, analytics and browser history.
- Legacy API client has centralized GET/HEAD retry (limited attempts), request deduplication, sync status signaling and optional performance diagnostics. Preserve useful UX capabilities selectively; retries should remain limited to safe/idempotent operations and diagnostics must not reveal secrets or sensitive payloads.

### V2 design implications
- Define an explicit API transport contract and request/response/error envelope, with authentication transport, method semantics, CSRF policy, pagination/filtering, idempotency and correlation IDs before frontend integration.
- Replace legacy method override unless compatibility or deployment constraints justify it; native HTTP method semantics should be preferred.
- Keep client caching/deduplication separate from authorization and integrity; cache keys must be tenant/user scoped and mutation invalidation must be explicit.

### Verification limitations
- Source inspection only; no runtime tests, token/session data, production data or secrets accessed. No production repository was modified. This increment does not close the exhaustive route/resource permission audit or Task 0.2.


## 52. Task 0.2 audit increment — frontend/backend authentication transport and shared API contract (2026-11-22)

### Source evidence (READ-ONLY)
- Re-read production `Router.js` and frontend `js/core/api.js`. The shared frontend client places the session token in the query string (`token`) for requests, and uses `_method` parameters with POST override for PUT/DELETE. Backend route/parser support was previously confirmed for query-token transport and method overrides.
- This confirms the transport behavior is an end-to-end legacy compatibility contract. V2 should not reproduce it by default: use an Authorization-header or secure HttpOnly/SameSite cookie approach selected during architecture design. If cookie-based auth is chosen, define CSRF protections; ensure URL secrets are absent from logs, analytics and browser history.
- Legacy API client has centralized GET/HEAD retry (limited attempts), request deduplication, sync status signaling and optional performance diagnostics. Preserve useful UX capabilities selectively; retries should remain limited to safe/idempotent operations and diagnostics must not reveal secrets or sensitive payloads.

### V2 design implications
- Define an explicit API transport contract and request/response/error envelope, with authentication transport, method semantics, CSRF policy, pagination/filtering, idempotency and correlation IDs before frontend integration.
- Replace legacy method override unless compatibility or deployment constraints justify it; native HTTP method semantics should be preferred.
- Keep client caching/deduplication separate from authorization and integrity; cache keys must be tenant/user scoped and mutation invalidation must be explicit.

### Verification limitations
- Source inspection only; no runtime tests, token/session data, production data or secrets accessed. No production repository was modified. This increment does not close the exhaustive route/resource permission audit or Task 0.2.


## 53. Task 0.2 audit increment — sessions and authorization resolution (2026-11-22)

### Verified source observations
- AccessControlService authenticates via AuthService/SessionManager, then resolves roles within the user tenant; admin bypass is derived from active tenant roles with SYS_ADMIN or ADMIN codes. Permission checks are tenant-aware and request-cached.
- PermissionService bulk-resolves permissions and caches results per tenant/user for the request. AccessControlService.require delegates to it. Generic enforceRouteAccess authenticates but intentionally returns true without domain permission enforcement; Router/service checks remain the effective operational gate. Thus missing route/service checks are a systemic fail-closed design risk, not evidence of a specific exploit.
- SessionManager stores session records in Script Properties keyed by raw token. Session expiry, status, tenant/user consistency and admin revocation are implemented; admin session list/revoke paths are tenant-scoped and admin-guarded. Admin listing hashes session identifiers for display/revocation matching; current session cannot be revoked through the admin revoke path.
- Permission resolution and session behavior were inspected at source level only. No live sessions, tokens, production data or tests were accessed.

### V2 requirements / risks
- Model sessions/revocation and tenant-bound identity as first-class durable structures; never store bearer secrets as directly enumerable property keys or expose raw tokens. Hash/token storage strategy, expiry, rotation, concurrent-session policy and revocation semantics need explicit design.
- Authorization should be centralized and fail-closed, with explicit route policies plus resource-level tenant/project/department scope validation. Admin role semantics should be explicit and tenant-bound; avoid broad implicit bypasses unless deliberate.
- Cache permission decisions only within a safe scope; invalidate/recompute after role/user/session changes and ensure tenant/user identity is part of keys.
- Build automated authorization matrix tests for every API route: unauthenticated, tenant mismatch, permission denied, department/project visibility, admin and relevant exceptions. Audit logging should capture denial without secrets.

### Limitations
- This does not constitute a full route/service/resource permission matrix or security testing. Task 0.2 remains in progress.


### Task 0.2 incremental notes (2026-11-22 — notification delivery reliability and attachment-storage reconciliation)
- Read-only source recheck of TelegramBotService and AttachmentService confirmed the Telegram integration supports tenant chat mapping, tenant notification gating, message formatting with event/form/project context and attachment delivery; polling is scheduled every minute and update offset is persisted in Script Properties.
- Polling advances and persists update offset after _processUpdate_ returns; handlers may perform registration and messaging side effects. Delivery outcomes are not represented by a durable queue/retry ledger. V2 notifications should use a transactional outbox, durable attempt state, deduplication keys, bounded retries/backoff, dead-letter/review state, and avoid duplicate notification delivery. Separate notification preferences/recipient policy from domain mutations.
- Attachment paths continue to reveal a split between Drive storage operations and attachment metadata/audit writes. Selected reference-image copy path attempts storage cleanup if metadata/audit work throws; cleanup itself can fail and is swallowed, so an orphan-storage reconciliation mechanism is required. This is a sampled path, not a claim that all attachment mutations share identical behavior.
- V2 file lifecycle should use durable metadata plus storage abstraction, scoped authorization on every access, upload validation and checksum, quarantine/scan policy if applicable, lifecycle state machine, transactional metadata changes, outbox-driven storage cleanup/copy/move, orphan detection and recovery. File migration must preserve source IDs/mappings where useful, metadata, associations, access policy and integrity hashes.
- Audit is source-only. No live Telegram settings, credentials, chats, Drive files, or production data were accessed; no tests or production writes were performed. Task 0.2 remains IN PROGRESS.


## Task 0.2 — Consolidated READ-ONLY audit closure (2026-11-22)

### Router/permission coverage sampled and categorized
- Read the legacy backend Router.js route dispatch across GET, POST, PUT and DELETE branches. Routes are permission-gated by a combination of AccessControlService.require, Router.requireAny, explicit admin checks and service-level tenant/resource visibility checks. Router's generic enforceRouteAccess remains authentication-only (previously recorded); domain permissions are applied in the Router branches and services, not that generic method.
- Observed route families and permission categories: auth/session; projects/drafts/project information; project events/details/workflow progress; attachments/gallery/download/upload/delete; workflow management/step rules/department assignments; forms/information types/columns; event types and procurement-event integration; roles/permissions/user-role assignment; departments; procurement workspace/orders/receipts/material needs; reports/audit; print templates; project statuses; admin project deletion preview/remove and session administration.
- GET authorization sometimes intentionally accepts alternative permissions (e.g. project creation or workflow management access to read choices), and routes for project/draft, gallery and procurement data have additional tenant/resource filtering. These behaviors require explicit, documented V2 policy decisions and regression tests rather than a mechanical port.
- The source audit did not execute a complete endpoint-permission test matrix or establish an exploitable vulnerability. The route catalogue above is a coverage-oriented categorization, not an exhaustive, validated matrix of every route and service condition.

### Cross-cutting migration and operational constraints (source-supported plus unknowns)
- Data store: Sheets repositories and service-layer relationship validation do not provide PostgreSQL-style FK/transaction enforcement. V2 must define constraints, indexes, tenant-scoped unique keys, transaction boundaries, idempotent mutation strategy, locking/concurrency behavior and repeatable migrations.
- Multi-resource mutations can cross sheets, Drive, audit and caches; sampled operations use locking and/or compensating cleanup, but these are not equivalent to a single atomic transaction. V2 should use DB transactions for DB state and an outbox/saga/reconciliation approach for external resources.
- Preserve IDs, tenant ownership, status/history, workflow versions and snapshots, procurement allocation relationships, form response snapshots, file associations and audit lineage as migration requirements, subject to discovery/validation.
- File storage and Telegram are external dependencies; V2 should abstract providers, avoid credentials in source/URLs/logs, support retry/deduplication, health monitoring and controlled recovery.
- Performance and capacity: production volume, peak concurrency, file volume/size distribution, retention, latency targets, operational availability and recovery objectives have not been measured/confirmed in this READ-ONLY audit. Require a data inventory and workload baseline before schema/performance design or deployment sizing.
- Recovery/cutover: require backups, rehearsal, row/count/hash/relationship validation, exception reports, freeze or controlled export window, explicit rollback criteria, no uncontrolled dual-writing, old PMS read-only retention and PO approval.
- Security review follow-up for implementation: route-by-route authorization tests, tenant isolation, resource-level scope, fail-closed behavior, secure session/token handling, CSRF if cookie auth, secrets management, log redaction, audit integrity/retention and file validation.

### Audit boundaries and closure
- READ-ONLY source audit only. No production data or live Google Sheets/Drive content, session tokens, secrets or Telegram configuration values were read. No runtime tests, live API tests or file/notification tests were performed. No existing production repositories were modified.
- Task 0.2 source-discovery work is concluded to the scope available through source inspection and documentation. Coverage limitations, unknowns and required follow-up validations are explicitly retained above. This conclusion is not a security certification, runtime validation, migration readiness approval or authorization to begin implementation/migrate/deploy.


## Phase 2 conceptual data model checkpoint (2026-11-22)
- The provisional dependency map, transaction boundary hypotheses and integrity invariants are maintained in DATA-MODEL-DRAFT.md in the Infra V2 repository.
- Treat this as design documentation, not a final schema. Domain field mapping, policy decisions, workload baseline and PO review remain required.
- No implementation, SQL migration, live-data access, dual-writing, deployment or production changes are authorized.


## Phase 2 source-to-target mapping checkpoint (2026-11-22)
- Legacy header-level source mapping for Project, Workflow, Event and Form domains is recorded in DATA-MODEL-DRAFT.md. The inventory is preliminary and does not validate record-level data or complete semantics.
- Production repositories remain read-only; no data extraction or schema implementation is authorized.


## Phase 2 procurement source-mapping checkpoint (2026-11-22)
- Procurement source mappings and limited observed service behavior are documented in DATA-MODEL-DRAFT.md. The evidence is source-level and provisional; exact quantity policies, data validation, schema and migration remain pending.


## Phase 2 identity and authorization mapping checkpoint (2026-11-22)
- Legacy source mapping and targeted auth/permission path observations are recorded in DATA-MODEL-DRAFT.md. The analysis is preliminary; the authorization matrix and security design remain pending review.


## Phase 2 / Task 2.1 — Attachment, Audit, Print and Integration mapping checkpoint (2026-11-22)
- Header-level source structures for `15_Attachments`, `16_AuditLogs` and `36_PrintTemplates` and legacy attachment configuration values are recorded in DATA-MODEL-DRAFT.md section 19.
- Observed legacy attachment entity types are PROJECT/EVENT and configured provider GOOGLE_DRIVE; these are not V2 provider restrictions.
- Dedicated service behavior was not fully validated in this checkpoint. Lifecycle, authorization, audit coverage/redaction, template payload/version semantics, and Telegram delivery remain discovery items.
- No production data, file contents, credentials, secret values, schema, SQL, migration or production changes were accessed/performed.


## 12. Source audit checkpoints — 2026-11-22
- Read-only audit continued across project creation/reference-copy policies, workflow draft-version cloning and publishing, form definition/version validation and material-planning revisions/reconciliation.
- Evidence checkpoint documents are maintained separately: SOURCE-MAPPING-CHECKPOINT-2.1.md, LIFECYCLE-SEMANTICS-CHECKPOINT-2.1.md, and PROJECT-WORKFLOW-FORM-MATERIAL-CHECKPOINT-2.1.md.
- Provisional implications: preserve source/reference project policy and provenance; version workflows and forms; capture stable definition snapshots for historical records; preserve material specification/calculation revisions and reconciliation state; make multi-record mutations transactional, replay-safe and observable.
- These implications are discovery notes only, not approved relational schema decisions. Further source audit and PO review are pending.
- No production repository was modified. No tests, schema, migration, deployment or live data access occurred.


## 13. Additional audit — procurement receipt constraints, attachment/storage and access (2026-11-22)
- Receipt paths validate received quantities against remaining ordered quantities and, where allocations exist, against project allocation remaining balances. Unallocated surplus is separately receivable to general stock within the unallocated portion.
- Attachment entity visibility is tenant-scoped and project visibility is checked; event-linked files inherit project visibility in the inspected path. Uploads validate entity ownership and attachment type/format constraints.
- The storage repository is an abstraction in the source but currently delegates to Google Drive storage. This is a migration dependency to replace; V2 should not retain Drive/GAS dependency.
- Access-control/authentication currently relies on AuthService token-based current-user resolution and request-level caching. Detailed permission matrix and role-grant semantics remain pending audit.
- These findings are provisional audit evidence only; they do not define the V2 storage provider or permissions design.


## 14. Identity, permissions and tenant isolation — audit evidence (2026-11-22)
- Source login resolves unique username match and rejects ambiguity; account status and session tenant/user consistency are checked.
- Session state is currently stored in Script Properties with configurable expiry and throttled last-seen writes; this is a GAS dependency to replace in V2.
- Roles and role assignments are tenant-scoped; permissions are resolved through role-permission mappings with request-scoped caches.
- Endpoint-specific authorization is implemented across Router handlers; a route-by-route coverage audit remains open. V2 should use explicit deny-by-default authorization policies with tests.
- Many legacy repository reads are broad and then tenant-filtered. V2 should make tenant scoping and relationship integrity enforceable at the database/API layer.
- Evidence is documented in IDENTITY-PERMISSIONS-TENANT-CHECKPOINT-2.1.md. This remains discovery evidence, not approved schema design.


## 15. Repository and lifecycle semantics — source audit checkpoint
- GoogleSheetsRepository provides row-oriented CRUD, bulk insert, request caches and cache invalidation. Sheets do not provide relational transactional guarantees; V2 will require PostgreSQL transactions and integrity constraints.
- Project, Event, Workflow, Form and Project Information service paths were inspected at function-level. Their distinct lifecycle/versioning/snapshot/history semantics must be preserved rather than collapsed into generic records.
- ProjectDeletionService collects project dependencies across events, event data, attachments, material needs, procurement records, receipts, material specifications and calculations. Deletion/retention semantics must be explicitly designed and validated.
- Findings and unresolved questions are recorded in REPOSITORY-LIFECYCLE-AUDIT-CHECKPOINT-2.1.md; discovery remains open and no schema design is approved.


## Print-template resolver and API surface audit (2026-11-22)
- Print templates resolve allow-listed project fields, project information, event fields, and event-form fields on demand, tenant/project-access scoped; templates store definitions/version and asset references rather than project values.
- Resolver excludes void/cancelled events in inspected paths and selectively reads required sources. Template image/PDF asset handling has validation and reference-aware cleanup behavior; V2 must preserve asset integrity and decide snapshot/audit semantics.
- Router route families and selected authorization requirements documented in PRINT-RESOLVER-API-CHECKPOINT-2.1.md. Full endpoint mutation matrix remains pending; no schema implementation has started.


## Reference-copy and dependency deletion audit (2026-11-22)
- Repeat projects use workflow-owned reference policy (LIVE_SYNC/INITIAL_COPY/INDEPENDENT); copy operation IDs support replay/recovery lookup, with per-information-type status and source revision in copy plan.
- Project deletion collects dependencies under admin access and lock, protects shared procurement items/orders, soft deletes dependent rows, trashes attachment storage after metadata deletion, and physically removes project only when dependency handling has no failure. Partial failures are surfaced; not atomic.
- Audit rows are tenant-scoped and store action/entity/actor, old/new JSON, reason and timestamp; coverage is service-call-based, not proven exhaustive.
- Attachment access is tenant and parent-project visibility scoped; retention/recovery remains pending.


## 2026-11-22 — Phase 2 Task 2.1 conceptual consolidation
The consolidated conceptual model and review gate are documented in DATA-MODEL-DRAFT.md. It separates source-evidenced relationships from recommendations and unresolved questions, including tenant isolation, project/event/form snapshots and amendment/void, reference-project sync/copy, material calculation provenance, procurement allocations/receipts, attachment retention, audit policy, print resolver and transaction/outbox boundaries. It also records security migration concerns (legacy SHA-256 password hashing, observed URL token transport, missing rate-limiting evidence) and limitations of the route audit. The next recommended task is detailed field-level source-to-target mapping and constraint validation. This package awaits Product Owner acceptance; no schema or migration was implemented.


## 2026-11-22 — Phase 2.2 mapping task started
Read-only field/constraint mapping started. SOURCE-TO-TARGET-MAPPING-2.2.md records registry header evidence and verified behavioral constraints while distinguishing unknowns. Full source-level validation of types, requiredness, uniqueness, FK/cardinality, tenant constraints, reversals, integration contracts and workload patterns remains pending. No schema or migration work has begun.


## 2026-11-22 — Phase 2 Task 2.2 consolidated mapping review
The Task 2.2 review package is recorded in SOURCE-TO-TARGET-MAPPING-2.2.md. It consolidates legacy registry fields and logical targets across identity, project/workflow, events/forms, project information, reference-copy, material planning, procurement, attachments/audit, print templates, workflow rules and integration configuration. It documents evidence levels, tenant-aware relationships, snapshots/history, idempotency, side-effect boundaries, known divergent receipt paths, and unresolved business/policy/constraint decisions. The package is ready for Product Owner review; executable schema/migration decisions remain gated.


## 2026-11-22 — Task 2.2 Product Owner acceptance
The consolidated source-to-target mapping and comprehensive review package was explicitly accepted by the Product Owner. Task 2.2 is closed. This acceptance does not approve schema/migration implementation, production data access or changes, deployment, or VPS configuration.


## Phase 2 / Task 2.3 checkpoint — field semantics & constraint validation (2026-11-22)
Task 2.3 is in progress. Read-only source excerpts validate selected API-level semantics, including optional Event.ProjectID, EventTypeID/PerformedBy required at event creation, parseable optional OccurredAt, configured information-type values/cardinality, form field visibility rules, and PROJECT/INDEPENDENT event-type restrictions. These validator findings do not establish database nullability, FK or uniqueness rules. JSON payload boundaries, boolean normalization, timestamps, numeric precision and complete constraints require further validation. Evidence and scope are recorded in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md. No schema, SQL, migration, live-data access, tests, production changes or deployment occurred.

Additional Task 2.3 source evidence: shared Validator.js distinguishes requiredness from type checks, permits null/undefined for enum/object/number/string/boolean helpers in several cases, and uses strict runtime types for number/boolean. The selected GoogleSheetsRepository._rowToObject maps header values directly without normalization in that function. Therefore migration normalization must be explicitly designed and validated per persistence path; API validation alone does not establish SQL constraints. Detailed findings recorded in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md.

Further Task 2.3 selected validator evidence: draft projects use a different validation path than regular projects; project quantity is optional but validated as an integer >=1 when supplied; workflow-rule configuration uses RequirementMode/DataSource/SequenceMode compatibility rules; user/role/department validators have differing DTO optionality; attachments validate entity type plus MIME/extension pair; audit DTO fields are partially optional; print-template definitions are versioned JSON with page defaults and enumerated page/orientation values. Evidence and limitations are recorded in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md. These observations are not finalized database constraints.

Task 2.3 repository/service semantics checkpoint: source repository wrappers expose application-level ProjectCode/Workflow Code lookups without proving database-enforced unique scope; EventData read-path assumes one record per event but uniqueness is unverified; attachments use polymorphic entity links and deleted-status filtering; project-information values distinguish active lookup from any-status lookup and have revision history; audit snapshots serialize old/new data; procurement numeric helper relies on JavaScript Number and transaction-safe reconciliation remains required. Detailed findings/caveats are in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md. No constraints approved yet.

Additional Task 2.3 material-planning evidence: project material specifications are revisioned by SpecKey/Revision; create validates ItemName/Unit, ConsumptionPerUnit (positive lower bound), WastagePercent (0–100, default 0), and rejects duplicate key in its project/tenant scope. Revision rejects stale/superseded records, implying latest-revision concurrency guard and history semantics. Detailed evidence/caveats are recorded in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md; precision, full calculations, transactional race protection and final schema constraints remain unresolved.

Task 2.3 additional evidence: project deletion is admin-gated, tenant-scoped, dependency-aware soft deletion; it protects procurement objects shared with other projects and fails closed when dependency reads fail. Event transitions are workflow/event-type driven and draft events do not apply lifecycle changes until explicit completion in the inspected logic. User-role assignment checks tenant/active role and duplicate association before insert; race-safe uniqueness should be database-enforced. Findings/caveats recorded in FIELD-SEMANTICS-CONSTRAINT-VALIDATION-2.3.md; these are candidate design implications, not approved constraints.


### Discovery checkpoint: Procurement routes and material-planning lifecycle (2026-11-22)

Read-only source audit confirms the observed procurement write entry points require `PROCUREMENT_MANAGE` for order creation and receipt registration (single/batch), while read endpoints apply procurement/report view permissions. Material-planning source-event void handling marks calculation history `SOURCE_EVENT_VOIDED` and active confirmed needs `NEEDS_REVIEW`; it does not evidence automatic procurement reversal. Specification revision and calculation idempotency rely on service-level lookup/locking patterns that V2 must strengthen with DB-enforced uniqueness and transactions. These are discovery findings, not finalized schema or business decisions.


## Phase 2 — Task 2.3 comprehensive audit package (2026-11-22)
Task 2.3 field semantics and constraint validation source audit has been consolidated and cross-reviewed against accepted Task 2.2 mapping and the conceptual data-model draft. Findings include API validator semantics vs database constraints, tenant relationships, field representation/normalization, JSON and temporal boundaries, numeric precision uncertainty, revision/history/idempotency patterns, transaction boundaries, status and reversal observations, polymorphic references, and candidate uniqueness/FK/atomicity rules. Procurement lifecycle observations remain bounded to inspected sources; missing routes/methods are not asserted system-wide, and receipt VOID path/status remains unverified.

The review package is **IMPLEMENTED — READY FOR PRODUCT OWNER REVIEW**, not COMPLETE until explicit acceptance. No tests were executed; no live Sheets data, SQL/schema/migrations, production changes, deployment or VPS activity occurred. Next after acceptance: Phase 2 Task 2.4, schema design/constraint decisions, only as roadmap permits.


## Phase 2 — Task 2.3 Product Owner acceptance (2026-11-22)
The Product Owner explicitly accepted the Task 2.3 field semantics and constraint validation audit. Task 2.3 is closed/accepted. The audit package remains discovery evidence and candidate constraints, not an authorization to implement schema/migrations or access production data. Next task is Phase 2 Task 2.4, subject to reviewing the roadmap and establishing its scope and gate.


## Phase 2 — Task 2.4 schema blueprint initiation (2026-11-22)
Task 2.3 has been accepted. Task 2.4 is now IN PROGRESS and design-only. Its scope is a logical PostgreSQL schema blueprint, tenant-aware keys/relationships, uniqueness/cardinality candidates, lifecycle/history/snapshot/polymorphic storage modeling, transaction/idempotency/concurrency design, and a decision register. No SQL/DDL/migration, DB provisioning, live legacy data, production changes, deployment or VPS work is authorized. The deliverable will be reviewed against accepted Task 2.1–2.3 evidence and presented for Product Owner acceptance before implementation.


## 2026-11-22 — Phase 2 Task 2.4 blueprint progress
Work packages 2.4-A through 2.4-D have been drafted in POSTGRESQL-SCHEMA-BLUEPRINT-2.4.md. The design covers logical domain inventory, tenant-aware keys and relationship constraints, history/snapshot and deletion policy candidates, external file-storage lifecycle, audit/outbox, transactions, idempotency, concurrency and future migration-run reliability. This is design-only and remains pending cross-review and Product Owner acceptance. No executable schema, SQL/DDL, migrations, DB provisioning, production/live-data access, production changes or deployment has occurred.


## Task 2.4 — Cross-review checkpoint (2026-11-22)
A cross-review was performed against accepted Task 2.1 conceptual model, Task 2.2 mapping, and Task 2.3 field/constraint findings. Alignment and unresolved gaps are recorded in the blueprint. Task 2.4 is design-only and READY FOR PRODUCT OWNER REVIEW, pending acceptance. It is not a migration-ready schema specification. No executable SQL/DDL, DB provisioning, migration, live data, production change or deployment occurred.


## Task 2.4 Product Owner acceptance (2026-11-22)
The Product Owner accepted the PostgreSQL logical schema blueprint and cross-review package. Task 2.4 is closed as a design artifact only. This acceptance does not approve executable SQL/DDL, migrations, database provisioning or real-data access. Detailed source mapping and unresolved decisions remain dependencies for migration-ready schema work.


## Task 2.5 — Schema implementation readiness & evidence gap closure (2026-11-22)
Task 2.5 started following acceptance of the Task 2.4 blueprint. It is read-only discovery and readiness assessment to close evidence gaps before executable schema work. Scope includes source mapping completeness, lifecycle/mutation evidence, constraints, payload/reporting needs, procurement correction/reversal semantics, attachment owners, identity/ID mapping, numeric precision/timezone and retention. No SQL/DDL, migration, database provisioning, live data, production change or deployment is authorized in this task. A readiness-gap register and prioritized review package will be produced.


## Task 2.5 — source evidence audit increment 1 (2026-11-22)
A source-only review of DatabaseSetup.js, ProcurementService.js, AttachmentService.js, EventService.js, ProjectInformationService.js and FormService.js confirmed registry and operation-family evidence while keeping implementation-readiness gaps open. The prioritized register is in `TASK-2.5-READINESS-GAP-REGISTER.md`. Priority blockers remain exhaustive mutation/relationship mapping, procurement correction/reversal contract, event/form payload semantics and attachment owner inventory. IDs, precision/units, timezone, identity and retention are high-priority unresolved. No live data, executable schema, SQL, migration, DB setup, production changes, deployment or tests were performed.


## Task 2.5 — mutation and tenant-boundary evidence increment 2 (2026-11-22)
A bounded read-only sample across Router, Project/User/Workflow/EventType/InformationType services, ProjectDeletionService and related repositories confirmed tenant-aware service checks in several pathways, but not exhaustive indirect relationship integrity. Router authorization, service validation and relational constraints are separate layers to assess. G-01 and G-10 remain blockers. Evidence is in `TASK-2.5-READINESS-GAP-REGISTER.md`. No live data, schema, SQL, migrations, tests, production change or deployment.


## Task 2.5 — procurement/material/attachment evidence increment 3 (2026-11-22)
Source sampling observed tenant-scoped order idempotency, receipt replay checks, a separate locked batch-receipt pathway, source-event-driven material recalculation and void propagation, confirmed-quantity event integration, PROJECT/EVENT attachment owner validation, and preservation of shared procurement records during project deletion. These findings are bounded evidence, not a complete lifecycle contract. G-02 remains a blocker; G-04 is partially evidenced but not exhaustive. Storage deletion ordering and recovery are unresolved. See `TASK-2.5-READINESS-GAP-REGISTER.md`. No SQL/DDL, migration, live data, DB setup, tests, production change or deployment.


## Task 2.5 — Event/Form and central-information evidence increment 4 (2026-11-22)
Read-only source sampling confirmed distinct event request idempotency, event void controls, versioned event-form response and definition snapshots, amendment-event linkage, current central-information projection, separate revision/history/approval and reference-sync conflict resolution. G-03 remains a blocker pending exhaustive payload, reporting and dependency mapping; G-01/G-10 remain blockers; G-12 remains open. Evidence in `TASK-2.5-READINESS-GAP-REGISTER.md`. No SQL/DDL, migration, live data, database setup, tests, production change or deployment.


## Task 2.5 — payload shape/form-definition increment 5 (2026-11-22)
Read-only review observed legacy EventData JSON object validation, separate legacy EventData and reusable form-response persistence, ordered form fields, visibility rules, snapshot contents, SINGLE/MULTIPLE helper behavior, and separate central-information value/history/sync records. G-03 remains blocker pending full type/cardinality and payload/reporting inventory. See `TASK-2.5-READINESS-GAP-REGISTER.md`. No SQL/DDL, migration, live data, tests, database setup, production change or deployment.


## Task 2.5 — Information-type/column/report evidence increment 6 (2026-11-22)
Read-only audit sampled type/column validators and services, revision and structural-change restrictions, cache invalidation, attachment upload validation contract, and report/dashboard aggregation paths. Data-type and validation evidence recorded, but complete field semantics/report query inventory is not closed. G-03/G-04/G-10 remain blockers; G-06 high; G-12 open. See TASK-2.5-READINESS-GAP-REGISTER.md. No tests executed, SQL/DDL, migration, live data, DB provisioning, production change or deployment.


## Task 2.5 evidence increment 7 — identifiers and tenant relationship integrity (2026-11-22)
A read-only sample reviewed core ID prefix constants, UUID-derived procurement identifiers, authentication lookup and fail-closed username behavior, UUID session token and tenant metadata, project/event validators, tenant-bearing database registry and project deletion dependency collection. This bounded evidence shows mixed identifier conventions and application-level relationship checks but does not establish universal uniqueness or relational tenant enforcement. G-05/G-08 remain HIGH and G-01/G-10 remain blockers. Evidence is recorded in TASK-2.5-READINESS-GAP-REGISTER.md. No executable SQL/DDL, migrations, database provisioning, live data, tests, production changes or deployment.


## Task 2.5 evidence increment 8 — quantity and temporal semantics (2026-11-22)
Bounded source review observed positive-integer project quantity validation, JavaScript numeric validation in procurement/material planning, and parseable OccurredAt plus ISO timestamp patterns in sampled event/session pathways. This does not establish decimal precision, rounding, unit conversion, timezone storage/display or Jalali policy. G-06/G-07 remain HIGH and G-01 remains a blocker pending exhaustive field and consumer inventory. See TASK-2.5-READINESS-GAP-REGISTER.md. No executable SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


## Task 2.5 evidence increment 9 — identity and lifecycle recovery (2026-11-22)
Read-only sampling observed user status/update/audit paths, repository physical-delete capability, role-permission association bulk deletion, dependency-aware project deletion, shared procurement preservation and Drive trash behavior. This does not establish identity migration, audit completeness/retention, restore/purge or cross-system recovery contracts. G-08/G-09/G-11 remain HIGH and G-01 remains a blocker pending complete mutation/lifecycle coverage. See TASK-2.5-READINESS-GAP-REGISTER.md. No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


## Task 2.5 evidence increment 10 — configuration relationship graph
Read-only sampling observed tenant-scoped workflows and active step-department associations, department access, permission-gated project status management, tenant-scoped versioned print templates with PDF/image asset metadata, and read-only historical EventField context. Candidate dependencies recorded (workflow/steps/departments/event types; project/status; print template/version/assets; historical event data/field context) are not schema declarations and require exhaustive constraint/lifecycle validation. G-01/G-10 remain blockers and G-12 open. See TASK-2.5-READINESS-GAP-REGISTER.md. No SQL/DDL, migrations, DB provisioning, live data, tests, production changes or deployment.


## Task 2.5 evidence increment 11 — registry inventory baseline
A read-only extraction of the APP_CONFIG.SHEETS registry in DatabaseSetup.js identified 38 named registry definitions, grouped into identity/access, project/workflow, event/form/configuration, material/procurement, and file/template/audit domains. This is an inventory baseline only; exhaustive mutation/read/report/constraint coverage is pending. Registry naming includes two definitions with `18_` sheet-name prefix (MATERIAL_NEEDS and PROJECT_STATUSES), recorded as an observation to validate, not a defect assertion. G-01/G-10 remain blockers and G-12 open. No live data, SQL/DDL, migration, DB provisioning, tests, production changes or deployment.


## Task 2.5 evidence increment 12 — partial service/repository mapping
Read-only sampling recorded selected tenant-aware service/repository pathways for user-role assignment, role and permission catalog access, workflow-step event-rule versioned repository/validator, workflow-step-department association persistence and project-status lifecycle. Coverage states remain bounded; route callers, delete/restore, published configuration immutability, atomicity, indirect tenant constraints and consumer graph require further mapping. G-01/G-10 remain blockers and G-12 open. No SQL/DDL, migration, DB provisioning, live data, tests, production changes or deployment.


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


### Task 2.5 — Increment 16 evidence checkpoint (2026-11-22)
EventDataService and FormService were re-read in the read-only production reference. Bounded evidence confirms tenant/parent-project visibility checks, independent-event handling, DataJSON parsing/rejection of malformed or non-object payloads, and form-response records containing ResponseJSON and DefinitionSnapshotJSON with update-or-insert behavior. This does not establish immutable response history, comprehensive payload query/report/search requirements, exhaustive amendment lifecycle, or transactional sync with central information. G-03 remains BLOCKER; G-01/G-10 remain BLOCKERS. No tests or implementation work.


## Task 2.7 — Evidence closure work package (2026-11-22)
Task 2.7 is a read-only discovery and traceability effort covering event/form history (G-03), attachment ownership/lifecycle (G-04), tenant-safe relationship integrity (G-10), and registry-to-route/service/dependency coverage (G-01). G-02 remains an open procurement lifecycle blocker. Physical schema design remains gated; source observations must not be converted into executable constraints without evidence and review. No production repository writes, live-data access, SQL/DDL or migration work is authorized in this task.


## Task 2.7 Increment 1 checkpoint (2026-11-22)
- Bounded read-only event/form and attachment lifecycle cross-check recorded in TASK-2.7-BLOCKER-CLOSURE.md.
- Evidence notes include void policy, form amendment/snapshot behavior, EventData access, polymorphic attachment owner and visibility, deletion ordering, project-image copy linkage and candidate tenant relationship graph.
- G-03/G-04/G-10/G-01 remain unresolved. No tests, live data, SQL/DDL, migration, DB provisioning or production modifications.


## Task 2.7 Increment 2 checkpoint (2026-11-22)
- Recorded DatabaseSetup.js header inventory and bounded Router.js route dispatch inventory.
- Covered security, project/workflow, event/form, information/template, attachment/audit and procurement structures; noted duplicate/legacy numbering/backup artifacts as reconciliation hazards.
- G-01/G-10 remain open. No tests, live data, SQL/DDL, migrations, provisioning or production changes.


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


### Task 2.7 Increment 6 — Tenant relationship integrity
- Source evidence indicates service-layer tenant validation is the dominant legacy safeguard; generic repositories frequently expose ID/global-sheet accessors without tenant isolation in their contract. Procurement reads are tenant-filtered, graph validation remains service-side.
- Polymorphic attachment ownership, nullable independent events, history and procurement cross-links are physical-schema challenges. Evidence does not establish relational enforcement or readiness.


### Task 2.7 Increment 7 — Event/form history
- Legacy event/form paths persist form response payloads and definition snapshots, with amendment replay/optimistic checks and void state restoration in sampled services. Writes are multi-step; atomicity, immutable lineage, full reporting parity and retention remain unproven.


### Task 2.7 Increment 8 — Attachment lifecycle
- Legacy upload and delete paths sequence storage and metadata/audit writes; compensation, restore, orphan reconciliation, retention/purge and transactional guarantees are not established. Attachment owner remains polymorphic PROJECT/EVENT.


## Task 2.7 closure assessment
- Audit package is deliverable-complete pending Product Owner acceptance, with nine bounded evidence increments and explicit closure conditions for G-01/G-02/G-03/G-04/G-10.
- Underlying readiness blockers remain open; schema readiness is NOT APPROVED. No schema/DDL/migration/provisioning/live-data/test/deployment or production write authorized/performed.


## Task 2.8 — Remaining blocker policy preparation
- Prepared provisional policy recommendations and explicit evidence/decision gates for G-01/G-02/G-03/G-04/G-10. Recommendations are not approved business rules. Task 2.8 is in progress; Schema Readiness NOT APPROVED.


## Task 2.8 — Proposed policy defaults (2026-11-22)
- Companion register: TASK-2.8-RECOMMENDED-POLICY-DEFAULTS.md.
- It proposes default business/technical policy for procurement, history/amendment/void, attachment lifecycle, tenant integrity and exhaustive legacy coverage.
- Status: proposals only. PO approval and evidence closure are required before these become policy or are translated into executable schema. No schema/migrations, tests, live-data work, provisioning, deployment or production modifications.


## Task 2.8 — Lifecycle and transaction contract draft
- TASK-2.8-LIFECYCLE-TRANSACTION-CONTRACTS.md proposes state transitions, invariants, transaction/concurrency pattern and test contracts across procurement, history, attachment and tenant domains.
- These contracts remain design proposals and are not implemented or runtime-tested. Schema readiness remains gated on policy acceptance and source evidence closure.


### Task 2.8 — G-01 source coverage increment
- Recursive tree inventories verified for production reference frontend (187 entries) and backend (153 entries). Tree counts are not coverage metrics.
- New evidence record: TASK-2.8-G01-SOURCE-COVERAGE-INCREMENT-1.md. Frontend page surface, backend component surface and reconciliation rows are explicitly PARTIAL.
- Next evidence: exhaustive Router.js operation inventory and cross-check with frontend route/navigation and test files. G-01 remains open.


### Task 2.8 — G-01 Router surface increment 2
- Fetched backend Router.js and frontend router/API core; documented GET/POST/PUT/DELETE surface families and client route-permission registry.
- Evidence: TASK-2.8-G01-ROUTER-SURFACE-INCREMENT-2.md. Dynamic route branches, exact handlers, permissions, tenant checks, callers and tests remain to reconcile. G-01 remains open.


### Task 2.8 — G-01 service/test evidence sampling
- Sampled core access-control, event, procurement, attachment, project-deletion, workflow and project-information services read-only; catalogued test file themes.
- Evidence document: TASK-2.8-G01-SERVICE-TEST-SAMPLING-INCREMENT-3.md. This remains partial evidence, not verification of runtime behavior. G-01 stays open.


### Mutation Risk Evidence — Increment 4
Read-only review recorded for project cascade removal, attachment deletion and procurement receipt operations. Important V2 incompatibilities include preserving receipt facts, separating metadata from physical attachment deletion, transactional domain/audit/outbox writes, and payload-bound idempotency. Evidence is limited and source-based.


### Event/Form Lifecycle Evidence — Increment 5
Read-only source review documents event creation/void, project-state restoration, direct metadata edits, form response definition snapshots and correction flows. V2 should preserve immutable revision history, transactional DB-local changes, and reconciled projections/outbox. Evidence is bounded; tests not run.


## Task 2.8 Increment 52 — Tenant-qualified relationship header crosswalk
- Added `TASK-2.8-TENANT-QUALIFIED-RELATIONSHIP-HEADER-CROSSWALK-INCREMENT-52.md`: header-derived candidate tenant-qualified relationship edges and special-case notes for parent-derived EventData, global-looking Permissions, polymorphic attachment/audit ownership, and nullable event scope.
- This is structural E1 evidence only; no live schema, runtime validation, SQL, migration or production write. G-10 remains OPEN.


## Task 2.8 Increment 53 — Independent event and attachment ownership trace
- Read-only EventService/AttachmentService trace recorded in `TASK-2.8-INDEPENDENT-EVENT-ATTACHMENT-OWNERSHIP-TRACE-INCREMENT-53.md`. Sampled independent events are tenant-filtered; project-linked events and attachments require parent-project tenant/access validation; polymorphic attachment ownership remains a DB integrity challenge.
- Bounded evidence only; no runtime tests or readiness approval. G-10/G-04/G-01 remain open.


## Task 2.8 Increment 54 — Repository lookup/tenant-filter boundary
- Read-only inspection of EventRepository, AttachmentRepository, ProjectRepository and generic ID lookup recorded in `TASK-2.8-REPOSITORY-LOOKUP-TENANT-FILTER-BOUNDARY-TRACE-INCREMENT-54.md`. Generic repository ID lookup and selected relation filters do not themselves enforce tenant ownership; service-level checks are relied upon in sampled callers.
- Bounded evidence only; exhaustive caller/runtime negative testing not done. G-01/G-10 remain OPEN.


## Task 2.8 Increment 55 — Identity/RBAC repository tenant boundary
- Read-only trace recorded in `TASK-2.8-IDENTITY-RBAC-REPOSITORY-TENANT-BOUNDARY-TRACE-INCREMENT-55.md`. Selected User/Role/Department ID lookups and UserRole association queries are not tenant-qualified at repository level; service-level tenant validation/filtering is observed in sampled paths.
- Bounded E1 evidence only. G-01/G-10 remain OPEN; no schema readiness approval.


## Task 2.8 Increment 56 — User-role mutation/audit boundary
- Bounded source trace recorded in `TASK-2.8-USER-ROLE-MUTATION-AUDIT-BOUNDARY-TRACE-INCREMENT-56.md`: assignment checks tenant and active role, duplicate association and user-role match, then separately writes audit; sampled permissions resolution is tenant-role scoped with global catalog read.
- E1 only; runtime/exhaustive mutation testing not performed. G-01/G-10 remain OPEN.


## Task 2.8 Increment 57 — Procurement/material planning lifecycle trace
- Source trace recorded in `TASK-2.8-PROCUREMENT-MATERIAL-PLANNING-LIFECYCLE-TRACE-INCREMENT-57.md`: bounded order/receipt/idempotency/locking and recovery behavior, material-spec revision and calculation ledger, source-event void cascade and separate audit writes.
- E1 only; no runtime testing. G-02/G-03/G-10 remain OPEN; schema readiness NOT APPROVED.


## Task 2.8 Increment 58 — Event void and ProjectInformation revision/sync
- Bounded source trace recorded in `TASK-2.8-EVENT-VOID-PROJECT-INFORMATION-REVISION-SYNC-TRACE-INCREMENT-58.md`: sequential event/project restoration and material cascade with surfaced reconciliation failure; ProjectInformation value/history/audit/reference sync writes; sampled form/event data visibility controls.
- E1 only; runtime testing not performed. G-03/G-10/G-01 remain OPEN.


## Task 2.8 Increment 59 — Attachment storage/delete lifecycle
- Bounded source trace recorded in `TASK-2.8-ATTACHMENT-STORAGE-DELETE-LIFECYCLE-TRACE-INCREMENT-59.md`: storage and metadata/audit writes are sequential; delete trashes the file before metadata/audit; retention/backup/recovery evidence remains pending.
- E1 only; no runtime testing. G-04/G-10 remain OPEN.


## Task 2.8 Increment 60 — Audit and project cascade deletion integrity
- Trace recorded in `TASK-2.8-AUDIT-PROJECT-CASCADE-DELETION-INTEGRITY-TRACE-INCREMENT-60.md`: audit query boundary and sequential cascade deletion/storage trashing, culminating in project physical deletion and partial-failure reporting.
- E1 only; no runtime tests. G-04/G-10/G-01 remain OPEN.


## Task 2.8 Increment 61 — Router/API permission boundary trace
- Sampled Router.js permission route trace recorded in `TASK-2.8-ROUTER-API-PERMISSION-BOUNDARY-TRACE-INCREMENT-61.md` for procurement, project material planning, information/history, attachment and audit endpoints.
- E1 only; full endpoint coverage/tests remain pending. G-01/G-10 remain OPEN.


## Task 2.8 Increment 62 — Workflow version/event rule lifecycle
- Bounded trace recorded in `TASK-2.8-WORKFLOW-VERSION-EVENT-RULE-LIFECYCLE-TRACE-INCREMENT-62.md`: versioned workflow rules, clone/replace operations and lifecycle/integrity implications.
- E1 only; runtime/full publish testing not done. G-01/G-03/G-10 remain OPEN.


## Task 2.8 Increment 63 — Legacy test inventory and V2 gap map
- Inventory recorded in `TASK-2.8-LEGACY-TEST-INVENTORY-VERIFICATION-GAP-MAP-INCREMENT-63.md`: 40 test files in legacy backend tests tree; representative suites reviewed by name. Existing tests were not executed; gaps in PostgreSQL, migrations, RLS, transaction/concurrency and end-to-end tenant verification are noted.
- G-01/G-10 remain OPEN; schema readiness NOT APPROVED.


## Task 2.8 — Consolidated audit matrix (Increment 64, 2026-11-23)
- Added consolidated domain coverage/evidence/readiness matrix, evidence classification and gap register across tenant/RBAC, workflow, projects, events/forms, information history, material planning, procurement, storage, audit, API authorization and migration/runtime validation.
- Matrix explicitly separates bounded source observations and test-source inventory from runtime verification; no runtime pass is claimed.
- G-01/G-10 remain blockers. Task 2.8 remains NOT READY for executable schema/migration. No production code/data or live database touched.


## Task 2.8 — GET dispatcher inventory (Increment 65, 2026-11-23)
- Bounded read-only GET branch-family inventory and permission-pattern observations documented. V2 recommendation: explicit route registry and route-by-route authorization/tenant-negative testing.
- GET only; mutation routes and runtime coverage remain unverified. No production changes.


## Task 2.8 — Router mutation-route inventory checkpoint (Increment 66, 2026-11-23)
- Read-only Router.js audit now includes bounded POST/PUT/DELETE dispatch inventory, permission guards, notable fallback branches, and review caveats.
- Source evidence: production Router.js blob SHA `f2542a17993b852c7dd2968a8fefe57f70b991f3`.
- Review note: print-template project-data binding PUT branch has no explicit Router-level `AccessControlService.require` visible in that branch; service-side authorization must be checked before drawing conclusions.
- Source-level inventory is not runtime verification. Tenant boundaries, service/repository invariants, full dependency coverage, and runtime tests remain open.


## Task 2.8 — Downstream verification checkpoint (Increment 67, 2026-11-23)
- Reviewed `PrintTemplateService.updateProjectBinding` and delegated handlers. The route's missing visible Router-level guard is not treated as a confirmed bypass: the service checks tenant-scoped template/project availability, and dispatch requires PROJECT_EDIT or EVENT_EDIT by source type. Delegated handlers also contain tenant, locking/version, field allow-list and event restrictions (bounded source observations).
- No runtime test performed; verification remains source-only.


## Task 2.8 — Tenant-boundary and role mutation trace (Increment 68, 2026-11-23)
- Recorded bounded UserRoleService/RoleService/UserService/PermissionService/WorkflowService and repository-boundary observations in `TASK-2.8-TENANT-BOUNDARY-ROLE-MUTATION-TRACE-INCREMENT-68.md`.
- Assignment tenant checks and role tenant/code checks were observed in sampled source; writes and audit appear separate in sampled flows. Generic project/event ID lookup is caller-dependent; procurement retrieval helpers tenant-filter.
- V2 requirements: tenant-aware composite integrity, transaction/audit strategy, protected-role invariant and cross-tenant negative tests. These are requirements only, not implemented.
- E1 only; no runtime tests. G-01/G-10 remain OPEN/BLOCKER; schema readiness NOT APPROVED. No production changes.


## Task 2.8 — Procurement and cascade-delete transactional gap trace (Increment 69, 2026-11-23)
- Bounded source review documented in `TASK-2.8-PROCUREMENT-AND-PROJECT-DELETE-TRANSACTIONAL-GAP-TRACE-INCREMENT-69.md`: locks/idempotency/prevalidation and compensation in procurement; sequential dependency mutation, storage trash and physical project delete in cascade path.
- These are current-system observations and V2 requirements, not validated runtime behavior. V2 should transactionally enforce quantity/allocation/idempotency invariants, tenant-aware constraints, audit/outbox and staged storage reconciliation.
- No tests executed. G-02/G-03/G-04/G-10 and G-01 remain open; schema readiness NOT APPROVED. No production changes.


## Task 2.8 — Project/Event/Information/Material/Attachment boundary trace (Increment 70, 2026-11-23)
- Bounded read-only source trace recorded in `TASK-2.8-PROJECT-EVENT-INFORMATION-MATERIAL-ATTACHMENT-BOUNDARY-TRACE-INCREMENT-70.md`: sampled tenant checks, optimistic concurrency/idempotency/locks and sequential multi-write/storage operations across project, event, information, material, attachment and audit paths.
- V2 requirements include transaction-backed revision/copy/recalculation, tenant-aware constraints, audit/outbox, external-storage reconciliation and cross-tenant negative testing.
- E1 only; no runtime tests. G-01/G-10 and G-02/G-03/G-04 remain open; schema readiness NOT APPROVED. No production changes.


## Task 2.8 — Legacy test intent review (Increment 71, 2026-11-23)
- Current tests-directory snapshot listed 41 files (earlier audit snapshot reported 40; count drift noted). Reviewed six representative test sources for coverage intent only, including material planning, procurement, project deletion, workflow enforcement, attachment access and schema/idempotency recovery.
- No test execution or pass claims. V2 test strategy must add PostgreSQL integration, tenant-negative, concurrency, rollback, migration, audit durability and storage recovery coverage. G-01/G-10/G-02/G-03/G-04 remain open; schema readiness NOT APPROVED.


## Task 2.8 Increment 72 — Relationship and constraint crosswalk (2026-11-23)
- Added `TASK-2.8-RELATIONSHIP-CONSTRAINT-CROSSWALK-INCREMENT-72.md`: bounded logical-edge matrix from DatabaseSetup headers and sampled repository contracts across tenant, project/event, forms, central information/history, workflow, materials/procurement and polymorphic storage/audit.
- Candidate tenant-qualified composite constraints, transaction-backed invariants and versioned-ledger requirements are documented as recommendations, not approved DDL. Cardinality and lifecycle policy remain unresolved; one repository fetch failed.
- E1 only; no runtime tests, schema implementation or production changes. G-01/G-10 BLOCKER and G-02/G-03/G-04 OPEN; readiness NOT APPROVED.


## Task 2.8 Increment 73 — Cross-operation dependency/failure boundary audit
- Recorded bounded source trace in `TASK-2.8-CROSS-OPERATION-DEPENDENCY-FAILURE-BOUNDARY-INCREMENT-73.md`: deletion dependency re-collection/shared procurement protections and sequential partial-state handling; event void checks; project update allow-list/optimistic version; information save lock; attachment storage/metadata/audit ordering.
- V2 requirements: durable deletion/reconciliation operations, transactional state and outbox for external storage, retry/fault-injection testing and shared allocation constraints. E1 only, not runtime verified; no production changes.


## Task 2.8 Increment 74 — Project deletion coverage gap
- Source review of ProjectDeletionService collector (SHA 62482bfb42c2c7bb3329becbb2f821e1efbb4c8e) and FormRepository found that the explicit dependency collector omits ProjectInformation/history, EventFormData responses and reference-sync logs from its visible child inventory. This is a coverage gap in the sampled source, not runtime evidence of orphaning.
- Also documented the post-project-delete audit write boundary. V2 should use a dependency registry/disposition policy, durable deletion operation and transaction/outbox; no physical deletion before full dependency accounting.
- E1 only; no tests or production changes. G-01/G-10 remain blockers; schema readiness NOT APPROVED.


## Task 2.8 Increment 75 — Event void and reference-copy reconciliation
- Bounded trace recorded in `TASK-2.8-EVENT-VOID-REFERENCE-COPY-RECONCILIATION-INCREMENT-75.md`: void validation/restoration and post-update material reconciliation failure reporting; reference-copy operation replay and image ownership validation.
- V2 should implement durable transactional state plus reconciliation outbox, retry-safe operation records and staged external file copy. E1 only; runtime tests and aggregate recovery remain unverified. No production changes.


## Task 2.8 Increment 76 — Form snapshot and workflow draft/publish boundary
- Recorded bounded source trace in `TASK-2.8-FORM-SNAPSHOT-AND-WORKFLOW-DRAFT-PUBLISH-BOUNDARY-INCREMENT-76.md`: FormService validates mapping/tenant/current version/visibility and requiredness, captures DefinitionSnapshot with event-form responses; mutations bump form version and invalidate cache. WorkflowService excerpts show published-version draft cloning and version-qualified rule replacement.
- Atomic response lineage/audit synchronization, workflow publish rollback and concurrency/recovery are unverified. E1 only; no runtime tests or production changes. G-01/G-03/G-10 remain open; G-02/G-04 remain open. Schema readiness NOT APPROVED.


## Task 2.8 Increment 77 — Independent events and procurement timeline projection
- Recorded bounded EventService source observations in `TASK-2.8-INDEPENDENT-EVENT-AND-PROCUREMENT-TIMELINE-PROJECTION-INCREMENT-77.md`: tenant-scoped list includes independent events without ProjectID; project-bound events are visibility-filtered; procurement orders/receipts are projected into project timelines from source procurement records rather than duplicate event rows.
- Independent-event mutation lifecycle, complete route coverage and projection consistency are unverified. E1 only; no tests or production changes. G-01/G-10 blockers and G-02/G-03/G-04 open. Schema readiness NOT APPROVED.
