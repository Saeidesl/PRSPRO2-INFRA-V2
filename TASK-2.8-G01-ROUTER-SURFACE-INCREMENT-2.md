# Task 2.8 — G-01 Router Surface Reconciliation (Increment 2)

Date: 2026-11-22
Status: Evidence increment; G-01 remains OPEN. READ-ONLY.

## Evidence
- Full Backend `Router.js` fetched from `PRSPRO2BACKEND-PRODUCTION`.
- Frontend `js/core/router.js` and `js/core/api.js` fetched from `PRSPRO2-PRODUCTION`.
- No source changes in either legacy repository.

## Backend GET surface families confirmed
Auth/session; departments; project statuses; project gallery/card image; project form lookup; projects and project information/history; project events/workflow progress/material specs/calculations/details/attachments; workflow management, steps, rules and department steps; print templates/revision/sources/images/PDF; forms/event-type links; information types/columns; event types and procurement integration; events/form responses/event data/attachments; users and role assignments; roles and permission mappings; permissions; audit/entity/user; project dashboard lookups; procurement workspace/reports/dashboard/needs/orders; project reports and dashboard.

## POST surface families confirmed
Login/logout; print-template create/resolve/preview; session/admin support paths; project-status/departments/roles/users; role-permission bulk and single assignment; procurement orders and receipt single/batch; project material specifications and reference copy; projects; workflow create/publish/steps/department assignments; information-type/columns and project information resolve/approve/save; forms/fields/event-type assignment; event-type and procurement integration configuration; events and attachments; workflow step completion; user role assignment.

## PUT surface families confirmed
Project status, roles, departments; information type and columns; print-template project binding and template edit; material specification revision; project update; workflow rules/configuration; forms/form fields; event types; event-form response amendment; event void; user update.

## DELETE surface families confirmed
Project information deactivation; information-type columns/types deactivation; print-template deactivation; administrator project removal; admin session revocation; role permissions and user-role associations; workflow department-step assignment removal; form-field and event-type-form association removal; attachment deletion.

## Frontend route registry and authorization notes
- Frontend page registry and route-permission map were read.
- Registry includes login, dashboard, projects/detail, print designer/output, events, workflows, statuses, event types, information types, forms, procurement, attachments, reports, users, departments, roles, permissions, audit, settings and admin.
- Client-side route access is permission-derived and is not a substitute for backend authorization.
- The API wrapper establishes request path/method dispatch and token-based request handling; this increment did not extract all caller references from module/page files.

## Important limits and reconciliation gaps
This is a route-family inventory, not a canonical exhaustive route table. Pattern branches and dynamic segments require systematic enumeration; exact payload contracts, handler operation mapping, route-specific permission evidence, callers, side effects, and tests remain unpaired in many cases. PUT print-template project-data binding appears as a special route branch; its explicit permission gate was not established in this bounded pass and must be rechecked in context rather than presumed. GET route permission exceptions (e.g., draft-owner project access and multi-permission routes) need operation-level matrix.

## Follow-up evidence required
1. Extract exact route patterns and handler names per HTTP method.
2. Cross-check every route against the responsible service operation.
3. Record required permissions, exceptions and tenant-scope checks.
4. Find frontend call sites and tests for each route.
5. Identify orphan routes/callers, mutation gaps and side effects.
No tests/runtime execution, SQL/DDL, migrations, live-data work, provisioning, deployment or production writes.
