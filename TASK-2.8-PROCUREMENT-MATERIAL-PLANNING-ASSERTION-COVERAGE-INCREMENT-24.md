# Task 2.8 — Procurement & Material Planning Assertion Coverage Increment 24
Date: 2026-11-22
Scope: Read-only inspection of actual test assertions (not execution) in procurement-service, procurement-event-integration and material-planning tests.

## Procurement service test assertions observed
`tests/procurement-service.test.js` directly asserts:
- project visibility lookup strategy switches to bulk read after repeated lookups;
- workspace tenant-scoped collection reads are reused once per request;
- estimated and confirmed need key normalization;
- cumulative allocation quantity capped at need amount and surplus recorded;
- workspace reuses mapped order response, user/schema context and visibility checks.

## Procurement event integration assertions observed
`tests/procurement-event-integration.test.js` asserts:
- central information type must be numeric, single cardinality, active and not table-shaped;
- rejects zero/negative quantities and duplicate form input conditions;
- confirmed quantity comes from central information tied to same source event;
- stale event value rejected;
- legacy receipt integration setting is inert; only allowed integration settings saved;
- source text order of event form preparation, integration validation, event insertion, form save, integration apply;
- form response central project info stores SourceEventID;
- legacy dynamic event-data writes and event field administration removed/read-only;
- standard procurement receipt/order routes remain separate.

## Material planning test assertion themes
`tests/material-planning.test.js` assertion sampling:
- calculation revision and variance quantities;
- confirmed quantity recalculation creates need and safe replay without duplicate need/calculation;
- source-event void marks needs for review and calculation SOURCE_EVENT_VOIDED without deleting history;
- schema setup before lock;
- lock contention rejects duplicate calculation;
- cross-tenant/invisible project rejected;
- revised material specification supersedes prior need and preserves calculation history.

## Coverage interpretation
These are meaningful source-level assertions covering several domain invariants. They are not execution results. In the sampled tests, there is no evidence yet for batch receipt atomicity/fault injection, audit-write failures, authorization route tests, PostgreSQL constraints, concurrent distributed execution, end-to-end tenant isolation or storage recovery. Test stubs may also omit production runtime behavior. These remain explicit unverified areas.

## V2 test design implications
- Translate each observed assertion into a V2 integration test with PostgreSQL constraints and real transaction behavior.
- Extend procurement tests for payload-bound idempotency, allocation and receipt invariant, batch rollback, partial failures, concurrency and recovery.
- Test event/source-void reconciliation across needs, calculations, allocation/order/receipt impacts and historical preservation.
- Include route permission tests and tenant-scoped query tests.

## Limits
Source-only test assertion inspection; tests not run. G-01/G-02/G-10 remain OPEN, schema readiness NOT APPROVED. No live data, DDL, migrations, provisioning, deployment or production modifications.
