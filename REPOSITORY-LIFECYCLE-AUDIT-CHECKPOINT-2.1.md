# Repository and lifecycle audit checkpoint — Phase 2.1

Date: 2026-11-22
Read-only source inspection of production backend repository/service implementation.

## Repository abstraction
GoogleSheetsRepository exposes getAll/findById/findOne/exists/insert/insertMany/updateById/deleteById and row/header/cache operations. Request-level record/list/find caches are present, and writes invalidate relevant cache structures. Lock usage is present in some mutation paths, but the platform's lock semantics and sheet operations do not provide relational transactions. V2 should use DB transactions, unique constraints, row locks where warranted and idempotency.

## Project service paths inspected
ProjectService includes list/get/create/update, project availability checks, project-code updates from event context, reference-image/information copying and template updates. Locks and revision/idempotency-related logic occur in project mutation paths. Exact recovery state machine and all copy-plan conditions remain to be exhaustively map.

## Event service paths inspected
EventService includes validation against project availability, workflow target steps/rules, department/user eligibility, configured changes, event create and void, step completion and workflow-progress calculations. Locking and revision/idempotency-related markers occur in mutation paths. Event void path and downstream reconciliation were previously inspected. V2 needs atomic transaction semantics for event plus data/form/attachment/material changes, and an audit trail.

## Workflow service
Management bootstrap and data reads, workflow creation/update, step creation, event-rule management and publish paths were inspected at function level. Earlier checkpoints established draft/published version behavior and validation before publishing. Workflow lifecycle must remain distinct from ordinary project workflow state.

## Project information repositories
Project information and history and reference-sync logs have dedicated repository methods. This supports treating current values, change history and synchronization log as separate relational entities in V2, rather than one opaque JSON table.

## Forms
FormService supports versioned definitions, field management, event-type assignment, preparation/saving responses, amendments and cached definitions. Previously observed response records include form-version and definition snapshots. This implies historical response compatibility should not depend on the latest mutable form definition.

## Project deletion
ProjectDeletionService performs dependency-aware tenant/project context collection, preview and remove paths, including linked events, event data, attachments, material needs, purchase records, receipts, material specs and calculations. The current deletion path includes soft-delete/status handling and Drive trash behavior. V2 needs a carefully defined retention/deletion policy and explicit integrity-aware deletion orchestration.

## Open issues
- Full table/header mapping and relation cardinalities remain incomplete.
- Idempotency behavior must be verified per mutation endpoint.
- Copy operation failure/recovery and all reference-copy policies remain to be mapped.
- Project deletion dependency list and retention behavior need exhaustive review.
- No schema design or migration implementation started.
