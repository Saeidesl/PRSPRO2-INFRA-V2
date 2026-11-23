# Task 2.8 — Event Create/Void Orchestration, Partial-Failure & Recovery Trace — Increment 157

Date: 2026-11-23
Scope: Bounded read-only source trace of EventService.js create and void flows and downstream form/material-planning integration.
Evidence: E1 source evidence only.

## Create flow observations
1. Event idempotency is tenant+user+key lookup and user-lock guarded, but no durable DB uniqueness/transaction guarantee evidenced.
2. A new event row is inserted first with IdempotencyStatus PROCESSING when a key is supplied.
3. Prepared reusable-form responses are validated before insertion; saved after the event insertion, with central-information side effects within FormService path.
4. Procurement/material-planning integration is invoked after event/form persistence. Its failure is caught, recorded as FAILED and audit logged; event creation continues.
5. Project workflow/status changes, event/project audit writes, and IdempotencyStatus COMPLETED update are sequential. Failure between steps can leave partially completed state.
6. Idempotent replay of a prior PROCESSING row is rejected with instruction to inspect existing event rather than retry blindly. Replay response does not rerun integrations.
7. Telegram is best-effort after completion; its failures are caught and logged as warning.

## Void flow observations
1. Tenant, project availability, event type, AllowVoid, department ownership and later-event constraints are validated.
2. If event-driven project state changes are expected, restoration requires captured previous-state snapshot and the current project state to match the expected post-event state, preventing blind overwrite.
3. Project restoration can precede event status VOID persistence. Workflow step status may then be recalculated and written separately.
4. Event void audit follows event/project writes. MaterialPlanningService.markEventVoided is subsequently invoked and failure is caught with a reconciliation-failure audit; event void is not rolled back.
5. These sequential side effects show a recovery/reconciliation requirement; transaction atomicity is not established in the sampled source.

## V2 design requirements
- Transactional event, form-response, project-state, material-planning and audit/outbox orchestration where feasible; otherwise a durable saga with explicit states, retries, idempotent handlers and reconciliation.
- Durable idempotency unique constraints and recoverable state machine (e.g. pending/completed/failed with structured diagnostics), avoiding opaque stuck PROCESSING rows.
- Separate best-effort external notification from durable core command; outbox delivery.
- Void/correction command should preserve snapshots, enforce optimistic concurrency, and atomically (or recoverably) reconcile material requirements and project/workflow state.
- Expose integration status to operational diagnostics; avoid success responses masking partial failure.
- Test crash/failure at each persistence boundary, replay, cross-tenant access, concurrent requests, later-event constraints and material reconciliation.

## Gaps
G-01/G-03/G-08/G-09/G-10 remain OPEN or pending discovery. Schema readiness NOT APPROVED.

## Evidence limits
E1 source evidence only; no runtime tests, production data, physical schema/migration or production changes.
