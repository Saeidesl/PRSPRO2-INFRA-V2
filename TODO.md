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
- [ ] Audit frontend repository structure, modules, routes, dependencies, and workflows.
- [ ] Audit backend services, routes, repositories, data access, permissions, integrations, and deployment.
- [ ] Inventory Google Sheets database tables, headers, identifiers, relationships, and data conventions.
- [ ] Map current business workflows and role/permission behavior.
- [ ] Inventory file storage and integrations (Drive, Telegram, etc.).
- [ ] Identify critical risks, performance constraints, and migration dependencies.
- [ ] Record findings and unknowns in ARCHITECTURE.md.

**Status:** NOT STARTED

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