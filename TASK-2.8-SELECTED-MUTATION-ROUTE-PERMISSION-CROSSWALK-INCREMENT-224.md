# Task 2.8 Increment 224 — Mutation Router authorization crosswalk (selected paths)

Date: 2026-11-24

## Scope
Re-read Router.js and crosschecked selected service mutation calls against Router dispatch/permission guards. Read-only.

## E1 observed route/service/permission crosswalk
| Route pattern | Router guard | Service target | Status |
|---|---|---|---|
| POST /events | EVENT_CREATE | EventService.create | E1 observed |
| POST /projects/:id/complete-step | branch reviewed in source | EventService.completeStep | partial; branch guard/method context further crosscheck required |
| POST /workflows/:id/publish | WORKFLOW_MANAGE | WorkflowService.publish | E1 observed |
| PUT /projects/:id/information/:typeId | PROJECT_EDIT | ProjectInformationService.saveWithLock | E1 observed |
| GET /projects/:id/information/:typeId/history | PROJECT_VIEW | ProjectInformationService.history | E1 observed |
| POST /projects/:id/information/:typeId/approve | PROJECT_EDIT | ProjectInformationService.approve | E1 observed |
| POST /projects/:id/information/:typeId/resolve-reference | PROJECT_EDIT | ProjectInformationService.resolveReferenceConflict | E1 observed |
| DELETE /projects/:id/information/:typeId | PROJECT_EDIT | ProjectInformationService.deactivate | E1 observed |
| PUT /events/:id/forms/:formId | EVENT_EDIT | FormService.updateEventFormResponse | E1 observed |
| GET /events/:id/forms | EVENT_VIEW | FormService.getEventResponses | E1 observed |

## Route guard vs service validation observations
- Router guards were observed for the selected dispatch branches above; service methods independently validate current user, tenant, entity relationships and revision/visibility where applicable.
- These selected branches do not establish exhaustive Router coverage or that every alternate dispatch/HTTP method/caller is represented.
- The event amendment form path uses EVENT_EDIT at Router; service-level checks tie the amendment event to the same tenant and project and examine configured/legacy amendment eligibility.
- Project Information routes use PROJECT_EDIT for mutation/approval/deactivation and PROJECT_VIEW for history/read; service-level tenant/project/type checks are observed.
- Workflow publish is guarded by WORKFLOW_MANAGE and then service tenant/step/rule validations.

## Gap classification
E1 source crosswalk only. Do not infer exploitability or full permission parity. Exhaustive path/caller/negative tests, method override behavior, tenant isolation, race/fault recovery and deployed-version equivalence remain unverified. G-01/G-03/G-10 OPEN; schema readiness NOT APPROVED.

## Safety
No tests, runtime execution, production changes, SQL/DDL, migration, provisioning or deployment.
