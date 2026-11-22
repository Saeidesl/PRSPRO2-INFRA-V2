# Task 2.8 — Relationship Edge & Tenant Enforcement Matrix (Increment 17)
Date: 2026-11-22
Scope: READ-ONLY source trace in legacy backend repositories/services/repositories. No live data.

## Evidence classes
- Direct source evidence: observed repository/service code.
- Candidate relation: logical relationship inferred from fields/repository calls; Sheets does not provide relational FK guarantees.
- Unverified: full call-site coverage, runtime behavior, live row consistency or transaction behavior not established.

## Relationship / enforcement matrix

| Edge / domain | Source evidence | Tenant / visibility enforcement observed | Integrity gap / V2 implication |
|---|---|---|---|
| Tenant -> User, Department, Role, UserRole, RolePermission | Registry fields carry TenantID for tenant-bound records; tenant-scoped role resolution observed in prior increments | Tenant-bound active role assignment/permission paths sampled | Require tenant-qualified unique keys/FKs and tenant consistency checks; permissions catalogue may be global, tenant mappings tenant-bound |
| Tenant -> Project | Projects include TenantID; ProjectService and selected services check project tenant and availability | Project visibility helper (admin/creator/department/user allow lists) sampled | Project child references should use tenant-qualified composite relations |
| Project -> Event | Event.ProjectID; Event repository filters by ProjectID; EventService validates project tenant/visibility in sampled paths | Sampled EventService/ProjectService validation | Event.ProjectID must be tenant-consistent; avoid cross-tenant parent linking |
| Event -> EventData | EventData is keyed by EventID in source mapping and EventService paths | Full event-data mutation/read authorization coverage not established here | FK/tenant-scoped join, cascade/archive policy, event snapshot preservation |
| Event -> EventFormData; Form -> FormFields; EventType -> EventTypeForms -> Form | Repository and service paths, snapshots/version metadata, amendments sampled | Tenant/project/amendment-event checks in selected FormService paths | tenant-qualified FKs; immutable response/amendment history; definition snapshot references |
| Project -> ProjectInformation -> History | ProjectInformationRepository reads by ProjectID; history by ProjectInformationID; ProjectInformationService _project checks tenant and availability, _type checks tenant | Selected service-level checks directly observed | Ensure project/type/value/history tenant congruence, revision uniqueness, append-only history contract |
| Source/Target Project -> ReferenceSyncLog | Repository filters SourceProjectID, TargetProjectID, InformationTypeID, optional TenantID; ProjectService sync paths sampled | Partial tenant/project visibility checks | Tenant-qualified edge constraints and explicit sync-log retention/cleanup policy |
| Workflow -> Steps -> StepDepartments / EventRules | workflow/step IDs and version context from repository/header and service sampling | Tenant-scoped management paths sampled, not exhaustive | enforce same-tenant workflow/step/department/type relationships; version-scoped rule consistency |
| Event -> EventType, WorkflowStep, User, Department, Project | event headers and service pathways carry these references | Route/service checks sampled; exhaustive integrity not established | composite tenant FK constraints and immutable historical snapshots where appropriate |
| Attachment -> PROJECT or EVENT owner | AttachmentRepository filters polymorphic EntityType/EntityID; AttachmentService validates tenant and typed entity; event attachment visibility follows project | Direct evidence for sampled visibility helper | Polymorphic owner cannot be a conventional single FK; V2 should use typed association tables or constrained owner model; prevent orphan files via lifecycle job |
| AuditLog -> polymorphic Entity | AuditRepository entity lookup is type/id; AuditService requires TenantID, Action, EntityType, EntityID and writes record; list tenant-filtered | Tenant filter observed on list; service calls sampled | Audit integrity is intentionally append-oriented; typed edge strategy or immutable entity reference registry, access policy, retention |
| MaterialNeed -> Project / Event / Spec / Calculation | Procurement/material-planning source pathways connect need to project/spec/event/calculation | Tenant/project validation sampled | tenant-qualified FK graph; define nullability for general needs and source event |
| PurchaseOrder -> Items -> Allocations/Receipts | Procurement repositories tenant-filter rows; service validates order/item/project/need graph, allocation/receipt totals in sampled paths | tenant/project visibility and validation sampled | enforce tenant-qualified joins, transactional creation and receipt posting, allocation/quantity invariants |
| Allocation / Receipt -> Project and Need | Receipt/allocation validation paths sampled including project-specific matching and general stock boundaries | sampled service checks | cross-project allocation prevention and explicit nullable semantics; reversal lineage |
| Project -> deletion dependencies | ProjectDeletionService collector samples child graph and safeguards shared orders/cross-project allocations | tenant/admin/source checks sampled; collector completeness and recovery not proven | dependency registry, transactional archive/delete, recovery, storage outbox; never cascade shared procurement facts |

## Tenant enforcement observations
1. Repository implementations frequently return broad sheet rows or use tenant filters only for procurement. Tenant integrity is predominantly enforced in service code and is not backed by database foreign keys in the legacy store.
2. Project visibility is a separate layer from tenant membership: selected helpers enforce tenant and creator/department/user availability.
3. Tenant ID on each row is not sufficient alone; child-parent tenant equality must be enforced on every edge and mutation, preferably through composite tenant-qualified constraints in V2.
4. Global catalog exceptions (e.g. permission catalogue) need explicit classification; don't force every entity into tenant-owned semantics.
5. Nullable and polymorphic links (general-stock procurement, optional event/project links, attachment/audit owner pointers) must be modeled deliberately rather than guessed.

## V2 candidate constraint strategy (design recommendations, not decisions)
- Tenant-bound parent/child tables: composite unique (TenantID, ID) on referenced parents and composite FK (TenantID, ParentID) where feasible.
- Tenant-scoped natural keys: define explicit scope and uniqueness only after policy confirmation.
- Global catalogs: explicitly tagged global and referenced through a separate policy rather than ambiguous TenantID nulls.
- Attachments: typed ownership association (or owner registry) plus storage object lifecycle and orphan reconciliation.
- Audit: immutable records, tenant-qualified actor/entity linkage where feasible, and controlled retention.
- Procurement: transaction boundaries, durable idempotency keys, allocation/receipt invariants and reversal facts.
- Project-information history: revision/sequence uniqueness and append-only records.
- Cross-project reference synchronization: explicit source/target tenant/project constraints and operation state.

## Limitations / status
- This matrix is a bounded source-level trace; it does not establish exhaustive relationship coverage, live-data consistency, schema types/cardinality, FK implementation or runtime enforcement.
- No tests run; no live data accessed; no SQL/DDL, migration, provisioning, deployment or production changes.
- G-10 and G-01 remain OPEN; G-02/G-03/G-04 remain OPEN. Schema readiness NOT APPROVED.
