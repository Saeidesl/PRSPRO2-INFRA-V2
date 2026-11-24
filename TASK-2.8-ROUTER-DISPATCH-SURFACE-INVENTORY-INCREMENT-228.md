# Task 2.8 Increment 228 — Router dispatch surface inventory (GET/POST/PUT/DELETE)

Date: 2026-11-24

## Scope
Read-only inventory from re-fetched production Router.js. Route branches and guards are grouped by domain for future target API mapping. Static source evidence only.

## GET surface groups (observed)
- Auth/session and permissions: /auth/me, /auth/me/permissions, admin sessions.
- Project data: project statuses, departments, image/gallery counts, project form lookups, projects, project information/list/history, project-linked events and attachments.
- Workflow: workflow management bootstrap, workflows and related branches.
- Print: template revision, templates, sources, images/PDF and project-bound resolve/preview.
- Forms/configuration: forms, information types, event types, form definitions/responses.
- Attachments: attachment listing/details/download.
- Identity/administration: users, roles, available permissions, permissions.
- Audit/report/dashboard: audit and entity/user audit, project dashboards, procurement workspace/reports/dashboard/needs/orders, project reports and dashboard.
- Other domain endpoints were present in dispatch tree; list is intentionally selected/grouped rather than exhaustive.

## Mutation surface groups (observed)
- Project status, department, project create/edit/draft/reference-copy/deletion and project information save/approve/conflict-resolve/deactivate.
- Workflow create/update/step/department assignment/event rules/publish.
- Form definition/fields, event-type/form mapping and event type/procurement configuration.
- Event create/edit/void/step completion, event form amendments and attachment uploads.
- Attachment upload/delete.
- User/role/permission administration.
- Procurement order and receipt operations.
- Print-template create/update/revision/resolve/preview and image/PDF uploads.

## Selected Router permission observations
Many branches call AccessControlService.require explicitly; observed permission families include PROJECT_VIEW/CREATE/EDIT, EVENT_VIEW/CREATE/EDIT, ATTACHMENT_VIEW/UPLOAD/DELETE, WORKFLOW_MANAGE, SYSTEM_SETTINGS, EVENT_TYPE_MANAGE, USER_VIEW/CREATE/EDIT, ROLE_MANAGE, PERMISSION_MANAGE, REPORT_VIEW, PROJECT_STATUS_MANAGE, DEPARTMENT_MANAGE and PROCUREMENT_MANAGE.
Some branches have custom conditions or delegate additional checks; this summary does not assert a complete or uniform guard for every path.

## Risks/gaps
- Router dispatch order, method-specific matching, overlapping prefixes and special-case guards require exhaustive per-route analysis; this inventory alone does not establish authorization correctness.
- Frontend callers, service-level tenant predicates, data/write contracts, idempotency, failure/recovery and test coverage still need row-by-row matching.
- No conclusions about confirmed vulnerabilities; partial observations are candidates for verification only.

## Evidence and safety
E1 source. No runtime tests, production changes, live data, SQL/DDL, migrations, provisioning or deployment. G-01/G-10 OPEN; schema readiness NOT APPROVED.
