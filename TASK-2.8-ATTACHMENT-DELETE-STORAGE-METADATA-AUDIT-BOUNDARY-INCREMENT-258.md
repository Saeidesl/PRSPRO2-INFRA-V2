# Task 2.8 — Attachment Deletion Storage/Metadata/Audit Failure Boundary (Increment 258)

## Source-backed ordering
AttachmentService.delete validates ID/reason, resolves current user, loads attachment, checks tenant and visibility, rejects already-DELETED status, then:
1. StorageRepository.delete(StorageKey)
2. AttachmentRepository.update(ID, Status=DELETED)
3. AuditService.log(DELETE)
4. success response.

## Failure windows
ATT-01: Storage delete/trash fails -> no metadata/audit mutation expected; verify provider semantics.
ATT-02: storage succeeds but metadata update fails -> stored object may be trashed while metadata still ACTIVE; retry behavior depends on storage idempotency.
ATT-03: metadata succeeds but AuditService.log fails -> record is DELETED but request may error; subsequent delete path rejects already DELETED, so ordinary retry may not repair audit omission.
ATT-04: repeated request after completed deletion is rejected as INVALID_STATUS; no explicit idempotent replay contract evidenced.
ATT-05: provider delete semantics are not transactional with metadata, and exact storage reconciliation/repair queue is not evidenced.
ATT-06: dependency deletion orchestration has a different ordering in the prior trace (metadata soft delete then storage trash), so single attachment deletion and project cascade need ordering/recovery parity review.

## Test blueprint ATT-01..ATT-10
- inject storage/provider failure;
- inject metadata update failure after storage success;
- inject audit failure after metadata success;
- replay after each failure and assert no false success, duplicate side effects or inability to repair audit state;
- test storage deletion idempotency and retry on already trashed objects;
- assert tenant/resource visibility and reason validation;
- test single-delete vs cascade parity;
- test reconciliation of ACTIVE metadata with trashed/missing object and DELETED metadata with still-live object;
- verify no shared files/foreign attachments are affected;
- test concurrent delete attempts;
- test correlation/repair state is observable.

## V2 recommendation
Use DB transaction to persist attachment lifecycle transition plus audit/outbox; external storage removal is a durable idempotent job. Model DELETE_PENDING/DELETED/DELETE_FAILED (or equivalent) and reconciliation, with tombstone/history. Do not make already-completed retries impossible where audit or storage reconciliation remains pending. Use stable operation keys and state-based retry.

## Verification
Static review only. No test/failure injection. Production references remain read-only. Task 2.8 IN PROGRESS.
