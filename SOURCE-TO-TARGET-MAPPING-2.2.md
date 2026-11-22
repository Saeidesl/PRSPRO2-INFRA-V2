# Phase 2.2 — Source-to-Target Mapping and Constraint Matrix (Discovery Checkpoint)

Updated: 2026-11-22
Status: IN PROGRESS — READ-ONLY source audit. No executable schema or migration.

## Scope
This task is field-level source mapping, constraints and lifecycle validation against existing legacy source code/configuration. The table registry/header evidence below is explicitly differentiated from behavioral evidence. This checkpoint is partial; the full mapping requires further file-by-file source review.

## Mapping conventions
- Legacy source sheet names and IDs are identifiers for discovery only, not PostgreSQL naming conventions.
- Suggested target names are logical concepts, not finalized schema/table names.
- Required/nullability, uniqueness and FK rules are marked UNKNOWN until supported by source mutation and validation paths.
- Tenant-scoping should be enforced relationally in V2, but exact composite key designs remain pending.
- Do not migrate backup/unused sheet content without discovery and approval.

## Registry-based field mapping matrix (header evidence)
| Legacy source | Logical target | Known source fields / evidence | Constraints / gaps |
|---|---|---|---|
| 01_Tenants | Tenant | Registry confirmed; field headers not yet fully mapped | Tenant identity, status, uniqueness, deletion semantics pending |
| 02_Users | User/account | User and tenant association inspected in auth paths | Password migration, identity scope and memberships pending detailed mapping |
| 03_Departments | Department | Tenant-scoped department reads observed | Header/requiredness/cardinality pending |
| 04_Roles | Role | Tenant-scoped role access observed | System vs tenant role scope pending |
| 05_Permissions | Permission | Permission catalog read path observed | Global/catalog scope and mutability pending |
| 06_UserRoles | User-role association | Tenant-aware assignment observed | Cardinality, uniqueness and effective-date behavior pending |
| 07_RolePermissions | Role-permission association | Permission resolution observed | Uniqueness and scope constraints pending |
| 08_Projects | Project | Project create/workflow/reference behavior audited | Complete field/type/unique-code mapping pending |
| 09_Workflows | Workflow | Versioned publish/draft behavior observed | Field and version uniqueness mapping pending |
| 10_WorkflowSteps | Workflow step | Step configuration reviewed | Exact fields and uniqueness/order rules pending |
| 11_EventTypes | Event type | Dynamic configuration observed | Complete header/type/validation matrix pending |
| 12_EventFields | Event field | ID,TenantID,EventTypeID,Name,Code,FieldType,IsRequired,DefaultValue,Options,ValidationRule,FieldOrder,Status,CreatedAt,UpdatedAt | Event type ownership, uniqueness, field-type compatibility and option encoding pending |
| 13_Events | Event | ID,TenantID,ProjectID,WorkflowID,WorkflowVersion,WorkflowStepID,EventTypeID,EventTypeVersion,PerformedBy,CreatedBy,DepartmentID,OccurredAt,CreatedAt,Description,Status,Color,Size,VariantQuantity,PreviousStepID,PreviousProjectStatus,PreviousAvailableDepartmentIDs,PreviousSnapshotCaptured,VariantDetailsJSON,PreviousCurrentStepStatus,IdempotencyKey,IdempotencyStatus | Project optionality; event-type/workflow snapshots; idempotency uniqueness and state constraints pending |
| 14_EventData | Event payload | ID,EventID,DataJSON,CreatedAt | Payload normalization/snapshot and validation semantics pending |
| 15_Attachments | Attachment metadata | ID,TenantID,EntityType,EntityID,FileName,MimeType,FileSize,StorageProvider,StorageKey,UploadedBy,UploadedAt,Status,AttachmentType,SourceAttachmentID | StorageKey uniqueness, lifecycle/recovery, retention and association integrity pending |
| 16_AuditLogs | Audit record | ID,TenantID,UserID,Action,EntityType,EntityID,OldData,NewData,Reason,CreatedAt | Coverage, immutable enforcement, redaction and retention pending |
| 17_WorkflowStepDepartments | Step-department assignment | Registry confirmed | Assignment cardinality, ordering, constraints pending |
| 18_MaterialNeeds | Material requirement | ID,TenantID,ProjectID,SourceEventID,ItemName,ItemCode,Unit,RequestedQuantity,NeededBy,Status,Notes,CreatedBy,CreatedAt,UpdatedAt,MaterialSpecID,MaterialSpecKey,SpecificationRevision,CalculationID,BasisQuantity,NeedType,CalculationKey | Quantity/status constraints, key uniqueness and generated vs manual needs pending |
| 18_ProjectStatuses | Project status | Registry confirmed | Header and lifecycle mapping pending; numeric prefix collision means independent logical naming |
| 19_PurchaseOrders | Purchase order | ID,TenantID,OrderNumber,SupplierName,OrderDate,ExpectedDeliveryDate,Status,Notes,CreatedBy,CreatedAt,UpdatedAt,IdempotencyKey | Number uniqueness scope, state transitions, cancellation/reversal policy pending |
| 20_PurchaseOrderItems | Order line | ID,TenantID,PurchaseOrderID,MaterialNeedID,ItemName,ItemCode,Unit,OrderedQuantity,UnitPrice,Notes,CreatedAt,UpdatedAt,SurplusQuantity,SurplusCost | Item/need relationship cardinality, quantity/cost invariants and line lifecycle pending |
| 21_PurchaseAllocations | Allocation | ID,TenantID,PurchaseOrderItemID,ProjectID,MaterialNeedID,AllocatedQuantity,CreatedAt | Sum/over-allocation checks, cross-tenant constraints, allocation reversal pending |
| 22_MaterialReceipts | Receipt | ID,TenantID,PurchaseOrderItemID,ProjectID,QuantityReceived,ReceivedAt,WarehouseDepartmentID,SourceEventID,Notes,ReceivedBy,CreatedAt,IdempotencyKey | Return/correction semantics, over-receipt limits, duplicate-key scope pending |
| 23_EventTypeProcurementSettings | Event procurement config | Registry confirmed | Header and configuration validation pending |
| 24_ProjectMaterialSpecs | Project material specification | ID,TenantID,ProjectID,SpecKey,ItemName,ItemCode,Unit,ConsumptionPerUnit,WastagePercent,EarlyOrderPolicy,EarlyOrderQuantity,Status,Revision,Notes,CreatedBy,CreatedAt,UpdatedAt | Revision uniqueness, allowed values, units, effective lifecycle pending |
| 25_MaterialNeedCalculations | Calculation ledger | ID,TenantID,ProjectID,MaterialSpecID,MaterialNeedID,SourceEventID,SourceEventVersion,CalculationKey,BasisQuantity,ConsumptionPerUnit,WastagePercent,CalculatedQuantity,PreviousCalculatedQuantity,VarianceQuantity,CalculationType,Status,CreatedBy,CreatedAt | Immutable ledger policy, replay uniqueness and reconciliation constraints pending |
| 28_InformationTypes | Information type | Registry and service behavior reviewed | Complete header/types/cardinality mapping pending |
| 29_InformationTypeColumns | Information column | Registry confirmed | Column typing/validation/uniqueness pending |
| 30_ProjectInformation | Project information value | Revision, type/column validation, file value validation and deletion behavior observed | Value storage shape, requiredness, uniqueness/cardinality and FK scope pending |
| 31_ProjectInformationHistory | Information history | Revision/action/old-new/source-event/reason/actor semantics observed | History immutability and retention pending |
| 32_Forms | Form definition | Versioned form/config caching observed | Header and publish/version constraints pending |
| 33_FormFields | Form field | Field links to information type/columns and visibility observed | Complete field/type/cardinality mapping pending |
| 34_EventTypeForms | Event-form association | Association and submission validation observed | Association cardinality and effective version semantics pending |
| 35_EventFormData | Form response | ID,TenantID,EventID,FormID,FormVersion,ResponseJSON,DefinitionSnapshotJSON,CreatedBy,CreatedAt | Response uniqueness/revision/amendment relation and snapshot policy pending |
| 36_PrintTemplates | Print template | ID,TenantID,Code,Name,Description,Status,Version,DefinitionJSON,CreatedBy,CreatedAt,UpdatedBy,UpdatedAt | Template version/uniqueness/rendering and historical reference policy pending |
| 37_WorkflowStepEventRules | Step-event rule | ID,TenantID,WorkflowID,WorkflowVersion,WorkflowStepID,EventTypeID,RequirementMode,DataSource,SequenceMode,GroupKey,GroupOrder,EventOrder,DisplayOrder,Status,CreatedAt,UpdatedAt,AllowRepeat | Composite scope, rule order and uniqueness pending |
| 38_ProjectReferenceSyncLog | Reference sync log | Registry and revision-aware conflict sync observed | Full headers, operation identity and recovery lifecycle pending |

## Already verified behavioral constraints (from prior read-only audit)
- Tenant-scoped resource access and parent-child access checks are applied in several audited paths; exhaustive matrix is still pending.
- Event submission validates project/workflow/rules, event type restrictions and configured transitions; event payload is stored separately.
- Project information revision/history, locking, file-field validation and logical deletion are present.
- Forms validate linkages, field visibility/requiredness and version/snapshot; amendments use locking, replay/idempotency and optimistic concurrency.
- Material needs and calculations retain provenance and reconciliation state.
- Procurement allocations and receipt quantity limits are enforced in service paths; cancellation/receipt return scope remains incomplete.
- Reference-copy policies and revision-aware sync conflicts are observed.
- Attachment authorization inherits project access for project-linked events; retention/recovery unresolved.
- Print resolver uses allow-listed/project-information/event/form sources; it is not stored as a project snapshot.

## Remaining Task 2.2 audit items
1. Full header/type matrix for all registry tables, source mutation validations, and identifiers.
2. Required/nullability and uniqueness scopes from actual write paths, not assumptions.
3. Tenant/composite relationship matrix and cross-tenant rejection paths.
4. Permission/authorization coverage across endpoint families and resource checks.
5. Copy/recovery and delete/reversal edge cases; backup sheet investigation limited to source references, not data extraction.
6. Notification/integration persistence and outbox/idempotency contracts.
7. Sanitized workload/query patterns before index choices.

## Safety
Read-only. No live data extraction, production changes, SQL/schema, migrations, VPS/deployment, dual-writing or secrets.


## Registry source verification (2026-11-22)
Fetched and read-only inspected the full `APP_CONFIG.SHEETS` registry from `DatabaseSetup.js` in `PRSPRO2BACKEND-PRODUCTION`. This confirms all defined table names and exact header arrays for the registry, rather than relying only on earlier summaries. The registry is still not proof of deployed-sheet contents or drift.

Additional exact header arrays verified in this retrieval:
- `01_Tenants`: ID,Name,Code,Timezone,Status,CreatedAt,UpdatedAt
- `02_Users`: ID,TenantID,Name,Username,PasswordHash,DepartmentID,Status,CreatedAt,UpdatedAt
- `03_Departments`: ID,TenantID,Name,Code,Status,CreatedAt,UpdatedAt
- `04_Roles`: ID,TenantID,Name,Code,Status,CreatedAt,UpdatedAt
- `05_Permissions`: ID,Code,Name,Module,Description,Status
- `06_UserRoles`: ID,TenantID,UserID,RoleID,CreatedAt
- `07_RolePermissions`: ID,TenantID,RoleID,PermissionID,CreatedAt
- `08_Projects`: ID,TenantID,ProjectCode,Title,Description,WorkflowID,WorkflowVersion,CurrentStepID,Status,CreatedBy,CreatedAt,UpdatedAt,Quantity,AvailableDepartmentIDs,AllowedUserIDs,NotifyEnabled,CurrentStepStatus,SourceProjectID,RecordState,ReferencePolicyJSON,ReferenceCopyStatusJSON,ReferenceCopyCompletedAt,ReferenceCopyOperationID
- `09_Workflows`: ID,TenantID,Name,Code,Version,Status,CreatedAt,UpdatedAt,WorkflowType,LifecycleState,PublishedVersion,DraftVersion,RepeatReferencePolicyJSON,PublishedRepeatReferencePolicyJSON
- `10_WorkflowSteps`: ID,TenantID,WorkflowID,Name,Code,StepOrder,Status,CreatedAt,UpdatedAt,WorkflowVersion
- `11_EventTypes`: ID,TenantID,Name,Code,Version,Description,DepartmentID,Category,WorkflowStepID,MovesProjectToStep,AllowAttachments,Status,CreatedAt,UpdatedAt,ConnectionType,TargetDepartmentID,TargetProjectStatus,AllowedDepartmentIDs,AllowedUserIDs,AvailableDepartmentIDs,AllowVoid,TrackVariantQuantity,TargetStepStatus,IsWorkCompletion,AllowOnDraft,IsFormAmendment,AmendableFormIDs
- `12_EventFields`: ID,TenantID,EventTypeID,Name,Code,FieldType,IsRequired,DefaultValue,Options,ValidationRule,FieldOrder,Status,CreatedAt,UpdatedAt
- `13_Events`: ID,TenantID,ProjectID,WorkflowID,WorkflowVersion,WorkflowStepID,EventTypeID,EventTypeVersion,PerformedBy,CreatedBy,DepartmentID,OccurredAt,CreatedAt,Description,Status,Color,Size,VariantQuantity,PreviousStepID,PreviousProjectStatus,PreviousAvailableDepartmentIDs,PreviousSnapshotCaptured,VariantDetailsJSON,PreviousCurrentStepStatus,IdempotencyKey,IdempotencyStatus
- `14_EventData`: ID,EventID,DataJSON,CreatedAt
- `17_WorkflowStepDepartments`: ID,TenantID,WorkflowID,WorkflowStepID,DepartmentID,Status,CreatedAt,UpdatedAt
- `23_EventTypeProcurementSettings`: ID,TenantID,EventTypeID,Action,FieldMapJSON,Status,CreatedBy,CreatedAt,UpdatedAt,ConfirmedQuantityInformationTypeID
- `18_ProjectStatuses`: ID,TenantID,Code,Name,Status,CreatedAt,UpdatedAt
- `28_InformationTypes`: ID,TenantID,Code,Name,Description,Cardinality,DataType,ValidationRuleJSON,OptionsJSON,Status,Revision,CreatedBy,CreatedAt,UpdatedAt,ShowOnCard
- `29_InformationTypeColumns`: ID,TenantID,InformationTypeID,Code,Name,DataType,IsRequired,OptionsJSON,ValidationRuleJSON,ColumnOrder,Status,CreatedAt,UpdatedAt
- `32_Forms`: ID,TenantID,Code,Name,Description,Version,Status,CreatedBy,CreatedAt,UpdatedAt
- `33_FormFields`: ID,TenantID,FormID,InformationTypeID,FieldOrder,GroupName,LabelOverride,HelpText,IsRequired,VisibilityRuleJSON,Status,CreatedAt,UpdatedAt
- `34_EventTypeForms`: ID,TenantID,EventTypeID,FormID,IsRequired,FieldOrder,Status,CreatedBy,CreatedAt,UpdatedAt
- `36_PrintTemplates`: ID,TenantID,Code,Name,Description,Status,Version,DefinitionJSON,CreatedBy,CreatedAt,UpdatedBy,UpdatedAt

Source file SHA observed: `DatabaseSetup.js` blob `f8e341a5c220105792eab030ea5de920c9921320`. Read-only fetch only. This retrieval supports exact header evidence; behavioral validation for each header remains incomplete.
