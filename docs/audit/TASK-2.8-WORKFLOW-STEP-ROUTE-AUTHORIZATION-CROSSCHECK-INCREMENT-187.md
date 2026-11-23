# Task 2.8 — Workflow step API route authorization crosscheck — Increment 187

Date: 2026-11-23. Read-only Router.js trace.

## Observations
- GET /workflows/:id/steps uses requireAny(WORKFLOW_MANAGE, PROJECT_VIEW) then WorkflowService.getSteps.
- GET /workflows/:id/steps/:stepId/event-rules requires WORKFLOW_MANAGE.
- GET /workflows/:id/management-data requires WORKFLOW_MANAGE.
- GET /workflows/:id/department-steps and workflow detail require WORKFLOW_MANAGE.
- POST /workflows/:id/steps and POST /workflows/:id/publish require WORKFLOW_MANAGE at the encompassing route branch.
- POST workflow department assignments routes require WORKFLOW_MANAGE.
- Step creation API route is a nested route without explicit step-specific update/delete routes observed in this bounded excerpt.
- GET /workflows list uses requireAny(WORKFLOW_MANAGE, PROJECT_CREATE, SYSTEM_SETTINGS) (earlier crosscheck).
- Authorization source crosscheck only; negative runtime tests not performed. No conclusion on vulnerabilities.

## V2 implications
- Explicit API policy inventory and permission-negative tests, including project-view access to steps vs management-data and rule API.
- Distinct lifecycle API operations for step edit/deactivate/remove only if supported by validated business semantics.
- Maintain tenant checks in service and database; route gate is not the sole integrity layer.

## Status
G-01/G-10 remain open. Read-only audit.
