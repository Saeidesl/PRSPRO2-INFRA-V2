# Task 2.8 — Route Dispatch & Caller Method Evidence Increment 9
Date: 2026-11-22

## Scope and safety
Bounded read-only source comparison of legacy frontend API wrapper and backend Router.js. Production repositories were not changed; no API calls were executed against production, and no tests/live data/schema/migrations/deployment were used.

## Verified method dispatch model
The frontend API wrapper exposes request methods mapping directly to HTTP verbs: Api.get -> GET, post -> POST, put -> PUT, delete -> DELETE. GET requests are deduplicated by request-key logic in the wrapper; this is client behavior, not server transaction/idempotency evidence.

The backend Router.js has distinct get(path,params), post(path,body,params), put(path,body,params), and delete(path,params) branches. The sampled endpoint path should be reconciled against the relevant verb-specific branch, not path-string presence alone.

## Workflow-progress discrepancy resolved (bounded)
The path `/projects/{projectId}/workflow-progress` is present as a dispatch branch within Router.get's project-prefix handling: `parts[3] === 'workflow-progress'` calls `EventService.workflowProgress(projectId, params)`. This resolves the earlier literal-string discrepancy: increment 8's targeted literal-search output was incomplete/negative, not evidence of a missing endpoint.

The enclosing GET project-prefix branch first requires PROJECT_VIEW (except specific material planning path branches); workflow-progress then returns EventService.workflowProgress. This is source-level dispatch and authorization evidence only. Service-level tenant isolation, exact output contract, caller payload expectations and tests are not fully validated here.

## Sampled caller/dispatch reconciliation
- Frontend API wrapper: verb helpers dispatch to request('GET'/'POST'/'PUT'/'DELETE'); observed in source.
- Project workflow-progress: GET dispatch branch exists, with PROJECT_VIEW gate in enclosing project branch.
- Procurement workspace and reports: Router GET branches invoke procurement diagnostic wrapper and permission gate, then service method.
- Procurement order create and receipts/batch/single: POST branches enforce PROCUREMENT_MANAGE and call ProcurementService.
- Project creation/update: POST/PUT branches and permission gates observed; draft-specific fallback checks are source-visible.
- Event/form response update: PUT branch for /events/{id}/forms/{formId} uses EVENT_EDIT; events void branch uses EVENT_EDIT.
- Form/field/event-type definition management: sampled PUT/POST/DELETE branches use SYSTEM_SETTINGS or EVENT_TYPE_MANAGE gates, with some explicit alternative gate behavior.
- Attachment download/list: GET routes use ATTACHMENT_VIEW; DELETE uses ATTACHMENT_DELETE.

## Limits and findings
- This is a targeted source reconciliation, not an exhaustive inventory of every frontend caller, HTTP route, handler, permission, tenant predicate or test.
- Router permission gates do not establish complete service/repository tenant-scoping correctness.
- No production endpoints were executed and no tests were run.
- G-01 remains OPEN pending comprehensive disposition of all source surfaces and test coverage; G-10 and other blocker status are unchanged. Schema readiness remains NOT APPROVED.
