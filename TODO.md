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
