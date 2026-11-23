# Task 2.8 Increment 117 — Material specification and calculation lifecycle

Date: 2026-11-23  
Evidence level: E1 — read-only source inspection  
Legacy repository: `Saeidesl/PRSPRO2BACKEND-PRODUCTION` (read-only)

## Sources inspected
- `MaterialPlanningService.js`
- `tests/material-planning.test.js` (test intent/fixtures inspected; not executed)
- Material-planning sheet definitions were cross-checked against the already inventoried database schema and lazy schema-preparation code.

## Observed behavior
- Material planning prepares the legacy material-needs, order/receipt, specification and calculation sheets through a lazy schema check; missing sheets/headers are ensured additively, existing row/column order is preserved, and a script-cache marker reduces repeated locking/checks.
- Specifications are tenant/project-scoped, use a stable SpecKey and generated item code, and are revisioned. Creation/revision validates item/unit, positive per-unit consumption and wastage (0–100). The service deliberately disables early-order policy fields regardless of caller payload.
- Revision inserts a new record as the next revision, then marks the prior record SUPERSEDED and logs audit. Insert/status/audit are sequential; rollback/atomicity and concurrent revision uniqueness are not demonstrated.
- Calculation history is append-oriented and keyed by tenant plus CalculationKey for replay. Calculation quantity is ceil(basis × consumption × (1 + wastage/100), rounded to four decimal places before ceiling; an explicit quantity override is accepted in the generic recording method. The record includes prior quantity, variance, event/version, spec/need links and calculation type.
- Confirmed recalculation validates visible same-tenant project and source event, takes a script lock, selects the latest active specification revision per SpecKey, and creates/updates confirmed needs. Previous active confirmed needs tied to prior spec revisions and estimated needs are superseded. The need and calculation ledger are then linked and audit is written; this sequence is not proven atomic.
- Stable calculation keys combine tenant, project, source event, spec key/revision and basis quantity. The source code's replay and reconciliation behavior is designed to avoid duplicate calculations, but durable uniqueness/race protection and partial-write recovery are not established.
- When a source event is voided, affected calculation records are marked SOURCE_EVENT_VOIDED and active confirmed needs are flagged NEEDS_REVIEW rather than silently deleted. Exact downstream procurement reconciliation and all partial-failure recovery are not fully verified in this increment.

## V2 implications
1. Model immutable material specification revisions and calculation ledger records; preserve source event/version and calculation inputs/output.
2. Make calculation key uniqueness durable (tenant-scoped) and compute/write calculation plus material-need projection transactionally.
3. Preserve supersession semantics for estimate/confirmed needs and specification revisions, with explicit statuses and auditable history.
4. Event void should atomically mark calculation lineage and affected needs for review, then trigger a durable reconciliation workflow for procurement/reservations.
5. Keep quantity arithmetic precision explicit and test rounding, override rules, zero/negative quantities, wastage bounds, and replay/concurrency cases.
6. Do not infer or migrate legacy per-unit rates for pre-existing needs where the source specification/calculation lineage is absent.

## Verification limits
- Source-only inspection; no legacy runtime, test suite, live sheet data or production operations were accessed.
- Test file inspected for test intent only; tests were not run.
- Findings are E1 source observations, not runtime-confirmed defects.
- Task 2.8 remains IN PROGRESS; PostgreSQL schema readiness remains NOT APPROVED. G-01 and G-10 remain open.

