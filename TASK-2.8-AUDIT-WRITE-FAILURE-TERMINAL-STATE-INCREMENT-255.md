# Task 2.8 — Audit-Write Failure and Terminal-State Consistency (Increment 255)

## Verified source sequence (read-only)
- ProjectDeletionService.remove records failures while processing dependency soft deletes and Drive trash; it only physically removes the project row if the dependency failure list is empty. It then calls AuditService.log after the project row may already have been removed, and only afterward throws for accumulated dependency failures or returns success.
- If the final AuditService.log itself throws, the project row/dependencies may already be removed/marked and the caller receives an exception without a persisted audit entry. There is no evidence in the inspected source of a durable repair ledger or transaction to reconcile that condition.
- AttachmentService.delete performs storage delete, attachment metadata DELETED update, then audit. If the audit write fails, the storage and metadata mutation may have already occurred; the service has no visible retry/convergence path in this method.
- AuditService.log validates data and directly inserts via AuditRepository; no buffering/retry or transactional coupling is shown in the inspected source.

## Test specification
### A-01..A-06 Project cascade
A-01 Audit insert failure after dependency mutations but before final project row deletion (where dependencies failed) — assert response state and retries.
A-02 Audit insert failure after physical project row deletion — record the orphaned audit gap and caller response; verify safe replay is impossible/handled.
A-03 Audit insert failure while failed dependency list is non-empty — ensure failure details remain observable and retry policy explicit.
A-04 Audit write succeeds but final response conflict for partial dependency failures — verify the audit's failedCount and failed list are sufficient/accurate; assess omitted failure detail.
A-05 repeated deletion after partial removal — validate dependencies and project context still permit or refuse retry in a deterministic way.
A-06 storage-trash failures after attachment metadata DELETED — assert durable retry/reconciliation.

### A-07..A-12 Attachment deletion
A-07 Storage delete succeeds, metadata update fails.
A-08 Storage delete fails and metadata remains unchanged.
A-09 Storage delete succeeds, metadata succeeds, audit insert fails.
A-10 Retry after A-07/A-09 with missing/already-trashed storage object and DELETED metadata.
A-11 Audit insert succeeds but response serialization fails (if relevant harness supports it).
A-12 Tenant visibility and permissions are preserved in each failure case.

### A-13..A-18 Audit contract
A-13 Reject invalid required audit fields.
A-14 OldData/NewData serialization failures (circular/non-serializable data) are surfaced predictably before insert.
A-15 AuditRepository insert failure propagated and mutation-specific recovery behavior documented.
A-16 tenant isolation for list/listByEntity/listByUser.
A-17 verify sensitive data minimization in OldData/NewData, including credential/secret fields.
A-18 audit ordering and correlation/operation IDs across multi-step mutations.

## Risks and V2 requirements
- Legacy multi-write paths cannot provide atomic business-write/audit-write consistency.
- PostgreSQL domain mutations and audit records must commit in a single transaction; immutable audit record should include operation/correlation ID.
- External storage deletions must use durable outbox jobs with retry and terminal/manual-review state; transactional DB audit/outbox records should be committed with domain state.
- Failure responses must communicate the durable operation state and retry token, not merely an exception after irreversible side effects.
- Add safe retry/idempotency constraints and reconciliation observability.

## Verification
- Static source trace: the named methods only.
- Test specifications: A-01..A-18; tests not run.
- Production references: READ ONLY.
