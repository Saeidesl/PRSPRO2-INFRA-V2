# Phase 2 / Task 2.1 — Lifecycle semantics checkpoint

Date: 2026-11-22
Evidence: targeted read-only review of the tails of EventService.js, ProjectInformationService.js, FormService.js and WorkflowService.js in PRSPRO2BACKEND-PRODUCTION.

## Event voiding (observed excerpt)
- Void updates event status to VOID and writes an audit record with reason.
- Project restoration is conditional: an expected state change and prior snapshot must both be present; prior step/status/department availability and current-step status are restored from captured values.
- Workflow step completion/progress is recalculated after void when the event relates to the current step.
- Material-needs reconciliation is attempted for project events. Failure is surfaced in a result status and an audit record requesting reconciliation.
- These paths include multiple sheet writes and audit operations that are not atomic in Sheets. V2 should design transactionally, but exact invariant/rollback behavior needs broader review.

## Project-information lifecycle (observed excerpt)
- Deactivation is logical, increases revision, records old value and history action DELETED, actor, source event and reason, and writes an audit entry.
- Save-with-lock uses a script lock around save; history returns tenant-filtered records and parses old/new values.
- Project-information reference sync writes history and sync-log entries with revisions, policy, action, conflict data, idempotency key and actor.
- The excerpts show revision/history patterns but do not fully define concurrency, approval or reference-sync conflict policy.

## Form amendment lifecycle (observed excerpt)
- Amendment flow uses a lock.
- It checks replay identity and rejects replay with changed payload.
- ExpectedUpdatedAt is used for conflict detection against concurrent response edits.
- Amendment event linkage is stored; amendment response updates and synchronizes corresponding project information, including logical deactivation for emptied fields.
- Replay path still completes project-information synchronization, suggesting recovery/idempotent completion is important.
- Form response read returns values and definition snapshot after tenant and project-visibility checks.

## Workflow publication (observed excerpt)
- Publishing requires at least one active step.
- Active rules are validated against active event types and active steps in the selected version.
- Publish stores published version and repeat-reference policy, clears draft-version marker, sets lifecycle state PUBLISHED and status ACTIVE, and records audit.
- Workflow version and rule behavior should be represented explicitly and historical event context preserved; exact edit-after-publish policy remains to validate.

## V2 design implications (provisional)
1. Event void must be a controlled state transition, with snapshot guards, step-progress recalculation, material-needs reconciliation and durable audit evidence.
2. Project-information deactivation is soft/history-preserving, not hard-delete semantics.
3. Form amendment must support conflict detection, replay-safe idempotency and reliable response-to-project-information synchronization.
4. Workflow publication should validate version-bound steps and rules; publish should produce a stable historical configuration reference.
5. V2 should make multi-record mutations atomic and add durable recovery/reconciliation rather than reproducing Sheets partial-write risk.

## Open items
- Full event registration and void code paths and whether all checks/actions are captured.
- Amendment permissions and eligibility rules, complete snapshot semantics and transactional synchronization.
- Workflow edits, draft creation, version incrementing, cloning and compatibility with existing project execution.
- Project-information approval and reference-sync conflict handling.
- Test coverage and concurrency/performance baselines.

No live data, secrets, deployment, schema or migration work was performed.