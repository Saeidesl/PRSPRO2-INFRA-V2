# Task 2.7 — Blocker Resolution & Evidence Closure

**Status:** IN PROGRESS — discovery/review only  
**Started:** 2026-11-22  
**Gate:** Physical PostgreSQL schema design remains NOT APPROVED.

## Purpose
Convert open readiness blockers into a traceable evidence and decision-closure package. This task is not permission to create SQL/DDL, migration tooling, database provisioning, or access live/production data.

## Work plan
1. **Blocker traceability and coverage matrix:** map each gap to source evidence, unanswered questions, required decision/evidence, and closure criteria.
2. **G-03 event/form history contract:** trace submit, amendment, void, dynamic payloads, snapshots, revision and reporting dependencies; document confirmed versus unknown behavior.
3. **G-04 attachment ownership and lifecycle:** inventory owner associations, metadata, copy, upload/download, delete, retention, storage-side effects, and recovery boundaries.
4. **G-10 tenant-safe relationship matrix:** identify candidate tenant-owned parent/child relationships, polymorphic pointers, tenant scoping and integrity enforcement needs; avoid asserting SQL constraints without evidence.
5. **G-01 coverage reconciliation:** reconcile registry/header inventory against services, repositories, routes, mutation families and dependency collectors; explicitly classify bounded/unverified coverage.
6. **Closure assessment:** determine whether evidence is sufficient to recommend schema readiness or identify remaining discovery/business-policy gates. Product Owner approval remains required.

## Evidence and safety rules
- Existing production repositories are read-only.
- No live production data or credentials/configuration secrets are accessed.
- Source review does not count as runtime verification.
- Do not infer schema constraints, field precision, nullability, or deletion policies from names alone.
- Unknowns remain explicit and are not silently converted into business decisions.
- No tests are claimed unless executed.

## Initial baseline
Task 2.5 closed as an audit deliverable, with readiness NOT approved. Task 2.6 completed its bounded procurement lifecycle/correction audit and left G-02 open. Task 2.7 is a distinct traceability and evidence-closure work package; it does not assume those blockers are resolved.


## Increment 1 — Event/form and attachment lifecycle source cross-check (2026-11-22)

### Source sampled (bounded, read-only)
Legacy backend: EventService.js, EventDataService.js, EventDataRepository.js, EventRepository.js, FormService.js, AttachmentService.js, AttachmentRepository.js, ProjectInformationService.js and Router.js. Production repository remains read-only. No runtime/test execution or data access.

### G-03 evidence observations
- Event void is a service-level operation with tenant and project-visibility checks, duplicate-void rejection, event-type AllowVoid policy, department/admin checks and downstream cross-department event restriction. This is evidence of validation policy, not proof of atomic reversal of all event-derived effects.
- EventData is accessed via EventDataService and a repository keyed by EventID; bounded source review does not establish full snapshot/version/retention/correction lineage or transactionality across event and data rows.
- Reusable form responses include ResponseJSON and DefinitionSnapshotJSON, and read APIs expose parsed values/definition snapshots. This indicates persisted response and definition snapshot material, but complete historical version retention and report parity remain unproven.
- Form amendment path requires an amendment event on the same project and verifies amendment event type configuration/markers, allowable form IDs where explicitly configured, existing response, current form version, and values. The sampled method's exact downstream writes/rollback behavior need further review; no atomic transaction guarantee is inferred.
- Router exposes event form responses, event data, and event attachment listing. Route reachability evidence is partial and does not prove every mutation endpoint/permission contract.

### G-04 evidence observations
- Attachment ownership is polymorphic across PROJECT and EVENT, with TenantID and EntityID. Visibility resolution looks up the owning project/event and, for project-linked events, checks inherited project access.
- Upload validates owner entity and tenant, applies project visibility and event-parent project visibility checks, and generates canonical project attachment names.
- Attachment deletion calls storage delete before metadata status update and audit logging. This ordering is a recovery/consistency risk if later steps fail; atomicity is not demonstrated. Deleted metadata status exists, but object recovery/retention semantics are not proven.
- Project image-copy operation copies object storage content, creates a new attachment record, and has source linkage/idempotent duplicate lookup in the bounded path. Full copy semantics for other attachment types and broader lifecycle remain unverified.
- Storage adapter/provider contract, staged upload, failure reconciliation, retention/purge, and orphan inventory need explicit V2 design and evidence.

### G-10 candidate relationship observations (not schema declarations)
- Tenant ownership is explicitly checked in sampled services for event, project, form-response, attachment and source/target project-image copy paths.
- Candidate relationships: Event -> Project (nullable for independent events); Event -> EventType; EventData -> Event; FormResponse -> Event and Form; form definition snapshot; Attachment -> tenant-scoped polymorphic PROJECT/EVENT owner; ProjectInformation/history -> Project and InformationType; amendment Event -> same-project base event (policy evidence in sampled form amendment method).
- Polymorphic owner pointers and cross-project reference/copy links cannot be safely represented by an ordinary single FK without a deliberate design choice. Composite tenant integrity, owner typing, retention and cascade/restrict behavior remain design decisions.
- Source-service validation alone does not establish database-level tenant-safe constraints.

### Coverage and limitations
- This is a bounded source cross-check, not exhaustive coverage of all endpoints, service methods, repositories, reporting consumers, deletion collectors or tests.
- Missing files queried by exact name (EventVoidService.js, FormResponseService.js) were not present at those paths; behavior was sampled in EventService/FormService instead. No absence claim beyond that path search.
- No live data, tests, SQL/DDL, migrations, DB provisioning, production modifications, deployment or VPS action.
- G-03, G-04, G-10 and G-01 remain OPEN/BLOCKER pending broader evidence and decision closure.
