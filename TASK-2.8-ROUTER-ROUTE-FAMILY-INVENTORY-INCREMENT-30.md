# Task 2.8 — Router Route-Family Inventory Increment 30
Date: 2026-11-22
Scope: Read-only extraction of literal route family dispatch points from backend Router.js. This is a dispatch inventory, not an exhaustive endpoint/security audit; dynamic routes and method-specific differences require separate crosswalk.

## Route families observed (GET and POST literals; duplicated where method-specific)
- Auth/session: `/auth/login`, `/auth/logout`, `/auth/me`, `/auth/me/permissions`, `/admin/sessions`.
- Projects: `/projects`, `/project-statuses`, `/project-form/lookups`, `/project-dashboard/lookups`, `/project-card-images`, `/project-image-gallery/counts`.
- Workflow: `/workflows`, `/workflow-management/bootstrap`.
- Departments: `/departments`.
- Print templates: `/print-templates`, `/print-templates/revision`, `/print-templates/sources`, `/print-templates/resolve`, `/print-templates/preview`.
- Forms/information: `/forms`, `/information-types`.
- Event: `/event-types`, `/events`.
- Attachment: `/attachments`.
- User/RBAC: `/users`, `/roles`, `/roles/available-permissions`, `/permissions`.
- Audit: `/audit`.
- Procurement: `/procurement/workspace`, `/procurement/reports`, `/procurement/dashboard`, `/procurement/needs`, `/procurement/orders`, `/procurement/receipts`, `/procurement/receipts/batch`.
- Reporting: `/reports/projects`, `/dashboard`.

## Dynamic route families require additional trace
Examples surfaced elsewhere in Router.js include parameterized project, event, workflow, role, form, information, attachment, audit entity/user, report project, material-specification, reference-copy, project deletion, event workflow progress/complete-step and form response/amendment paths. Literal extraction does not fully enumerate dynamic patterns, their methods, permissions, or payload validators.

## Coverage caveat
The route inventory is drawn from literal dispatch points and is useful for creating a critical route matrix. It does not certify every dynamic path, authorization check, frontend call, method dispatch, service side-effect or tenant guard. No route tests executed.

## Closure step
Use this inventory as a checklist to produce a method + path + permission + service + tenant scope + mutation side effects + test assertion matrix. Mark route families as traced, partially traced or pending; do not infer completeness from this inventory.

## Limits
Read-only Router source review. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests, live data, DDL, migration, provisioning, deployment or production modifications.
