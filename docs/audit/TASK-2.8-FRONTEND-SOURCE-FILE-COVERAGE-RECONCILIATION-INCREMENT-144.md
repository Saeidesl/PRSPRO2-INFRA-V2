# Task 2.8 Increment 144 — Frontend source-file audit coverage reconciliation

Date: 2026-11-23  
Evidence: E1/E2 — source-tree comparison with caller audits and test inventory.

## Frontend module coverage map
| Module family | Audit evidence | Status |
|---|---|---|
| Core API/session/permissions/router/cache/app | 120 | Source trace; runtime and cache/security tests partial |
| Layout/modal/form/table/loading/alert/theme/sidebar | 122 | Shared component trace; browser/accessibility runtime partial |
| Projects/project detail/project images | 129 | Caller trace; full DTO/runtime partial |
| Events and event context/forms/amendments/attachments | 129, 133 | Caller trace; route mismatch candidate and runtime partial |
| Dashboard + dashboard redesign | 129, 137 | Caller trace; pagination/runtime partial |
| Material planning/procurement | 130 | Caller trace; full payload matrix/runtime partial |
| Workflow management/department assignments/settings preference | 130, 137 | Caller trace; mutation/caller runtime partial |
| Reports/print designer/output | 130 | Caller trace; local template policy and print/browser runtime partial |
| Users/roles/permissions/departments/project statuses/event types/forms/information types/attachments/audit/admin/settings | 131 | Caller inventory; exact DTO/permission/cache runtime partial |
| Auth module | 137 | Auth lifecycle trace; browser/credential/session runtime partial |

## Frontend tree coverage observations
- Source tree lists 26 frontend module files; all listed families are represented in the audit coverage map above, including helper modules with no API callers.
- Caller-path inventory and crosswalk increments 129–131/137 cover the main endpoint families, but exact per-call payload shape, response contract, error behavior, cache key/invalidation and caller-to-test mapping are not exhaustive.
- 46 frontend test files exist in the source tree; test inventory is in Increment 142. Tests were not executed.

## Remaining frontend G-01 work
1. Complete per-caller API DTO and response shape map for all modules and nested routes.
2. Reconcile each caller to Router branch, permission and tenant/resource check, service/persistence effect and test.
3. Validate module lifecycle cleanup (listeners, observers, request cancellation, stale refresh) and cache/session scoping.
4. Runtime browser testing: keyboard/accessibility, mobile, print, localStorage/IndexedDB failures, pagination, large datasets and cross-account cache isolation.
5. Resolve the `/event-types/{id}/fields` mismatch candidate only after deployment/runtime verification and approved fix process.

## Limits
- Inventory/crosswalk only; no browser tests, production changes or live data.
- E1/E2 only; G-01/G-10 remain blockers; schema readiness NOT APPROVED.
