# Task 2.8 — Deletion & Source-Event Dependency Reconciliation Increment 19
Date: 2026-11-22
Evidence: read-only targeted source review of ProjectDeletionService.js, MaterialPlanningService.js and ProcurementEventIntegrationService.js.

## Direct source observations

### Project cascade deletion collector and mutation sequence
- `_collect(project)` explicitly discovers tenant/project events and EventData through event IDs; project needs; project allocations (by ProjectID or referenced need IDs); order items linked to needs/allocated item IDs; excludes order lines allocated to other projects; only includes orders where all active items are in the deletion set and no other allocation remains; receipts by item, project or included order; project specs/calculations; and PROJECT/EVENT attachments.
- Collector refuses to treat read failures as an empty dependency list.
- `remove()` validates admin/tenant and confirmation project code, takes a script lock, recollects dependencies, and revalidates.
- Child records are soft-deleted in a sequential series; events use VOID due to allowed status values. Attachment records are marked deleted before storage trashing. Project row is physically deleted only if no recorded failure.
- The operation is not a DB transaction. It may partially mutate rows/files before reporting failure. A retry requires inspection, not blind replay. Audit is attempted after project-row deletion attempt and is itself a separate write; audit-write failure handling and durable recovery are not demonstrated in this source slice.

### Material planning on source-event void
- `markEventVoided(eventId)` marks calculations associated with the tenant/event as SOURCE_EVENT_VOIDED and changes active confirmed needs sourced from that event to NEEDS_REVIEW.
- It uses a script lock and writes an audit record after these mutations.
- This path does not directly reverse procurement receipts or allocations in the inspected implementation. Procurement lifecycle reconciliation remains separate and policy-dependent.

### Procurement integration
- The integration settings connect event-type configuration to a central numeric single-value InformationType; validation rejects non-number, non-single, or types with active columns.
- Orders/receipts remain independent from the integration service according to its source comment. This is an explicit separation, not evidence that all workflow callers have been traced.

## V2 implications / unresolved decisions
1. Project cascade delete should not be recreated as a destructive cascade. Prefer a dry-run dependency report, explicit irreversible confirmation, transactional archive/deletion orchestration and staged file-object deletion with recoverable outbox.
2. Shared order lines and cross-project allocations must survive project lifecycle changes; referential integrity should model order/item ownership separately from project allocation ownership.
3. Source-event void should trigger an explicit affected-needs reconciliation state and review queue; do not assume it automatically reverses downstream procurement accounting.
4. Audit writes must be transactionally coupled or durable via outbox for destructive operations; if an operation fails, retain an actionable recovery record.
5. Soft-delete and physical-delete policy must be separately defined per domain; a single generic cascade is unsafe.

## Evidence limits
- Bounded source inspection only; not full dependency/caller coverage, runtime tests, live data, storage inspection, recovery execution or proof of atomicity.
- G-01/G-02/G-03/G-04/G-10 remain open; schema readiness NOT APPROVED.
- No tests, live data, SQL/DDL, migration, provisioning, deployment or production modifications.
