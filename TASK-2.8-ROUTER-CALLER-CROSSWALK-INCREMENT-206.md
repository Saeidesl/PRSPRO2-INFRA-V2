# Task 2.8 Increment 206 — Backend router and frontend route/API crosswalk (preliminary)

Date: 2026-11-24

## Source snapshots fetched (read-only)
- Backend Router.js blob SHA: `f2542a17993b852c7dd2968a8fefe57f70b991f3`
- Frontend `js/core/api.js`: `51a68b72f57696a83b4f3b5afb439afede541641`
- Frontend `js/core/router.js`: `2aa3bec548fc424bc35815a9b53ec7e5f51e9487`
- Frontend `js/core/permissions.js`: `8fe1b343e73b505573e4cce5bdaae114928b63f6`
- Backend `PermissionService.js`: `15a33c07e50a409e69e938418c0e4b2d4f73977b`
- Backend `AccessControlService.js`: `4672948ff7246e56c1493996df6317281fc73b1d`

## Router static route inventory (not exhaustive behavioral mapping)
Extracted from backend Router.js literals and route patterns:
- Exact path dispatch literals identified (38 distinct): `/auth/me`, `/auth/me/permissions`, `/admin/sessions`, `/project-statuses`, `/departments`, `/project-image-gallery/counts`, `/project-card-images`, `/project-form/lookups`, `/projects`, `/workflow-management/bootstrap`, `/workflows`, `/print-templates/revision`, `/print-templates`, `/print-templates/sources`, `/forms`, `/information-types`, `/event-types`, `/events`, `/attachments`, `/users`, `/roles/available-permissions`, `/roles`, `/permissions`, `/audit`, `/project-dashboard/lookups`, `/procurement/workspace`, `/procurement/reports`, `/procurement/dashboard`, `/procurement/needs`, `/procurement/orders`, `/reports/projects`, `/dashboard`, `/auth/login`, `/print-templates/resolve`, `/print-templates/preview`, `/auth/logout`, `/procurement/receipts/batch`, `/procurement/receipts`.
- Prefix dispatch families: `/departments/`, `/projects/`, `/workflows/`, `/forms/`, `/information-types/`, `/event-types/`, `/events/`, `/attachments/`, `/users/`, `/roles/`, `/audit/entity/`, `/audit/user/`, `/reports/project/`, `/project-statuses/`, `/form-fields/`, `/event-type-forms/`.
- Parameterized regex families identified include project deletion preview, project detail/information history/item/list, print-template images/PDF, project material specifications, reference-copy, information-type columns, reference resolution and approval, event-type procurement, complete-step, workflow event-rules, event form access, material-specifications, and admin project/session paths.

This extracted inventory is a navigation baseline, not an exhaustive list of route operations; GET/POST/PUT/DELETE branches, validation, permission guards, mutation side effects and caller mapping still need explicit reconciliation.

## Frontend route-level permissions (observed in router.js)
Pages and client permission gates include dashboard/report view, projects/project view/create, project detail, print designer/system settings, events event view/create, workflows, project-statuses, event-types, information-types/system settings, forms/system settings, procurement view/manage, attachments, reports, users, departments, roles, permissions, audit/report view, settings, admin. Client gates are not security proof; backend guards are authoritative.

## API/auth behavior observations
- API module requires explicit `window.PMS_API_BASE_URL` and deliberately does not fall back to a development/staging deployment.
- Frontend Router loads permission data from `/auth/me/permissions`, and server response is authoritative for admin status.
- Backend router has `requireAny` and AccessControlService.require guard paths; actual coverage, exception paths and service-level tenant scoping need exhaustive trace.
- Draft-project exception in project detail path is explicitly tenant, DRAFT state, creator and PROJECT_CREATE checked when PROJECT_VIEW fails (source trace observation; negative tests not executed).

## Coverage gap classification
- G-01: route family inventory advances, but all operation-method dispatch branches and file/caller dependencies remain to be mapped.
- G-10: guards and tenant predicates require route-by-route negative test proof; UI gates alone are insufficient.
- G-03/G-04: route paths involving event/form amendments and attachments need lifecycle downstream-effects crosswalk.
- Existing test-file names inventoried; test assertions and execution are separate evidence.

## Next
Continue route-by-route through backend Router method dispatch and domain handlers, mapping each operation to frontend caller, permission, tenant check, input validator, side effects, audit/history, notification/projection, tests and target-domain decision. No production changes or tests.

## Safety
Production source retrieval only, no edits. No SQL/DDL, migration, live data, provisioning, VPS or deployment. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED.
