# Phase 2 / Task 2.1 — Detailed source mapping checkpoint (supplement)

Date: 2026-11-22
Status: IN PROGRESS — evidence inventory; no schema approval.

## Evidence and scope
Read-only retrieval of `DatabaseSetup.js` from `PRSPRO2BACKEND-PRODUCTION` (default branch) and targeted source excerpts for EventService, ProjectInformationService, FormService and WorkflowService. No production writes, data rows, credentials, live configuration, or file contents were accessed.

## Confirmed event header inventory
`13_Events`:
`ID, TenantID, ProjectID, WorkflowID, WorkflowVersion, WorkflowStepID, EventTypeID, EventTypeVersion, PerformedBy, CreatedBy, DepartmentID, OccurredAt, CreatedAt, Description, Status, Color, Size, VariantQuantity, PreviousStepID, PreviousProjectStatus, PreviousAvailableDepartmentIDs, PreviousSnapshotCaptured, VariantDetailsJSON, PreviousCurrentStepStatus, IdempotencyKey, IdempotencyStatus`.

`14_EventData`: `ID, EventID, DataJSON, CreatedAt`.

Observed event-related mapping implications:
- ProjectID appears in the header but source logic must retain project-independent events; nullable behavior needs exact route/service verification.
- Event records carry workflow/event-type version context and prior-state snapshot fields. V2 must preserve traceable transition context and distinguish state restoration data from authoritative history.
- Idempotency fields exist; status and retry semantics require further source-path validation.
- EventData stores a JSON payload; field-level mapping and repeatable values require payload samples from sanitized data, not assumptions.

## Configuration / workflow rule source inventory
`11_EventTypes`: `ID,TenantID,Name,Code,Version,Description,DepartmentID,Category,WorkflowStepID,MovesProjectToStep,AllowAttachments,Status,CreatedAt,UpdatedAt,ConnectionType,TargetDepartmentID,TargetProjectStatus,AllowedDepartmentIDs,AllowedUserIDs,AvailableDepartmentIDs,AllowVoid,TrackVariantQuantity,TargetStepStatus,IsWorkCompletion,AllowOnDraft,IsFormAmendment,AmendableFormIDs`.

`12_EventFields`: `ID,TenantID,EventTypeID,Name,Code,FieldType,IsRequired,DefaultValue,Options,ValidationRule,FieldOrder,Status,CreatedAt,UpdatedAt`.

`37_WorkflowStepEventRules`: `ID,TenantID,WorkflowID,WorkflowVersion,WorkflowStepID,EventTypeID,RequirementMode,DataSource,SequenceMode,GroupKey,GroupOrder,EventOrder,DisplayOrder,Status,CreatedAt,UpdatedAt,AllowRepeat`.

Observed in EventService excerpt:
- Event types include department/user availability restrictions.
- Project availability is checked before event operations.
- Target step lookup validates tenant/workflow and department-to-step assignment.
- Workflow movement is rule/config driven, not safe to infer from event-name strings.
- Draft project events do not trigger configured workflow changes in the inspected excerpt.
- Later events in other departments are considered in event-void/ordering logic in an inspected helper. Exact whole lifecycle remains to be reviewed.

## Project-information and forms mapping reaffirmation
`28_InformationTypes`, `29_InformationTypeColumns`, `30_ProjectInformation`, and `31_ProjectInformationHistory` form a configuration/value/history cluster. History records include revision, action, old/new values, source-event link, reason and actor fields. Exact immutable revision and concurrency policy remains unresolved.

`32_Forms`, `33_FormFields`, `34_EventTypeForms`, `35_EventFormData` comprise form definition, field associations, event-type association, and response/snapshot storage. EventFormData explicitly carries FormVersion and DefinitionSnapshotJSON, suggesting historical interpretation context must be preserved.

Observed in ProjectInformationService excerpt:
- Project information operations validate tenant and project visibility.
- File/image values are validated against project attachment metadata, attachment entity/type/status and MIME rules.
- Image field values may be arrays (limited in the inspected excerpt); file fields use a single object path.
- Approval history is queried by action/revision; details and revision mutation rules require further audit.

Observed in FormService excerpt:
- Forms and event types are tenant validated.
- Active form fields resolve associated active information types and active columns.
- Field visibility rules are interpreted from configured JSON.
- Definition cache invalidation is version-keyed and optimization-only; cache failure does not fail valid configuration retrieval.
- Snapshot, amendment and version immutability behavior require complete mutation-path review.

Observed in WorkflowService excerpt:
- Workflow-management bootstrap combines tenant-filtered workflows, departments, event types and information types.
- Workflow version selection uses draft/published/version fallback in inspected paths.
- Management data filters steps by tenant/version and assignments by tenant, active status and step visibility.
- Detailed publish/version immutability and transition semantics remain pending.

## Remaining validation / not inferred
- Full event registration, amendment, void, completion and workflow restoration paths.
- Exact project-independent event types and ConnectionType semantics.
- Complete event/form snapshot mutation behavior.
- Full repeatable/multi-row JSON payload shapes from sanitized fixtures.
- Workflow publish/draft rules and version retention.
- Project-information approval/revision concurrency semantics.
- Source uniqueness scope and nullable constraints.
- Test inventory/run results and performance workload baseline.

## Safety boundary
No SQL or executable schema, migration script, test-data extraction, real-data access, VPS, deployment, or production-system modification occurred.