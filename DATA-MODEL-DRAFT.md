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
