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


## Task 2.2 — Final audit consolidation and review package (2026-11-22)

### Mapping coverage review
This deliverable consolidates the header inventory with source-supported behavior and a field-family mapping/constraint assessment. It is a design/review package, not a claim that every legacy row or live sheet has been inspected.

| Domain / field families | Logical target mapping | Evidence level | Mapping / constraint conclusions |
|---|---|---|---|
| Tenant and identity (TenantID, username, account status, department) | Tenant, User, Department | Registry headers + selected auth/access source paths | Tenant boundary is essential. Username may repeat across tenants in the source model; login path fails closed if lookup returns ambiguous matches. V2 uniqueness should be tenant-scoped, and password migration/reset policy must be decided separately. |
| Role / permission associations | Role, Permission, UserRole, RolePermission | Headers + permission resolution and route excerpts | Tenant-aware role assignment/resolution observed. Association uniqueness, global permission catalog scope, effective dates and complete authorization matrix remain to be confirmed. |
| Projects and workflow binding | Project, Workflow, WorkflowVersion, WorkflowStep | Headers + selected project/workflow creation, draft and reference-copy paths | Project code uniqueness scope is not established. Preserve workflow version and project-state snapshots. Draft ownership exceptions must be modeled explicitly. |
| Event type and dynamic field configuration | EventType, EventField, EventTypeForm, Form, FormField | Headers + selected event/form validation/amendment paths | Dynamic configuration needs versioned definitions/snapshots. Field-code uniqueness and full required/nullability rules are not fully source-proven. |
| Event lifecycle and payload | Event, EventData, EventFormResponse | Headers + event validation, sequencing, void, amendment and idempotency paths | Project association is optional for independent event types. Preserve occurred-at vs created-at, performer/creator, workflow/type version, prior-state snapshot, payload, idempotency status. Voids/restoration are guarded and not equivalent to deletion. |
| Project information | InformationType, Column, ProjectInformation, History | Headers + revision, validation, locking, conflict and history paths | Values are revisioned; type/cardinality/column validation and source-event linkage apply. History must be retained separately. Exact relational/unique scopes are not fully proven. |
| Reference project copy/sync | Project reference relation and SyncLog | Headers + LIVE_SYNC / INITIAL_COPY / INDEPENDENT and conflict paths | Policies and operation identity must survive; stale/conflict detection and idempotent replay matter. Failed-copy recovery semantics remain partly open. |
| Material specifications, needs and calculations | Spec, Need, Calculation ledger | Headers + selected planning/reconciliation paths | Preserve calculation provenance, source event/version, basis quantity, revisions, variance/reconciliation. Exact precision, units, keys and lifecycle constraints require explicit V2 policy. |
| Procurement order and item | PurchaseOrder, OrderItem | Headers + procurement service validation/idempotency/recovery | Order graphs validated before writes; partial failure recovery context is observed. Number uniqueness, cancellation, returns and durable transaction semantics remain policy gaps. |
| Allocation and receipt | Allocation, Receipt | Headers + allocation and receipt paths | Allocation and receipt limits, project/need relationship and tenant checks observed; batch/single receipt pathways differ in some validation/replay details. Harmonize in V2. Returns/corrections are unresolved. |
| Attachment metadata | Attachment, entity association, storage object | Headers + attachment validation, authorization, association and delete paths | Entity pointer is polymorphic; source uses Drive provider. Storage cleanup, retention, recoverability, provider-independent keys and transactional outbox need explicit design. |
| Audit | Audit record | Headers + selected audit paths | Old/new payloads, action, entity pointer, reason and actor are represented. Coverage is not exhaustive; immutable storage, redaction, retention and completeness policy are open. |
| Print templates | Template/version/definition | Headers + selected template service/validator/resolver paths | Tenant-scoped code duplicate checks observed in create/update paths; version increment and definition validation observed. Resolver sources are allow-listed but rendered data is not persisted as a project snapshot. |
| Workflow-step rules/departments | Workflow rule and step-department association | Headers + selected workflow rules | Version and sequence/group/order/require-repeat fields exist. Exact uniqueness and publication invariants require verification. |
| Procurement event configuration | Event type procurement mapping | Headers + selected integration path | Limited central-information-type mapping is observed; independent procurement orders/receipts remain outside this event linkage. |
| Notifications / external integration | Notification delivery / integration config (candidate) | Partial source audit only | Telegram integration uses Script Properties and tenant-chat registration; secrets/configuration were not accessed. Persistence, retry, idempotency and outbox contracts are not adequately evidenced for mapping. Deferred to explicit discovery, not inferred. |

### Constraint and relationship audit summary
- **Tenant scope:** the source commonly validates tenant/resource ownership through services and parent-resource lookups. V2 should use tenant-inclusive foreign keys/unique constraints where applicable and enforce tenant isolation at API/service boundaries. This is a design requirement, not a finalized constraint list.
- **Logical links:** project→events/information/material; event→payload/forms; event-type→forms; form→fields/information types; information→history; workflow/version→steps/rules/departments; procurement→items→allocations/receipts; project/spec→needs/calculations; attachment/audit polymorphic entity pointers. These source relationships do not imply enforced SQL foreign keys in the legacy store.
- **Snapshot/version retention:** project workflow version, event type version, form response definition/version, material calculation provenance, and project-information revision are identified as history-sensitive. V2 must not collapse them into mutable current definitions.
- **Idempotency:** event submission, reference-copy operations, procurement order creation and receipt operations have source-level replay/locking mechanisms in reviewed paths. V2 should move these to durable DB uniqueness and transaction semantics, with clear replay responses.
- **Deletion/void/reversal:** project deletion is dependency-aware and can partially fail in Sheets; event void attempts guarded state restoration; attachment storage deletion is a side effect. V2 should use archive/soft-delete where appropriate, transactional domain state changes and asynchronous durable cleanup. Exact retention policy is still a PO/business decision.
- **Uniqueness:** source checks are often service-level (e.g., print-template code per tenant and active/non-deleted rows) rather than globally enforced. For each candidate uniqueness rule, V2 must define active/deleted semantics and scope before DDL.
- **Authorization:** inspected route/service checks are not a full permission audit. The generic route hook relies on route-specific/service checks; V2 should centralize authorization, resource scope checks and fail-closed behavior. No penetration/runtime test is claimed.
- **Query/workload:** only source query/read patterns were sampled. No live data, query timing, row-count distribution or production workload metrics were accessed; index selection and performance sizing are explicitly deferred.

### Comprehensive review checklist
- [x] Registry headers compared to source registry definition; distinction from live-sheet validation documented.
- [x] Major domain families and their logical target mappings consolidated.
- [x] Source-supported lifecycle and validation evidence incorporated from prior read-only audit.
- [x] Tenant scope, logical relationships, polymorphic references, snapshots and idempotency flagged.
- [x] Known divergent paths (batch vs single receipt) and partial-failure boundaries documented.
- [x] Security review scope/caveats, storage side effects and unresolved business decisions documented.
- [x] Explicitly prohibited unsafe assumptions: inferred SQL FK/unique/nullable rules, migrations, real-data inspection or testing.
- [ ] Exhaustive field-level SQL type/nullability/precision mapping: intentionally deferred until schema-design validation; legacy headers do not establish SQL types.
- [ ] Live deployed-sheet drift/real-data validation: excluded by safety boundary and not needed for this read-only source mapping checkpoint.
- [ ] Full permission/penetration audit and performance/workload measurements: not claimed; separate QA/security tasks.

### Review findings and required design gates
1. Do not translate legacy sheet column types mechanically; JSON payloads, date representations, boolean encodings and numeric precision must be validated with sanitized fixtures or business rules before DDL.
2. Never infer a foreign key from a column name. Relationship integrity needs tenant-aware design, explicit orphan handling and migration validation.
3. Keep history/snapshot tables and source-event references; do not overwrite historical configuration/response/calculation context.
4. Use DB transactions for graph operations (event submission, information revisions, procurement order/allocation, receipt accounting, project/reference-copy updates where atomicity is needed); external storage/notifications require durable outbox or compensating cleanup.
5. Avoid destructive cascade on shared procurement or cross-project allocations; define archive, cancellation, receipt return, correction and recovery policies before schema implementation.
6. User/password migration cannot simply preserve legacy SHA-256. Establish reset/rehash plan, session invalidation and secure credential transport before authentication implementation.
7. No SQL, executable migration, production data, secret/configuration access, tests, deployment or production repository writes occurred in this task.

**Task 2.2 audit package status:** IMPLEMENTED — ready for Product Owner review and acceptance. It is not marked COMPLETE until the Product Owner accepts this package.
