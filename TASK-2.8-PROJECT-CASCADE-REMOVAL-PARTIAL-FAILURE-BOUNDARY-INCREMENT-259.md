# Task 2.8 — Project Cascade Removal Partial-Failure Boundary (Increment 259)

## Source-backed sequence
Read-only trace of ProjectDeletionService.remove:
- prepare schema, resolve project context, exact project-code confirmation, acquire ScriptLock, revalidate project/dependencies;
- sequential soft-delete of event data, receipts, allocations, purchase-order items, orders, material needs, calculations, specifications, attachment metadata;
- trash attachment storage objects;
- mark project events VOID;
- if no prior failure, physically delete the project row;
- write cascade audit record;
- if any failures were accumulated, throw conflict; otherwise return success.
Failures in many child row/file actions are accumulated, and physical project-row deletion is gated on the accumulated failure list. However, audit is invoked after the physical deletion attempt and is not visibly enclosed in the same transaction.

## Failure windows / questions
PD-01: child soft-delete updates can partially succeed; failures are collected but no durable deletion-operation state is evidenced.
PD-02: attachment metadata is soft-deleted before storage trash in cascade; storage failure leaves deleted metadata and may block normal AttachmentService retry path.
PD-03: events marked VOID after other children; failure can leave mixed lifecycle states.
PD-04: project row is physically deleted only if the accumulated list is empty; this gating is a protection, but its runtime behavior is untested.
PD-05: physical project row deletion succeeds, then cascade audit fails -> operation may not return success and project cannot be readily reloaded for a normal retry; durable audit/recovery evidence is not shown.
PD-06: schema preparation/ensureColumn can fail, and per-sheet processing may continue/accumulate errors; retry convergence across legacy headers and rows unverified.
PD-07: nested ScriptLock boundaries may exist in repository soft-delete/update primitives; reentrant/lock behavior on GAS is not proven by the inspected tests.
PD-08: retry after partial cleanup must reconstruct dependency graph even when event rows VOID and children DELETED; test covers VOID event filtering during collection but not full remove/retry orchestration.
PD-09: shared procurement order protections must remain invariant during cleanup and replay.
PD-10: failure details are included in returned error text; V2 should avoid leaking sensitive internal identifiers/diagnostics to end-user while keeping administrator-safe operation logs.

## Test blueprint PD-01..PD-14
1. Fail each child-row soft-delete at first/middle/last row.
2. Fail ensureColumn per sheet.
3. Fail storage trash after metadata status mutation.
4. Fail event VOID update.
5. Fail project physical deletion.
6. Fail audit after successful physical project deletion.
7. Retry after each partial state and assert safe convergence or explicit terminal manual-review state.
8. Assert shared/general purchase order and other-project allocations are preserved.
9. Assert no cross-tenant dependencies are selected.
10. Assert VOID/DELETED children are not reprocessed incorrectly.
11. Assert exact project confirmation and admin authorization at operation entry.
12. Assert lock contention is surfaced and state remains unchanged.
13. Assert audit payload/status reveals incomplete operation without sensitive leakage.
14. Run full cascade only in isolated fixture/temporary DB, never production.

## V2 recommendation
Represent project deletion as durable lifecycle/archival operation with a transactionally recorded dependency plan and status. Preserve project history rather than physically removing core entity. Child cleanup and file removal via idempotent outbox tasks; shared procurement dependencies remain restricted. Audit and operation status commit atomically. Provide administrator recovery/reconciliation controls and redact user-facing error details.

## Verification
Static trace only; tests/failure injection not run. Production reference remains read-only. Task 2.8 IN PROGRESS.
