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


## Work package 2.4-C — History, snapshots, deletion, files and audit

### C.1 Versioned configuration and snapshot policy
**Proposal:** Configuration used to interpret a business record should be versioned or captured in a bounded snapshot when later edits could change its meaning. Candidate snapshot/version references include workflow version/step rules, event-type configuration, form versions and response context, material specification revision, project-information revision, and print-template version. This avoids retroactively reinterpreting historic records. Exact legacy semantics and retention remain validation gates.

- Prefer immutable published configuration versions and explicit activation/deactivation rather than mutating in-use definitions.
- Store snapshot payloads as versioned, validated JSONB only where schema variability justifies it; keep searchable/high-integrity relationships as relational columns/tables.
- Snapshot payloads must be bounded, redact sensitive fields and carry a format/schema version.
- Do not indiscriminately duplicate every source value; document which snapshot is authoritative and which is a reference.

### C.2 Current-state and historical records
Candidate patterns:
- Current project information values plus append-only revisions/history.
- Specification revision rows with stable specification key and explicit revision number; calculations retain source spec/revision and event provenance.
- Workflow definition versions separated from mutable draft editing; project execution history records transition context.
- Event amendment/void lifecycle modeled explicitly, with original-event context and audit; exact model pending semantics.
- Procurement quantities/allocations/receipts are not overwritten blindly; quantity changes and reversals require a validated history/transaction model.
- User, membership, role and department changes may require audit/effective dates; whether temporal tables are needed remains open.

### C.3 Deletion, retention and recovery
- Do not apply a universal soft-delete policy without domain review. Current source deletion behavior varies and project deletion is dependency-aware.
- Candidate state-based deletion/archive for recoverable business objects; preserve immutable audit and essential historical provenance according to retention policy.
- Shared procurement objects and cross-project allocations must not be deleted as a side effect of one project lifecycle.
- File object deletion is a coordinated lifecycle: metadata/association state, object-storage action, retries, reconciliation and recovery window. Avoid irreversible storage deletion before relational references and policy checks.
- Retention durations, purge approvals and restore procedures remain open business/operational decisions.

### C.4 Files and external object storage
- PostgreSQL stores file metadata, provider/storage namespace, object key, MIME/extension validation outcome, size, checksum, owner associations, uploader and lifecycle state; actual bytes remain external.
- Use upload state transitions and an operation record to reconcile failed uploads or orphaned objects.
- Prefer typed associations with FKs, or controlled owner registry if the owner type count/operations justify it.
- Store secrets and signed access tokens outside database records where feasible; URLs should be generated at access time, not persisted as permanent access grants.
- Storage provider, multipart/resumable approach, malware scanning, checksum policy and retention are pending design decisions.

### C.5 Audit, outbox and operational history
- Audit records should be append-only by policy, with tenant, actor, action, target reference, timestamp, reason and bounded before/after snapshots where justified. Redaction and access controls apply.
- Audit is not a substitute for domain history or event records. Coverage must be defined for consequential operations.
- Outbox entries should be created in the same DB transaction as the business state change that triggers notification/integration, then dispatched asynchronously with retry and deduplication.
- Outbox payloads should contain minimal necessary data and reference authoritative records; delivery attempts/status/error summaries should be bounded and sanitized.
- Idempotency records protect request replay and should be scoped, time-retained and designed not to expose sensitive response content.

### C.6 Polymorphic audit/file references
For finite owner types, typed association tables are preferred. For audit target references, a controlled entity registry may be evaluated, but it can add coupling. If polymorphic references remain, validate owner/tenant in the service and provide integrity reconciliation tooling; explicitly document that FK enforcement is not native.

### C.7 Snapshot and lifecycle decision register
- Which event/form values are immutable snapshots vs live references?
- How event amendment and VOID relate to prior events and form responses?
- Which project-information revisions are retained and which become active?
- Are procurement receipts corrected by reversal records or mutable updates?
- Which entities require soft deletion, archive or hard purge, and retention period?
- File owner types, storage provider, scanning, orphan reconciliation and recovery window?
- Audit coverage, redaction, retention, immutability and access roles?
- Which notification/integration messages require outbox delivery and retry?


## Work package 2.4-D — Transaction, idempotency, concurrency and consistency

### D.1 Transaction boundaries (candidate operations)
Transactions should group related DB changes so partial state is not visible. Candidate boundaries:
- Create project and initial workflow/context plus initial information rows where source operation requires atomicity.
- Publish workflow/config version and its steps/rules/assignments.
- Register/complete an event, persist event payload/form response/attachments associations, apply workflow state transition and audit/outbox records, when the source lifecycle semantics confirm these are one operation.
- Create/update material specifications, calculation provenance, generated needs and reconciliation status.
- Create purchase order/items and allocations; receipt registration and allocation/remaining-quantity reconciliation.
- Project reference-copy/sync operation and per-item state, including replay/recovery markers.
- File association state changes, metadata and outbox/reconciliation operation (object storage itself cannot be transactionally joined; use saga/outbox-style recovery).
- User-role and permission assignment changes with audit.

Transaction boundaries are proposals; workflows and partial-failure semantics require final validation.

### D.2 Concurrency and race protection
- DB unique constraints should protect duplicate assignment, version, revision, code or idempotency keys where scope is confirmed.
- Use transaction isolation and explicit row locks/optimistic version checks for contested aggregate operations (e.g. receipt quantities, allocation balances, workflow transitions, material-spec revision updates). Choose lock granularity from workload and correctness needs.
- Avoid relying solely on application pre-checks; concurrent requests can race.
- Include optimistic concurrency tokens (e.g. revision/version column) where stale edits must be rejected.
- Keep transactions short; avoid external network/storage calls while DB locks are held.
- For cross-service/file/integration effects, use outbox/saga and compensating/reconciliation steps rather than pretending external effects are transactional.

### D.3 Idempotency/replay design
Candidate idempotency record fields conceptually: tenant scope, operation namespace, key, request fingerprint, result/resource reference, lifecycle/status and timestamps. Requirements:
- same key + same fingerprint should return/resolve the same completed operation;
- same key + different fingerprint should be rejected;
- in-progress operations need safe recovery and bounded ownership/lease policy if required;
- keys and response data must not retain sensitive information unnecessarily;
- durable uniqueness should be DB-enforced.

### D.4 Proposed integration reliability pattern
Use transactional outbox for notifications/integrations. A domain transaction writes outbox row; dispatcher claims safely, sends outside the transaction, records outcome and retries with backoff. Consumers/provider endpoints should support deduplication where available. Delivery ordering, retry limits, dead-letter handling and manual replay policy remain to be designed.

### D.5 Migration-run architecture (future phase, not implemented)
- Migration tooling separate from application schema and operational tables where possible.
- Reproducible runs with run ID, source checkpoint, mapping version, counts, validation errors and reconciliation results.
- Never expose production data in logs/repositories. Sanitized fixtures only in development/test.
- Migration should be repeatable and idempotent, with explicit checkpoint/resume, dry-run and rollback/recovery plan. A data rollback strategy must account for writes and not rely on reverse SQL alone.
- Real migration remains gated on UAT, backup/restore validation and explicit approval.

### D.6 Performance considerations without premature tuning
- Index candidate patterns: tenant + lifecycle/status + common query key; FK columns; temporal/revision lookups; idempotency; allocation/receipt links; file owner association. Final index list requires workload/query review.
- Avoid indiscriminate indexes; each adds write and storage overhead.
- Reporting may use reviewed read models/materialized views later, with explicit freshness and invalidation rules.
- Partitioning is deferred until measured row volume, query patterns and operational cost justify it.
- Performance acceptance targets must be measured after representative synthetic data and APIs exist; no latency claims made now.

### D.7 Consistency review of work packages A–D
- The model preserves independent events (nullable project association), general procurement (no fabricated project link), versioned workflow/spec context and tenant-scoped relationships.
- Flexible JSON is limited to bounded configuration/snapshots/payloads, while core relationships remain relational.
- File bytes are outside DB; relational metadata/association integrity and asynchronous storage lifecycle are accounted for.
- Transaction, idempotency, concurrency and outbox recommendations address observed legacy lock/replay/partial-side-effect patterns without claiming their implementation.
- Exact field types, nullability, code scopes, event response cardinality, receipt reversals, RLS, deletion/retention and migration identity mapping remain open.
- No executable SQL/DDL, schema migrations, DB setup or data access was performed.

## Task 2.4 preliminary completion criteria
- [x] Work package 2.4-A logical inventory drafted.
- [x] Work package 2.4-B key/relationship/constraint candidates drafted.
- [x] Work package 2.4-C history/snapshot/file/audit models drafted.
- [x] Work package 2.4-D transaction/idempotency/concurrency and migration reliability principles drafted.
- [ ] Cross-check blueprint against accepted 2.1–2.3 documents and architecture record.
- [ ] Read-back verification of all updated documentation.
- [ ] Present proposal to Product Owner; task remains pending acceptance.


## Cross-review against accepted discovery artifacts (2026-11-22)

### Evidence alignment matrix
| Discovery evidence | Blueprint treatment | Review outcome |
|---|---|---|
| Events may have absent/null/blank ProjectID in create validation | Optional project FK; tenant ownership remains required | Consistent; do not require project for independent events |
| Event validator requires EventTypeID and PerformedBy; some fields optional | Requiredness is not automatically translated into SQL nullability; finalize from full write-path and migration evidence | Consistent, decision pending |
| Event status validator observed ACTIVE/VOID | Candidate lifecycle/history and reversal model; no generalized deletion assumption | Consistent; exact VOID semantics still unresolved |
| EventData registry includes DataJSON | Payload/field-value alternatives remain open; preserve schema/version context and bounded payload validation | Consistent, performance/query benchmark later |
| Information types support SINGLE/MULTIPLE and several data types incl. image/file | Separate cardinality semantics; file values reference metadata rather than byte content; validation config bounded/versioned | Consistent |
| Project-information history and approval selection are distinct from history/audit | Separate revisions/history and approval decision records; no conflation with audit log | Consistent |
| Material specification revisions and calculation history are observed | Revision/provenance and calculation identity retained as relational candidates | Consistent |
| Procurement includes project-linked and general orders, allocations, receipts; validation/replay paths have differences | Order independent of project; allocations express project/need links; transactional quantities, replay/reversal behavior not finalized | Consistent, contract needs explicit future validation |
| Legacy attachments have EntityType/EntityID-style polymorphic linkage | Prefer typed FK associations or registry; if polymorphic remains, reconciliation and tenant checks required | Target improves integrity; migration mapping needs owner inventory |
| Source mapping explicitly remains partial and many target constraints are UNKNOWN | Blueprint treats names/keys/precision/cardinality as proposals, not final schema | Consistent; schema implementation is not yet authorized |

### Findings and limitations from cross-review
1. The discovery mapping remains partial. The blueprint is not a migration-ready table-by-table design and should not be used to generate migrations until source mapping completeness and mutation paths are audited.
2. Event payload choice (JSONB vs normalized response/value tables) remains an explicit design trade-off. Before choosing, define required search/report/filter behavior, validation, version snapshots and representative load benchmarks.
3. Procurement integrity rules need a unified V2 contract for create order, allocations, batch/single receipt, correction/reversal, idempotent replay and remaining quantities. Legacy lock/write differences are not to be reproduced as uncontrolled partial writes.
4. Attachment target types and ownership lifecycle must be enumerated before selecting typed association tables. Storage provider operations require reconciliation, not cross-system DB transaction assumptions.
5. Tenant-aware integrity must include every relationship edge, including indirect links (project, need, order item, allocation, receipt, event, workflow context, attachments), not only root entity ownership.
6. Numeric precision, currency, unit conversion, date/time timezone, legacy identifier mapping, global-vs-tenant catalog scope and uniqueness are still open.
7. This review found no basis to declare implementation/testing readiness. It validates conceptual consistency only.

### Task 2.4 review conclusion
Work packages A–D are drafted and the evidence consistency pass is recorded. The logical blueprint is **READY FOR PRODUCT OWNER REVIEW**, not complete/accepted. It remains design-only and must not be treated as approved schema or migration specification until the Product Owner accepts it and the subsequent detailed source/constraint design tasks are authorized.
