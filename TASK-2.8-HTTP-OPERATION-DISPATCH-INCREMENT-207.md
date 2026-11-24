# Task 2.8 Increment 207 — HTTP operation dispatch reconciliation (static router inventory)

Date: 2026-11-24

## Source
Backend `Router.js`, fetched read-only. Blob SHA: `f2542a17993b852c7dd2968a8fefe57f70b991f3`.

## Operation families enumerated
A static extraction segmented Router dispatch into GET, POST, PUT and DELETE branches:
- GET: 31 exact path literals, plus 13 prefix dispatch families and 7 regex families.
- POST: 19 exact path literals, plus 7 prefix families and 10 regex families.
- PUT: 9 prefix families and 6 regex families; no exact literal routes in the extractor.
- DELETE: 6 prefix families and 5 regex families; no exact literal routes in the extractor.

These totals are extraction/navigation counts and may overlap or miss nonliteral dispatch logic; they are not an endpoint-completeness claim.

## Representative operation families (across methods)
Authentication/session; project CRUD, deletion preview and deletion; project information history/item/list and approve/reference resolve; project material specs and calculations; workflow and department assignment/step rules; event and event-form access; form definitions/fields and event-type assignment; procurement orders and single/batch receipts; attachments; print-template resolve/preview/images/PDF/revision; information types/columns; roles/users/permissions; audit and reports; project statuses and dashboards.

## Security/validation/side-effects status
Route-level guard checks were observed in source, including explicit admin checks, AccessControlService.require, requireAny, and some relationship prechecks (e.g., workflow assignment relationship and draft project exception). This inventory has not yet systematically mapped every branch to:
- guard and role/permission code;
- tenant boundary predicate and relationships;
- validator and payload contract;
- service/repository writes, atomicity, idempotency and lock behavior;
- history/audit, attachment/storage, projections, notifications, report and workflow effects;
- frontend caller and relevant test assertions.

## EP-01/G-01 disposition
Static HTTP-method branch inventory advances the route coverage baseline. EP-01/G-01 remain open until each branch and nested dispatch path is traced and all source entries are explicitly covered, excluded, legacy, or unused with justification.

## Safety
Read-only source audit only. No tests executed and no production changes. G-02/G-03/G-04/G-10 remain open; schema readiness NOT APPROVED.
