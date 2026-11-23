# Task 2.8 Increment 128 — Backend Router endpoint and permission crosswalk (GET/POST/PUT/DELETE)

Date: 2026-11-23  
Evidence: E1 — read-only Router.js inspection; route dispatch and permission checks recorded

## GET route families observed
- Authentication/me and permissions; admin sessions and project deletion preview.
- Project statuses, departments, projects, project details/information/history/events/workflow progress/material specifications/calculations/attachments/image gallery/lookups.
- Workflows, steps, management data, event rules and department assignments.
- Print-template list/revision/source, bindings, image/PDF download and template data.
- Forms, information types/columns, event types/forms/procurement settings, events/form responses/dynamic data/attachments.
- Users, user-role lists, roles/permissions, audit logs, dashboard/lookups, procurement workspace/reports/dashboard/needs/orders and project reports.

## Mutation route families observed
- Login/logout.
- Print template create/resolve/preview/image/PDF upload/update/project-data binding.
- Project status/department/role/user CRUD and role permission single/bulk assignment/removal; admin session revoke.
- Procurement order creation and receipt single/batch.
- Material specification create/revise; project reference-copy; project create/update/complete-step and attachment upload.
- Workflow create/update/step/publish/department assignment/rules.
- Information type/column create/update/deactivate and project-information save/approve/reference conflict resolution/deactivate.
- Form/field create/update/remove, event-type form assign/unassign, event-type create/update/procurement integration.
- Event create, event/form response update, event void, event/project attachment upload, attachment upload and delete.

## Permission checks sampled directly from Router
- Admin-only: project deletion preview/removal and session list/revoke.
- PROJECT_STATUS_MANAGE: project-status read/update; DEPARTMENT_MANAGE: department mutation; USER_* for users/user-role; ROLE_MANAGE for role and role permission mutations.
- PROJECT_VIEW/PROJECT_EDIT/PROJECT_CREATE paths include project visibility, draft creator exceptions, information, material specification and reference copy.
- EVENT_VIEW/EVENT_CREATE/EVENT_EDIT paths cover event lists/details/create/void/response edits; ATTACHMENT_VIEW/UPLOAD/DELETE paths cover attachment surfaces.
- WORKFLOW_MANAGE covers workflow mutations/rules/publish/assignment, with limited PROJECT_VIEW/PROJECT_CREATE read access.
- SYSTEM_SETTINGS covers form/information type and template configuration mutations; EVENT_TYPE_MANAGE covers event-type mutations and integrations.
- PROCUREMENT_MANAGE for orders/receipts; PROCUREMENT_VIEW or MANAGE for procurement read endpoints; REPORT_VIEW for dashboard/report/audit endpoints.
- AccessControlService.enforceRouteAccess itself only authenticates selected paths; detailed permission checks are in Router and services.

## Crosswalk limits and candidate gaps
- This increment establishes dispatch and permission families but is not an exhaustive route-by-route permission matrix: every branch, nested route, response shape, caller and service check must still be reconciled.
- Frontend callers, route matching edge cases and method override behavior require separate matrix verification and automated negative tests.
- Some paths rely on service-level checks, project visibility helpers or multi-permission alternatives; no runtime authorization tests were executed.
- Source-level observations must not be interpreted as confirmed access-control defects without full route/caller validation.

## V2 implications
1. Generate/maintain an explicit API catalog with route, method, schema, permission policy, service, idempotency and tests.
2. Centralize authorization policy and deny-by-default; keep tenant/project resource checks at query boundaries.
3. Test all endpoint branches with allowed/denied roles, tenant mismatches, project visibility and resource identifiers.
4. Define API versioning, error envelopes, read/write scopes and consistent authorization for nested endpoints.
5. Ensure all UI caller paths map to explicit endpoint contracts and eliminate undocumented fallback routes.

## Limits
- Source-only inspection; no runtime tests, production changes or live data accessed.
- E1 only; route crosswalk remains IN PROGRESS; G-01/G-10 remain open; schema readiness NOT APPROVED.
