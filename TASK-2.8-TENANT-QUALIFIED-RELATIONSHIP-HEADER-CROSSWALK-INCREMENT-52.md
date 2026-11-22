# Task 2.8 — Tenant-Qualified Relationship Header Crosswalk (Increment 52)
Date: 2026-11-22
Status: READ-ONLY header-level analysis; candidate enforcement edges, not verified runtime integrity.

## Purpose and source
Crosswalk the declared DatabaseSetup.js headers inspected in Increment 51 to identify relationships where both child and parent carry TenantID, and relationships where a child must derive ownership through a parent. This is structural evidence only. It does not establish live sheet contents, uniqueness, caller behavior or actual validation.

## Candidate tenant-qualified edges (header-derived)
| Child | Parent candidates | Child TenantID present? | Required V2 integrity direction |
|---|---|---|---|
| UserRoles | Users, Roles | Yes | tenant + user + role match; assignment uniqueness/revocation semantics separately discover |
| RolePermissions | Roles, Permissions | Yes | tenant + role match; Permission catalog likely global pending policy |
| WorkflowSteps | Workflows | Yes | tenant + workflow + version-context integrity |
| EventFields | EventTypes | Yes | tenant + event type |
| Events | Projects (nullable/independent scope policy pending), Workflows, WorkflowSteps, EventTypes, Departments, Users | Yes | tenant-consistent links and explicit independent/draft project rules |
| InformationTypeColumns | InformationTypes | Yes | tenant + information type |
| ProjectInformation | Projects, InformationTypes, SourceEvents | Yes | tenant-qualified relation and event/project ownership consistency |
| ProjectInformationHistory | ProjectInformation, Projects, InformationTypes, SourceEvents, actor User | Yes | tenant-qualified links; revision lineage consistency |
| FormFields | Forms, InformationTypes | Yes | tenant-qualified links |
| EventTypeForms | EventTypes, Forms | Yes | tenant-qualified association; uniqueness/order semantics pending |
| EventFormData | Events, Forms | Yes | tenant-qualified event/form links and immutable definition snapshot |
| WorkflowStepDepartments | Workflows, WorkflowSteps, Departments | Yes | same tenant; workflow/step/version semantics |
| WorkflowStepEventRules | Workflows, WorkflowSteps, EventTypes | Yes | tenant + workflow version/step + event-type consistency |
| ProjectReferenceSyncLog | Source/Target Projects, InformationTypes, actor | Yes | both projects in same tenant; preserve lineage and conflict state |
| EventTypeProcurementSettings | EventTypes, InformationTypes (confirmed quantity mapping) | Yes | tenant-safe configuration links |
| MaterialNeeds | Projects, SourceEvents, MaterialSpecs, Calculation | Yes | tenant-consistent project/event/spec/calculation and lifecycle linkage |
| ProjectMaterialSpecs | Projects | Yes | tenant + project; key/revision semantics |
| MaterialNeedCalculations | Projects, Specs, Needs, SourceEvents | Yes | tenant-qualified graph and provenance |
| PurchaseOrderItems | PurchaseOrders, MaterialNeeds | Yes | same tenant; nullable need linkage policy |
| PurchaseAllocations | OrderItems, Projects, MaterialNeeds | Yes | tenant-consistent graph, allocation invariants |
| MaterialReceipts | OrderItems, Projects, source events, department, actor | Yes | tenant-consistent graph; posted facts/reversal policy pending |
| Attachments | Project/Event polymorphic owner; uploader; source attachment | Yes | replace or constrain polymorphic ownership; tenant-safe owner resolution |
| AuditLogs | actor, polymorphic entity | Yes | tenant-safe typed/registry entity linkage; retention/redaction policy pending |

## Parent-derived or special cases
- EventData lacks TenantID; ownership must be derived from Event and constrained through the parent relationship.
- Permissions has no TenantID and appears to be a shared catalog; formal global classification is still pending acceptance.
- Tenants is the root tenant record, not tenant-owned by another tenant.
- Projects may have nullable/independent or draft semantics in sampled workflows; exact relation policy must not be inferred from header presence.
- Attachments and AuditLogs use polymorphic EntityType/EntityID patterns; headers alone cannot prove safe referential integrity.

## Candidate DB design safeguards (recommendations only)
1. Composite tenant-qualified keys/FKs on tenant-owned parent and child relations where feasible, not TenantID columns alone.
2. For EventData, FK through EventID plus a tenant-consistent relational design; avoid independent ambiguous ownership.
3. Typed attachment ownership or validated owner registry; do not retain unconstrained polymorphic IDs as the only integrity mechanism.
4. Audit entity links require a deliberate registry/typed association and preserve historical entities without destructive cascades.
5. Cross-tenant negative tests must exercise indirect graph edges, including procurement allocation/receipt, form/event, project information/history, files and report/export paths.
6. Explicitly classify nullable and global relationships before DDL.

## Evidence limitations
E1 only. Header declarations are not live-schema verification, runtime validation, FK/unique proof, or permission coverage. No tests were executed; no live data, DDL, migration, DB provisioning or production changes were performed.

## Follow-up
Prioritize targeted source trace for nullable ProjectID/independent Event semantics and polymorphic attachment/audit ownership, then systematically map these candidate edges to operation-level source evidence and isolated negative tests when an isolated runtime is available. G-10 remains OPEN.
