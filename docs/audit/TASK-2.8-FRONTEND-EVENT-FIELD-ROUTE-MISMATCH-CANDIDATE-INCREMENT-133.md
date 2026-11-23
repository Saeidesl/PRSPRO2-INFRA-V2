# Task 2.8 Increment 133 — Frontend caller vs Router mismatch candidate: event-type fields

Date: 2026-11-23  
Evidence: E1 — source crosswalk; runtime verification pending.

## Source evidence
- Frontend `js/modules/dashboard.js` (selected-project timeline) requests GET `/event-types/{EventTypeID}/fields`, caches the response and uses field definitions to label historical DynamicData values. Failure is intentionally caught and the timeline renders without field labels.
- Backend `Router.js` GET event-type nested route branch inspected in Increment 128 accepts `/event-types/{id}/forms` and `/event-types/{id}/procurement`; other nested parts are explicitly rejected as not found. The inspected branch does not dispatch `/fields`.
- `EventFieldRepository.js` is read-only and provides historical event-field definitions, but no corresponding `/event-types/{id}/fields` GET route was found in the inspected Router branch.

## Assessment
This is a credible frontend/backend route-contract mismatch candidate. In the dashboard selected-project timeline, dynamic field labels may not load and the UI silently falls back to field codes/keys. Runtime effect and actual deployment/version match are not verified; therefore it is not declared a confirmed production defect.

## V2 / verification actions
1. Inventory the complete GET route tree and frontend caller set for event-field definitions.
2. Decide whether V2 should provide an event-type fields API or include historical field-definition snapshots in event data.
3. Preserve tenant/event-type visibility and permission rules; expose only active or historical definitions appropriate to the event snapshot.
4. Add contract test asserting the frontend route exists, plus runtime test for dashboard dynamic-data labels and permission-denial cases.
5. Avoid silently swallowing contract errors without observability, while keeping the dashboard resilient.

## Limits
- Source-only crosswalk; no runtime API call, browser test or production change.
- Candidate mismatch, not confirmed defect. E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.


## Additional verification within source (Increment 147 follow-up)
- EventTypeService.list/get was re-read: default list normalizes event-type records; optional `includeFormDefinitions` adds form definitions only, and `get` returns normalized event-type record. No EventFields list is exposed through these paths.
- EventFieldRepository is a separate read-only repository. No Router GET route was found in the complete inspected EventType nested branch to expose its fields. This strengthens the route-contract mismatch candidate but still does not establish runtime/deployed behavior.
