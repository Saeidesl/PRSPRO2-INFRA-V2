# Task 2.8 — Event Void State-Transition Failure Test Specification (Increment 254)

## Source trace (read-only)
EventService.void performs a sequence of mutation and side-effect steps: tenant/project visibility, already-VOID gate, event-type AllowVoid, department/admin, later-event gate, project snapshot/state consistency, possible ProjectRepository restoration, EventRepository VOID update, project audit, workflow-progress refresh/update/audit, event audit, material-planning markEventVoided with failure catch and reconciliation-failure audit. This sequence is not transactional.

## Failure/edge test cases
- V-01 Reject missing reason/id.
- V-02 Reject missing event and cross-tenant event (uniform not-found).
- V-03 Reject already VOID.
- V-04 Respect event type AllowVoid false and tenant-bound event type.
- V-05 Respect originating department vs admin.
- V-06 Reject when a later event exists in another department.
- V-07 For configured project state changes, reject if snapshot absent.
- V-08 Reject if current project state no longer matches event's expected configured changes; do not overwrite newer state.
- V-09 Failure during ProjectRepository.update restore: event must remain non-VOID or operation state must clearly report recoverable partial status; characterize existing behavior.
- V-10 Failure during EventRepository.update after project restore: assert restoration is not falsely presented as complete; identify rollback/reconciliation gap.
- V-11 Failure on project-restoration audit after project/event writes.
- V-12 Workflow progress recomputation and project update fail.
- V-13 Workflow-progress audit fails after state update.
- V-14 Event audit fails after event status update.
- V-15 Material planning reconciliation failure is surfaced in result and logged; test audit-failure path for reconciliation alert too.
- V-16 Success path: snapshot restoration, status transition, workflow refresh, material-needs response and audit records align.
- V-17 Retry after partial failure must not duplicate void audit/history or accidentally restore state twice.
- V-18 concurrent void attempts: only one state transition; no duplicate audit/reconciliation.
- V-19 Independent event (no project) void behavior.
- V-20 Void of event whose step is no longer current: progress recomputation only when source logic requires it.

## Required harness
Use a scripted repository mock that can fail at call N for ProjectRepository.update, EventRepository.update, AuditRepository.insert and MaterialPlanningService.markEventVoided. Keep full ordered call log and persistent in-memory state; run retry against the same state. Assertions should inspect final project/event/audit/planning state and response semantics, not only thrown errors.

## V2 design implication
Implement event-void state transition as a PostgreSQL transaction for relational state and audit/outbox. Reconciliation for external/non-transactional subsystems must be durable and retryable. Add explicit operation ID/idempotency and status fields for manual-review conditions. Avoid silent rollback assumptions across external services.

## Verification status
Specification only. No production changes, no runtime tests/failure injection. Static trace limited to EventService.void and relevant tests previously reviewed.
