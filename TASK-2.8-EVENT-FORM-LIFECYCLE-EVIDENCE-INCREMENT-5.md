# Task 2.8 — Event & Form Amendment Lifecycle Evidence (Increment 5)
Date: 2026-11-22
Read-only source audit of production Backend reference. No runtime or tests executed.

## Event lifecycle findings
- Event creation validates Tenant and project/workflow context, captures previous project state fields in event records (observed in create flow), and may persist form responses separately after event insertion.
- Event idempotency is supported with tenant/user/key lookup. A prior record marked PROCESSING is treated as an incomplete operation and prevents silent duplicate creation; observed replay response derives form persistence status from associated responses.
- Event void requires a reason, tenant/project visibility, valid event type, event-type AllowVoid configuration, and department/admin authorization. Void is rejected when a later event in another department exists.
- Void attempts guarded project-state restoration using expected changes and previous-state snapshot matching, updates event status to VOID, and records Audit entries. Material-planning reconciliation is attempted but may fail separately; failure is surfaced and audit logged. This indicates the legacy multi-operation void flow is not atomic.
- updateFieldFromTemplate permits direct same-event edits to OccurredAt/Description with lock and optimistic UpdatedAt comparison; editing OccurredAt is blocked when later cross-department event exists. This is a distinct mutable event metadata path.

## Form definition/response findings
- Event-form response preparation checks mapping, form version and fields, validates values, and attaches DefinitionSnapshot to prepared response.
- saveEventResponses stores ResponseJSON and DefinitionSnapshotJSON on response records. However, inspected code updates an existing response record if found, rather than always creating a new immutable response-history record; audit log captures the operation separately.
- Direct response edits on template are supported with optimistic timestamp/form-version/information revision checks and synchronize project-information values.
- updateEventFormResponse is a correction path via an amendment event on the same project. It requires a configured form-amendment type or legacy marker, allowed form IDs when explicitly configured, and validates current form version. It writes updated response values and LastAmendmentEventID; replay/conflict logic is present. It also synchronizes project information and records Audit on non-replay.
- The current response model appears to retain a latest state with amendment pointer, while full immutable amendment-chain/previous-value record persistence is not demonstrated by inspected sections.
- Form definition version is captured as FormVersion and DefinitionSnapshotJSON on response, but full immutable field/column/type dependency snapshot coverage and schema-change retention contract are not proven in this audit.

## V2 implications
1. Preserve immutable original event/form response revision and append correction records (or explicit revision entities), never overwrite the sole historical representation.
2. Model amendment event reference, reason, author, timestamps, predecessor revision and effective/latest projection.
3. Void must be explicit, governed and transactional for DB-local state/history/audit/outbox; downstream material planning, notifications and storage side effects should reconcile asynchronously.
4. Event metadata edits must use explicit correction semantics, concurrency version and impact checks rather than unconstrained update.
5. Preserve exact form definition snapshots including field ordering, labels, visibility/required rules, information types and validation semantics as needed to interpret historic responses.
6. Distinguish effective/current view from audit/history view.
7. Determine how project-information projection changes are rebuilt, reversed or reconciled when form amendment/void affects them.
8. Source audit is bounded; route/permission/caller and test evidence remain part of G-01 coverage reconciliation.

## Status
Source-level evidence only. No tests executed, no implementation, no SQL/DDL, no production modifications or data migration.
