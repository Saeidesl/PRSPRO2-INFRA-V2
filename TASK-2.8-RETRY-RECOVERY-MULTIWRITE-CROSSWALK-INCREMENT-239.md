# Task 2.8 — Retry/recovery and multi-write failure crosswalk (Increment 239)
Date: 2026-11-24
Read-only review of ProjectDeletionService, AttachmentService.delete, EventService.void, MaterialPlanningService.markEventVoided and tests previously inspected.

## Observed mutation/recovery patterns
1. Project cascade: per-record soft-delete calls accumulate errors and continue; file trash follows attachment metadata soft-delete; events become VOID; physical project row removal is conditional on no accumulated failures; Audit is attempted before returning conflict/success. Lock release is in finally.
2. Attachment standalone delete: storage file is trashed first, then metadata is set DELETED, then audit. No local compensation shown if metadata or audit write fails after trash. Conversely, cascade deletes metadata first then trashes file, creating a different partial-failure window.
3. Event void: validates tenant/project access, event-type AllowVoid, department/admin, later-event restriction and state snapshot consistency. It may restore project state, void the event, recalculate workflow progress, log several audit records and then mark associated material calculations SOURCE_EVENT_VOIDED and confirmed needs NEEDS_REVIEW.
4. MaterialPlanning markEventVoided updates calculations and needs, and logs audit. EventService catches failure from that helper, reports FAILED and attempts a reconciliation audit rather than undoing the event void. If this second audit fails, exception handling may not preserve a structured failure response (not proven without runtime).
5. The project cascade's VOID event state supports retry selection; attachment DELETED metadata, by contrast, appears excluded from active dependency collection. Retry implications for partially failed attachment trash are a hypothesis requiring isolated tests.

## Failure-window matrix (source hypotheses; no runtime validation)
- Attachment delete: Drive trash succeeds -> metadata write fails: file is trashed while metadata remains active; retry behavior/restore semantics unknown.
- Attachment delete: metadata succeeds -> Audit fails: operation may have changed data while request fails; no compensation shown.
- Project cascade: attachment metadata soft-delete succeeds -> Drive trash fails: retry may not rediscover metadata because inactive; likely orphaned/trash-pending file recovery concern.
- Event void: project state restoration/event VOID/audit/material-needs updates are multiple writes; no atomic transaction. Material-planning failure is surfaced as FAILED and reconciliation audit attempted.
- Cascade: child operations partial failure; project row retained, failures accumulated; rerun reconciliation behavior differs per entity/status.

## Recommended validation cases
- Isolated test fixtures (never production) inject each failure in each write order; verify retry discoverability, file/metadata consistency, tenant boundaries, duplicate effects, Audit failure behavior, and whether project deletion can safely resume.
- Define a consistent recoverable lifecycle for file deletion and cascade mutation; a durable retry/reconciliation record is preferable to relying solely on active metadata scans.
- Add explicit operational guidance to avoid manual rerun before assessing reported failures, consistent with service error message.

## Status
E1 static review only; tests not run. Task 2.8 remains IN PROGRESS, G-01/G-10 open, schema readiness NOT APPROVED.
