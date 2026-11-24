# Task 2.8 — Material Planning Recalculation / Void Reconciliation Trace (Increment 245)

Date: 2026-11-24  
Evidence level: E1 (read-only source)

## Scope
Read-only review of `MaterialPlanningService.js` focusing on project-wide recalculation and event-void reconciliation. Production source repository remains read-only. No tests, live data, runtime, mutation or migration activity was performed.

## Source observations
- Material-planning schema readiness is lazy and cached; a script lock guards additive schema creation/header completion. This setup path is not a transactional migration.
- Specification revisions are append-first: a new ACTIVE revision is inserted, then the previous row is updated to SUPERSEDED, then audit is written. Failure in the second or third step may leave mixed revision/audit state, although latest revision selection uses Revision number.
- Recalculation takes a script lock, validates tenant/project/source event, selects the latest active spec revision by SpecKey, and calculates needs per specification.
- Recalculation performs a sequence of writes per spec: possible old-spec need superseding, estimated need superseding, append calculation ledger entry, create/update need, link calculation to need, then final operation audit. No atomic rollback/reconciliation queue was evidenced in this inspected function.
- The calculation key includes tenant, project, source event, spec key, revision and basis quantity. Existing ledger replay is returned by recordCalculation, but its reuse does not by itself make the downstream need update/link and all per-spec writes atomic.
- A replayed calculation may be linked to a need through a subsequent update. Failures between ledger insert, need insert/update, ledger-link update, or audit can leave incomplete linkage or operation state.
- When an active confirmed need for the current spec is absent, code looks for an active confirmed need tied to earlier spec IDs and marks it SUPERSEDED. Selection/status edge cases and retry behavior need dedicated tests.
- Estimated needs are superseded only when an active estimated need matching MaterialSpecKey is found. Need aggregation semantics and how legacy/manual needs coexist with calculated needs remain business-policy questions.
- Event-void handling locks, marks calculation records SOURCE_EVENT_VOIDED and confirmed active needs NEEDS_REVIEW sequentially, then audits. Failure mid-loop can leave only some ledger/need rows updated; no durable retry/outbox or repair method was demonstrated in the inspected service.
- Event void reconciliation does not automatically recompute needs from another valid source event; it marks review-needed state.
- EarlyOrderPolicy/EarlyOrderQuantity are set to DISABLED/blank in the reviewed create/revise paths; this is observed implementation behavior, not a business requirement.

## Risks / V2 design implications (recommendations, not adopted decisions)
1. Model specification revision, calculation, material need and source-event invalidation as transactional DB operations.
2. Enforce tenant-scoped uniqueness for calculation idempotency keys and specification revision identity.
3. Add durable operation/reconciliation states and outbox/repair processing for failed multi-step integrations if atomicity across domains is required.
4. Treat event void as a transactional invalidation operation with explicit review workflow; preserve immutable calculation history.
5. Define whether estimated/manual/confirmed needs coexist, how supersession and procurement reservations affect quantity, and the intended EarlyOrderPolicy.
6. Add tests for partial failures at each write boundary, replay after each failure, multi-spec recalculation, stale revision, voiding, retry, tenant isolation and aggregate procurement impact.

## Verification limits
Static source evidence only. No test was executed. Findings are hypotheses/risks where runtime outcome is not demonstrated, not confirmed defects. G-01/G-02/G-03/G-10 remain open; schema readiness NOT APPROVED. No production repository changes.
