# Task 2.8 Increment 114 — Project lifecycle, draft completion and repeat-reference copy (2026-11-23)

## Scope
READ-ONLY inspection of ProjectService.js, ProjectRepository.js and ProjectValidator.js, covering draft creation/completion, initial project creation, workflow validation, repeat-project source policies, information/image copy orchestration, code uniqueness and update boundaries.

## Observations (E1 source-only)
- Tenant is server-derived on create. Draft creation is a distinct path with minimal data (title), empty workflow/status/code and audit/notification, and RecordState='DRAFT'.
- Draft completion requires ACTIVE state and validates title, code, workflow, departments; non-draft project cannot be reverted to DRAFT in sampled update route.
- Active create validates tenant-owned active/published workflow, first step, departments, allowed users and status. Repeat workflows require a valid accessible active source project; initial workflows reject source-project linkage.
- Repeat-copy policy is read from workflow configuration, not accepted from client. Allowed policy values observed: LIVE_SYNC, INITIAL_COPY, INDEPENDENT. Policy is normalized to type IDs and checks active tenant information types.
- Creation validates tenant active departments/users and initial status; project code uniqueness is case-insensitive tenant-scoped and checked inside script lock before insert. Storage-level unique constraint not demonstrated.
- Reference-copy operation id is optional. Existing operation lookup returns prior project for same tenant and operation identifier. DB uniqueness/concurrency guarantee is not demonstrated.
- Project is inserted first with copy-plan state. Copy tasks are processed separately under ScriptLock; individual information types are copied, source revision recorded in plan, and status is updated. A FAILED state is attempted on error.
- Initial copy may duplicate image attachments, including project profile image. Copy of information values and associated history/sync log is sequential; transactional atomicity across image storage, metadata, information/history/sync-log and copy-plan state is not proven.
- Copy operation has visible idempotent status guards (COPIED/INDEPENDENT/EMPTY, SKIPPED_FILE conditions) and policy verification; same-task retry recovery and concurrent operation protection depend on lock and repository semantics.
- Project update tenant/access scoped, non-draft workflow immutable; workflow change for draft allowed only when no event history, and only active published workflow with valid first step. Code uniqueness rechecked under lock when changed. Project updates and audit sequential.
- Direct template update field allowlist and expected UpdatedAt check observed; locks and audit sequential.
- Draft and active project access model permits admin/creator or access-list membership (departments/users); list additionally gates broader viewing permissions and draft visibility.
- Source code excerpt did not establish DB-level composite constraints, transaction support, durable locks, or exhaustive behavior for project deletion/archival.

## V2 implications
- Project lifecycle explicit state machine, Draft->Active transition, permissions and immutable workflow once event history exists.
- Repeat project relationship, policy version snapshot, source revisions, operation identity, per-item copy state and retry/recovery modeled explicitly.
- Unique tenant-scoped project code and copy-operation constraints in PostgreSQL; race-safe enforcement.
- Copy execution should use transactions for DB records and object-storage compensation/outbox for files; bounded retries and error visibility.
- Access list normalization into relational membership tables and tenant composite FK.
- Test draft validation, tenant boundaries, workflow selection, code collision concurrency, repeat source access, copy operation replay, failed image/object copy, retries, LIVE_SYNC conflict and authorization.
## Verification
E1 source-only; no runtime tests executed; no production changes. Partial copy/recovery concerns not confirmed defects. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.
