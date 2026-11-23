# Task 2.8 — Material Planning Specification, Calculation Ledger & Reconciliation Trace — Increment 155

Date: 2026-11-23
Scope: Bounded read-only source inspection of legacy MaterialPlanningService.js.
Evidence level: E1 source evidence only. No tests or live data.

## Observations
- Material-planning service performs a lazy additive sheet/header migration, guarded by script lock and script-cache readiness. This is a legacy operational behavior, not a V2 design precedent to copy mechanically.
- Specifications are tenant/project scoped, keyed by SpecKey, revisioned, and selected by latest revision. Revision writes insert new spec then update prior revision to SUPERSEDED, followed by separate audit logging.
- Specification input includes item/unit, consumption-per-unit, wastage percentage (0–100), notes. Early-order policy sampled is hard-set DISABLED.
- Calculation ledger is append-oriented and uses CalculationKey replay detection via a tenant-filtered read, but source does not establish a DB-enforced uniqueness constraint. Quantities are numeric JS values; default calculated amount is ceil to 4 decimal places after applying wastage.
- Calculation ledger captures source event, source event version, spec revision context, basis quantity, consumption, wastage, calculated amount, prior quantity and variance, and calculation type. Audit logging follows insert.
- Recalculation validates project and source event tenant/project relationship, selects active/latest specs, and iteratively supersedes old confirmed/estimated needs, records calculation, creates or updates need rows, then updates calculation MaterialNeedID and emits aggregate audit. These are sequential writes; transaction atomicity not established.
- Recalculation is lock-protected. The calculation key incorporates tenant, project, source event, SpecKey, revision and basis quantity, supporting retries for that tuple; however lookup-based replay and separate writes leave concurrency/partial-failure behavior unverified.
- When a source event is voided, calculations for that event are marked SOURCE_EVENT_VOIDED and active confirmed needs are marked NEEDS_REVIEW, with separate audit. The trace does not establish a fully automatic replacement/reversal ledger or transaction.
- Existing legacy material needs are not assigned invented consumption rates by the service's own introductory comment; migration/design must preserve distinction between legacy manually recorded needs and calculated confirmed needs.

## V2 candidate requirements
- Model specification revisions and immutable calculation records with durable tenant-qualified uniqueness for calculation replay keys.
- Store precise quantities with explicit decimal precision and rounding policy; 4-decimal ceiling behavior observed is legacy behavior to assess, not automatically approved.
- Link source events, versions, material specs, needs, calculation records and tenant ownership using relational constraints.
- Transactionally persist calculation, material-need projection and audit/outbox, with deterministic recovery/reconciliation.
- Represent source-event void as a controlled state/reversal workflow; define replacement recalculation and review policy.
- Preserve manual/legacy needs without fabricated spec or consumption values.
- Test concurrency/replay, revision race, partial failures, cross-tenant access, event void and projection integrity.

## Gap status
G-01, G-03, G-06, G-10 remain OPEN; G-02 remains OPEN/BLOCKER (procurement receipt lifecycle is separate). Schema readiness NOT APPROVED.

## Evidence limits
E1 source inspection only. No tests, runtime behavior, live data, schema/migration or production changes.
