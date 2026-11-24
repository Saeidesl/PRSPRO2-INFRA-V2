# Task 2.8 — Route-to-Mutation Assurance Reconciliation Protocol (Increment 268)

## G-01 closure protocol
Route inventory is not closed until each route/operation has a disposition row with all fields:
- HTTP method, canonical path, method-override aliases, router branch and dispatch order.
- Frontend caller(s), including dynamic URL construction, alternate UI paths and non-UI API callers.
- Authentication and route permission.
- Resource-level visibility, tenant predicate, creator/department/admin exception.
- Input schema/validator and normalization.
- Service method and helper/repository call chain.
- Writes: direct and secondary (project state, event/form history, attachments, procurement, workflow, material planning).
- Lock/transaction/idempotency behavior.
- Audit and external side-effect behavior.
- Tests: positive, negative permission, cross-tenant, validation, concurrency, failure injection and replay.
- Status/edge conditions (ACTIVE/VOID/DELETED/legacy/null).
- Evidence level, source hash/commit, review date and disposition (verified, candidate, policy pending, inaccessible, runtime pending).

## Required dispositions
- VERIFIED SOURCE: exact branch and service trace.
- TESTED: exact test executed against isolated environment, results and commit captured.
- POLICY PENDING: behavior cannot be finalized without PO choice.
- SOURCE INACCESSIBLE: bounded blocker, no guessed behavior.
- RUNTIME PENDING: source trace cannot prove platform/deployment behavior.
- NOT APPLICABLE: reason documented and reviewer signoff.
No route is closed based on inventory presence alone.

## Prioritization
1. Mutating operations with external side effects or multi-write sequences.
2. Tenant/permission/resource boundaries and method override.
3. Status transitions, idempotency, replay, retries and failure recovery.
4. Read routes exposing project/event/form/attachment/audit data.
5. Setup/administrative routes and global catalogs.

## Closure evidence bundle
- Commit-pinned source manifest.
- Route/caller/service/repository/test crosswalk.
- Isolated test results (including negative tests).
- Mutation/failure/replay/concurrency results.
- Decisions for policy gates and global/tenant classifications.
- Residual risks and approved dispositions.

## Verification
Protocol only; no route/test closure implied. G-01 remains OPEN.
