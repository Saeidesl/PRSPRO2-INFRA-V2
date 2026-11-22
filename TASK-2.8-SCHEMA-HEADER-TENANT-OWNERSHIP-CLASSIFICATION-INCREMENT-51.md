# Task 2.8 — Schema Header Tenant Ownership Classification (Increment 51)
Date: 2026-11-22
Status: read-only header audit of legacy DatabaseSetup.js; not DB constraint proof.

## Classification based on declared sheet headers
### Explicit TenantID in headers
The inspected APP_CONFIG.SHEETS definitions explicitly include TenantID for: Users, Departments, Roles, UserRoles, RolePermissions, Projects, Workflows, WorkflowSteps, EventTypes, EventFields, Events, InformationTypes, InformationTypeColumns, ProjectInformation, ProjectInformationHistory, Forms, FormFields, EventTypeForms, EventFormData, PrintTemplates, Attachments, AuditLogs, WorkflowStepEventRules, ProjectReferenceSyncLog, WorkflowStepDepartments, EventTypeProcurementSettings, MaterialNeeds, ProjectMaterialSpecs, MaterialNeedCalculations, PurchaseOrders, PurchaseOrderItems, PurchaseAllocations, MaterialReceipts and ProjectStatuses.

### Header sets without direct TenantID
- Tenants: primary tenant entity, not tenant-owned by another tenant.
- Permissions (05_Permissions): shared/global-looking catalog, scope requires explicit policy.
- EventData (14_EventData): ID, EventID, DataJSON, CreatedAt only. Ownership is represented through EventID and therefore relies on parent event linkage/authorization. V2 should explicitly model tenant-qualified ownership/constraints through the parent event.

## Key integrity interpretation
1. A TenantID header alone does not establish integrity: Google Sheets provides no relational FK/composite FK enforcement. Source/service checks and ID conventions cannot replace DB constraints.
2. EventData's missing TenantID may be a deliberate child-of-event normalization in legacy, but V2 must enforce EventID and tenant-consistent parent linkage, and must avoid independent tenant ambiguity.
3. Permission catalog appears global from headers, but this remains an unapproved policy classification.
4. Other polymorphic/derived relations may need owner registry or typed FK tables; header presence alone is insufficient.
5. Classification reflects the reviewed schema-definition source only and does not prove that all live sheets/headers match it.

## V2 schema-design implications
- Define tenant-qualified unique keys on tenant-owned parent rows and composite FK edges for tenant-owned relations where practical.
- EventData should have a tenant-safe FK to Event; tenant scope could be denormalized for composite enforcement or enforced via an alternate relational design. Avoid polymorphic loose ownership.
- Global permission registry should have documented global scope and separate tenant-scoped role associations.
- Tenant-owned audit, form, event, workflow, procurement, attachment and project records must be protected against cross-tenant direct and indirect reference.

## Evidence boundary
E1 header inspection only. No live database/header verification, runtime testing, DDL, migrations, production writes or schema readiness approval.
