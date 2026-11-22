# PMS V2 — Phase 2 / Task 2.1: PostgreSQL Conceptual Data Model (Draft)

Updated: 2026-11-22
Status: IN PROGRESS; based on legacy source audit and accepted planning documents. This is a conceptual inventory only, not executable schema.

## 1. Scope and guardrails
This first data-model task consolidates entities and relationships already evidenced by read-only source discovery into a conceptual model. It does not create SQL, migrations, database infrastructure, or access production data. Entity names below are conceptual; exact columns, keys, optionality, cardinality and normalization must be validated before schema work.

## 2. Entity clusters and conceptual ownership

### Identity / Tenant / Organization
- Tenant
- User / account
- Tenant membership
- Role
- Permission
- Role-permission association
- Department / organization unit
- User/department assignment (details to verify)

### Projects
- Project
- Project information / central information
- Project information revision/history
- Project lifecycle/status references
- Project-related workflow context

### Workflow
- Workflow definition
- Workflow version
- Workflow step
- Step-to-department assignment
- Workflow rule/transition policy (exact structure to verify)
- Project workflow state/history (exact model to verify)

### Events / Forms
- Event type
- Event
- Event data/field values
- Form definition
- Form fields
- Form-event/type associations (exact relationships to verify)
- Form response / response snapshot
- Event revision/amendment/void history (verify exact persistence semantics)

### Materials / Procurement
- Material/specification or requirement
- Requirement line/item
- Procurement order (project-linked and general)
- Procurement order item
- Allocation/association to requirements or projects
- Receipt/delivery records and quantity history
- Unit and code reference entities where verified

### Files
- File/object metadata
- File association/link to event/project/form or other supported resource
- Upload/lifecycle state (details to verify)
- Storage object identifier and integrity metadata

### Audit / Notifications
- Audit record
- Notification configuration/type
- Notification/delivery record
- Integration/provider configuration reference (secrets excluded)
- Retry/outbox/dedup records (V2 design elements)

### Reporting / Print
- Report query/projection (derived design, not authoritative write entities)
- Print template
- Template elements/bindings (exact existing relationships to verify)

## 3. Relationships evidenced or inferred as conceptual links
The following links were identified during source audit; implementation cardinality and constraints remain subject to validation:
- Tenant scopes users/memberships, organizational settings and tenant-owned business records.
- Project.ID is referenced by Events.ProjectID.
- Event.ID is referenced by EventData.EventID and EventFormData.EventID in the audited legacy model.
- Event types and forms relate to event/form field structures; exact association details require confirmation.
- Workflow definitions, versions, steps and department assignments relate to project progression and event transitions.
- Project information revision/history relates to project identity and change tracking.
- Procurement structures connect project-specific or general orders, items, requirements, allocations and receipt/delivery history.
- File metadata/associations connect files to supported business resources; actual bytes are outside the relational model.
- Audit records identify actor and target/action where available; exact retention and linkage are pending policy.
- Notifications/integrations link events/configuration and delivery records, with durable retries/deduplication recommended for V2.

## 4. Key modeling principles to apply later
- Tenant-aware foreign keys/unique constraints where applicable.
- Explicit ownership and relationship integrity.
- Preserve legacy identifiers and historical snapshots where justified.
- Do not flatten versioned historical information into mutable current-state-only records.
- Separate authoritative write data from reporting projections.
- Use deletion policy carefully for historical, audit and file-linked records.
- Model general procurement without forcing a fake project relationship.
- Do not assume every event is project-bound.
- Keep secrets out of relational migration datasets and repository files.

## 5. Unresolved modeling questions
- Exact tenant ownership and cross-tenant relationships for every entity.
- Project/event/form snapshot and revision semantics, including amendment and void.
- Workflow version retention and transition-history requirements.
- General procurement scope and material identity/code generation.
- Receipt/allocation quantity and deletion/reversal rules.
- File ownership, permission inheritance, metadata, lifecycle and storage migration.
- Audit retention, actor attribution and privacy classification.
- Template binding and rendering data relationships.
- Which legacy calculated fields are derived vs authoritative.
- Identifier strategy and mapping approach, including uniqueness scope.
- Required indexes based on real workload; volume and query patterns remain unmeasured.

## 6. Deliverables and acceptance criteria
- Conceptual entity inventory and relationship notes consolidated.
- Unknowns explicitly recorded, without invented constraints.
- No executable schema until detailed model review and approval.
- Before schema implementation: detailed source-to-target mapping, tenant matrix, relationship cardinality, snapshot/history strategy and deletion/recovery rules must be validated.

## 7. Safety boundary
No live data extraction, production DB access, schema deployment, migration, dual-writing, VPS acquisition, deployment or legacy repository modification.


## 8. Relationship matrix (conceptual, evidence-aware)

Legend: **Observed link** means the source audit identified the link or key reference; exact FK/cardinality are not yet schema-verified. **Design candidate** means the relationship is a modeling proposal requiring validation.

| Parent/owner | Related object | Link/evidence | Status / modeling caution |
|---|---|---|---|
| Tenant | User/membership/roles and tenant-owned records | Tenant scope observed in service/auth paths | Candidate tenant FK and compound constraints; cross-tenant access must fail closed |
| Project | Event | Project.ID -> Events.ProjectID observed | Event may be project-independent; do not make Project mandatory |
| Event | EventData | Event.ID -> EventData.EventID observed | Preserve field/value structure and any revision semantics |
| Event | EventFormData / response | Event.ID reference observed | Validate whether this is separate response storage or snapshot |
| EventType | Form and fields | Conceptual links identified | Cardinality, selection rules and historical snapshot need verification |
| Workflow definition | Version | Versioning observed/documented | Decide immutable version semantics and active-version rules |
| Workflow version | Step | Conceptual/implementation references identified | Step ordering, uniqueness and transition rules must be validated |
| Step | Department assignment | Assignment references reviewed | Model multiple assignments/ownership without assuming cardinality |
| Project | Workflow state/history | Lifecycle and workflow execution traced | Preserve history, not merely current step |
| Project information | Revision/history | Revision/history reviewed | Establish immutable revision vs mutable current record semantics |
| Procurement order | Order item | Structure audited | General and project-related orders must both be supported |
| Requirement/material item | Procurement/allocation/receipt | Relationship graph reviewed | Validate quantity accounting, reversals and allocation rules |
| File metadata | Business resource association | Attachment visibility/lifecycle reviewed | Use association strategy only after source relationship and access rules verified |
| Actor/user | Audit record | Attribution observed where available | Actor may be unavailable for system actions; preserve safe attribution |
| Notification config/event | Delivery/outbox record | Integration path reviewed; durable outbox is V2 design | Outbox/delivery tracking are design candidates, not legacy entities |
| Print template | Template element/binding | Print/template paths reviewed | Verify direct binding and permissions; don't treat as source-of-truth business data |

## 9. Candidate transaction boundaries (not final)
These are hypotheses for later validation and design, not commitments:
- Project identity + initial central-information record/revision.
- Event submission + event data + form response/snapshot + project/workflow effect + audit attribution, where applicable.
- Procurement order/item changes + allocation/quantity accounting + audit.
- Receipt/reversal and allocation reconciliation.
- File metadata/association changes coordinated with storage operations through outbox/reconciliation.
- Notification generation recorded transactionally with business mutation; provider delivery occurs asynchronously.

Avoid bundling unrelated domains into long transactions. External storage/provider calls must not be held inside database transactions.

## 10. Relationship validation checklist for subsequent source-to-target mapping
- Verify every source identifier, its uniqueness scope and legacy mapping.
- Confirm nullable vs required links from actual mutation and read paths.
- Confirm deletion, reversal, void and amendment semantics for each edge.
- Identify historical/snapshot links that must remain immutable.
- Verify tenant scoping on both parent and child and authorization checks.
- Verify cross-domain references, orphan handling and repair/reconciliation paths.
- Confirm whether relations are direct, join-table-based or inferred from payloads.
- Validate multi-row forms and procurement quantity relationships.
- Capture read/write paths and query patterns before choosing indexes.


## 11. Focused entity notes — Project, Event, Forms and workflow context (audit-backed; provisional)

### Project aggregate (conceptual)
- Project identity/code and tenant scope.
- Current project lifecycle/workflow context.
- Central/project information and its revision/history.
- Associated events (not all events should be assumed project-bound).
- File associations and report/template references are links, not necessarily aggregate-owned rows.
- Exact legacy columns and uniqueness scope are deliberately not specified pending detailed source-to-target mapping.

### Event aggregate (conceptual)
- Event identity and type.
- Optional project link where applicable.
- Event payload/field-value records.
- Form-related response/snapshot data.
- Attachments and audit references.
- State transitions such as amendment/revision or voiding must be modeled from actual behavior; do not assume a simple delete/update lifecycle.
- Preserve the historical event representation and associated workflow context where needed.

### Form model
- Form definition and its version/history behavior (to verify).
- Form field definitions and data types.
- Event type/form selection or association.
- Submitted response values and their snapshot/immutability rules.
- Multi-row or repeatable field structures need explicit modeling; avoid encoding all repeatable data as an opaque string unless legacy mapping and query needs justify it.

### Workflow context
- Workflow definition and version.
- Ordered step definitions and departmental assignments.
- Transition rules and event-driven state changes.
- Project state/history representation.
- Event-to-workflow impact must be a rule-driven association, not inferred from event-name text or UI labels.

### Candidate relational constraints (subject to validation)
- Tenant ownership should be enforced through FK paths or equivalent constraints so tenant-scoped child records cannot be attached to another tenant's parent.
- Event.ProjectID, if present, must reference a project within the same tenant.
- EventData.EventID and form-response EventID references must resolve to an event.
- Historical snapshots/version references should not be silently reassigned or deleted.
- Unique constraints should reflect the actual legacy uniqueness scope, not a guessed global uniqueness rule.

### Questions still open
- Is project code unique globally, per tenant, or only under some lifecycle/status rules?
- Are central information revisions immutable snapshots, or mixed mutable/history records?
- How are form versions frozen for historical event submissions?
- What exact structure represents repeatable fields, color/size breakdowns, or multi-row entries?
- Which event states are represented explicitly vs inferred from records?
- What are the actual deletion/reversal policies for projects, events, forms and workflow history?
- Which associations are direct FK references vs service-level payload links?


## 12. Focused conceptual notes — Tenant, identity and authorization (source-backed; provisional)

### Tenant and identity boundaries
- Treat Tenant as the top-level isolation boundary for memberships, role assignments, departments and business records, subject to confirmation of legacy ownership for every entity.
- Conceptually separate a global/identity User record from tenant-specific Membership, status, role assignments and organizational context. This is a V2 design proposal, not a claim that legacy source has a fully separate membership entity.
- User-to-tenant access should be explicit and revocable; requests must resolve tenant context from trusted server-side membership/session state, not accept arbitrary tenant IDs as authority.
- Candidate constraints: unique membership per tenant/user; tenant-scoped role assignment; membership and role must belong to the same tenant; disabled membership must not authorize requests. Exact unique scopes and lifecycle states need discovery.

### Roles and permissions
- Legacy source shows tenant-bound active role resolution and role-permission mapping; SYS_ADMIN/ADMIN bypass behavior is derived from active roles within tenant scope.
- Conceptual model: Tenant -> Roles; Permission catalog (whether global or tenant-specific pending validation); Role-Permission association; User-Membership -> Role assignment; Department assignment/context separately from role-based permission.
- Do not encode admin bypass as a broad unchecked shortcut. V2 should have an explicit, testable policy for privileged roles and tenant scope.
- Permission grants should be reconciled with resource-level visibility, department/project scope and special exceptions; role-level grants alone are not sufficient.
- Candidate constraints: association uniqueness; inactive/disabled role or membership blocks authorization; tenant-consistent assignment paths; audit role/permission changes.

### Sessions / credential lifecycle (V2 design)
- Legacy sessions are held in Script Properties using token-keyed records. V2 must not replicate raw bearer secrets as enumerable database keys or expose them in URLs/logs.
- Model session/token metadata, expiry, revocation, rotation and safe lookup; exact persistence strategy and concurrent-session policy remain open.
- Security requirements: secure token storage/verification, revocation checks, rate limits, audit attribution, redaction and explicit CSRF strategy if cookie-based transport is selected.

### Open identity questions
- Is the User identity globally unique or tenant-specific in legacy behavior?
- Can a user have simultaneous active memberships in multiple tenants?
- Are roles and permissions tenant-specific, globally catalogued, or mixed?
- How are department assignments represented and combined with permissions?
- What are account/membership/role disablement, session revocation and transfer semantics?
- Which privileged role codes are configurable and what is their exact policy scope?

## 13. Focused conceptual notes — Materials, procurement, allocation and receipts

### Domain concepts and relationship graph
- Project/material specification or calculated material requirement -> requirement/need lines (the exact legacy entity distinction must be preserved after source-to-target mapping).
- Purchase order -> order items; order items may link to a requirement and may be general/unallocated.
- Purchase-order item -> allocations to project/need context, plus receipt records; receipt records track quantities and source/replay context where present.
- Orders may contain shared/general items and allocations to multiple projects; do not model every order as belonging to exactly one project.

### Quantity integrity rules evidenced in source
- Order creation validates positive quantities, requirement visibility, tenant/project scope and allocation totals against order-item quantity; project-specific needs cannot be allocated to unrelated projects.
- Outstanding needs consider active allocations and unallocated order items; surplus/unallocated quantity is separately tracked.
- Receipt validation prevents cumulative receipt quantities exceeding ordered quantities; project-bound receipt quantity must stay within allocation limits. General/unallocated surplus is handled separately.
- Batch and single-receipt paths have differing validation/replay behavior in legacy code; V2 must define one unified contract and test it.
- Legacy order creation/receipt mutations use locking and idempotency patterns, but multiple sheet writes are not atomic. V2 should enforce transactionally quantity and allocation invariants, durable idempotency and reconciliation.

### Candidate database invariants (subject to detailed mapping)
- Tenant consistency for order, item, need, allocation, receipt and project references.
- Allocated quantity cannot exceed eligible order-item quantity; received quantity cannot exceed ordered quantity or valid project allocation; rules for reversals, returned materials, adjustments and unit conversion are not yet confirmed.
- General procurement must remain possible without a project FK; allocations create project associations as appropriate.
- Preserve audit trail and immutable receipt/reversal evidence rather than silently overwriting quantities.

### Open procurement questions
- Exact quantity/unit precision, conversion and rounding rules.
- Whether receipts can be edited, reversed, returned or corrected and how history is stored.
- Allocation lifecycle, deallocation and surplus-stock policy.
- Need recalculation semantics and what happens to outstanding allocations when project specs/events change.
- Purchase-order state transitions and recovery/NEEDS_REVIEW semantics.
- Whether legacy IDs need globally unique mapping or tenant-scoped keys.

## 14. Focused conceptual notes — File storage and audit

### File metadata and associations
- Separate relational metadata/association/lifecycle from file bytes in independent storage.
- Associations are polymorphic in legacy paths (project/event and potentially other resource types); V2 should replace unrestricted polymorphic pointers with explicit association tables or validated resource types, whichever best preserves the verified domain.
- Candidate metadata includes stable legacy mapping, tenant, object key/provider, filename/type/size, checksum, lifecycle state, timestamps and uploader; exact fields/retention policy require validation.
- Every upload/download/thumbnail/delete operation needs tenant/resource permission and validation. Object IDs or signed links alone must not grant broad access.
- Storage mutation and DB metadata must be coordinated via transactional intent/outbox and reconciliation; orphan and partial cleanup paths are required.

### Audit trail
- Conceptually preserve actor, tenant, action, resource reference, timestamp and safe contextual details. Do not store tokens, credentials or unnecessary sensitive payloads.
- Define append-only policy, redaction, retention, query indexes, access restrictions and export policy.
- Audit linkage for deleted/voided/amended records must remain stable; soft deletion or archival cannot silently erase audit evidence.
- Exact source audit fields, target polymorphism and retention are still pending detailed schema mapping.

### Open file/audit questions
- Supported association types and whether any are currently indirect or embedded in payloads.
- Legacy file identifiers, duplicate/orphan patterns, lifecycle and retention.
- Maximum size/type constraints, thumbnail generation and provider failure recovery.
- Audit detail payload, redaction policy, retention and administrative access policy.
- Required checksums and migration integrity strategy for file bytes.


## 15. Consolidated conceptual dependency and transaction map (review draft)

### Domain dependency sequence (logical, not a mandated implementation order)
1. **Tenant & identity:** Tenant, User/account, tenant membership, session, role, permission, department and scoped assignments.
2. **Configuration foundations:** workflow definitions/versions/steps/rules, event types, information types, form definitions/fields/versions, units and other validated reference catalogs.
3. **Project lifecycle:** project identity/status, workflow execution context, central-information current state and revision history.
4. **Operational events:** event/type/project (project optional), event data, response/snapshot, amendment/void state and workflow effects.
5. **Materials planning:** project/specification/calculation context and material requirement/need lines, with source-event trace where applicable.
6. **Procurement:** orders, items, allocation, receipt/quantity history and reconciliation.
7. **Supporting resources:** files/associations/lifecycle, audit trail and notification/outbox records coordinated with domain operations.
8. **Presentation/read models:** report projections, print templates/elements/bindings and derived views; these must not become competing sources of authoritative business data.

This sequence is a conceptual dependency map. It does not assert exact cardinality, table names, or that every domain must be deployed in this order.

### Transaction candidates and side-effect boundaries
| Operation | Candidate DB transaction contents | External/async work | Failure/recovery requirement |
|---|---|---|---|
| Create/activate project | Project + initial lifecycle/workflow state + central info/revision + audit | File copy or notifications via outbox | Idempotency, validate state and retry side effects |
| Register event | Event + data + form response snapshot + workflow/project effect + audit + notification intent | Provider notification, file/storage actions if required | Atomic domain mutation; replay protection; outbox retry |
| Amend form response | Amendment record/response revision + central-information synchronization + audit | Notifications or dependent recalculation if external | Conflict detection and idempotency; preserve historical snapshot |
| Void event | Void state + validated restoration/recalculation + audit | Material-planning reconciliation if asynchronous | Dependency guard; no unsafe state restoration; recovery log |
| Procurement order | Order + items + allocations + idempotency record + audit | None in critical transaction unless confirmed | Atomic constraints, retry/replay, consistent quantities |
| Receive items | Receipt records + quantity/status accounting + audit + idempotency | Notifications if required | Atomic batch semantics, prevent over-receipt, reversal history |
| Upload/attach file | Metadata + association + lifecycle intent + audit | Object upload/scan/thumbnail/storage | Outbox; checksum; cleanup/reconciliation; access checks |
| Delete/archive resource | Validated lifecycle/archive/dependency changes + audit + cleanup intent | Storage cleanup and external cleanup | Explicit policy; shared-resource protections; retryable cleanup |
| Notify | Durable outbox creation in originating transaction | Provider delivery and retry | Deduplication, backoff, dead-letter/review, observability |

Table is a design hypothesis; detailed source mapping and Product Owner decisions precede schema/implementation.

### Cross-domain invariants to validate and implement
- Tenant isolation is enforced consistently across every parent/child/reference path.
- Permission checks include tenant and resource scope; authenticated status alone is not authorization.
- Events may be project-independent; event names or UI labels must not drive state transitions.
- Workflow/form snapshots retain the configuration context required to interpret historical records.
- Revisions, amendments and voids are traceable and do not erase history silently.
- Procurement quantities, allocation and receipt invariants are enforced atomically, including concurrent requests.
- General procurement and shared allocations remain supported.
- External file/notification operations are coordinated with DB state through durable intent and reconciliation, not long-running DB transactions.
- Audit evidence is protected, safely redacted and traceable for both user and system actions.
- Derived reports/templates do not replace authoritative transactional records.

### Decisions/validation gate before executable schema
1. Source-to-target mapping and legacy ID/uniqueness scope for each domain.
2. Tenant ownership/membership/role/permission scope, including multi-tenant user behavior.
3. Entity cardinality, optionality, FK/compound constraints and polymorphic-link replacements.
4. Project/event/form/workflow snapshot, amendment, void, deletion/archive and recovery semantics.
5. Procurement units/precision/conversion, allocation, surplus, receipt, correction/return and status transitions.
6. File association types, storage metadata, access inheritance, integrity and lifecycle/retention.
7. Audit classification, redaction, retention, integrity and privileged access.
8. Query patterns and indexes, based on workload measurement—not guessed indexes.
9. Transaction boundaries, locking/concurrency, idempotency and outbox/reconciliation.
10. Migration mapping, staged rehearsal, counts/checksums, exception handling, rollback and cutover approval.

### Task 2.1 status and remaining work
- Conceptual entity clusters, relationship matrix, focused domain notes, candidate transaction boundaries and validation checklist are consolidated.
- Still pending detailed source-to-target field/ID mapping, exhaustive cardinality/uniqueness validation, data-volume/query baseline, business decisions listed above and Product Owner review.
- Task 2.1 is not complete and no SQL schema is authorized by this draft.


## 16. Source-to-target mapping checkpoint — Project, Workflow, Event and Forms

**Evidence source:** read-only fetch of legacy backend `DatabaseSetup.js` from `PRSPRO2BACKEND-PRODUCTION` on 2026-11-22. This is a sheet-header inventory, not validation of data contents or all service semantics. Existing repository remains read-only.

### Project and lifecycle source tables
| Legacy sheet | Header inventory | V2 conceptual target / cautions |
|---|---|---|
| 08_Projects | ID, TenantID, ProjectCode, Title, Description, WorkflowID, WorkflowVersion, CurrentStepID, Status, CreatedBy, CreatedAt, UpdatedAt, Quantity, AvailableDepartmentIDs, AllowedUserIDs, NotifyEnabled, CurrentStepStatus, SourceProjectID, RecordState, ReferencePolicyJSON, ReferenceCopyStatusJSON, ReferenceCopyCompletedAt, ReferenceCopyOperationID | Project + lifecycle/workflow context + access/availability policy + reference-copy tracking; JSON/policy normalization and historical semantics pending |
| 18_ProjectStatuses | ID, TenantID, Code, Name, Status, CreatedAt, UpdatedAt | Tenant-scoped status catalog; uniqueness and transition rules pending |
| 28_InformationTypes | ID, TenantID, Code, Name, Description, Cardinality, DataType, ValidationRuleJSON, OptionsJSON, Status, Revision, CreatedBy, CreatedAt, UpdatedAt, ShowOnCard | Information-type definition/versioning; immutable revision semantics and cardinality meaning need validation |
| 29_InformationTypeColumns | ID, TenantID, InformationTypeID, Code, Name, DataType, IsRequired, OptionsJSON, ValidationRuleJSON, ColumnOrder, Status, CreatedAt, UpdatedAt | Definition columns/fields; identify constraints and snapshot behavior |
| 30_ProjectInformation | ID, TenantID, ProjectID, InformationTypeID, ValueJSON, Revision, Status, SourceEventID, CreatedBy, UpdatedBy, CreatedAt, UpdatedAt | Current/project information values, revision, provenance; JSON shape and concurrency policy pending |
| 31_ProjectInformationHistory | ID, TenantID, ProjectInformationID, ProjectID, InformationTypeID, Revision, Action, OldValueJSON, NewValueJSON, SourceEventID, Reason, ActorUserID, CreatedAt | Historical changes; preserve provenance, actor and revision ordering |
| 38_ProjectReferenceSyncLog | ID, TenantID, SourceProjectID, TargetProjectID, InformationTypeID, Policy, SourceRevision, TargetRevision, Action, ConflictJSON, IdempotencyKey, ActorUserID, CreatedAt | Reference-copy/sync operations and conflict/idempotency history; mapping and ownership semantics pending |

### Workflow source tables
| Legacy sheet | Header inventory | V2 conceptual target / cautions |
|---|---|---|
| 09_Workflows | ID, TenantID, Name, Code, Version, Status, CreatedAt, UpdatedAt, WorkflowType, LifecycleState, PublishedVersion, DraftVersion, RepeatReferencePolicyJSON, PublishedRepeatReferencePolicyJSON | Workflow definition and draft/published version state; decide immutable published versions and active references |
| 10_WorkflowSteps | ID, TenantID, WorkflowID, Name, Code, StepOrder, Status, CreatedAt, UpdatedAt, WorkflowVersion | Version-bound step definitions; verify ordering uniqueness and version FK |
| 17_WorkflowStepDepartments | ID, TenantID, WorkflowID, WorkflowStepID, DepartmentID, Status, CreatedAt, UpdatedAt | Step/department assignment; validate version relationship and tenant integrity |
| 37_WorkflowStepEventRules | ID, TenantID, WorkflowID, WorkflowVersion, WorkflowStepID, EventTypeID, RequirementMode, DataSource, SequenceMode, GroupKey, GroupOrder, EventOrder, DisplayOrder, Status, CreatedAt, UpdatedAt, AllowRepeat | Event-driven step rules; rule semantics and sequence/group behavior need detailed source mapping |

### Event source tables
| Legacy sheet | Header inventory | V2 conceptual target / cautions |
|---|---|---|
| 11_EventTypes | ID, TenantID, Name, Code, Version, Description, DepartmentID, Category, WorkflowStepID, MovesProjectToStep, AllowAttachments, Status, CreatedAt, UpdatedAt, ConnectionType, TargetDepartmentID, TargetProjectStatus, AllowedDepartmentIDs, AllowedUserIDs, AvailableDepartmentIDs, AllowVoid, TrackVariantQuantity, TargetStepStatus, IsWorkCompletion, AllowOnDraft, IsFormAmendment | Event type definition/version and workflow/permission effects; JSON/list fields and historical snapshot policy pending |
| 12_EventFields | ID, TenantID, EventTypeID, Name, Code, FieldType, IsRequired, DefaultValue, Options, ValidationRule, FieldOrder, Status, CreatedAt, UpdatedAt | Event type field definitions; confirm definition version/snapshot behavior and field typing |
| 13_Events | ID, TenantID, ProjectID, WorkflowID, WorkflowVersion, WorkflowStepID, EventTypeID, EventTypeVersion, PerformedBy, CreatedBy, DepartmentID, OccurredAt, CreatedAt, Description, Status, Color, Size, VariantQuantity, PreviousStepID, PreviousProjectStatus, PreviousAvailableDepartmentIDs, PreviousSnapshotCaptured, VariantDetailsJSON, PreviousCurrentStepStatus, IdempotencyKey, additional trailing headers pending full review | Event plus workflow context, state restoration snapshots, idempotency, variant details; confirm exact complete header list, lifecycle and semantics. ProjectID may be nullable |
| 14_EventData | ID, EventID, DataJSON, CreatedAt | Event payload/value storage; shape, multiple rows vs JSON and immutability need mapping |

### Form and response source tables
| Legacy sheet | Header inventory | V2 conceptual target / cautions |
|---|---|---|
| 32_Forms | ID, TenantID, Code, Name, Description, Version, Status, CreatedBy, CreatedAt, UpdatedAt | Form definition/version; determine publication and historical immutability |
| 33_FormFields | ID, TenantID, FormID, InformationTypeID, FieldOrder, GroupName, LabelOverride, HelpText, IsRequired, VisibilityRuleJSON, Status, CreatedAt, UpdatedAt | Form-field association and presentation/visibility rules; avoid duplicating shared information-type definitions unnecessarily |
| 34_EventTypeForms | ID, TenantID, EventTypeID, FormID, IsRequired, FieldOrder, Status, CreatedBy, CreatedAt, UpdatedAt | Event type/form association; verify selection/ordering and effective version behavior |
| 35_EventFormData | ID, TenantID, EventID, FormID, FormVersion, ResponseJSON, DefinitionSnapshotJSON, CreatedBy, CreatedAt | Submitted form response and definition snapshot; preserve frozen historical interpretation and amendment policy |

### Read-only mapping observations and cautions
- Headers indicate JSON-bearing columns for configuration, policy, value payloads and snapshots; the relational target should not blindly flatten or blindly preserve every JSON column. Use normalized relations where integrity/query behavior requires them and JSON for bounded snapshot/payload structures when justified.
- Several project/event access policy fields are serialized lists or JSON; access semantics must be reconstructed from service logic, not guessed from headers.
- Workflow draft/published fields, versions and event snapshots suggest that historic configuration interpretation must be preserved. Confirm source semantics before selecting version tables and constraints.
- `13_Events` header extraction was truncated at a trailing field in this inventory pass. The complete header list and relevant mutation/read paths must be verified in a subsequent read-only pass; no field-level mapping is claimed complete.
- This checkpoint does not extract actual records, sample production data, passwords, secrets or credentials.

### Pending next source mapping areas
- Material planning and procurement, including exact quantity/reversal semantics.
- Identity/role/permission sheets and service authorization paths.
- Attachments and audit details, plus print templates.
- Full event headers and exact service paths for project information/history and form response amendment.


## 17. Source-to-target mapping checkpoint — Material Planning & Procurement

**Evidence:** Read-only inventory of legacy `DatabaseSetup.js` headers and targeted review of `ProcurementService.js` in `PRSPRO2BACKEND-PRODUCTION` on 2026-11-22. This records source structure and limited observed service behavior; it is not a record-level data audit.

### Source inventory
| Legacy sheet | Header fields | Provisional V2 target and validation concerns |
|---|---|---|
| 23_EventTypeProcurementSettings | ID, TenantID, EventTypeID, Action, FieldMapJSON, Status, CreatedBy, CreatedAt, UpdatedAt, ConfirmedQuantityInformationTypeID | Event-type procurement action/configuration mapping; field mapping and quantity confirmation semantics pending |
| 18_MaterialNeeds | ID, TenantID, ProjectID, SourceEventID, ItemName, ItemCode, Unit, RequestedQuantity, NeededBy, Status, Notes, CreatedBy, CreatedAt, UpdatedAt, MaterialSpecID, MaterialSpecKey, SpecificationRevision, CalculationID, BasisQuantity, NeedType, CalculationKey | Material need/request, source and calculation linkage; determine whether source event optional, key scope and lifecycle |
| 24_ProjectMaterialSpecs | ID, TenantID, ProjectID, SpecKey, ItemName, ItemCode, Unit, ConsumptionPerUnit, WastagePercent, EarlyOrderPolicy, EarlyOrderQuantity, Status, Revision, Notes, CreatedBy, CreatedAt, UpdatedAt | Project material specification/revision; precision, policy values, revision history and key uniqueness pending |
| 25_MaterialNeedCalculations | ID, TenantID, ProjectID, MaterialSpecID, MaterialNeedID, SourceEventID, SourceEventVersion, CalculationKey, BasisQuantity, ConsumptionPerUnit, WastagePercent, CalculatedQuantity, PreviousCalculatedQuantity, VarianceQuantity, CalculationType, Status, CreatedBy, CreatedAt | Calculation provenance; repeat/recalculation rules and immutable history need validation |
| 19_PurchaseOrders | ID, TenantID, OrderNumber, SupplierName, OrderDate, ExpectedDeliveryDate, Status, Notes, CreatedBy, CreatedAt, UpdatedAt, IdempotencyKey | Purchase order header, supplier/date/state and idempotency; order-number scope and state transitions pending |
| 20_PurchaseOrderItems | ID, TenantID, PurchaseOrderID, MaterialNeedID, ItemName, ItemCode, Unit, OrderedQuantity, UnitPrice, Notes, CreatedAt, UpdatedAt, SurplusQuantity, SurplusCost | Order item and possibly surplus accounting; unit/price precision, item-level need linkage and surplus rules pending |
| 21_PurchaseAllocations | ID, TenantID, PurchaseOrderItemID, ProjectID, MaterialNeedID, AllocatedQuantity, CreatedAt | Item-to-project/need allocation; allocation totals, multi-project/general order eligibility and reallocation policy pending |
| 22_MaterialReceipts | ID, TenantID, PurchaseOrderItemID, ProjectID, QuantityReceived, ReceivedAt, WarehouseDepartmentID, SourceEventID, Notes, ReceivedBy, CreatedAt, IdempotencyKey | Receipt ledger entry, optionally project/event/dept associated; correction/return semantics and limits pending |

### Limited observed service behavior
- `ProcurementService` exposes order creation, single receipt, batch receipt, needs listing, orders listing, workspace/report/dashboard paths.
- Order creation and single receipt use a Script Lock in the inspected code; batch receipt also uses a lock. V2 needs database transactions and appropriate locking/constraints instead of a process-global lock.
- Order creation checks for an existing order by tenant and idempotency key and returns an idempotent replay result when found.
- Single receipt checks idempotency key for existing receipts before proceeding.
- Receipt batch logic builds accounting views by item/project from existing receipt and allocation rows; the complete batch validation logic and all quantity semantics still require further inspection.
- Project and source-event validation in the inspected helper checks tenant and project visibility; project may be omitted in relevant contexts. V2 authorization should be explicit at service/API level and validated against trusted context.
- Needs listing combines needs, order items/orders, allocations and receipts; cancelled/void/deleted/needs-review order states are excluded from a countable-item calculation in the inspected excerpt. Full status accounting is pending.

### Integrity and transaction candidates
1. Need/spec/calculation provenance consistency and tenant consistency.
2. Ordered quantity and unit/precision validation.
3. Allocation quantity cannot exceed eligible quantity under a confirmed business policy; no assumption made yet about whether surplus is allocatable.
4. Receipts must reconcile against item quantities and project allocations, while preserving partial receipts and any approved surplus path.
5. Concurrent order/allocation/receipt writes require DB transactions, constraints, locks appropriate to isolation, and idempotency.
6. Corrections, returns, cancellations and reallocation should be modeled explicitly; no destructive rewriting of receipt history presumed.
7. General orders and multi-project allocation capability are noted from prior read-only audit; exact rules remain to be confirmed.

### Open business and technical questions (not decided)
- Units, decimal precision and conversion rules.
- Whether receipt quantity is limited to ordered, allocated, or separate surplus allowance for each path.
- Surplus ownership and project allocation eligibility.
- Returns, correction and reversal representation.
- Allocation changes after order or receipt and status recalculation rules.
- Need recalculation, superseding calculations and history retention.
- Order/need/receipt status definitions and transitions.
- Uniqueness scopes for item codes, calculation keys, order numbers and idempotency keys.

No schema, SQL, migration, live-data extraction or production modification was performed.


## 18. Source-to-target mapping checkpoint — Identity, Tenant and Authorization

**Evidence:** Read-only source review of legacy `DatabaseSetup.js`, `AccessControlService.js`, `PermissionService.js`, `AuthService.js`, selected repositories and Router excerpts in `PRSPRO2BACKEND-PRODUCTION`, 2026-11-22. Header-level mapping and targeted logic observations only; not a comprehensive security audit.

### Source tables and provisional mapping
| Legacy sheet | Header fields | Provisional V2 target / cautions |
|---|---|---|
| 01_Tenants | ID, Name, Code, Timezone, Status, CreatedAt, UpdatedAt | Tenant boundary; code uniqueness and tenant lifecycle pending |
| 02_Users | ID, TenantID, Name, Username, PasswordHash, DepartmentID, Status, CreatedAt, UpdatedAt | User/account, tenant relationship, password hash, primary department; global identity vs tenant-scoped username unresolved |
| 03_Departments | ID, TenantID, Name, Code, Status, CreatedAt, UpdatedAt | Tenant-scoped department; code uniqueness and multi-department membership behavior pending |
| 04_Roles | ID, TenantID, Name, Code, Status, CreatedAt, UpdatedAt | Tenant role; scope and privileged role semantics pending |
| 05_Permissions | ID, Code, Name, Module, Description, Status | Permission catalog; source appears catalog-wide, scope and versioning pending |
| 06_UserRoles | ID, TenantID, UserID, RoleID, CreatedAt | User-to-role assignments; tenant consistency constraints required |
| 07_RolePermissions | ID, TenantID, RoleID, PermissionID, CreatedAt | Role-to-permission assignments; tenant consistency and uniqueness required |

### Observed legacy authorization behavior (limited excerpts)
- Authentication retrieves the current session/token context through AuthService; AccessControlService calls AuthService.me and rejects missing/invalid user context.
- The legacy user record includes TenantID and DepartmentID. Department lookup validates tenant and active status.
- UserRole rows are filtered by user and tenant. Roles are filtered by tenant and active status.
- Admin detection recognizes active tenant roles with codes SYS_ADMIN or ADMIN (observed in AccessControlService).
- Permission resolution combines active roles, tenant-scoped UserRoles and RolePermissions; Permission catalog records are read globally in the observed path.
- Router.requireAny checks authenticated user, allows admin path, otherwise checks any supplied permission code.
- AccessControlService.enforceRouteAccess, in the inspected excerpt, authenticates but returns true after noting operational checks occur in Router/services. This reinforces the need for explicit authorization at each V2 endpoint and resource-level policy.
- Login path in AuthService checks matching username records and fails closed unless exactly one matching record exists; source comments note usernames may repeat across tenants and tenant selector is not supplied in this path.
- Request-level caching is present in AccessControlService and PermissionService excerpts. V2 cache design must not leak authorization decisions across tenants, users, resource scopes or sessions and should invalidate safely.

### V2 security architecture requirements (design candidates, not implementation)
1. Resolve tenant context from authenticated membership/session and never trust a tenant ID supplied only by the client.
2. Decide whether identity is global with tenant memberships or tenant-scoped; support explicit tenant selection if membership can span tenants.
3. Enforce compound tenant constraints on membership, department, role, role-permission and resource relationships.
4. Centralize authorization policy with explicit endpoint and resource-level checks; no broad admin shortcut without documented scope.
5. Require active membership/account, session expiry/revocation, password policy and secure credential hashing; avoid legacy hash compatibility assumptions until migration assessment.
6. Use secure session storage, rotation/revocation, rate limiting, anti-brute-force controls, CSRF policy for cookie sessions, and redacted logs.
7. Audit privilege changes, membership changes, permission changes, session lifecycle and sensitive access with retention/export safeguards.
8. Design authorization tests for cross-tenant access, inactive users/roles/departments, role assignment, missing permissions, privilege escalation and object-level access.

### Open policy decisions
- Global user identity versus tenant-specific user identities and usernames.
- Users with multiple tenant memberships and tenant switching behavior.
- Role and permission scope, tenant/system roles and any exceptional administrator privileges.
- Department assignment cardinality (single department or membership list) and department-based visibility rules.
- Account/membership disable, termination, session invalidation and audit retention semantics.
- Password/session migration and legacy hash conversion/forced reset strategy.
- Authorization matrix for resource access and department/project restrictions.

No credential values, token values or user data were extracted. No executable schema, migration or production modification performed.


## 19. Source-to-target mapping checkpoint — Attachments, Audit, Print Templates and Integrations (2026-11-22)

**Evidence:** Read-only header/configuration inspection of `DatabaseSetup.js` in `PRSPRO2BACKEND-PRODUCTION`. This checkpoint is header-level and config evidence, not a full behavioral review. No Drive files, audit rows, templates, Telegram configuration or secrets were accessed.

### Source structures observed

| Legacy sheet/config | Header or observed configuration | Provisional target and cautions |
|---|---|---|
| `15_Attachments` | `ID, TenantID, EntityType, EntityID, FileName, MimeType, FileSize, StorageProvider, StorageKey, UploadedBy, UploadedAt, Status, AttachmentType, SourceAttachmentID` | File metadata/attachment association; `EntityType` observed allowed values `PROJECT`, `EVENT`; provider configuration observed `GOOGLE_DRIVE`; status values `ACTIVE`, `DELETED`. Validate lifecycle, uniqueness, association semantics, storage access and deletion behavior against service logic. |
| `16_AuditLogs` | `ID, TenantID, UserID, Action, EntityType, EntityID, OldData, NewData, Reason, CreatedAt` | Append-oriented audit domain candidate. Validate event coverage, JSON/data encoding, retention, redaction, immutability, actor handling, and whether audits are transactionally coupled to mutations. |
| `36_PrintTemplates` | `ID, TenantID, Code, Name, Description, Status, Version, DefinitionJSON, CreatedBy, CreatedAt, UpdatedBy, UpdatedAt` | Tenant-scoped template definition/versioning candidate; preserve definition and version lineage. Validate template payload structure, revision semantics, rendering dependencies and version retention. |
| Attachment config | `ATTACHMENT_ENTITY_TYPES: ['PROJECT','EVENT']`; `STORAGE_PROVIDERS: ['GOOGLE_DRIVE']` | These are observed legacy config values, not necessarily V2 restrictions. V2 storage must be provider-independent. |
| Permission catalog excerpts | `ATTACHMENT_VIEW`, `ATTACHMENT_UPLOAD`, `ATTACHMENT_DELETE`, `ATTACHMENT_MANAGE`, `AUDIT_VIEW`, `REPORT_VIEW`, `SYSTEM_SETTINGS` | Permission names observed in setup/catalog excerpt; endpoint and resource-level enforcement must be mapped from complete source behavior, not inferred from names. |

### Integration evidence and limits
- Google Drive is the configured legacy attachment provider in the inspected setup. V2 should use a storage abstraction and not retain a dependency on Drive IDs, Drive permissions or Apps Script credentials.
- Telegram notification integration was documented in prior source-audit notes, but this checkpoint did not inspect live configuration or token/chat values. Provider delivery, webhook behavior, retry/idempotency, tenant mapping, notification event triggers and credential handling require further source review.
- Repository file discovery/code-search requests did not return usable matches for dedicated attachment/audit/template/integration service files during this checkpoint. `DatabaseSetup.js` was successfully fetched and inspected; exact service behavior is therefore not asserted here.

### Candidate V2 design requirements (not final schema)
1. Attachment records retain tenant/resource association, uploader, timestamps, source lineage where relevant, validated metadata and integrity checksums; storage object operations are external side effects coordinated through an outbox/reconciliation pattern.
2. Enforce authorization for upload, retrieval, download, preview, deletion and association at resource level; do not expose storage keys or rely on provider permissions as application authorization.
3. Define upload limits, MIME/content validation, safe filenames, quarantine/scan policy, lifecycle/soft-delete and retention, orphan detection, and recovery before implementation.
4. Audit records should be structured, append-oriented and redact sensitive fields; define coverage, retention, access controls, integrity and export rules. Avoid silently coupling audit availability to unsafe partial business mutations; decide transactional/outbox semantics.
5. Print-template definitions need validation/versioning, tenant isolation, deterministic rendering inputs and controlled compatibility behavior; preserve historic template references where records depend on them.
6. Integration delivery should be provider-abstracted, secret-managed, tenant-scoped, retryable, deduplicated, observable and recoverable; record delivery state without exposing credentials or sensitive payloads.

### Open validation questions
- Attachment access policy, allowed resource types, multi-file behavior, size/type limits, previews, download semantics, storage-key uniqueness, delete/restore and copy/reference lineage.
- Audit action coverage, `OldData`/`NewData` shape, sensitive-field redaction, append-only guarantees, retention and actor/resource semantics.
- Print definition JSON structure, supported element types, data binding semantics, rendering pipeline, revision/compatibility and permissions.
- Telegram/integration trigger rules, retries, idempotency, tenant-chat mapping, secrets rotation and failure/recovery handling.
- Workload/volume estimates, retention, storage provider choice and performance targets.

No schema, SQL, migration, live data, secret/config value, file content or production modification was performed.


## 20. Task 2.1 — Consolidated review package (2026-11-22)

### Model boundary and relationship summary
Dependency clusters: (1) Tenant/account/membership/role/permission/department/session; (2) configuration catalogs including workflow versions/steps/rules, event types/fields, information types/columns and form definitions/fields; (3) Project identity/status/workflow execution, central-information values/revisions and reference-project synchronization; (4) Events with optional project link, event payloads, form responses/snapshots, amendment/void lifecycle and workflow effects; (5) material specifications, calculation provenance and material needs; (6) purchase-order header/items, project/need allocations and receipts/reversal history; (7) attachment metadata/association/lifecycle separate from storage bytes; (8) audit and notification/outbox; and (9) print templates, resolver bindings and reporting projections.

### Relationship rules supported by source audit
- Tenant/resource scope must be validated across parent and child references. Preserve global case-insensitive username uniqueness as the observed current policy.
- Project selects a workflow context; project-linked events reference ProjectID, but project-independent event types and procurement must remain possible.
- Event payload and form response are separate structures; responses retain form/version/definition snapshots. Amendment is tied to a same-project amendment event, version checks, stale-write rejection and idempotent replay/conflict behavior.
- Workflow rules are version-specific; event completion/transition depends on rule configuration and step context, not event-name heuristics.
- Project information has revision/history, approval and sync behavior. LIVE_SYNC, INITIAL_COPY and INDEPENDENT policies are configured from workflow repeat-project policy; source/target conflict resolution is revision-aware. Copy operations use operation identity/state for replay/recovery.
- Material spec revisions feed calculation ledger records with source event/version, basis quantity, variance and replay keys; generated needs are reconciled, not overwritten blindly.
- Procurement header, items, allocations and receipts are separate. Shared order items can be allocated across projects. Preserve surplus/unallocated quantities and prevent quantity over-allocation/over-receipt. Cancellation and receipt corrections/returns remain policy gaps.
- File metadata and associations are permission-filtered; current legacy storage is Drive. Project deletion attempts metadata deletion then storage trash, but recovery retention is not established.
- Audit records have tenant/user/action/entity/time and old/new data in source, but exhaustive coverage and retention/immutability are not verified.
- Print-template resolver derives values on demand from project, central-information, event and event-form sources; it is not an authoritative project snapshot.

### Proposed V2 transaction and reliability boundaries (recommendations)
1. Project create: identity, workflow context, initial info/revision and audit; external file-copy/notification effects through durable jobs/outbox.
2. Event submit/transition: event, payload, response snapshots, workflow/project changes, audit and notification intent atomically where applicable.
3. Amendment: amendment identity, response revision, central-information synchronization, audit and idempotency state atomically.
4. Void: validated state restoration/recalculation, void record and audit; material reconciliation durable and retryable.
5. Procurement order: header/items/allocations/audit/idempotency in one transaction with tenant and quantity constraints.
6. Receipt/return/adjustment: append-only quantity records and reconciliation; never silently overwrite history.
7. File lifecycle: DB metadata intent and association with durable storage job, checksum/reconciliation, retries and cleanup; storage calls outside DB transaction.
8. Delete/archive: recoverable soft-delete/retention first, dependency preview and authorization, durable cleanup; hard purge only after policy-defined retention and checks.
9. Notifications: transactional outbox, deduplication, retry/backoff, dead-letter/review and safe observability.

### Risks and policy gates
- Legacy sheets do not provide FK/atomic transaction guarantees. Several operations are progressive and may leave partial state; V2 must use relational transactions and durable orchestration.
- Route permission audit is source-level sampling/inventory, not exhaustive method-by-method testing. V2 requires deny-by-default route policy, service-level authorization, tenant/resource scope and automated tests.
- Legacy password hashing is SHA-256 without observed salt/work factor; it is unsuitable as V2 storage design. Use modern hashing and controlled migration/reset. Login throttling was not observed in reviewed paths; add rate limiting and monitoring. Do not carry forward URL-token transport.
- Purchase order cancellation, receipt returns/corrections, attachment retention/recovery, cascade-delete recovery, audit coverage/retention, repeat-copy partial recovery and exact template binding contracts remain unresolved.
- No real-data/workload analysis has been done; indexes and performance targets need sanitized workload and baseline.
- IDs are legacy-prefixed strings; preserve mapping, but uniqueness scope/internal key strategy requires field-level mapping.
- Auxiliary backup sheet and deployed-sheet drift remain unverified.

### Recommended next task after acceptance
Task 2.2 — Detailed source-to-target field mapping and constraint matrix (still read-only): table/header inventory, normalized data types, nullable/required rules, uniqueness scope, tenant-composite keys, cardinality, snapshot policy, deletion/reversal behavior and legacy ID mapping. Only after this mapping and policy decisions are approved should executable schema/migration work begin.

### Acceptance gate
Ready for Product Owner review, not yet accepted or marked COMPLETE. Policies listed above must be decided or explicitly deferred with documented defaults before schema implementation. No SQL or migration was created.
