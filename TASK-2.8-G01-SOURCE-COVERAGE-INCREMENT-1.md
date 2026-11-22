# Task 2.8 — G-01 Source Coverage Reconciliation (Increment 1)

**Status:** Bounded inventory increment; G-01 remains OPEN. Read-only evidence gathering only.
**Date:** 2026-11-22

## Evidence capture

Recursive Git trees were successfully retrieved from the default branch of the READ-ONLY production reference repositories:
- `PRSPRO2-PRODUCTION`: 187 tree entries.
- `PRSPRO2BACKEND-PRODUCTION`: 153 tree entries.

These are tree-entry counts (directories and files), not active-code counts, coverage percentages or evidence that all implementation paths are mapped.

## Frontend structure surface (tree-level)

Observed HTML pages include admin, attachments, audit, dashboard, departments, event-types, events, forms, information-types, login, permissions, print-designer, print-output, procurement, project-detail, project-statuses, projects, reports, roles, settings, users and workflows, plus index and test-data-seeder.

Previously sampled modules: `js/core/api.js`, `js/core/cache.js`, `js/core/router.js`, `js/modules/projects.js`, `js/modules/events.js`, `js/modules/procurement.js`.

**Coverage classification:** PARTIAL. Tree names establish the surface but not route wiring, callers, user flows, contracts or consumer completeness.

## Backend structure surface (tree-level)

Observed service/repository components include:
- Router, DatabaseSetup, GoogleSheetsRepository.
- Authentication/access: AuthService, AccessControlService, UserService/Repository, RoleService/Repository, PermissionService/Repository, UserRoleService/Repository, RolePermissionService/Repository.
- Project: ProjectService/Repository, ProjectDeletionService, ProjectInformationService/Repository, ProjectStatusService.
- Workflow: WorkflowService/Repository, WorkflowStepDepartmentService/Repository, WorkflowStepEventRuleRepository.
- Event/form: EventService/Repository, EventDataService/Repository, EventTypeService/Repository, EventFieldRepository, FormService/Repository.
- Information model: InformationTypeService/Repository.
- Procurement/material: ProcurementService/Repository, MaterialPlanningService, ProcurementEventIntegrationService.
- Attachments/storage: AttachmentService/Repository, StorageRepository.
- Reporting/printing/audit/integration: ReportService/Repository, PrintTemplateService/Repository, AuditService/Repository, TelegramBotService.

**Coverage classification:** PARTIAL. Presence is verified at tree level; exact operations, route exposure, permission mapping, dependency/caller relationships and side effects are not yet exhaustively reconciled.

## Reconciliation matrix (increment 1)

| Surface | Tree evidence | Prior source evidence | Coverage state | Missing evidence |
|---|---|---|---|---|
| Frontend pages and entrypoints | 187-entry tree; named HTML pages above | core API/cache/router and selected modules | PARTIAL | all routes, navigation/callers, page-to-module mapping, forms and consumers |
| Backend router | Router.js in tree | Router families described in earlier audit | PARTIAL | every route/method, handler, auth gate, payload, caller and error semantics |
| DB sheet structures | DatabaseSetup.js in tree | declared header arrays and entity inventory recorded earlier | PARTIAL | exact header-to-service mapping, uniqueness/ID semantics, unused/legacy structures and data constraints |
| Domain services | 153-entry tree; service inventory above | bounded sampling in Tasks 2.5–2.7 | PARTIAL | every public operation and mutation, dependency graph, external side effects |
| Repositories | repository files observed | selected reads/writes sampled | PARTIAL | operation-level mapping, query filters, consistency assumptions and data access paths |
| Tests and CI | test/workflow files present in trees | workflow definitions inventoried; no execution | PARTIAL | test-to-domain/route mapping, uncovered mutations, actual execution status |
| Integrations | Telegram and procurement event integration present | selected source paths sampled | PARTIAL | exhaustive external calls, retry, failure, secret/config dependencies, scheduling |
| Attachments | attachment/storage components present | upload/download/delete/copy/owner evidence sampled | PARTIAL | complete owner registry, consumers, lifecycle recovery and purge/restore |
| Reporting/projections | report components and frontend pages present | selected dashboard/report consumers sampled | PARTIAL | complete projection inventory, data source parity and access controls |

## Verified repository access and safety

- Read-only tree retrieval succeeded for both production reference repositories.
- No production source files were changed.
- No test or runtime execution was performed.
- No production data or secret/configuration values were accessed.

## Next evidence increment

Proceed with an operation-level route registry derived from Router.js and cross-check it against frontend router/navigation and tests. Fetch and analyze source excerpts in bounded batches; distinguish confirmed route entries from handler/caller/permission details that remain unverified. G-01 remains a blocker; this increment does not claim exhaustive coverage or schema readiness.
