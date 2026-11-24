# Task 2.8 — Increment 274: Form definition, project information and material planning mutation cross-check (2026-11-24)

## Scope
Read-only review of FormService, ProjectInformationService and MaterialPlanningService selected create/revise/validation paths.

## Observations
- FormService performs tenant-scoped form/event-type reference checks, definition validation, field visibility reference checks and versioned definition cache invalidation. Cache invalidation errors are swallowed intentionally as optimization failures; cache TTL is finite.
- ProjectInformationService validates values against type/column rules and verifies referenced project attachments are tenant/project-linked, active and type-compatible. Structured values reject unknown columns and enforce cardinality/row count bounds in inspected validation code.
- MaterialPlanningService ensures schemas additively under a script lock, tenant/project-scopes specification access, validates consumption/wastage bounds, and records versioned specification rows. Revision path checks latest revision and rejects superseded/stale versions.
- No confirmed defect from bounded review; lock coverage, append/revision consistency under concurrent writes, audit failure behavior, and schema-cache invalidation remain test gaps.
- Some _ensureSchema_ paths may create/add schema at request time, which is an operational dependency to verify in controlled non-production tests.

## Test targets
FM-01..FM-12: tenant-isolated forms and event types; visibility reference/order enforcement; invalidation/cache consistency; attachment-reference validation; structured field validation; stale revision conflicts; concurrent revision writes; audit failure handling; lazy schema creation failure/retry; additive header preservation; cache reset after migration; cross-tenant spec ID denial.

## Limits
Static only; tests not executed; no production data/writes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
