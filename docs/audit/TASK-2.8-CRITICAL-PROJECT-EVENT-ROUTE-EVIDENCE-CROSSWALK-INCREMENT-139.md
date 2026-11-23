# Task 2.8 Increment 139 — Critical Project/Event route-to-evidence crosswalk (bounded)

Date: 2026-11-23  
Evidence: E1 Router/service/frontend source; E2 test-source assertions where inspected/inventoried. Tests not run.

## Project route matrix
| Route family | Router permission/exception | Service/tenant evidence | Persistence/lifecycle | Test evidence | Coverage status |
|---|---|---|---|---|---|
| GET /projects | PROJECT_VIEW or PROJECT_CREATE | project list filters/availability observed in ProjectService | read projection; no mutation | project picker/project tests inventoried | PARTIAL |
| GET /projects/{id} | PROJECT_VIEW; creator exception for own draft with PROJECT_CREATE | tenant + project visibility sampled | project detail projection | draft/project tests inventoried | PARTIAL |
| POST /projects | PROJECT_CREATE | create validates tenant/workflow/step/refs; draft path observed | project insert and repeat-copy orchestration sequential | single-project entry/draft tests inventoried | PARTIAL |
| PUT /projects/{id} | PROJECT_EDIT; own draft exception PROJECT_CREATE | project tenant/status/field validations sampled | sequential project update/sync/copy operations | project tests inventoried | PARTIAL |
| GET project information/history | PROJECT_VIEW | project/type/tenant validation | current values/history projection | information tests inventoried | PARTIAL |
| POST project information / approve / conflict resolve | PROJECT_EDIT | validation, expected revision, source/target checks observed | current revision/history/audit/sync sequential | information tests inventoried | PARTIAL |
| DELETE project information | PROJECT_EDIT | tenant/project/type validation | logical deactivate; history | no exhaustive route test map | PARTIAL |
| GET project events/details/workflow progress | PROJECT_VIEW and/or EVENT_VIEW | project visibility + event tenant checks sampled | read/pagination projections | event/detail tests inventoried | PARTIAL |
| GET material specs/calculations | PROJECT_VIEW or PROJECT_EDIT for path family | project scope validation in service | specification/calculation projections | material tests inventoried | PARTIAL |
| POST material spec | PROJECT_EDIT or own draft creator PROJECT_CREATE | project and tenant validations | revision/spec writes + audit sequential | material-planning tests inventoried | PARTIAL |
| POST reference copy | PROJECT_EDIT or own draft PROJECT_CREATE | source/target and tenant policies observed | multi-step reference copy with operation state | repeat-copy tests inventoried | PARTIAL |
| POST complete-step | EVENT_CREATE | workflow-step validation in EventService | event/step completion sequence | workflow enforcement tests inventoried | PARTIAL |
| DELETE /admin/projects/{id} | admin-only; confirmation code/project code | tenant/admin/dependency checks sampled | cascade/dependency soft-delete and physical project delete gating | project deletion tests inventoried | PARTIAL |

## Event route matrix
| Route family | Router permission | Service/tenant evidence | Persistence/lifecycle | Test evidence | Coverage status |
|---|---|---|---|---|---|
| GET /events | EVENT_VIEW | tenant/project visibility filtering | event list projection | event tests inventoried | PARTIAL |
| GET /events/{id} | EVENT_VIEW | event tenant/project checks | event detail projection | event tests inventoried | PARTIAL |
| GET /events/{id}/data | EVENT_VIEW | parent event and project visibility validated | JSON historical data read | event data tests limited/inventory | PARTIAL |
| GET /events/{id}/forms | EVENT_VIEW | event tenant/project and response visibility | response snapshots projection | forms tests inventoried | PARTIAL |
| POST /events | EVENT_CREATE | type/workflow/project/department/user/tenant validation | idempotency marker; event/form/procurement/project/audit sequential | idempotency/form tests source inspected | PARTIAL |
| PUT /events/{id}/forms/{formId} | EVENT_EDIT | amendment event/form/tenant checks and expected timestamps/replay | response/central-information sync sequential | amendment tests source inspected | PARTIAL |
| PUT /events/{id}/void | EVENT_EDIT | event type AllowVoid, department/admin and downstream guards sampled | snapshot restoration, status void, recalculation/audit sequential | void tests inventoried | PARTIAL |
| GET event attachments / upload / delete | ATTACHMENT_VIEW / UPLOAD / DELETE | parent visibility and tenant owner validation sampled | storage/metadata/audit sequential | attachment tests inventoried | PARTIAL |

## Key unresolved crosswalk items
- Route rows are sampled from Router; all nested path combinations, malformed IDs, method override, permission alternatives and resource-level negative tests not exhaustively mapped.
- Exact frontend caller payload schemas and service invariants are not tied to every route in this increment.
- Test filenames/source assertions do not prove runtime pass; no tests executed.
- Transaction boundary, FK/unique constraint and recovery mapping are still not complete for critical routes.

## V2 implications
1. API route manifest should include method/path, permission policy, tenant/resource predicate, DTO/schema, service invariant, transaction/persistence effect, idempotency and test ID.
2. Crosswalk should be generated/maintained with OpenAPI and requirements-to-test traceability.
3. Negative tests must cover tenant mismatch, hidden projects, role alternatives, draft exceptions, independent events, amendments and voids.
4. Database constraints and transactional operations must independently enforce critical invariants, not merely mirror Router checks.

## Limits
- Bounded source crosswalk, not complete closure of G-01.
- E1/E2 only; no runtime tests, live data or production changes. G-01/G-10 remain blockers; schema readiness NOT APPROVED.
