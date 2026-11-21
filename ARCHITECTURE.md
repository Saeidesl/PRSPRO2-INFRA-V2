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

## 9. Next Work
Phase 0, Task 0.1: verify access/availability of the three remaining V2 repositories.
Phase 0, Task 0.2: perform read-only audit of the production frontend and backend source, database structure and workflows.