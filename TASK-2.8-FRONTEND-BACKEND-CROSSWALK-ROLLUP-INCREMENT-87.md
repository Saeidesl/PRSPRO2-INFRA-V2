# TASK-2.8-FRONTEND-BACKEND-CROSSWALK-ROLLUP-INCREMENT-87

Date: 2026-11-23
Evidence level: E1 source inspection only
Reference repos: PRSPRO2-PRODUCTION and PRSPRO2BACKEND-PRODUCTION (read-only)
No tests, production writes, migration or deployment.

## Consolidated caller families observed
- Auth/session/permission lookup: auth, core router/session/permissions.
- Project/dashboard/project status/images/detail: projects, project-images, project-detail, reports, admin.
- Events/EventData/forms/attachments: events, forms, attachments, project detail, event types.
- Workflow and event-type configuration: workflows, event-types, settings.
- Information types/central project information: information-types, projects, forms.
- Material planning: material-planning.
- Procurement projections/integration: events, project-detail, reports (no standalone procurement JS asset in sampled index).
- Administrative configuration: users, roles, departments, project-statuses, permissions, audit, admin.
- Reports and print designer: reports and print-designer.

## Backend counterpart sampled
- Router route families and service authorization excerpts for project information, EventData/form responses, attachments, procurement, workflow, event types, users/roles/permissions and admin.
- Source excerpts for service/repository behavior in projects, events, forms, project information, material planning, procurement, deletion, attachments and audit.

## Crosswalk status
- Endpoint-family coverage is useful but not a fully exhaustive route-to-caller-to-service-to-repository matrix. Literal regex extraction misses dynamic request builders, path branches and some API calls.
- Permission enforcement is source-sampled, not fully reconciled or runtime-negative-tested.
- Production test sources were inspected in earlier increment 71 but were not executed.
- The current evidence cannot support marking G-01 route/dependency coverage complete, G-10 tenant integrity proven, or any transaction/recovery gap closed.

## Recommended V2 deliverable
Generate and maintain a route registry (OpenAPI or equivalent) with endpoint ID, HTTP method, request/response schema, permission, tenant scope, handler/service, data dependencies, idempotency and test references. Validate it with contract tests, authorization-negative tests and dependency coverage.

No implementation or production change. E1 only; G-01/G-10 blockers remain; schema readiness NOT APPROVED.
