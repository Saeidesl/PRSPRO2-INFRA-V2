# Task 2.8 — Event/form preparation and tenant/payload boundary trace (Increment 151)

Date: 2026-11-23
Evidence: bounded read-only source retrieval from PRSPRO2BACKEND-PRODUCTION default branch. No legacy repository writes.

## Sources retrieved
- EventTypeService.js
- FormService.js
- EventDataService.js
- EventService.js (selected initial section)
- Router.js (retrieval; dispatcher inventory already recorded in Increment 66)

## Observations
1. EventDataService.getByEventId validates EventID, resolves current user, retrieves the event, checks tenant ownership and, for project-linked events, parent project tenant and availability. It retrieves event data by event ID, parses DataJSON and rejects malformed/non-object/array data. This path is read-only and project-independent events are supported by the observed conditional.
2. FormService.prepareEventResponses validates submitted response array shape and size (up to 30), checks duplicate FormID entries, limits responses to active tenant mappings and forms, enforces required mapped forms, and verifies supplied FormVersion against current form version.
3. It validates response Values as object payloads, checks field visibility rules, required fields and non-visible field values, and validates structured values through project-information validation where applicable. This trace is bounded to the inspected portion and does not establish every persistence/caller path.
4. FormService event-type mapping operations shown in source use tenant checks, status validation, mapping updates, cache invalidation and separate audit writes. This is source observation; transaction atomicity is not established.
5. EventTypeService normalization handles array-valued access/configuration fields, flags, connection type and amendment configuration; it removes legacy/obsolete fields during normalization. This establishes payload normalization behavior, not a complete schema mapping.

## V2 implications (candidates, not approved schema)
- Event/form response payloads require validation against a versioned form-definition snapshot; do not assume mutable current definitions are sufficient for historical events.
- Tenant ownership checks and parent-resource availability checks must be represented in authorization/relational constraints where possible and covered by negative cross-tenant tests.
- JSON payload validation must reject unexpected shape and invalid hidden-field values while preserving approved historical snapshot semantics.
- Cache invalidation and audit side effects need a transaction/outbox strategy in V2, subject to further source mapping.

## Evidence limits and gap status
- E1 source evidence only; no E3 runtime test or E4 live data evidence.
- This trace does not close G-01 (complete write-path/caller/test matrix), G-03 (payload/snapshot/report/search model), or G-10 (tenant-qualified relational constraints).
- No SQL/DDL, migrations, DB provisioning, test execution, live data, production modifications, deployment or VPS actions.
