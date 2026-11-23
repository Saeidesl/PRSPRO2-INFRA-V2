# Task 2.8 Increment 140 — Critical Workflow/Procurement/Admin route-to-evidence crosswalk

Date: 2026-11-23  
Evidence: E1/E2 source crosswalk; tests not run.

## Workflow route matrix
| Route family | Permission observed | Tenant/service/persistence evidence | Test-source evidence | Status |
|---|---|---|---|---|
| GET /workflows | WORKFLOW_MANAGE, PROJECT_CREATE or SYSTEM_SETTINGS | workflow tenant list and project-creation read exception | workflow tests inventoried | PARTIAL |
| GET workflow steps | WORKFLOW_MANAGE or PROJECT_VIEW | workflow/version step projection | workflow tests inventoried | PARTIAL |
| GET workflow management/rules/assignments | WORKFLOW_MANAGE | tenant-scoped workflow, step/department validation | rule/assignment tests inspected/inventoried | PARTIAL |
| POST workflow/create-step/publish/assign | WORKFLOW_MANAGE | service validation/version clone/publish/assignment; sequential mutation | test source inspected, not run | PARTIAL |
| PUT workflow/rules | WORKFLOW_MANAGE | draft version and step/event rule validation; source concerns previously documented | version/rule tests inventoried | PARTIAL |
| DELETE department-step association | WORKFLOW_MANAGE | relationship check; soft status transition | limited source/test coverage | PARTIAL |

## Procurement route matrix
| Route family | Permission observed | Service/persistence evidence | Test-source evidence | Status |
|---|---|---|---|---|
| GET workspace/needs/orders/dashboard | PROCUREMENT_VIEW or PROCUREMENT_MANAGE | tenant/project visibility and derived allocation/receipt projections | procurement tests inventoried | PARTIAL |
| GET procurement reports | REPORT_VIEW or procurement permission | visibility-aware aggregation | report/procurement tests inventoried | PARTIAL |
| POST order | PROCUREMENT_MANAGE | idempotency, allocations/surplus validation; header/items/allocations sequential and partial NEEDS_REVIEW | order tests inventoried | PARTIAL |
| POST receipt single/batch | PROCUREMENT_MANAGE | order/item/tenant/project/allocation limits, replay, sequential receipt/status/audit | receipt tests inventoried | PARTIAL |
| GET project material specs/calculations | PROJECT_VIEW or PROJECT_EDIT | project scope and calculation/spec projections | material tests inventoried | PARTIAL |
| POST/PUT material specs | PROJECT_EDIT (draft creator exception for create) | validation/revision/calc lineage sequential | material tests source inspected/inventoried | PARTIAL |

## Administration/configuration route matrix (bounded)
| Route family | Permission observed | Evidence | Status |
|---|---|---|---|
| Users list/create/update/roles | USER_VIEW/CREATE/EDIT | tenant user and role mutation source sampled | PARTIAL |
| Roles/permissions | ROLE_MANAGE, USER_EDIT read alternatives, PERMISSION_MANAGE | tenant-role links and permission resolution source sampled | PARTIAL |
| Departments/statuses | DEPARTMENT_MANAGE, PROJECT_STATUS_MANAGE plus read alternatives | tenant validation, code checks, status transitions | PARTIAL |
| Event types/forms/info types | EVENT_TYPE_MANAGE/SYSTEM_SETTINGS/EVENT_CREATE alternatives | tenant reference validation, revision/mapping/deactivation paths | PARTIAL |
| Audit | REPORT_VIEW | tenant filtering and entity/user query | PARTIAL |
| Admin sessions/project delete | Admin role | tenant/session/project dependency checks and confirmation | PARTIAL |
| Print templates | SYSTEM_SETTINGS/PROJECT_VIEW (read), SYSTEM_SETTINGS (mutation) | tenant/versioned template and binding/source permission checks | PARTIAL |

## Remaining crosswalk gaps
- Exact route payload schemas, nested branch combinations, caller graph and service invariant coverage are not exhaustive.
- Permission alternatives and admin exceptions require negative tests for each role/tenant/resource combination.
- Test sources may be static contract assertions; runtime tests, concurrency, transaction and recovery remain unverified.

## V2 implications
- Build a route manifest with method/path, auth policy, tenant predicate, DTO, service invariants, transaction, idempotency and tests.
- Centralize permission policy and constrain resource queries by tenant/project before projection.
- Test workflow publish/rule/version mutation, procurement allocations/receipts/replay, role/configuration mutations and admin operations under fault/concurrency/tenant-isolation conditions.

## Limits
- Bounded crosswalk; G-01 remains open. E1/E2 only; no runtime tests or production changes; G-10 remains blocker; schema readiness NOT APPROVED.
