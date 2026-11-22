# Task 2.8 — Structured Legacy Entity Manifest Increment 14
Date: 2026-11-22
Source: Read-only extraction of the declared SHEETS registry/header definitions from production Backend DatabaseSetup.js. This is registry evidence only, not live database inspection. Names/headers are captured as registry entries; relationships below are candidate links inferred from field names and require consumer/source validation.

## Registry inventory (37 declarations)
| Sheet | Domain grouping | Candidate relationship/notes | Evidence status |
|---|---|---|---|
| 01_Tenants | Tenant | tenant root | Registry |
| 02_Users | Identity | TenantID, DepartmentID | Registry; relation inferred |
| 03_Departments | Identity/org | TenantID | Registry |
| 04_Roles | Identity/access | TenantID | Registry |
| 05_Permissions | Access catalog | potentially global | Registry; scope unresolved |
| 06_UserRoles | Access edges | UserID, RoleID, TenantID | Registry; relation inferred |
| 07_RolePermissions | Access edges | RoleID, PermissionID, TenantID | Registry; relation inferred |
| 08_Projects | Project | WorkflowID, CurrentStepID, SourceProjectID | Registry |
| 09_Workflows | Workflow | tenant, versioning fields | Registry |
| 10_WorkflowSteps | Workflow | WorkflowID, WorkflowVersion | Registry |
| 11_EventTypes | Event config | WorkflowStepID, DepartmentID | Registry |
| 12_EventFields | Event config | EventTypeID | Registry |
| 13_Events | Event | ProjectID nullable, Workflow and event-type versions | Registry |
| 14_EventData | Event payload | EventID, DataJSON | Registry |
| 15_Attachments | Files | polymorphic EntityType/EntityID; SourceAttachmentID | Registry |
| 16_AuditLogs | Audit | polymorphic EntityType/EntityID | Registry |
| 17_WorkflowStepDepartments | Workflow org | WorkflowID, WorkflowStepID, DepartmentID | Registry |
| 18_MaterialNeeds | Procurement/material | ProjectID, SourceEventID, MaterialSpecID, CalculationID | Registry |
| 18_ProjectStatuses | Project config | independent status catalog; prefix collision with MaterialNeeds | Registry |
| 19_PurchaseOrders | Procurement | tenant-level order | Registry |
| 20_PurchaseOrderItems | Procurement | PurchaseOrderID, MaterialNeedID | Registry |
| 21_PurchaseAllocations | Procurement | PurchaseOrderItemID, ProjectID, MaterialNeedID | Registry |
| 22_MaterialReceipts | Procurement | PurchaseOrderItemID, ProjectID, SourceEventID | Registry |
| 23_EventTypeProcurementSettings | Event/procurement integration | EventTypeID | Registry |
| 24_ProjectMaterialSpecs | Material specs | ProjectID, revision/spec key | Registry |
| 25_MaterialNeedCalculations | Material calculation | ProjectID, MaterialSpecID, MaterialNeedID, SourceEventID | Registry |
| 28_InformationTypes | Dynamic information config | tenant; revision | Registry |
| 29_InformationTypeColumns | Dynamic information config | InformationTypeID | Registry |
| 30_ProjectInformation | Project information | ProjectID, InformationTypeID, SourceEventID | Registry |
| 31_ProjectInformationHistory | History | ProjectInformationID, ProjectID, InformationTypeID, SourceEventID | Registry |
| 32_Forms | Form config | versioned | Registry |
| 33_FormFields | Form config | FormID, InformationTypeID | Registry |
| 34_EventTypeForms | Form/event mapping | EventTypeID, FormID | Registry |
| 35_EventFormData | Event response | EventID, FormID, FormVersion | Registry |
| 36_PrintTemplates | Print/template | DefinitionJSON, version | Registry |
| 37_WorkflowStepEventRules | Workflow rules | WorkflowID/version, step, EventTypeID | Registry |
| 38_ProjectReferenceSyncLog | Reference sync | SourceProjectID, TargetProjectID, InformationTypeID | Registry |

## Registry extraction notes
- 37 registry entries were parsed from DatabaseSetup.js, including 37 declared header arrays.
- Numeric prefix is not unique: 18_MaterialNeeds and 18_ProjectStatuses coexist. Prefixes are unsuitable as entity identifiers.
- Registry order and numeric prefixes do not imply domain ordering.
- Fields such as TenantID, ProjectID, EventID, WorkflowID, FormID and PurchaseOrderID indicate candidate edges only; constraints, optionality, cardinality, cascades and all consumer semantics remain unverified.
- Polymorphic edges explicitly visible in registry: Attachments EntityType/EntityID and AuditLogs EntityType/EntityID. Additional JSON/list fields may encode non-relational references.
- No live Sheets, row counts, values, data quality, header drift, runtime handlers or tests were examined/executed.

## Required next reconciliation
1. Crosswalk each entry to repository consumers/repositories/services and mutation/read paths.
2. Identify omitted/obsolete structures not represented in this registry declaration.
3. Resolve tenant/global classification and nullable relationship rules.
4. Map each candidate edge to source enforcement, tests and target PostgreSQL enforcement proposal.
5. Preserve unknown/partial classifications until evidence supports closure.

## Safety
Read-only registry extraction only. No production modification, live-data access, tests, DDL, migration, provisioning, deployment or VPS work.
