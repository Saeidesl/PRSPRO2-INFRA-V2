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
