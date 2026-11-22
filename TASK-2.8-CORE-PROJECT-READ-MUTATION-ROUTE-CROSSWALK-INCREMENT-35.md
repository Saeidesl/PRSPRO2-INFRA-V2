# Task 2.8 — Core GET & Mutation Route Crosswalk Increment 35
Date: 2026-11-22
Scope: Read-only route/service/permission crosswalk for core project reads and selected mutations from Router.js.

## GET routes traced (bounded)
- `/projects`: PROJECT_VIEW or PROJECT_CREATE → ProjectService.list.
- `/projects/{id}`: PROJECT_VIEW; fallback allows creator of same-tenant DRAFT with PROJECT_CREATE → ProjectService.get.
- `/project-form/lookups`: PROJECT_CREATE.
- project image gallery paths: PROJECT_VIEW or EVENT_VIEW plus ATTACHMENT_VIEW.
- `/project-card-images`: PROJECT_VIEW plus ATTACHMENT_VIEW.
- `/project-image-gallery/counts`: PROJECT_VIEW or EVENT_VIEW plus ATTACHMENT_VIEW.
- `/project-statuses`: PROJECT_STATUS_MANAGE.
- print template resolve/preview reads: PROJECT_VIEW; some template bindings path requires separate dispatch inspection.

## Mutation routes traced (bounded)
- project-status creation/update: PROJECT_STATUS_MANAGE.
- department create/update: DEPARTMENT_MANAGE.
- role create/update: ROLE_MANAGE; permission bulk/remove: ROLE_MANAGE.
- project update: PROJECT_EDIT, with same-tenant own-DRAFT PROJECT_CREATE fallback.
- material specification create: PROJECT_EDIT with same-tenant creator-DRAFT PROJECT_CREATE fallback; revise specification: PROJECT_EDIT.
- project reference-copy: route requires separate deeper method trace; service policy/permission tests sampled in Increment 26.
- information type/column create/update/deactivate: SYSTEM_SETTINGS.
- print-template create/update/upload image/upload PDF/deactivate: SYSTEM_SETTINGS; update project binding handler does not show an explicit gate in the sampled branch and requires follow-up validation (potential route-level gate gap, not proof of unauthorized access because service may guard).
- workflow step department assignment removal: WORKFLOW_MANAGE and relationship validated to workflow ID.
- form field update/remove and form definition update: SYSTEM_SETTINGS.
- event-type form unassign: EVENT_TYPE_MANAGE or SYSTEM_SETTINGS.
- attachment delete: ATTACHMENT_DELETE.

## Specific potential gap requiring follow-up
`PUT /print-templates/{id}/project-data` dispatches `PrintTemplateService.updateProjectBinding` in the sampled Router branch without a visible explicit Router permission requirement. Service authorization may exist; this is flagged as an evidence gap, not a vulnerability conclusion. Trace service next.

## Limits
Route trace is bounded and not exhaustive for every method/route. Test assertions not executed; no runtime verification. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production changes.
