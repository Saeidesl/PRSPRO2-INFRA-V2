# Task 2.8 Increment 226 — Additional frontend caller inventory

Date: 2026-11-24

## Scope
Read-only source inspection of additional PRSPRO2-PRODUCTION frontend modules: attachments, audit, auth, departments, event-types, information-types, material-planning, procurement, reports, users, roles and permissions.

## Selected route literals (E1)
- Attachments: /attachments (dynamic variants).
- Audit: /audit.
- Auth: /auth/login, /auth/logout, /auth/me.
- Departments: /departments.
- Event types: /departments, /users, /project-statuses, /event-type-forms, /event-types, /forms, /information-types.
- Information types: /information-types, /information-type-columns.
- Material planning: /material-specifications, /projects.
- Procurement: /procurement/workspace, /procurement/dashboard, /procurement/needs, /procurement/orders, /procurement/receipts/batch, /projects.
- Reports: /reports/projects, /reports/project.
- Users: /users.
- Roles: /roles, /roles/available-permissions.
- Permissions: /permissions.

Static literal extraction is incomplete for variable/interpolated paths and does not establish backend contract, authorization, caller coverage or test status.

## Target V2 discovery implications
These domains and routes must be captured in a verified API/domain contract matrix: auth/session, attachments, audit, departments, event types, information types/columns, material planning, procurement, reports, users, roles and permissions. Capture lifecycle/side effects, tenancy, access-control, idempotency, storage, and recovery for each. Preserve source workflows but design target contracts independently.

## Limits
Selected modules only, E1. This increment extends but does not close the exhaustive frontend/backend path/caller crosswalk. No runtime tests or production modifications. G-01/G-10 OPEN; schema readiness NOT APPROVED.
