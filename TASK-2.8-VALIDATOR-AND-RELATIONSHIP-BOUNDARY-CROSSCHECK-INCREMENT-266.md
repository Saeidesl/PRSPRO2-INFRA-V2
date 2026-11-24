# Task 2.8 — Increment 266: Procurement/project/event validation boundary cross-check (2026-11-24)

## Scope
Read-only review of ProjectValidator.js, EventValidator.js and selected ProcurementService.js helper boundaries in PRSPRO2BACKEND-PRODUCTION.

## Observations
- ProjectValidator validates core project fields and lifecycle state shape; it validates WorkflowID format, but this validator itself does not establish tenant ownership/availability of workflow or departments. This must be enforced downstream in service logic and tested end-to-end.
- EventValidator validates basic field types, timestamps, status enum on update, and DynamicData object shape; it does not itself prove tenant/project/EventType/Department authorization or dynamic-field schema integrity.
- ProcurementService._validateProject_ checks project existence, TenantID and project visibility; _validateEvent_ checks event existence and TenantID and project visibility for project-linked events. These helper paths are useful enforcement points but coverage across all callers and empty/general-event semantics remains unverified.
- Project/Event validator and procurement helpers have distinct responsibilities; validator-only tests would not demonstrate cross-tenant or relationship integrity.
- No confirmed defect is claimed. Static observations and boundary test requirements only.

## Follow-up test targets
PV-EV-01..08: tenant-scoped WorkflowID/department availability, project lifecycle transition, event project/event-type tenant matching, event department eligibility, draft-project event behavior, general events with no ProjectID, tenant-isolation of procurement helper lookup, fail-closed when project/event lookup fails.

## Limits
Static source inspection only. Tests were not run; production repositories remain read-only; no production data accessed and no schema/migration/deployment changes made. G-01/G-02/G-10 remain OPEN; Schema Readiness NOT APPROVED.
