# Task 2.8 — Project cascade deletion orchestration (Increment 237)
Date: 2026-11-24
Scope: READ-ONLY source audit of ProjectDeletionService.js, AttachmentService.js, AttachmentRepository.js, StorageRepository.js and AuditService.js in PRSPRO2BACKEND-PRODUCTION.

## Evidence (static source)
- Admin-only project context: service verifies admin, project lookup and TenantID equality. Removal requires exact ProjectCode confirmation and a ScriptLock; context is revalidated and dependency collection rerun while lock held.
- Dependency collection: active tenant/project events, event data, material needs, purchase-order allocations/items, receipts, specifications, calculations and project/event attachments. Shared/general orders/items are protected when active items or allocations are shared with other projects.
- Cascade ordering: event data, receipts, allocations, items, selected orders, needs, calculations, specs, attachment metadata, then storage files, then event rows (VOID), and finally project physical row removal if no prior failures.
- Soft-delete helper first ensures a Status column. Per-row failures are collected; event records are marked VOID (sheet validation excludes DELETED). Rows without the status schema may be left untouched and recorded as failure.
- Attachment file deletion occurs AFTER attachment metadata is soft-deleted. Storage keys resolve from StorageKey/FileID/DriveFileId. No compensating restoration of metadata/files is shown in inspected code.
- Project row physically deleted only if accumulated dependency/Drive deletion failures are absent. Cache invalidation is performed after physical project-row deletion.
- Audit log for cascade operation is attempted after dependency processing and project row deletion attempt, recording counts and failure count. Audit failure handling is not wrapped locally in the inspected segment; could interrupt response after partial mutations.
- Failure returns a conflict with up to 10 failure details; ScriptLock released in finally. The inspected code does not demonstrate durable retry queue, transactional rollback, compensation or idempotent reconciliation for partial cascade failure.
- AttachmentService copy-project-image path has compensating storage delete if metadata insert/audit throws (try/catch surrounding insert+audit). This contrasts with cascade delete.
- AttachmentRepository is a thin Sheet adapter. StorageRepository delegates to GoogleDriveStorage; storage semantics and permanent-vs-trash behavior are not asserted here because implementation was not audited in this increment.

## Risk / verification classification
- E1 static source evidence only. Potential failure/recovery risks, not confirmed defects: cascade partial writes; attachment metadata-first soft-delete followed by storage deletion; audit failure after cascade changes; retry/reconciliation behavior; schema/status-column failures.
- No runtime, destructive-operation, or failure-injection tests executed.
- Recommended follow-up validation: isolated test fixtures for every cascade stage; injected failure matrix (schema preparation, each child write, storage deletion, event marking, physical project deletion, audit); retry/reconciliation assertions; verify shared procurement preservation and tenant boundaries.

## Status
Task 2.8 remains IN PROGRESS. G-01/G-10 and remaining gate requirements remain open. Schema readiness NOT APPROVED.
