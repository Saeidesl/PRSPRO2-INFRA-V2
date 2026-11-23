# Task 2.8 — Router GET Dispatch Branch Inventory (Increment 65)

Date: 2026-11-23  
Reference: read-only inspection of PRSPRO2BACKEND-PRODUCTION Router.js (GET dispatcher only). This is a bounded branch inventory, not exhaustive route authorization certification.

## Branch families observed in inspected GET dispatcher
- Auth: /auth/me; /auth/me/permissions.
- Admin/session: project deletion preview; admin session list.
- Project status/departments: /project-statuses, /departments and department ID lookup.
- Project imagery: image-gallery counts, project-card-images, project image-gallery path.
- Project entry/list/detail: project-form/lookups; /projects list; project ID lookup; information/history; project events; workflow progress; material specifications/calculations; details page/detail; project attachments.
- Workflow: management/bootstrap; list; steps/event-rules; management-data; steps; department-steps; workflow ID lookup.
- Print templates: revision/list/sources/image/pdf/template detail.
- Forms/information types: list/detail, form-event-type associations, information-type columns.
- Event types: list/detail/forms/procurement integration.
- Events: list/detail/forms responses/event data/attachments.
- Attachments: list/detail/thumbnail/download.
- Users and role/permission surfaces: user list and user roles; roles/available-permissions and role list branches are observed in excerpt.
- Procurement and remaining route families continue beyond the inspected excerpt and require separate review; this inventory is deliberately partial.

## Permission patterns observed in the inspected branches
- Some branches use AccessControlService.require for a single permission.
- Several use Router.requireAny, allowing multiple permission codes or administrator override.
- Some project detail branches use a conditional draft-access fallback (tenant, draft state and creator checks) when PROJECT_VIEW fails, then require PROJECT_CREATE.
- Some permission checks are applied before branching into subpaths, while selected nested branches add a second permission check (e.g. event/project attachment listing).
- Router authorization alone is not proof of authorization at service/repository level or full tenant isolation.

## V2 implications
- Produce a generated route registry with explicit method, path pattern, authn requirement, permission expression, handler, tenant scope and request validation.
- Authorization should be tested for each route branch and nested resource path, including positive and negative role/tenant scenarios.
- Avoid broad fallback dispatch that unintentionally maps unknown nested paths to a parent resource; explicit 404 handling should be a standard.
- Keep authorization and visibility checks in domain services as defense in depth, and tenant-qualified constraints in PostgreSQL.

## Evidence and limitations
- Evidence is E1 (source observed) only.
- GET dispatcher review does not cover POST/PUT/PATCH/DELETE mutation routes, all nested branches, helper functions, handler/service behavior, or runtime behavior.
- No test was executed; no production repository was modified.
- G-01 remains open because full endpoint/mutation matrix coverage is not yet established.
