# Task 2.8 — Dynamic Router Path Pattern Inventory Increment 31
Date: 2026-11-22
Scope: Read-only extraction of dynamic/prefix dispatch patterns from Router.js; patterns are method-dispatch inventory, not exhaustive endpoint tracing.

## GET-oriented pattern families surfaced
- `/admin/projects/{id}/deletion-preview`
- `/departments/{...}`
- `/projects/{id}`; `/projects/{id}/information`; `/projects/{id}/information/{typeId}`; history; image-gallery related paths; generic project subpaths
- `/workflows/{...}`
- `/print-templates/{id}/images/{imageId}`; PDF
- `/forms/{...}`; `/information-types/{...}`; `/event-types/{...}`; `/events/{...}`; `/attachments/{...}`
- `/users/{...}/roles`; `/roles/{...}`
- `/audit/entity/{type}/{id}`; `/audit/user/{id}`
- `/reports/project/{id}`

## POST-oriented pattern families surfaced
- print-template image/PDF uploads;
- project material-specification create and reference-copy;
- workflow operations and event-rule operations;
- information-type columns and project reference conflict resolution/approval/save;
- form fields, event-type form assignment and procurement configuration;
- event form amendment and event actions;
- project complete-step and project mutations;
- user role assignment, role permission bulk operations;
- project-status, role and department mutation families;
- material-specification update and project update.

## PUT/PATCH/DELETE-oriented families surfaced
- information-type and column update/delete;
- material specification update;
- project update and central information delete;
- workflow/event-type/form/field mutations;
- event-form response amendment;
- user and role operations;
- project deletion under admin route;
- admin session revocation;
- attachment mutations;
- project reference resolution and role/department/status management.

## Coverage caveat
Pattern extraction does not enumerate all path/method combinations, payload shapes, route-level permission checks or service-level validation. Some generic prefix handlers dispatch several subroutes. These must be individually traced before this inventory can become a complete route matrix.

## Closure use
Combine increments 30–31 into a method/path/permission/service/persistence/test matrix. Prioritize destructive/admin operations and cross-tenant polymorphic paths. Explicitly mark incomplete traces and avoid treating this inventory as coverage certification.

## Limits
Read-only Router source review; no tests executed. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production modifications.
