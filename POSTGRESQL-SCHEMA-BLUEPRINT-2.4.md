# Phase 2 — Task 2.4: PostgreSQL Schema Blueprint (Design-only)

**Started:** 2026-11-22  
**Status:** IN PROGRESS — DESIGN DISCOVERY  
**Inputs:** Accepted Task 2.1 conceptual model, Task 2.2 source-to-target mapping, Task 2.3 field semantics/constraint validation.  
**Boundary:** Design-only. No SQL/DDL, migration files, database provisioning, live legacy data access, production changes, deployment or VPS action.

## Objective
Translate accepted discovery evidence into a coherent PostgreSQL logical schema blueprint: domain/table groupings, tenant-aware relationship strategy, key/constraint candidates, lifecycle/history modeling, transaction boundaries and unresolved decisions. This is a design proposal, not approved implementation.

## Design principles
1. Tenant boundaries must be represented and enforced at the DB/API boundary; evaluate tenant-scoped composite keys/FKs where they prevent cross-tenant relationships.
2. Preserve historical snapshots and version context for workflows, forms, event responses, information revisions, specifications/calculations and audit records where evidenced.
3. Use normalized relational structures for core entities and associations; use JSONB only for bounded snapshots/configuration or payloads whose structure is variable/versioned, with validation policy.
4. Model polymorphic attachment/audit references explicitly; do not pretend conventional FKs enforce polymorphic targets. Evaluate typed association tables or explicit owner/reference tables.
5. Define durable idempotency scopes and unique constraints; design transactions for multi-record operations and concurrency-sensitive aggregate limits.
6. Status transitions and reversal/void predicates must be explicit; preserve evidence uncertainty and do not invent legacy capabilities.
7. Separate core schema from external object storage. File metadata, storage keys, associations, retention and cleanup/outbox responsibilities must be modeled.
8. Candidate SQL types, precision, nullability and constraints are proposals pending domain and migration validation.

## Proposed work packages
- 2.4-A: Logical schema/domain inventory and table grouping.
- 2.4-B: Tenant-scoped key/FK strategy, association cardinalities and unique constraints.
- 2.4-C: Historical, snapshot, soft-delete, audit, polymorphic and storage models.
- 2.4-D: Transaction/idempotency/concurrency strategy, unresolved decision register and consistency review.

## Proposed domain groups (initial; names provisional)
- Tenant, user, membership, department, role, permission and association tables.
- Project, project-information current values/revisions/approvals and reference-sync/copy logs.
- Workflow definition/version/step/department assignment/event rules and project execution context.
- Event, event data, event types, form definitions/fields/associations, form response snapshots and amendment/void context.
- Material specification revisions, material needs, calculation records and reconciliation states.
- Procurement orders, items, allocations, receipts and explicit operational status/history where verified.
- Attachment metadata, typed associations, storage lifecycle/outbox.
- Audit records, print templates/versions/assets and integration configuration as confirmed.
- Operational idempotency and migration-run/validation records where appropriate.

## Decision gates / unresolved items
- Exact tenant composite-key implementation and global catalog scope.
- ID strategy and preservation/translation of legacy identifiers.
- User/password/session migration policy.
- Numeric precision, quantity units, currency and rounding policy.
- Timestamp/timezone and Jalali display-vs-storage policy.
- Exact uniqueness scopes for codes, keys, event responses, revision numbers and replay identifiers.
- Receipt VOID/reversal semantics and whether any unsupported mutation path exists.
- Attachment polymorphism strategy and object-storage provider.
- Audit retention, soft-delete/archive and recovery policy.
- Snapshot retention and event/form amendment representation.
- Migration source anomalies and live-data validation (deferred until separately approved).

## Deliverables
- This blueprint document, expanded with evidence-backed proposals.
- A schema decision register, separating proposed decisions, accepted legacy behavior, unknowns and business choices.
- Updated TODO.md and ARCHITECTURE.md.
- Comprehensive consistency review and presentation for Product Owner approval.

## Verification
Read back all changed documents from GitHub. No schema or migration tests are in scope; no such tests will be claimed. Task 2.4 will remain IMPLEMENTED/READY FOR REVIEW until explicit acceptance.


## Work package 2.4-A — Logical schema inventory and group boundaries

### A.1 Recommended database namespaces
**Proposal:** Keep one application database and organize entities by logical domains in naming/documentation (rather than separate databases), with schemas only if later operations/security needs justify them. Candidate domain grouping:
- identity & access
- projects
- workflow
- events & forms
- information/configuration
- materials & procurement
- files
- audit & operations
- print/reporting
This avoids premature complexity and permits cross-domain relational integrity.

### A.2 Logical entity inventory (candidate table families)
Names are conceptual and not SQL names. Presence is source-evidenced or a design candidate; implementation/cardinality must be validated.

**Identity and tenant**
- tenants; users/accounts; tenant_memberships (if account identity is global); departments; roles; permissions (catalog); role_permissions; user_roles; user_department assignments.
- Candidate separation: globally unique user identity vs tenant-specific membership and role assignment. Unknown: current uniqueness and account-sharing policy.

**Projects**
- projects (tenant-owned, status/workflow context, code and lifecycle fields).
- project_status_catalog or tenant status configuration (only if source behavior warrants a configurable catalog).
- project_information_types; information_type_columns/configuration; project_information current-value records; project_information_history/revisions.
- project reference-copy/synchronization operation records, per-item status and source revision, to preserve recovery/idempotency evidence.
- project deletion/recovery records or deletion-state fields; final retention policy unresolved.

**Workflow**
- workflows; workflow_versions; workflow_steps; workflow_step_department associations.
- workflow_step_event_rules (version-bound where possible).
- workflow/project execution state and transition history (shape needs further validation).
- Published versions should be immutable; edits should create new version records rather than alter a version already used by projects/events (proposal based on observed version context).

**Events and dynamic forms**
- event_types and their version/configuration history (exact persistence strategy TBD).
- events (ProjectID nullable to support independent events; tenant ownership still mandatory).
- event_data / structured event payload or field-value rows (choose after payload/query needs are assessed).
- forms; form_versions; form_fields/field associations; event-type-form links.
- event form responses and immutable snapshots where needed.
- amendment/revision records and void lifecycle metadata, subject to confirmed semantics.
- event attachments through typed associations.

**Information configuration**
- information types and column/configuration entities; field options/validation/visibility configuration as versioned JSON only where structurally variable.
- active current values vs revision/history must be distinct where legacy behavior supports them.

**Materials and procurement**
- material specifications and revisions, project-scoped spec keys, consumption/wastage parameters.
- material need records; calculation provenance/history and reconciliation status.
- purchase orders (support project-linked and general orders without fabricated project IDs); purchase order items; allocations; receipt/delivery records.
- order/item/allocation/receipt history or audit trail, based on confirmed lifecycle behavior.
- units/codes catalog only if a stable shared catalog is confirmed.

**Files**
- file/object metadata and typed association records (project/event/form/template etc.); storage provider/key; integrity/size/type metadata; upload and deletion states.
- external bytes remain outside PostgreSQL. A typed association approach is preferred to a free-form polymorphic FK because ordinary FK integrity is otherwise unavailable.

**Audit, operational reliability and integrations**
- audit records with actor, tenant, action, target reference and bounded before/after snapshots; redact secrets and sensitive values.
- integration/provider settings metadata, secret references only (secret values external).
- notification configuration/delivery records if confirmed scope; transactional outbox and retry state.
- idempotency records for operations whose retry safety is important, scoped by tenant, operation and key with request fingerprint/result reference.
- migration run/validation ledger belongs to migration tooling and operational metadata; avoid mixing migration staging into live domain tables unless necessary.

**Printing/reporting**
- print templates and version/definition; template assets or typed file association.
- resolver bindings are definitions, not cached project truth; generated outputs are optional retained artifacts and policy must be decided.
- reporting views/projections are derived and must not become authoritative domain write tables without explicit rationale.

### A.3 Cross-domain ownership boundaries
- Tenant is the ownership/security root.
- Project is an aggregate root for project-centric lifecycle, but event/material/procurement/file interactions can cross aggregate boundaries and should use explicit transactional service operations.
- Workflow version and form/template version/config snapshots should be referenced from records where evidence supports snapshot semantics.
- Event is a record of occurrence and status; form responses and dynamic payloads remain associated rather than embedded indiscriminately.
- Purchase order is not necessarily project-owned; allocation expresses the project/need association.
- Attachment metadata and typed associations are distinct from object-storage bytes and provider operations.
- Audit/outbox/idempotency are operational cross-cutting entities, not generic substitutes for business tables.

### A.4 Proposed cardinality and relationship map
These are design candidates, not finalized constraints:
- tenant 1:N tenant memberships, departments, roles and tenant-owned entities.
- user 1:N memberships and user-role assignments; membership-level assignments preferred if users can belong to multiple tenants.
- role M:N permissions via role_permissions; user M:N roles via user_roles (tenant context required).
- workflow 1:N versions; version 1:N steps/rules; step M:N departments.
- project N:1 workflow version/current execution context; project 1:N events and information revisions.
- event N:1 project optional; event N:1 event-type version/config where snapshot context is preserved; event 1:1 or 1:N data/response structure pending verified legacy semantics.
- form 1:N versions and fields; event type M:N forms; event 1:N form responses.
- project 1:N material needs/specification revisions/calculation records.
- purchase order 1:N items; order items M:N requirements/projects through allocations; receipts refer to order item and/or allocation according to confirmed business semantics.
- file metadata M:N supported owners through typed association tables or controlled owner registry.
- audit records reference target by typed reference or registry; FK enforcement strategy is a design decision.
- template 1:N versions and assets.

### A.5 Modeling recommendations with confidence
| Recommendation | Confidence | Reason / caveat |
|---|---|---|
| Tenant ownership on tenant-scoped business records | High | Repeated tenant-scoped source behavior |
| Optional project link for events | High | Validator and independent event semantics |
| Separate purchase orders from project allocations | High | General/project-related procurement coexist |
| Version workflow definitions | High | Draft/published/version behavior observed |
| Retain material-spec revision provenance | High | Revision and superseded-source behavior observed |
| Typed file associations rather than unconstrained polymorphic links | Medium-high | Legacy polymorphic links; target integrity needs improved modeling |
| Account-global user plus tenant membership | Medium | Architectural proposal; current uniqueness/sharing policy unknown |
| Event payload JSONB vs normalized response field rows | Medium-low | Query/reporting and validation requirements need benchmark/decision |
| Soft-delete/archive universal policy | Low | Deletion semantics vary; avoid global assumption |
| Partitioning tables by tenant/time | Low | Premature until volumes and query patterns measured |

### A.6 Proposed default table-level integrity strategy
- Tenant-scoped rows: tenant ownership explicit and consistent.
- Cross-tenant relationships: composite FK strategy candidate (tenant_id plus referenced key) or equivalent DB enforcement; application-only tenant filters are insufficient as sole defense.
- Tenant-local code uniqueness: unique indexes scoped by tenant and lifecycle rules, but exact fields/scopes remain undecided.
- Role/permission association: unique role-permission pair within defined scope.
- Revision sequence: unique spec key/revision, and versioned workflow/form/template sequence within owner scope.
- Idempotency: unique tenant + operation namespace + idempotency key; request fingerprint to detect reuse with a different payload.
- Receipt quantity and allocations: no final check constraints until unit, precision, reversal and partial receipt rules are validated.
- All mutation operations must use transactional service boundaries; DB constraints protect races.

### A.7 Work package status
2.4-A inventory and logical ownership design has been drafted. This remains a proposal and is not an accepted schema. Next work package 2.4-B will refine tenant-scoped keys, associations, FK candidates and uniqueness without generating executable DDL.


## Work package 2.4-B — Tenant keys, relationships and constraint candidates

### B.1 Key strategy (proposal)
- UUID primary keys for V2 records; preserve legacy IDs in dedicated compatibility/mapping fields when required. Final generation and mapping strategy is pending.
- Explicit tenant_id on tenant-owned rows; candidate alternate key (tenant_id,id) enables tenant-safe composite FK patterns.
- Global catalogs (permission definitions, potentially) remain global unless tenant customization is evidenced. Tenant-owned assignments remain scoped.
- Membership, user-role, department assignment and role-permission associations need explicit scope and uniqueness.

### B.2 Tenant isolation
Preferred candidate: composite FK patterns (tenant_id plus referenced key) for tenant-owned relationships, so a mismatched tenant relationship is rejected by the database. API authorization, composite constraints and optional PostgreSQL RLS are complementary; RLS remains unselected pending pooled-session/context safety analysis. Membership-level assignment is preferred if user accounts can belong to multiple tenants.

### B.3 Candidate relationship/constraint rules
**Identity:** candidate unique membership (tenant_id,user_id); user_roles reference membership and role, unique within tenant; role-permission uniqueness depends on global vs tenant role scope; department assignment uniqueness unless effective-date history is required.

**Projects/information:** project code uniqueness likely tenant-scoped but draft/no-code and lifecycle exceptions must be resolved. Project-information uniqueness applies only to SINGLE cardinality; MULTIPLE must be modeled distinctly. History revision sequencing and copy-operation/per-item idempotency constraints remain conditional on validated semantics.

**Workflow:** candidate unique workflow version (tenant_id,workflow_id,version_number); step codes/order uniqueness only where confirmed; step-department association uniqueness; event-rule identity/uniqueness needs further evidence.

**Events/forms:** event references must be tenant-safe and ProjectID remains nullable. Idempotency candidate unique (tenant_id,operation_scope,key) with request fingerprint. EventData cardinality is not assumed. Form version/field/association unique scopes depend on version semantics; response cardinality and amendment/void linkage are unresolved.

**Materials/procurement:** specification revision candidate unique (tenant_id,project_id,spec_key,revision); calculation idempotency scoped by tenant and calculation scope/key plus provenance/fingerprint. MaterialNeed uniqueness is conditional on generated/manual semantics. Order code uniqueness, allocation uniqueness, receipt replay safety and quantity/reversal constraints require further validation and transactional enforcement.

**Files/audit/templates:** storage keys unique within provider/storage namespace; association uniqueness is policy-dependent. Audit append-only is a candidate, with redaction. Template version unique within owner. Outbox delivery/idempotency scope must prevent duplicate dispatch.

### B.4 File association alternatives
1. Typed association tables (preferred for finite owner types) with tenant-safe FKs.
2. Controlled owner registry (generic but lifecycle/registry integrity complexity).
3. Unconstrained polymorphic text links (least preferred; API checks and reconciliation required).
Decision awaits final owner inventory/query needs.

### B.5 Constraint layers
- CHECK/NOT NULL only for validated semantics, not guesses.
- FK/composite FK enforce confirmed relationships and tenant coherence.
- UNIQUE protects verified uniqueness and race conditions.
- Partial/exclusion constraints only for confirmed state invariants.
- Application validation provides usable errors; DB constraints remain integrity authority.
- RLS optional defense in depth and must be tested with pooled connections/context resets.
- Transactional service operations plus constraints protect multi-row integrity and concurrency.

### B.6 Open decisions
Global vs tenant-local user identity; tenant/legacy ID mapping; tenant-local code uniqueness scopes; temporal vs immutable revision entities; event/form response and amendment model; receipt allocations/reversals; file-owner typing; RLS and tenant-context mechanism; ID generation.
