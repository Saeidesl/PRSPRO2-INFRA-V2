# Task 2.8 — Procurement Event Integration Trigger & Central Information Source Trace — Increment 156

Date: 2026-11-23
Scope: Read-only source inspection of ProcurementEventIntegrationService.js and its call to MaterialPlanningService.
Evidence level: E1.

## Findings
- Procurement event integration is tenant-scoped by EventTypeID/TenantID. Its configured action is NONE or RECALCULATE_CONFIRMED_NEEDS.
- Configuration requires a selected central information type that is active, tenant-owned, numeric, single-valued, and has no active columns. The configuration write is followed by separate audit.
- Event response validation requires the configured quantity exactly once across mapped form responses, validates positive quantity and project availability, and rejects independent events when confirmed recalculation is configured.
- Apply reads central project information and checks tenant/source-event linkage, parses stored value JSON, then invokes material-planning recalculation using event version or event-type version.
- Legacy procurement receipt mappings are explicitly described as inert in this integration service; receipt APIs remain independent.
- Configuration retrieval/saving and validation/apply paths have tenant/type checks; full EventService caller sequence and transaction atomicity between event, central information and need recalculation still require separate verification.
- Central-information persistence and calculation side effects appear sequential and recovery behavior is not established in this sampled integration service.

## V2 requirements
- Explicit event-command orchestration with one transaction boundary for event/form responses, central-information source snapshot, material calculation and audit/outbox (or documented saga/recovery).
- Source-event provenance and versioning preserved; unique association between source event and the quantity source.
- Enforce tenant/project/type ownership in relational constraints.
- Define void/amendment behavior for central-information snapshots and recalculated needs.
- Ensure receipt lifecycle remains distinct from material-needs calculation, with clear domain boundaries.

## Gaps
G-01/G-03/G-06/G-10 remain OPEN; schema readiness NOT APPROVED.

## Evidence limits
E1 source inspection only; no tests/live data/schema/migration or production modification.
