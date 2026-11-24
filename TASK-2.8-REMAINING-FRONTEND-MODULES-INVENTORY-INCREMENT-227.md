# Task 2.8 Increment 227 — Remaining selected frontend modules caller inventory

Date: 2026-11-24

## Scope
Read-only inspection of PRSPRO2-PRODUCTION frontend modules: project-detail, project-images, project-statuses, project-workflow-settings, print-designer, admin, settings, dashboard, dashboard-redesign and workflow-department-assignments.

## Selected route literals (E1)
- Project detail: /events, /attachments, /projects.
- Project images: /projects, /attachments.
- Project statuses: /project-statuses.
- Project workflow settings: no literal route captured in simple static extraction; likely dynamically composed, not inferred.
- Print designer: /print-templates, /print-templates/revision, /print-templates/sources, /projects, /print-templates/preview, /attachments, /print-templates/resolve.
- Admin: /admin/sessions, /admin/sessions/:id, /projects, /admin/projects.
- Settings: /workflows.
- Dashboard modules: no static API route literals captured in simple extraction for dashboard.js; dashboard-redesign captures /procurement/needs and /attachments.
- Workflow department assignments: /workflows/:id/... route family.

## Implications and limitations
This broadens the frontend inventory into project dossier/history, gallery, status, print-template lifecycle, admin session/project operations, settings and workflow department assignment. Dynamic strings and API wrapper abstractions make literal extraction incomplete. Each endpoint needs backend guard, service, tenant predicate, write/read effects, replay/failure and tests crosswalk.

## Evidence and safety
E1 static source. No runtime tests, production modifications, data, SQL/DDL, migration, provisioning or deployment. G-01/G-10 OPEN; schema readiness NOT APPROVED.
