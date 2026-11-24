# Task 2.8 Increment 223 — Event/Form/Project-information/Workflow mutation crosswalk

Date: 2026-11-24

## Scope
Read-only source re-fetch and bounded mutation-side-effect trace of EventService, FormService, ProjectInformationService, and WorkflowService in PRSPRO2BACKEND-PRODUCTION.

## Evidence (E1 source)

### EventService
- Event creation obtains current user/tenant, validates event payload, tenant-scopes event type, checks project availability for project-linked events, validates workflow-related routing and department/event-type rules, and persists via the creation path.
- Idempotency is user+tenant scoped and protected with UserLock when a key is supplied. A PROCESSING replay is refused rather than blindly re-created; the unlocked legacy path remains when no key is provided.
- The creation path includes multiple persistence/downstream effects (event, response/form/central info integration, project/workflow updates, audit, optional notifications); exact fault recovery/transaction semantics remain unverified.
- Event void validates tenant, linked-project access, event type and configured void policy and has downstream state/snapshot/material planning effects documented in earlier Increment 213. Multi-write recovery still unproven.
- Manual step completion verifies project visibility, assigned-role/permission, workflow requirement progress and required-event satisfaction; then project status update and audit. This sequence is not shown to be atomic.

### FormService
- saveEventResponses inserts or updates event-form response snapshots (FormVersion, ResponseJSON and DefinitionSnapshotJSON) then audits, and synchronizes non-empty values to central Project Information, including project-code special handling.
- Response save is a sequence across response/audit/central information and possible project code update. Transaction, rollback and failure replay guarantees are not evidenced.
- updateEventFormResponse requires the source event and project to be tenant-visible and an amendment event for the same project; amendment eligibility uses an explicit configuration flag/allowed-form list or legacy marker. This legacy marker inference is a policy risk candidate; not a confirmed defect. Amendment updates and recovery/test behavior remain partial.

### ProjectInformationService
- Save validates current tenant/project visibility, information type tenant/status, optional source event relationship, value/type and expected revision; same source-event/value replay is idempotent in the observed branch.
- saveWithLock wraps save using ScriptLock; save itself creates/update revision and history/audit, with live-reference synchronization downstream in relevant flows.
- Approval writes history and audit then attempts live-reference sync. Conflict resolution checks source/target/project/type, LIVE_SYNC policy and target revision consistency; exact transaction boundaries and failure recovery are not proven.
- File/image values have special attachment validation; file-based conflict transfer and complete consumer behavior were noted in earlier increment 215.

### WorkflowService
- Publish tenant-scopes workflow, requires active steps, validates active tenant event types and rules against steps/version, then persists published snapshot/version and audit (prior Increment 218 traced related publication/material recalculation).
- Publish's multi-write atomicity, concurrent draft/publish conflict handling and failure recovery remain unverified.
- Workflow create/update checks tenant ownership and duplicate code and validates repeat-reference policy against active information types and supported values. Route permission/caller parity is not fully reconciled.

## Cross-domain risk/gap disposition
- Across these mutation paths, source evidence shows tenant-scoped validations and locks in selected paths, but locks are not a database transaction and do not establish atomicity across sheets/services.
- Repeated side effects (primary rows, revision/history, audits, central information, project/workflow status, material planning, notifications) need explicit target transaction/outbox/reconciliation policy, idempotency keys and failure-state design before PostgreSQL implementation.
- Required test design: tenant-negative and cross-project authorization; stale revision; duplicate/replay; lock/concurrency; injected failure after each write; consistency/reconciliation; version snapshot parity; amendment-policy cases; publish races; notification isolation.
- These are proposed requirements/risks, not confirmed production defects.

## Evidence level and safety
E1 static source. No tests, runtime/fault injection, production data, production changes, SQL/DDL, migrations, provisioning or deployment.
G-01 (route/caller/effect disposition), G-03 (event/form amendment and history policy), G-10 (tenant/global classification and negative tests) remain OPEN. Schema readiness NOT APPROVED.
