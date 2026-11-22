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
