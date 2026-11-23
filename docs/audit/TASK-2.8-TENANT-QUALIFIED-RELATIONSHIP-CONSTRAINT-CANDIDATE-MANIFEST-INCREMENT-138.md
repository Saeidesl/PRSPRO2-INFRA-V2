# Task 2.8 Increment 138 — Tenant-qualified relationship and constraint candidate manifest (G-10)

Date: 2026-11-23  
Evidence: E1 — DatabaseSetup header registry plus bounded service/repository traces. This is a candidate relationship manifest, not proof of existing constraints.

## Relationship candidates derived from headers/source
| Child / relationship family | Candidate parent(s) | Scope / integrity note |
|---|---|---|
| Users | Tenant, Department | Tenant-consistent department; tenant username policy and ID mapping |
| Departments, Roles | Tenant | Tenant-scoped code uniqueness candidate |
| UserRoles | Tenant, User, Role | Composite tenant-compatible relationship and uniqueness of user-role |
| RolePermissions | Tenant, Role, Permission | Tenant-role association; permission catalog scope must be decided (global or tenant-owned) |
| Projects | Tenant, Workflow, WorkflowVersion/Step, User creator, SourceProject, project status (status field/code semantics) | Project workflow/version and repeat-project lineage constraints; available/allowed ID arrays need policy/model |
| WorkflowSteps | Tenant, Workflow, WorkflowVersion | Step identity/order/code constraints by workflow/version |
| WorkflowStepDepartments | Tenant, Workflow, Step, Department | Validate same workflow/tenant/version context and uniqueness policy for active assignment |
| WorkflowStepEventRules | Tenant, Workflow, WorkflowVersion, Step, EventType | Composite version/tenant constraints and requirement/sequence uniqueness candidates |
| EventTypes | Tenant, Department, WorkflowStep, TargetDepartment, TargetProjectStatus, AmendableForms | Arrays of allowed/available users/departments require normalized link tables or typed policy model |
| EventFields | Tenant, EventType | Event-type field definition and historical snapshot context |
| Events | Tenant, Project (nullable for independent), Workflow/Version/Step, EventType/Version, PerformedBy/CreatedBy, Department, PreviousStep/Status snapshot | Independent event semantics; tenant-qualified links and snapshot lineage |
| EventData | Event | Parent-event relationship; tenant may be inherited rather than stored in registry |
| EventTypeForms | Tenant, EventType, Form | Composite uniqueness and tenant-qualified association |
| FormFields | Tenant, Form, InformationType | Field order and unique mapping constraints |
| EventFormData | Tenant, Event, Form, FormVersion | Response snapshot and replay/unique event-form rules |
| InformationTypeColumns | Tenant, InformationType | Unique code/order per type and tenant; historical column snapshots |
| ProjectInformation | Tenant, Project, InformationType, SourceEvent | Unique project/type active-value policy and revision constraint |
| ProjectInformationHistory | Tenant, ProjectInformation, Project, InformationType, SourceEvent, Actor | History retention and revision uniqueness; audit distinction |
| ProjectReferenceSyncLog | Tenant, SourceProject, TargetProject, InformationType, Actor | Cross-project integrity; conflict/idempotency and retention |
| ProjectMaterialSpecs | Tenant, Project, SpecKey, creator | Revision unique by project/spec key; active/latest selection semantics |
| MaterialNeeds | Tenant, Project, SourceEvent, Spec/Revision, Calculation | Need/calc linkage, status/supersession and key uniqueness |
| MaterialNeedCalculations | Tenant, Project, Spec, Need, SourceEvent/version | CalculationKey uniqueness and immutable input/provenance policy |
| PurchaseOrders | Tenant, creator; OrderNumber/idempotency key | Tenant-scoped order-number and idempotency constraints |
| PurchaseOrderItems | Tenant, Order, Need (nullable/unplanned) | Order/tenant consistency, surplus rules and item relationship |
| PurchaseAllocations | Tenant, OrderItem, Project, Need | Cross-project allocation, shared-order and quantity constraints |
| MaterialReceipts | Tenant, OrderItem, Project, WarehouseDepartment, SourceEvent, receiver | Receipt lineage, allocation limits, replay keys and correction policy |
| Attachments | Tenant, polymorphic EntityType/EntityID, uploader, source attachment | Replace polymorphic links with typed owner relations or explicit integrity policy; file lifecycle |
| AuditLogs | Tenant, User, polymorphic EntityType/EntityID | Actor/tenant constraints and append-only retention; domain audit coupling |
| PrintTemplates | Tenant, creator/updater, asset keys | Tenant-scoped code/version and object-storage asset ownership |

## Tenant and constraint design recommendations
1. Prefer composite tenant-qualified FKs (e.g., child TenantID + parent ID) or equivalent schema design where both records are tenant-owned, preventing cross-tenant link creation independently of service validation.
2. For global catalogs such as Permission, explicitly decide scope; use global FK where catalog is global and tenant-owned FK where it is tenant-specific.
3. Normalize ID arrays (AllowedDepartmentIDs/AllowedUserIDs/AvailableDepartmentIDs/Project access arrays) into relationship tables or typed policy tables where constraints and queryability matter.
4. Tenant-independent EventData may inherit tenant from Event; use FK and service query joins, but design RLS/authorization to prevent unscoped data access.
5. Polymorphic attachment/audit links cannot receive ordinary FK enforcement; use typed tables/owner relation, explicit registry or carefully documented validation + reconciliation.
6. Model nullable project association for independent events explicitly; avoid treating nullable FK as tenant bypass.
7. Define composite unique indexes for associations, active revision keys, idempotency scopes and business codes based on source-verified policy.
8. Add integrity/reconciliation tests for cross-tenant parent-child combinations and indirect joins; header evidence alone is not constraint proof.

## Source observations and limits
- The legacy registry contains 38 named definitions (some numeric prefixes are reused, including the two registry entries with prefix 18). This is a registry naming observation; no sheet rename or deployed data conclusion is made.
- Source service traces demonstrate selected tenant validations, but generic repositories are often ID-oriented and storage-level FK/unique enforcement is not established.
- This matrix is intentionally candidate-based; all relation/constraint candidates must be validated against full routes, business semantics and migration data before schema design approval.

## Readiness
G-10 remains BLOCKER. G-01 remains BLOCKER because full route/mutation/consumer mapping and exhaustive tenant relation verification remain incomplete. E1 only; no live data, SQL/DDL, migration, tests or production changes; schema readiness NOT APPROVED.
