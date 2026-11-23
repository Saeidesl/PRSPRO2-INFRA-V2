# TASK-2.8-WORKFLOW-ROUTE-PERMISSION-CROSSCHECK-INCREMENT-99

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/Router.js SHA f2542a17993b852c7dd2968a8fefe57f70b991f3; WorkflowService.js SHA eb8373c14085975a9eb521d5ba20ec2e5146bfad
No runtime tests or production changes.

## Router crosscheck
- GET /workflow-management/bootstrap requires WORKFLOW_MANAGE.
- GET /workflows uses any of WORKFLOW_MANAGE, PROJECT_CREATE, SYSTEM_SETTINGS for read-only workflow choice list.
- GET /workflows/:id requires WORKFLOW_MANAGE except steps path allowing WORKFLOW_MANAGE or PROJECT_VIEW; step event rules and management data require WORKFLOW_MANAGE.
- POST /workflows/:id/publish and step creation appear under workflow route branch requiring WORKFLOW_MANAGE.
- POST /workflows/:id/steps/:stepId/event-rules requires WORKFLOW_MANAGE.
- PUT /workflows/:id update requires WORKFLOW_MANAGE.
- Assignment management routes require WORKFLOW_MANAGE.
- DELETE assignment router validates assignment ID/workflow relationship before service remove; service additionally tenant-checks item.

## Conclusion on Increment 98
Route permission gates the generic update mass-assignment concern behind WORKFLOW_MANAGE. This reduces public reachability but does not replace explicit field allowlisting, auditability, or safe lifecycle transitions. It is not classified as an external unauthenticated exploit.

## Remaining validation
- Actual effective permissions and role composition, tests, and bypass paths remain unknown.
- Route-level audit sampled; exhaustive Router coverage is still G-01 open.
- E1 source evidence only, no tests, production unchanged.
