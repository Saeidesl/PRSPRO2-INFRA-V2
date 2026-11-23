# Task 2.8 Increment 202 — Coverage gap closure plan and evidence exit criteria

Date: 2026-11-23
Status: evidence-management artifact; no implementation or test execution.

## Why this increment
Increment 201 maps source families to domains but cannot establish exhaustive coverage while production source retrieval, exact caller inventory and runtime evidence are incomplete. This plan converts the gaps into finite evidence work packages and exit criteria without repeating already documented source traces.

## Evidence work packages

| ID | Evidence package | Inputs already available | Missing evidence | Exit criterion | Blocker |
|---|---|---|---|---|---|
| EP-01 | Reproducible production source-tree inventory | bounded source traces, route inventories and prior audit documents | authenticated read of complete frontend/backend production trees, branch/commit identifiers, file manifest | immutable manifest for reviewed refs, hashes/commit refs, all files categorized | connector/API retrieval failure |
| EP-02 | Router branch-to-operation register | inspected Router GET/POST/PUT/DELETE branches; increment 148 | exhaustive nested branches, method override, exact handler signatures and DTOs, all branches crosschecked against tree | each branch maps to operation, auth predicate, handler, payload, effects, callers, tests or disposition | EP-01 |
| EP-03 | Service mutation/effect ledger | sampled services, Project/Event/Procurement/Attachment paths | all mutation methods, secondary writes, compensation/recovery, transaction boundary | complete operation ledger per service; each effect/recovery documented | EP-01/02 |
| EP-04 | Frontend caller/API contract register | 26 module inventory; route crosswalk; known event-field mismatch candidate | every request call site, payload/response assumptions, permissions, retry/cache behavior, caller disposition | exact caller-to-endpoint map and unresolved mismatch list | EP-01 |
| EP-05 | Tenant/security verification matrix | route/service checks sampled; RBAC design notes | full per-operation authorization predicate and tenant isolation negatives; polymorphic access boundaries | positive/negative matrix reviewed and test scenarios defined; actual tests later in QA phase | EP-01/02 |
| EP-06 | Persistence/relationship invariant catalog | sheet headers, repository relationship map, candidate constraints | exact headers and semantics, uniqueness/cardinality/null/delete rules, all polymorphic links, tenant/global scope | verified field/relationship manifest with confidence and constraint candidates | EP-01 |
| EP-07 | Lifecycle/correction/recovery catalog | increments 198–200 and bounded deletion/file traces | every state transition, reversal/amendment, idempotency, concurrency, partial failure, reconciliation | lifecycle matrix for all mutations; PO decisions separated | EP-01/03 |
| EP-08 | Report/print/notification consumer trace | sampled reports/templates/Telegram source | all consumer queries, report semantics, delivery and retry paths | consumer list and effects reconciled to source manifest | EP-01/04 |
| EP-09 | Legacy test/CI evidence mapping | file inventory 41 backend/46 frontend, workflows | test-to-operation mapping, actual execution, test environment and sanitized fixtures | map each operation to test cases; run only in isolated V2/legacy test setup after approved access | EP-01/02; tests not authorized/executed in audit |
| EP-10 | G-10 tenant/global and sharing decisions | candidate relationships | PO classification, complete resource list, sharing policy and cross-tenant tests | classification accepted and constraints/test plan established | EP-01/06 and PO |
| EP-11 | G-02 procurement policies | increment 198/200 | PO decisions for receipt, stock, allocations, reversal, batch and NEEDS_REVIEW | all business policy choices explicitly accepted | PO |
| EP-12 | G-03 history/form policies | increment 199/200 | amendment scopes, void policy, report state semantics, impact catalog | all business policy choices explicitly accepted | PO |
| EP-13 | G-04 attachment/storage lifecycle | increment 134/194/201 | exhaustive owner types, retention, restore, purge, object reconciliation and scan decision | owner/lifecycle contract and recovery evidence | EP-01/03 and PO |

## Sequence and dependencies
1. EP-01 source access and exact version inventory is the prerequisite for claims of exhaustiveness. Use a reproducible retrieval method; stop repeated retries on a single failing connector path and document alternatives.
2. EP-02 and EP-04 can proceed in parallel only after stable source manifest is available; reconcile route-handler and frontend caller maps.
3. EP-03, EP-06 and EP-07 derive complete mutation/relationship/lifecycle contracts from exact source evidence.
4. EP-05 and EP-08 derive from operation and caller maps.
5. EP-10–13 require evidence packages plus PO decisions; avoid asking PO prematurely.
6. G-01 closure requires EP-01–09 substantially closed with uncovered paths explicitly dispositioned; G-10/G-02/G-03/G-04 remain their own gates.

## Practical fallback if source access remains blocked
- Keep bounded source-audit artifacts labeled E1/E2, never claim exhaustiveness.
- Attempt only supported retrieval channels once per path family; record error and date.
- Continue synthesis of the accessible source evidence and define test plans without execution.
- Request only the minimum necessary access action if it is a hard blocker, not routine work.

## Status
This plan does not close any gate. G-01/G-02/G-03/G-04/G-10 OPEN. Schema readiness NOT APPROVED. No tests, DDL, migration, live data, provisioning, VPS/deployment or production modification.
