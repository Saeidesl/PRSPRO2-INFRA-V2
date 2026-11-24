# Task 2.8 — Event Void Write-Sequence Failure Analysis (Increment 256)

## Evidence
Read-only trace of EventService.void and selected workflow enforcement tests. Event void sequence in the inspected implementation is:
1. Validate tenant/project access, status, event-type AllowVoid, department/admin, later-event constraints and restoration snapshot consistency.
2. Restore project workflow/status state when configured expected changes and snapshot conditions are met (ProjectRepository.update).
3. Update event status to VOID (EventRepository.update).
4. Write project restoration audit record, if applicable.
5. Recompute current workflow step status and possibly update/audit project again.
6. Write event VOID audit.
7. Attempt MaterialPlanningService.markEventVoided; on failure, catch and return FAILED reconciliation status, and attempt a secondary audit entry.

Selected workflow-event-enforcement tests are largely source-pattern assertions; they include snapshot/version and workflow rule checks, not runtime injected failure testing of this void sequence. Material-planning tests cover happy-path marking NEEDS_REVIEW and lock unavailable but not the failure boundaries across the above sequence.

## Critical mutation windows
- V-01: Project state restoration succeeds, event VOID update fails -> project may be restored while event remains ACTIVE.
- V-02: Event becomes VOID, project restoration audit fails -> domain mutation is persisted without corresponding audit guarantee.
- V-03: step-progress recompute update fails after restoration and event VOID -> status may be stale.
- V-04: step-progress audit fails after its update -> audit/domain divergence.
- V-05: event audit fails -> void persisted, but audit trail incomplete.
- V-06: material planning reconciliation fails; response indicates FAILED, but secondary failure audit could also fail. Durable retry is not evidenced.
- V-07: sequential updates are not transactional; retry is not necessarily equivalent to replay unless state transitions and operations are idempotent.

## Test specification V-01..V-10
1. Fail ProjectRepository.update during restore: event unchanged, no false success.
2. Fail EventRepository.update after restore: assert surfaced partial state and safe repair/retry.
3. Fail first restoration audit: inspect outcome and audit recovery visibility.
4. Fail workflow progress update: assert consistency/reconciliation marker.
5. Fail workflow progress audit: assert pending audit/observable state.
6. Fail event VOID audit: assert no false audit-complete claim.
7. Fail MaterialPlanningService.markEventVoided: explicit FAILED status, retryable reconciliation marker.
8. Fail secondary reconciliation-failure audit as well: operation status remains visible.
9. Replay after each boundary: no duplicate audit/mutation and no unsafe restoration.
10. Concurrency: two void attempts, later-event registration race, and project status changes during void; require platform-faithful lock model.

## V2 recommendation
Model event void as a transactionally recorded state transition and persist reconciliation tasks atomically in an outbox. External or downstream reconciliation must have explicit status and idempotent retry. Preserve original snapshot and transition history. Ensure a stale project state cannot be silently restored; use version checks/optimistic concurrency or row locking. Audit records should be transactionally written with domain transition or have a durable audit-outbox guarantee.

## Verification status
Static trace only. No tests, failure injection, concurrency runs or production writes. Task 2.8 remains IN PROGRESS.
