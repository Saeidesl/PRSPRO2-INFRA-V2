# PMS V2 — Authoritative Phase Status

Updated: 2026-11-23

## Phase 0 — Discovery & Repository Preparation
COMPLETE and accepted (Tasks 0.1, 0.2, 0.3). Task 0.2 closure pack Increment 149 was explicitly accepted on 2026-11-23 and recorded in TODO.md. The three implementation V2 repositories are confirmed present and private, but remain empty (no README content yet); repository existence/access is verified, initial code/documentation bootstrapping remains future work.

## Phase 1 — Foundation & Architecture
COMPLETE and accepted. Tasks 1.1–1.5:
- Collaboration conventions.
- CI baseline inventory (not CI implementation).
- Architecture/API/security/logging/error standards.
- Domain boundaries and migration strategy planning baseline.
- Testing strategy and quality gates planning baseline.

## Phase 2 — Data Model & Migration Design
- Task 2.1 — Consolidate conceptual entity inventory and relationships: IN PROGRESS.
- Draft in DATA-MODEL-DRAFT.md; relationship matrix, transaction hypotheses, and focused Project/Event/Form notes added; pending Product Owner review/acceptance.
- Task 2.1 is conceptual only; executable schema and migration tooling are not started.

## Evidence / current repo state
- CI-BASELINE.md, ARCHITECTURE-STANDARDS.md, DOMAIN-MIGRATION-STRATEGY.md, TESTING-STRATEGY.md and DATA-MODEL-DRAFT.md documented.
- The three implementation repositories remain empty at last verified checkpoint; infrastructure repository contains governance/design documents.
- No software tests executed for these design tasks.

## Safety boundaries
- Existing production repositories remain READ-ONLY.
- No production data access/export, migration, dual-writing, deployment, VPS purchase or production changes.


## Phase 2 — Task 2.1 checkpoint (2026-11-22)
- Added focused conceptual notes for tenant identity/membership, roles/permissions, session lifecycle, procurement/allocation/receipt quantity integrity, file metadata/associations and audit-trail requirements to DATA-MODEL-DRAFT.md.
- These are provisional models and source-supported rules, with explicit unknowns. No SQL, executable schema, tests, runtime access or migration has started.
- Task 2.1 remains IN PROGRESS pending final conceptual consolidation and Product Owner acceptance.


## Phase 2 — Task 2.1 consolidation checkpoint (2026-11-22)
- Consolidated conceptual domain dependency sequence, candidate transaction/side-effect boundaries, cross-domain invariants and schema design validation gates in DATA-MODEL-DRAFT.md.
- Conceptual inventory now covers identity/tenant/security, project/workflow/event/forms, materials/procurement, files/audit, notifications/outbox and reporting/printing.
- Source-to-target mapping, cardinality/unique-scope validation, business policy decisions and workload baseline remain pending. Task 2.1 remains IN PROGRESS; no schema or migration is implemented.


## Phase 2 — Task 2.1 source mapping checkpoint (2026-11-22)
- Read-only DatabaseSetup.js sheet-header inventory was extracted for Project, Project Information/History, reference-copy logs, Workflow/Steps/Departments/EventRules, EventTypes/Fields/Events/EventData and Forms/Fields/Associations/Responses.
- Source-to-target mapping notes and cautions were appended to DATA-MODEL-DRAFT.md. This is header-level evidence only; data, full event header and semantic paths remain to be validated.
- No production repository was modified. No production data, schema or migration accessed.


## Phase 2 — Procurement mapping checkpoint (2026-11-22)
- Extracted headers for procurement settings, material needs/specs/calculations, orders/items, allocations and receipts from read-only legacy DatabaseSetup.js.
- Targeted ProcurementService review observed locks, order and receipt idempotency checks, tenant/project/event visibility helpers, and combined accounting reads. These observations are provisional and do not certify every branch/path.
- Added provisional source mapping, transaction/integrity candidates and open business rules to DATA-MODEL-DRAFT.md.
- No live data, schema, SQL or migrations were used.


## Phase 2 — Identity/Authorization mapping checkpoint (2026-11-22)
- Extracted source headers for Tenant, User, Department, Role, Permission, UserRoles and RolePermissions.
- Targeted read-only review of AuthService, AccessControlService, PermissionService, selected repositories and Router excerpts documented observed authentication, role filtering, admin recognition, permission checks and potential gaps.
- Added provisional mapping, V2 security requirements and open policy decisions to DATA-MODEL-DRAFT.md.
- This is not a full security audit and no live data/credentials were accessed.


## Phase 2 — Task 2.1 Attachment/Audit/Print/Integration checkpoint (2026-11-22)
- Extracted header-level source mappings for attachments, audit logs and print templates, and configured attachment entity/provider values from DatabaseSetup.js.
- Documented provisional V2 requirements and unresolved service/policy questions in DATA-MODEL-DRAFT.md section 19.
- Dedicated service paths and behavior remain unverified; Task 2.1 remains IN PROGRESS.
- No live data, secrets, file content, schema, SQL, migration or production modifications.


## Phase 2 — Task 2.1 final consolidation (2026-11-22)
- Conceptual entity and relationship package consolidated in DATA-MODEL-DRAFT.md, including source-supported lifecycle/transaction notes and explicit unresolved policies.
- Ready for Product Owner review. Task remains IN PROGRESS until acceptance. Task 2.2 field-level source-to-target mapping is recommended next.
- No SQL, migration scripts, tests, live data, production changes or deployment.


## Phase 2 — Task 2.1
- Status: COMPLETE — Product Owner acceptance recorded on 2026-11-22.
- Next: Task 2.2 detailed source-to-target field mapping and constraints matrix (read-only audit). Schema/migrations remain gated on subsequent review and approval.


## Phase 2 — Task 2.2
- Status: IN PROGRESS — read-only detailed source-to-target mapping and constraints audit.
- Partial header-evidence matrix checkpoint recorded in SOURCE-TO-TARGET-MAPPING-2.2.md; complete field, type, uniqueness, nullability, FK and lifecycle mapping remains pending.
- No schema/migrations.


## Phase 2 — Task 2.2 review-ready status (2026-11-22)
- Task 2.1: COMPLETE — accepted by Product Owner.
- Task 2.2: IMPLEMENTED — comprehensive read-only mapping/review package prepared; awaiting Product Owner acceptance.
- Main deliverable: SOURCE-TO-TARGET-MAPPING-2.2.md.
- Review includes domain mapping, evidence tiers, tenant/relationship/integrity analysis, lifecycle/snapshot/idempotency, known divergent mutation paths, unresolved business policies and schema design gates.
- Remaining intentionally outside this task: live data/sheet drift checks, exact executable SQL types/nullability/index/FK decisions, runtime tests, full permission/penetration audit, workload measurements and migration execution.
- No schema/migrations or production-system modifications.


## Phase 2 — Task 2.2 acceptance (2026-11-22)
- Status: COMPLETE — Product Owner explicitly accepted the consolidated mapping and comprehensive review package.
- Deliverable: SOURCE-TO-TARGET-MAPPING-2.2.md.
- Acceptance does not authorize SQL/schema, migration execution, live data access, deployment or production modifications.
- Next: continue to the next roadmap task only after checking dependencies and scope.


## Phase 2 — Task 2.5 current checkpoint (2026-11-22)
- Task 2.5 — source-evidence/readiness audit: IN PROGRESS; NOT READY for schema implementation.
- Increment 17 added bounded route-to-service mutation/permission evidence for selected project, project-information, event and form pathways to TASK-2.5-READINESS-GAP-REGISTER.md.
- G-01 (exhaustive mutation/dependency coverage) and G-10 (tenant-safe relational constraints) remain BLOCKERS; G-02/G-03/G-04 also remain blockers as recorded in the gap register.
- No schema, SQL/DDL, migration, live-data access, database provisioning, tests, production modification, deployment or VPS action authorized or performed.


## Phase 2 — Task 2.5 checkpoint (2026-11-22, Increment 18)
- Continued bounded read-only mutation/route sampling for configuration and workflow-related domains; evidence appended to TASK-2.5-READINESS-GAP-REGISTER.md.
- Workflow, workflow-step-department, event-type, department, project-status and information-type operation families and selected permission gates are documented.
- G-01 and G-10 remain blockers; G-12 remains open/high. Task 2.5 remains IN PROGRESS and schema/migration readiness is not established.
- No SQL/DDL, migrations, live data access, tests, production modifications, deployment or VPS action.


## Phase 2 — Task 2.5 checkpoint (2026-11-22, Increment 19)
- Continued read-only sampling across identity/role assignment, audit, reporting and print-template domains.
- Added bounded source evidence for user validation and password hashing paths, tenant-safe role assignment/removal, audit fields/read filters, project-report/dashboard consumers and print-template definition/PDF/image metadata and selected route gates.
- Task 2.5 remains IN PROGRESS; G-01 and G-10 remain blockers. G-08, G-09, G-11 and G-12 remain open/high. No readiness approval for schema implementation.
- No tests, SQL/DDL, migrations, live data, DB provisioning, production modifications, deployment or VPS action.


## Phase 2 — Task 2.5 checkpoint (2026-11-22, Increment 20)
- Read-only cross-check of procurement lifecycle, dependency-aware project deletion, shared procurement-order preservation and attachment/storage lifecycle.
- Source evidence highlights non-transactional multi-record cascade and storage operations, partial failure reporting, tenant/parent visibility checks and Drive file dependency. Target transaction/compensation and recovery design remain pending discovery.
- Task 2.5 remains IN PROGRESS. G-01/G-02/G-04/G-10 remain blockers; G-06/G-09/G-11 remain open/high. No schema implementation readiness established.
- No tests, SQL/DDL, migrations, live data, DB provisioning, production modification, deployment or VPS action.


## Phase 2 — Task 2.5 checkpoint (2026-11-22, Increment 21)
- Read-only audit of event-void conditional restoration and downstream material-planning reconciliation path.
- Documented snapshot/state guard, multi-write sequence, audit records and potential partial completion, with migration implications in readiness register.
- Task 2.5 remains IN PROGRESS. G-01/G-02/G-03/G-10 remain blockers; G-09 remains open/high. No readiness approval.
- No tests, SQL/DDL, migrations, live data, DB provisioning, production modifications, deployment or VPS action.


## Phase 2 — Task 2.5 checkpoint (2026-11-22, Increment 22)
- Read-only audit of material-specification revision paths, CalculationKey idempotency, recalculation multi-write sequence and event-void reconciliation.
- Recorded revision/provenance, status mutability, NEEDS_REVIEW behavior and precision observations/limitations in readiness register.
- Task 2.5 remains IN PROGRESS. G-01/G-02/G-03/G-10 remain blockers; G-06/G-09 remain open/high. No readiness approval.
- No tests, live data, SQL/DDL, migrations, DB provisioning, production modifications, deployment or VPS action.


## Phase 2 — Task 2.5 checkpoint (2026-11-22, Increment 23)
- Read-only audit of event dynamic data parent linkage, project information/history/reference-sync collections and workflow step relationship repositories.
- Identified dependency-policy gap: project deletion collector sampled does not enumerate project-information, history or reference-sync collections; intentional retention vs cascade remains unresolved. Documented PostgreSQL tenant/relationship constraints in readiness register.
- Task 2.5 remains IN PROGRESS; G-01/G-03/G-10 blockers; G-09/G-12 open/high. No approval or migration readiness conclusion.
- No tests, live data, SQL/DDL, migration, DB provisioning, production modifications, deployment or VPS action.


## Phase 2 — Task 2.5 closure checkpoint (2026-11-22)
- Task 2.5 READ-ONLY discovery/readiness audit package: COMPLETE as an audit deliverable; NOT a schema-readiness approval.
- Increment 24 sampled authentication/session, tenant-boundary, Telegram notification and legacy configuration/storage evidence. Consolidated readiness register remains the source of unresolved decisions and blockers.
- Schema design readiness: NOT READY. G-01/G-02/G-03/G-04/G-10 remain BLOCKERS; high/open gap items remain tracked.
- Task closure does not authorize SQL/DDL, migrations, live data access, DB provisioning, production changes, deployment, VPS or cutover.
- Next: prepare a blocker-resolution/discovery decision task, prioritizing exhaustive lifecycle/dependency coverage, reversal/history/attachment contracts and tenant-safe relationship enforcement. Do not start physical schema work until blockers are resolved and readiness is reviewed.


## Phase 2 — Task 2.6 kickoff (2026-11-22)
- Task 2.6: IN PROGRESS — Resolve Discovery Blockers before Schema Design.
- Increment 1 completed: bounded review of Procurement order/receipt and event integration paths; proposed G-02 contract documented, not finalized.
- G-02 remains BLOCKER pending further source coverage. No physical schema or migration work.


### Task 2.6 — Increment 2
- Completed bounded review of procurement projection/report/workspace and project deletion dependency semantics.
- G-02 remains unresolved and IN PROGRESS. No schema implementation.


### Task 2.6 — Increment 3
- Bounded review of receipt batch invariants and API read surface; test source files reviewed but not executed.
- G-02 remains unresolved.


### Task 2.6 — Increment 4
- Bounded review of order creation recovery, single receipt and route service inventory; provisional contract consolidated.
- G-02 remains unresolved. No schema work.


### Task 2.6 — Increment 5
- Provisional procurement lifecycle matrix recorded; sampled service/repository inventory contains create/read projection paths but no dedicated correction/deallocation/reversal API surfaced.
- G-02 remains BLOCKER and decision matrix is not yet finalized.


### Task 2.6 — Increment 6
- Reviewed cross-service procurement/material-planning integration and project-delete dependency semantics.
- G-02 remains unresolved; provisional contract and retention implications documented.


### Task 2.6 — Increment 7
- Bounded configuration lifecycle/versioning review and provisional decisions documented.
- G-01/G-10 remain unresolved; no schema implementation.


### Task 2.6 — Increment 8
- Bounded identity/role/permission relationship review; provisional tenant integrity and authorization design recorded.
- G-01/G-10 remain unresolved.


### Task 2.6 — Increment 9
- Bounded attachment ownership/storage lifecycle review; provisional V2 storage decisions documented.
- G-04/G-11/G-10 remain unresolved.


### Task 2.6 — Increment 10
- Bounded audit/report/history/legacy-ID relationship review and provisional V2 decisions recorded.
- G-01/G-05/G-09/G-10 remain unresolved.


### Task 2.6 — Increment 11
- Cross-cutting technical baseline for numeric/unit, chronology, tenant integrity and deletion semantics documented.
- G-06/G-07/G-10 remain unresolved; physical schema is still not authorized/readiness-approved.


## Phase 2 — Task 2.6 Increment 12 checkpoint (2026-11-22)
- Read-only review of procurement need/order/workspace/report/dashboard projections; documented divergent status filters, allocation receipt distribution, surplus and display caps, and project visibility handling.
- Recommended canonical receipt net projection and shared reporting/workspace accounting semantics; G-02 remains BLOCKER.
- No tests, live data, SQL/DDL, migration, DB provisioning or production modifications.


## Phase 2 — Task 2.6 Increment 13 checkpoint (2026-11-22)
- Bounded read-only audit of single/batch receipt commands and Router dispatch: differences in general-stock receipt validation, source-event replay/idempotency, receipt aggregation and post-insert status/audit write sequence recorded.
- Recommended unified receipt command, durable idempotency constraints, explicit reversal lineage, consistent allocation rules, transactional persistence and reconciliation.
- G-02 remains BLOCKER. No tests, live data, SQL/DDL, migration, DB provisioning or production modifications.


## Phase 2 — Task 2.6 closure assessment (2026-11-22)
- Increment 14 read-only review of order lifecycle/cancellation status handling and receipt eligibility/partial-write recovery recorded.
- Task 2.6 audit is marked AUDIT COMPLETE / DESIGN READINESS NOT APPROVED; blockers remain open. Physical schema/SQL/migration are not authorized.
- No tests, live data, SQL/DDL, migration, DB provisioning or production changes.


## Phase 2 — Task 2.7 kickoff (2026-11-22)
- Task 2.7 — Blocker Resolution & Evidence Closure: IN PROGRESS, read-only discovery and evidence work.
- Work package and scope/guardrails documented in TASK-2.7-BLOCKER-CLOSURE.md.
- Prior Task 2.6 is closed as a bounded audit deliverable, not blocker closure or schema-readiness approval.
- Planned review focuses on G-03 event/form history contract, G-04 attachment lifecycle, G-10 tenant-safe relational integrity and G-01 coverage reconciliation. G-02 remains open and is tracked.
- No SQL/DDL, migrations, live data, tests, DB provisioning, production modifications, deployment or VPS action.


## Task 2.7 Increment 1 checkpoint (2026-11-22)
- Bounded read-only event/form and attachment lifecycle cross-check recorded in TASK-2.7-BLOCKER-CLOSURE.md.
- Evidence notes include void policy, form amendment/snapshot behavior, EventData access, polymorphic attachment owner and visibility, deletion ordering, project-image copy linkage and candidate tenant relationship graph.
- G-03/G-04/G-10/G-01 remain unresolved. No tests, live data, SQL/DDL, migration, DB provisioning or production modifications.


## Task 2.7 Increment 2 checkpoint (2026-11-22)
- Recorded DatabaseSetup.js header inventory and bounded Router.js route dispatch inventory.
- Covered security, project/workflow, event/form, information/template, attachment/audit and procurement structures; noted duplicate/legacy numbering/backup artifacts as reconciliation hazards.
- G-01/G-10 remain open. No tests, live data, SQL/DDL, migrations, provisioning or production changes.


### Task 2.7 Increment 3 — service-operation inventory (2026-11-22)
- Read-only method inventory reconciled across ProjectService, EventService, FormService, AttachmentService, ProcurementService, AuditService and Router.
- Recorded lifecycle/projection cross-dependencies and high-risk delete/void/amend/copy paths; bounded coverage only, no exhaustive handler/dependency matrix or runtime tests.
- G-01/G-03/G-04/G-10 remain open; G-02 remains unresolved. Schema readiness NOT APPROVED; no SQL/DDL, live data, migrations, provisioning, deployment or production modification.


### Task 2.7 Increment 4 — Project deletion dependency collector (2026-11-22)
- Read-only review of ProjectDeletionService and related Project/Event/Attachment/Storage repositories plus Router delete path.
- Collector evidence maps event/data, needs, procurement allocations/items/orders/receipts, specs/calculations and project/event attachments; cross-project allocation safeguards noted.
- Deletion is multi-step/non-atomic, soft-delete/void child handling followed by file trash and physical project row removal if no prior failures; recovery/rollback unknown. This is legacy evidence, not a V2 design decision.
- G-01/G-04/G-10 remain open; G-02 open. No tests, live data, SQL/DDL, migration, provisioning, deployment or production modification.


### Task 2.7 Increment 5 — Definition/history mutation review (2026-11-22)
- Read-only sampled WorkflowService/Repository, EventTypeService, InformationTypeService, ProjectInformationService and FormService/Repository.
- Evidence recorded: tenant-scoped lookup/management, soft-delete and audit paths for information types/columns, project information history, form field/mapping deletion, and workflow/event type update/version/configuration.
- Dependent effect enforcement, complete version lineage, retention and all mutation paths remain unproven; bounded review only. G-01/G-03/G-04/G-10 open; G-02 open. No live data/tests/DDL/migrations/provisioning/deployment/production write.


## Task 2.7 Increment 6 checkpoint — tenant relationship integrity (2026-11-22)
- Read-only matrix records repository access patterns vs service-layer tenant validation, procurement tenant-filtered helpers, polymorphic attachment owner and nullable event/project relationships.
- Physical tenant-safe relational constraints remain unproven; G-10 OPEN, G-01/G-03/G-04/G-02 OPEN. No live data, runtime tests, SQL/DDL, migrations, provisioning, deployment or production changes.


## Task 2.7 Increment 7 checkpoint — event/form history (2026-11-22)
- Evidence recorded for event create/form response snapshots, amendment/replay/optimistic checks, void state restoration and material review, audit and sampled report projections.
- G-03 OPEN: multi-step transactionality, immutable lineage, projection parity and retention unproven. No tests/live data/DDL/migration/provisioning/deployment/production changes.


## Task 2.7 Increment 8 checkpoint — attachments (2026-11-22)
- Recorded storage/metadata/audit sequencing and failure risks for upload/delete/copy, owner visibility and polymorphic relationships.
- G-04 OPEN pending reconciliation/recovery/retention/restore evidence; no tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Task 2.7 — Blocker Resolution & Evidence Closure — AUDIT DELIVERABLE COMPLETE (2026-11-22)
- Nine evidence increments documented, including final bounded registry/source reconciliation, blocker status matrix and closure criteria.
- Task deliverable complete pending Product Owner acceptance. Underlying blockers G-01/G-02/G-03/G-04/G-10 remain OPEN; Schema Readiness NOT APPROVED.
- No tests/live data/DDL/migrations/provisioning/deployment/VPS or production changes.


## Task 2.8 — Remaining Blocker Policy & Evidence Decision Pack (2026-11-22)
- IN PROGRESS: prepared provisional recommendations and decision/evidence gates for G-01/G-02/G-03/G-04/G-10 following Task 2.7 audit closure. This is policy preparation, not approval or schema readiness.
- Document: TASK-2.8-BLOCKER-POLICY-DECISION-PACK.md. No SQL/DDL, migration, live data, DB provisioning, production changes or deployment.


## Phase 2 — Task 2.8 checkpoint (2026-11-22, continued)
- Created TASK-2.8-RECOMMENDED-POLICY-DEFAULTS.md, a structured proposal register covering procurement, event/form history, attachments, tenant integrity and coverage-reconciliation gates.
- Recommended defaults include immutable posted receipt facts with reversal lineage; transaction-safe/idempotent operations; sequential amendment lineage; logical deletion and controlled purge for attachments; tenant-qualified relationships; and explicit 100% dispositioned source coverage.
- These remain proposed policies pending Product Owner acceptance. Schema readiness remains NOT APPROVED. No tests, SQL/DDL, migrations, live data, DB provisioning, production changes or deployment.


## Task 2.8 — Lifecycle/transaction contract checkpoint (2026-11-22)
- Added proposed state/lifecycle contracts, invariants, DB transaction/concurrency pattern and design-level acceptance test cases for procurement, event/form history, attachments and tenant integrity.
- Deliverable: TASK-2.8-LIFECYCLE-TRANSACTION-CONTRACTS.md. This is design-only; no executable tests or implementation performed. Task 2.8 remains IN PROGRESS and schema readiness NOT APPROVED.


### Phase 2 — Task 2.8 G-01 evidence increment (2026-11-22)
- Verified recursive trees of read-only production frontend (187 entries) and backend (153 entries); recorded surface inventory and partial reconciliation matrix in TASK-2.8-G01-SOURCE-COVERAGE-INCREMENT-1.md.
- G-01 remains OPEN; operation-level route/caller/permission mapping is still pending. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED. No runtime tests, DDL, migration or production writes.


### Task 2.8 G-01 Router evidence increment (2026-11-22)
- Full backend Router.js and frontend router/API core retrieved read-only. Route families across HTTP method branches and frontend page/permission map documented in TASK-2.8-G01-ROUTER-SURFACE-INCREMENT-2.md.
- Handler/caller/permission/tenant-scope/test reconciliation remains incomplete; G-01 OPEN. Schema Readiness NOT APPROVED. No runtime tests or production modifications.


### Task 2.8 G-01 increment 3
- Read-only service sampling and test-tree inventory recorded in TASK-2.8-G01-SERVICE-TEST-SAMPLING-INCREMENT-3.md. Source observations and test-name coverage are explicitly not execution evidence.
- G-01 remains OPEN. No runtime test execution, schema work, migration, deployment or production modifications.


### Task 2.8 — Increment 4 evidence
Read-only inspection of project deletion, attachment delete and procurement receipt mutations recorded in TASK-2.8-MUTATION-RISK-EVIDENCE-INCREMENT-4.md. It identifies bounded source risks and V2 design implications, without declaring legacy behavior unsafe in general or claiming tests. G-01 remains open.


### Task 2.8 — Increment 5 evidence
Read-only Event/Form lifecycle source evidence recorded. Void restoration/reconciliation, mutable form response representation, definition snapshots, and amendment governance documented. No tests/implementation; G-01 coverage reconciliation remains open.


## Phase 2 — Task 2.8 G-01 Increment 6 (2026-11-22)
- Recorded bounded source evidence for registry/header semantics, reused sheet-number prefixes, JSON/list and polymorphic fields, and destructive legacy reset routine in `TASK-2.8-SCHEMA-REGISTRY-AUXILIARY-EVIDENCE-INCREMENT-6.md`.
- The reset routine was not executed; no live data or configuration accessed. G-01/G-10 remain open; schema readiness remains NOT APPROVED.
- No tests, SQL/DDL, migration, provisioning, production modifications, deployment or VPS action.


## Phase 2 — Task 2.8 G-01 Increment 7 (2026-11-22)
- Read-only frontend API caller sampling across API/router/permissions and event, form, procurement, project, attachment modules recorded in `TASK-2.8-FRONTEND-API-CALLER-SAMPLING-INCREMENT-7.md`.
- Coverage remains bounded; no exhaustive caller/route/permission/test matrix established. G-01 OPEN; schema readiness NOT APPROVED. No tests, schema/DDL, migrations, production changes or deployment.


## Phase 2 — Task 2.8 G-01 Increment 8 (2026-11-22)
- Recorded bounded frontend/backend Router string cross-check and unresolved workflow-progress path discrepancy in `TASK-2.8-BACKEND-ROUTER-CROSSCHECK-INCREMENT-8.md`.
- String matches do not prove handler, method, permission or test coverage. G-01 remains open; schema readiness not approved. No tests or production modifications.


## Phase 2 — Task 2.8 G-01 Increment 9 (2026-11-22)
- Route dispatch/caller method evidence recorded in `TASK-2.8-ROUTE-DISPATCH-CALLER-METHOD-EVIDENCE-INCREMENT-9.md`.
- Resolved the workflow-progress discrepancy at source dispatch level: GET Router project-prefix branch dispatches to EventService.workflowProgress behind PROJECT_VIEW. This does not prove service-level tenant correctness or complete contract/test coverage.
- G-01 remains OPEN; schema readiness NOT APPROVED. No tests, production execution or changes.


## Phase 2 — Task 2.8 G-10 Increment 10 (2026-11-22)
- Recorded targeted service-level tenant/visibility evidence for workflow progress and selected procurement/project helpers in `TASK-2.8-TARGETED-TENANT-SERVICE-VALIDATION-INCREMENT-10.md`.
- Inspected paths include tenant ID checks and project availability checks. This is not exhaustive method/relation coverage. G-10 and G-01 remain OPEN; schema readiness NOT APPROVED. No tests, live data or production changes.


## Phase 2 — Task 2.8 G-01 Increment 11 (2026-11-22)
- Recursive read-only test-path inventory from legacy frontend/backend recorded in `TASK-2.8-TEST-SURFACE-INVENTORY-INCREMENT-11.md`.
- Test filenames do not establish execution, pass status or comprehensive assertion coverage; source/test traceability remains open. G-01 remains OPEN; schema readiness NOT APPROVED. No tests or production changes.


## Phase 2 — Task 2.8 G-01 Increment 12 (2026-11-22)
- Sampled source-to-test assertion trace recorded in `TASK-2.8-SOURCE-TO-TEST-ASSERTION-TRACE-INCREMENT-12.md` across workflow, procurement, attachments, deletion, schema and idempotency.
- Evidence is partial/static and test execution is absent. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No production modifications.


## Phase 2 — Task 2.8 Increment 13: consolidated gap register (2026-11-22)
- Consolidated evidence and remaining gaps across route/API, workflow, events/forms, procurement, attachments, project lifecycle, definitions, audit, schema and tests in `TASK-2.8-CONSOLIDATED-GAP-REGISTER-INCREMENT-13.md`.
- G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests executed, no production changes or live data.


## Phase 2 — Task 2.8 Increment 14: structured entity manifest (2026-11-22)
- Parsed the declared DatabaseSetup.js sheet registry into 37 entries, domain grouping and candidate relationship notes in `TASK-2.8-STRUCTURED-ENTITY-MANIFEST-INCREMENT-14.md`.
- Explicitly distinguished registry facts from inferred relationships and live-data/runtime unknowns. G-01 remains OPEN; no tests or production changes.


## Phase 2 — Task 2.8 Increment 15: registry-to-code crosswalk (2026-11-22)
- Read-only path-level crosswalk of registry domains against backend service/repository candidates and frontend module candidates documented in `TASK-2.8-REGISTRY-CODE-SURFACE-CROSSWALK-INCREMENT-15.md`.
- Path matching is not complete source-consumer evidence. G-01 remains OPEN; no tests or production changes.


## Phase 2 — Task 2.8 Increment 16: high-risk lifecycle trace (2026-11-22)
- Recorded targeted read-only source trace for event/form amendment, tenant-aware attachments, procurement idempotency/batch receipts, project dependency collection and reference-copy replay in `TASK-2.8-HIGH-RISK-LIFECYCLE-TRACE-INCREMENT-16.md`.
- Findings are partial source evidence only; G-01/G-02/G-03/G-04/G-10 remain OPEN. No tests, live data or production changes.


## Phase 2 — Task 2.8 Increment 17 (2026-11-22)
- Relationship edge and tenant enforcement matrix added as read-only source evidence across selected domain/service/repository paths.
- Candidate tenant-qualified integrity design recorded; not yet approved. G-01/G-10 and lifecycle blockers remain open; schema readiness NOT APPROVED.
- No tests/live data/DDL/migrations/provisioning/deployment/production modifications.


## Phase 2 — Task 2.8 Increment 18 (2026-11-22)
- Header-grounded inventory of nullable, polymorphic and versioned relationship edges documented in `TASK-2.8-NULLABLE-POLYMORPHIC-VERSIONED-EDGE-INVENTORY-INCREMENT-18.md`.
- Recorded tenant derivation for EventData, repeated parent references in history/sync, optional procurement edge combinations, attachment/audit polymorphism and version-sensitive rule relationships. Nullability remains unconfirmed without full source/data validation.
- G-01/G-10/G-02/G-03/G-04 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 19 (2026-11-22)
- Read-only reconciliation of project cascade deletion dependency collector and source-event-void material planning path recorded in `TASK-2.8-DELETION-SOURCE-EVENT-DEPENDENCY-RECONCILIATION-INCREMENT-19.md`.
- Evidence confirms collector protects shared order lines/cross-project allocations but mutation is sequential and non-transactional; project row physical deletion is gated on child failures, and audit recovery is not established. Source-event void moves calculations/needs to review but does not directly reverse receipts/allocations in the inspected path.
- G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 20 (2026-11-22)
- Targeted read-only AttachmentService failure-boundary review documented in `TASK-2.8-ATTACHMENT-FAILURE-BOUNDARY-RECONCILIATION-INCREMENT-20.md`.
- Normal upload stores object before metadata/audit without demonstrated compensation; delete removes object before metadata/audit; copyProjectImage has bounded compensating cleanup on error. Recommendations recorded for staged/outbox storage operations and reconciliation.
- G-04/G-10/G-01 remain OPEN; schema readiness NOT APPROVED. No tests, live files/data, DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 21 (2026-11-22)
- Read-only AuditService/AuditRepository and ProcurementService batch receipt/order failure-boundary trace recorded in `TASK-2.8-PROCUREMENT-RECEIPT-BATCH-AUDIT-FAILURE-BOUNDARY-INCREMENT-21.md`.
- Batch receipts validate and normalize before one receipt-sheet insertMany, then update order status sequentially and write audits separately. No cross-sheet transaction is demonstrated; replay keys do not prove payload-equivalence or uniqueness enforcement. Audit rows are tenant-filtered at service reads but immutable/transactional durability not established.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 22 (2026-11-22)
- Read-only trace of Audit GET route permission gates, tenant filtering, validator/service distinction and test-tree inventory documented in `TASK-2.8-AUDIT-ROUTE-PERMISSION-TEST-TRACE-INCREMENT-22.md`.
- Router gates `/audit`, `/audit/entity/*`, `/audit/user/*` with REPORT_VIEW; service tenant-filters results and validates same-tenant target user. AuditRepository scans broad sheet then filtering occurs in service. No dedicated audit test filename was identified in test tree; filename absence is not assertion coverage proof.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 23 (2026-11-22)
- Read-only crosswalk of procurement mutation POST routes, permission gates, service defense and test-tree evidence recorded in `TASK-2.8-PROCUREMENT-MUTATION-ROUTE-PERMISSION-TEST-CROSSWALK-INCREMENT-23.md`.
- Order, single receipt and batch receipt POST routes require PROCUREMENT_MANAGE; relevant service checks/locks/validation are documented, while multi-sheet transaction boundaries remain unresolved. Procurement test filenames found but assertion mapping and execution are not yet proven.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 24 (2026-11-22)
- Actual assertions in procurement-service, procurement-event-integration and material-planning test files were read and mapped in `TASK-2.8-PROCUREMENT-MATERIAL-PLANNING-ASSERTION-COVERAGE-INCREMENT-24.md`.
- Source assertions cover allocation cap/surplus, request-level read reuse, event-source-bound confirmed quantity, event integration restrictions, replay, source void history, revision and cross-tenant/invisible project rejection. They were not executed.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 25 (2026-11-22)
- Actual assertion crosswalk for project deletion, event idempotency and workflow enforcement tests documented in `TASK-2.8-PROJECT-DELETION-EVENT-IDEMPOTENCY-WORKFLOW-ASSERTION-CROSSWALK-INCREMENT-25.md`.
- Assertions observed include shared-order dependency preservation, tenant/project scoped deletion collection, event replay/PROCESSING/COMPLETED safeguards and workflow rule revalidation under lock. Tests were not executed; production behavior and complete recovery coverage remain unverified.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 26 (2026-11-22)
- Assertion crosswalk for attachment visibility, repeat-reference copy and schema-contract tests documented in `TASK-2.8-ATTACHMENT-REFERENCE-COPY-SCHEMA-CONTRACT-ASSERTION-CROSSWALK-INCREMENT-26.md`.
- Positive assertions cover attachment event/project visibility, reference copy operation plan/history/live-sync/idempotency/policy and source-level schema/deploy guard contracts. Attachment storage failure recovery, runtime execution and PostgreSQL migration correctness remain unverified.
- G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 27 (2026-11-22)
- Read-only assertion crosswalk for access-control caches, user validation, reusable forms and form API contract documented in `TASK-2.8-ACCESS-CONTROL-USER-VALIDATION-REUSABLE-FORM-ASSERTION-CROSSWALK-INCREMENT-27.md`.
- Assertions cover request-scoped caches, password validation, reusable form snapshots/visibility/requiredness/idempotency and amendment permission/concurrency/replay. Runtime route authorization, auth/session security and DB-level tenant constraints remain unverified. Tests not run.
- G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 28 (2026-11-22)
- Read-only selected frontend assertion crosswalk documented in `TASK-2.8-FRONTEND-BACKEND-CONTRACT-ASSERTION-CROSSWALK-INCREMENT-28.md`; production frontend repo remains unmodified.
- Tests assert dossier/revision history, event submit progress/retry/accessibility, five-second success close, repeat-reference sync/conflict/retry and receipt form loading/error behavior. Tests not executed and are not API/database integrity evidence.
- G-01 remains OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 29 (2026-11-22)
- Consolidated blocker evidence classifications and measurable closure criteria across increments 1–28 recorded in `TASK-2.8-CONSOLIDATED-EVIDENCE-COVERAGE-BLOCKER-CLOSURE-CRITERIA-INCREMENT-29.md`.
- Evidence currently E1/E2 only (source and test assertion inspections); E3 runtime test evidence and E4 live-data/database evidence are absent by scope. Critical closure criteria for G-01/G-02/G-03/G-04/G-10 and cross-cutting audit/transaction/migration/auth risks are recorded. Schema readiness remains NOT APPROVED; Task 2.8 not complete.


## Phase 2 — Task 2.8 Increment 30 (2026-11-22)
- Read-only literal route-family inventory from backend Router.js recorded in `TASK-2.8-ROUTER-ROUTE-FAMILY-INVENTORY-INCREMENT-30.md` (51 dispatch literal matches in source extraction; duplicates represent method-specific route occurrences). Dynamic path families explicitly flagged as requiring separate trace.
- Route inventory is a checklist, not an exhaustive authorization or tenant audit. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 31 (2026-11-22)
- Dynamic/prefix Router.js pattern inventory recorded in `TASK-2.8-DYNAMIC-ROUTER-PATH-PATTERN-INVENTORY-INCREMENT-31.md`, including GET, POST, update/delete and admin/destructive families.
- Inventory remains a dispatch-pattern extraction; route-method combinations, permissions, payload validation and service effects are not exhaustively traced. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 32 (2026-11-22)
- Focused read-only Router.js trace for admin project deletion preview/remove, admin session listing/revocation and attachment list/download/upload gates recorded in `TASK-2.8-HIGH-RISK-ADMIN-DELETION-ATTACHMENT-ROUTE-TRACE-INCREMENT-32.md`.
- Explicit admin checks and ATTACHMENT_VIEW/ATTACHMENT_UPLOAD gates observed in sampled routes; project attachment ownership is path-bound. Runtime authorization, exhaustive variants and tests remain unverified.
- G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 33 (2026-11-22)
- Read-only Router crosswalk for central project information save/resolve/approve/deactivate, reusable form definition/assignment, event form amendment, event void, workflow update and workflow event-rule mutation recorded in `TASK-2.8-PROJECT-INFORMATION-FORM-EVENT-WORKFLOW-MUTATION-PERMISSION-TRACE-INCREMENT-33.md`.
- Explicit gates observed: PROJECT_EDIT, SYSTEM_SETTINGS, EVENT_TYPE_MANAGE (or SYSTEM_SETTINGS for assignment), EVENT_EDIT, EVENT_CREATE and WORKFLOW_MANAGE. Exhaustive service/runtime/tenant test coverage remains unverified.
- G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 34 (2026-11-22)
- Read-only Router trace for user/role/permission, department and workflow GET route gates documented in `TASK-2.8-RBAC-DEPARTMENT-WORKFLOW-ROUTE-PERMISSION-TRACE-INCREMENT-34.md`.
- Explicit gates observed, including broad requireAny permission lists for department and workflow read routes. Least-privilege business suitability, service tenant checks and runtime tests remain unverified.
- G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increments 35–36 (2026-11-22)
- Core project read/mutation route crosswalk documented in `TASK-2.8-CORE-PROJECT-READ-MUTATION-ROUTE-CROSSWALK-INCREMENT-35.md`; route families and sampled gates catalogued.
- Follow-up on PUT print-template project-data binding documented in `TASK-2.8-PRINT-TEMPLATE-PROJECT-BINDING-GATE-VERIFICATION-INCREMENT-36.md`. Router branch lacks visible explicit gate, but PrintTemplateService enforces source-specific PROJECT_EDIT/EVENT_EDIT, tenant-scoped template lookup, active template, latest-binding and stale-source checks; no vulnerability conclusion.
- G-01/G-10 remain OPEN; schema readiness NOT APPROVED. Tests not executed; no live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 37 (2026-11-22)
- GET route permission/domain crosswalk for project nested reads, event/form/data, workflow, reusable forms/information types/event types, procurement read and reporting routes recorded in `TASK-2.8-GET-ROUTE-PERMISSION-DOMAIN-CROSSWALK-INCREMENT-37.md`.
- Source gates and dispatch are recorded; exhaustive callers, service tenant validation and runtime test evidence remain unverified. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 38 (2026-11-22)
- POST mutation route crosswalk recorded in `TASK-2.8-POST-ROUTE-MUTATION-PERMISSION-CROSSWALK-INCREMENT-38.md`, covering project/draft fallback, reference copy, workflow, config/forms, events/attachments and RBAC.
- Special same-tenant own-DRAFT fallback and route-bound attachment ownership highlighted for test coverage; runtime tests and exhaustive service validation remain unverified. G-01/G-10 OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 39 (2026-11-22)
- PUT/DELETE route crosswalk recorded in `TASK-2.8-PUT-DELETE-ROUTE-CROSSWALK-INCREMENT-39.md`, covering update/deactivate/remove, admin delete/session, attachment and workflow/form/RBAC families.
- Dispatch gates documented; service-level tenant guards, atomicity, retention/recovery and runtime test evidence remain unverified. G-01/G-03/G-04/G-10 OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 40 (2026-11-22)
- Draft V2 transaction/integrity/test matrix across tenant/RBAC, project, central info/history, event/form, workflow, material planning, procurement/allocation/receipt, attachments, deletion and audit documented in `TASK-2.8-V2-TRANSACTION-INTEGRITY-TEST-MATRIX-DRAFT-INCREMENT-40.md`.
- Matrix and transaction strategy are evidence-derived proposals only, not approved business policy or schema. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No implementation, tests, live data, DDL/migrations, provisioning or deployment.


## Phase 2 — Task 2.8 Increment 41 (2026-11-22)
- Remaining technical defaults vs unresolved business decisions and evidence boundary recorded in `TASK-2.8-REMAINING-POLICY-DECISIONS-EVIDENCE-BOUNDARY-INCREMENT-41.md`.
- Source evidence pack is bounded and documented, but business-policy items remain unconfirmed, and no E3 runtime tests or E4 live-data evidence exist. Task 2.8 discovery/decision pack cannot declare schema readiness. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 42 (2026-11-22)
- Read-only tenant header/repository boundary trace added in `TASK-2.8-TENANT-INTEGRITY-HEADER-REPOSITORY-BOUNDARY-TRACE-INCREMENT-42.md`.
- Source observations: tenant-qualified headers in key tenant-owned entities and relation tables; global-looking Permission registry; AccessControlService tenant/user cache filtering; selected repository wrappers delegate ID-only operations without tenant predicate, so caller/service validation is a critical boundary.
- G-10/G-01 remain OPEN; source evidence is E1 only. Schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 43 (2026-11-22)
- Read-only authenticated tenant-context/RBAC resolution trace added in `TASK-2.8-AUTHENTICATED-TENANT-CONTEXT-RBAC-RESOLUTION-TRACE-INCREMENT-43.md`.
- Source evidence records session tenant/user binding, tenant-scoped role and RolePermission resolution, tenant-specific admin check and global permission catalog read. Router/service-specific authorization is relied upon beyond generic authenticated hook.
- G-10/G-01 remain OPEN; E1 only. Schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 44 (2026-11-22)
- Exact recursive-tree test inventories reconciled: 43 frontend test files and 43 backend test files, documented in `TASK-2.8-G01-FRONTEND-BACKEND-TEST-TREE-RECONCILIATION-INCREMENT-44.md`.
- Filename-level thematic mapping is preliminary and not test execution or coverage proof. G-01 remains OPEN; schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 45 (2026-11-22)
- Assertion source inspection across five backend tests documented in `TASK-2.8-SELECTED-LEGACY-TEST-ASSERTION-CROSSWALK-INCREMENT-45.md`, identifying tested assertion scope and mocked boundaries.
- E2 only; test execution not claimed. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 46 (2026-11-22)
- Assertion source crosswalk covering event idempotency, schema-contract and workflow event tests recorded in `TASK-2.8-EVENT-IDEMPOTENCY-SCHEMA-WORKFLOW-ASSERTION-CROSSWALK-INCREMENT-46.md`.
- E2 only; no runtime tests claimed. G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 47 (2026-11-22)
- Frontend assertion-source crosswalk recorded in `TASK-2.8-FRONTEND-ASSERTION-CROSSWALK-INCREMENT-47.md` for five selected tests.
- E2 only; no runtime tests/browser execution claimed. G-01 remains OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 48 (2026-11-22)
- Assertion-source crosswalk for form API, material planning, procurement integration and draft/reusable form lifecycle recorded in `TASK-2.8-FORM-MATERIAL-PROCUREMENT-DRAFT-ASSERTION-CROSSWALK-INCREMENT-48.md`.
- E2 only; no runtime tests claimed. G-01/G-02/G-03 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 49 (2026-11-22)
- Assertion-source crosswalk for report, print template, reference copy, image gallery and thumbnail test sources recorded in `TASK-2.8-REPORT-PRINT-REFERENCE-ATTACHMENT-ASSERTION-CROSSWALK-INCREMENT-49.md`.
- E2 only; mocked/storage boundaries noted. G-01/G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No runtime tests/live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 50 (2026-11-22)
- Consolidated evidence matrix and closure dependencies documented in `TASK-2.8-CONSOLIDATED-EVIDENCE-MATRIX-CLOSURE-DEPENDENCIES-INCREMENT-50.md`.
- G-01/G-02/G-03/G-04/G-10 remain OPEN. E1/E2 evidence only; E3 runtime testing unavailable via current GitHub-only access and prohibited against production. Seven business policy items remain unconfirmed. Schema readiness NOT APPROVED.


## Phase 2 — Task 2.8 Increment 51 (2026-11-22)
- Legacy schema-header tenant ownership classification documented in `TASK-2.8-SCHEMA-HEADER-TENANT-OWNERSHIP-CLASSIFICATION-INCREMENT-51.md`. TenantID presence mapped; Permission catalog appears global; EventData relies on EventID parent relation.
- E1 header inspection only; G-10 remains OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


## Phase 2 — Task 2.8 Increment 52 (2026-11-22)
- Header-derived tenant-qualified relationship crosswalk recorded in `TASK-2.8-TENANT-QUALIFIED-RELATIONSHIP-HEADER-CROSSWALK-INCREMENT-52.md`.
- Candidate tenant-consistent FK edges mapped from declarations; special cases noted. E1 only; G-10 remains OPEN, schema readiness NOT APPROVED. No tests/live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 53 (2026-11-22)
- Bounded read-only EventService/AttachmentService trace documented in `TASK-2.8-INDEPENDENT-EVENT-ATTACHMENT-OWNERSHIP-TRACE-INCREMENT-53.md`: independent-event listing/retrieval behavior, parent-project checks for project-linked events and attachment ownership/visibility/copy checks.
- Source evidence only; no runtime tests. G-10/G-04/G-01 remain open; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 54 (2026-11-22)
- Bounded repository lookup/filter boundary review documented in `TASK-2.8-REPOSITORY-LOOKUP-TENANT-FILTER-BOUNDARY-TRACE-INCREMENT-54.md`: generic ID lookup and selected relation repository filters are not themselves tenant-qualified; sampled service callers apply additional tenant/access checks.
- E1 source inspection only; exhaustive caller review/runtime negative tests remain pending. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 55 (2026-11-22)
- Bounded identity/RBAC repository tenant-boundary trace recorded in `TASK-2.8-IDENTITY-RBAC-REPOSITORY-TENANT-BOUNDARY-TRACE-INCREMENT-55.md`: selected ID lookup and association repository methods are not tenant-qualified themselves; sampled service-level role validation/filtering is layered above.
- E1 only; uniqueness scopes and exhaustive mutation/caller/runtime testing remain unresolved. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 56 (2026-11-22)
- Bounded UserRoleService/RolePermissionService/PermissionService trace documented in `TASK-2.8-USER-ROLE-MUTATION-AUDIT-BOUNDARY-TRACE-INCREMENT-56.md`: assignment tenant/role checks, duplicate guard and separate audit write sequence; tenant-based active-role resolution and global permission-catalog read sampled.
- E1 only; runtime behavior and complete mutation coverage unverified. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 57 (2026-11-22)
- Bounded procurement/material-planning lifecycle trace recorded in `TASK-2.8-PROCUREMENT-MATERIAL-PLANNING-LIFECYCLE-TRACE-INCREMENT-57.md`: tenant-scoped repository reads, locked/idempotent order and receipt paths, compensating NEEDS_REVIEW behavior for partial order writes, calculation/spec revision lifecycle, source-event void handling and separate audit writes.
- E1 source review only; no runtime testing. G-02/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 58 (2026-11-22)
- Event void and ProjectInformation revision/sync trace recorded in `TASK-2.8-EVENT-VOID-PROJECT-INFORMATION-REVISION-SYNC-TRACE-INCREMENT-58.md`: sequential void restoration/cascade/audit writes and recoverable material reconciliation failure, plus information current/history/audit/reference-sync write boundaries and sampled Form/EventData controls.
- E1 only; runtime tests not executed. G-03/G-10/G-01 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 59 (2026-11-22)
- Attachment storage/delete lifecycle trace recorded in `TASK-2.8-ATTACHMENT-STORAGE-DELETE-LIFECYCLE-TRACE-INCREMENT-59.md`: Google Drive storage abstraction, sequential file/metadata/audit writes and delete-to-trash/soft-delete boundary; recovery/retention/backup policies not established in inspected source.
- E1 source review only; runtime testing not executed. G-04/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 60 (2026-11-22)
- Audit/project cascade deletion integrity trace recorded in `TASK-2.8-AUDIT-PROJECT-CASCADE-DELETION-INTEGRITY-TRACE-INCREMENT-60.md`: audit service/repository boundaries and sequential dependency soft deletion, storage trashing and physical project row deletion with partial failure reporting.
- E1 source review only; runtime tests not executed. G-04/G-10/G-01 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 61 (2026-11-22)
- Bounded Router/API permission boundary trace recorded in `TASK-2.8-ROUTER-API-PERMISSION-BOUNDARY-TRACE-INCREMENT-61.md`: sampled procurement, project material planning, information/history, attachment and audit routes with route permission layers.
- E1 only; full endpoint matrix and route tests pending. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 62 (2026-11-22)
- Workflow version/event rule lifecycle trace recorded in `TASK-2.8-WORKFLOW-VERSION-EVENT-RULE-LIFECYCLE-TRACE-INCREMENT-62.md`: versioned rule fields, clone/replace behavior under lock and bounded workflow/event type version validation.
- E1 only; runtime tests and full aggregate publish path review pending. G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data/DDL/migrations/provisioning/deployment/production changes.


### Task 2.8 Increment 63 (2026-11-22)
- Legacy test inventory and V2 verification gap map recorded in `TASK-2.8-LEGACY-TEST-INVENTORY-VERIFICATION-GAP-MAP-INCREMENT-63.md`. Verified 40 test files in the reference backend tests tree; read representative test names for attachment access, project deletion, procurement, workflow rules, event idempotency and material planning.
- Test execution/pass status is not claimed. PostgreSQL/transaction/RLS/route matrix/storage recovery coverage remains unverified. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No production changes.


## Phase 2 — Task 2.8 Increment 64 (2026-11-23)
- Consolidated increments 52–63 into a domain-by-domain coverage/evidence matrix and readiness gap register in `TASK-2.8-CONSOLIDATED-COVERAGE-EVIDENCE-READINESS-MATRIX-INCREMENT-64.md`.
- G-01 (complete mutation/route/dependency coverage) and G-10 (tenant integrity) remain OPEN/BLOCKERS; G-02/G-03/G-04 remain OPEN/HIGH. Runtime verification is not established.
- Task 2.8 remains IN PROGRESS; schema/migration readiness is NOT APPROVED. No production modifications or live data access.


## Phase 2 — Task 2.8 Increment 65 (2026-11-23)
- Recorded a bounded Router.js GET dispatcher branch inventory and selected authorization patterns in a dedicated audit artifact.
- Evidence is source-observed only; mutation route coverage and runtime tests remain pending. G-01 remains OPEN/BLOCKER; no production changes.


## Phase 0 — READ-ONLY audit checkpoint (Increment 66, 2026-11-23)
- Corrected GitHub owner spelling for retrieval; Router.js was successfully retrieved and read-only POST/PUT/DELETE route dispatch inventory documented in `docs/audit/TASK-2.8-ROUTER-MUTATION-ROUTE-INVENTORY-INCREMENT-66.md`.
- Source review observation requiring follow-up: print-template project-data binding PUT route lacks visible Router-level permission guard; downstream authorization not yet verified, so this is not classified as a confirmed vulnerability.
- Task 0.2 remains IN PROGRESS; no runtime testing or production modification.


## Phase 0 — Task 0.2 acceptance reconciliation (2026-11-23)
- Product Owner accepted the Increment 149 read-only source audit closure pack.
- Task 0.2 is COMPLETE; TODO.md records acceptance and limitations. This document's earlier historical entries that describe Task 0.2 as IN PROGRESS are checkpoint records, not the current status.
- Readiness limitations are unchanged: E1/E2 evidence only, no E3 execution evidence or E4 live-data evidence. G-01/G-10 remain blockers; schema readiness remains NOT APPROVED.
- Repository recheck confirms PRSPRO2-V2, PRSPRO2BACKEND-V2, PRSPRO2DATABASE-V2 exist and are private but empty. No files bootstrapped in this increment.


## Phase 2 — Task 2.8 Increment 151 (2026-11-23)
- Bounded read-only event/form response preparation and tenant-boundary trace recorded in `docs/audit/TASK-2.8-EVENT-FORM-PREPARATION-TENANT-PAYLOAD-TRACE-INCREMENT-151.md`.
- Observed EventDataService parent/tenant/project availability checks and JSON validation; FormService mapped response validation, required forms, duplicate IDs, form version, field visibility/required checks and tenant-scoped mappings; EventType normalization observations recorded.
- E1 source evidence only; G-01/G-03/G-10 remain open. No tests, live data, schema, migrations, production modifications, deployment or VPS actions.


## Phase 2 — Task 2.8 Increment 152 (2026-11-23)
- Procurement order/single receipt/batch receipt locking and idempotency paths traced in a bounded read-only review; documented in `docs/audit/TASK-2.8-PROCUREMENT-IDEMPOTENCY-LOCK-BATCH-RECEIPT-TRACE-INCREMENT-152.md`.
- Source observed lock/idempotency replay checks and separate batch receipt validation/accounting path. Transaction/constraint guarantees are not established from source alone. G-01/G-02/G-06/G-10 remain open. No runtime tests, live data, SQL/migrations, provisioning or production modifications.


## Phase 2 — Task 2.8 Increment 153 (2026-11-23)
- Bounded read-only receipt accounting/projection trace documented in `docs/audit/TASK-2.8-PROCUREMENT-RECEIPT-QUANTITY-RECONCILIATION-PROJECTION-TRACE-INCREMENT-153.md`.
- Observed single/batch receipt path differences in general-stock allocation validation, replay semantics, cumulative allocation/quantity checks and post-write order-state/audit sequence; read projections use differing receipt accounting/clamping/distribution logic. Canonical net receipt/reversal contract remains unverified.
- G-02 remains OPEN/BLOCKER; G-01/G-06/G-10 remain OPEN. Schema readiness NOT APPROVED. E1 source evidence only; no runtime tests/live data/schema/migration/provisioning/production modifications.


## Phase 2 — Task 2.8 Increment 154 (2026-11-23)
- Procurement Router and repository surface crosswalk documented in `docs/audit/TASK-2.8-PROCUREMENT-ROUTER-REPOSITORY-SURFACE-CROSSWALK-INCREMENT-154.md`.
- Retrieved Router excerpt shows procurement read endpoints and order/receipt single/batch POST commands with permission gates; no receipt correction/reversal route was visible in this bounded excerpt. ProcurementRepository sampled file is tenant-filtered collection read-only access. Absence is not asserted beyond inspected excerpt.
- G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. E1 only, no tests/live data/schema/migrations/provisioning/production modifications.


## Phase 2 — Task 2.8 Increment 155 (2026-11-23)
- Material-planning specification revision, calculation ledger, confirmed-need reconciliation and source-event void handling trace documented in `docs/audit/TASK-2.8-MATERIAL-PLANNING-SPECIFICATION-CALCULATION-RECONCILIATION-TRACE-INCREMENT-155.md`.
- Observed latest-spec revision selection, calculation-key replay lookup, 4-decimal ceiling calculation, sequential calculation/need/audit updates and source-event-void marking NEEDS_REVIEW. Atomicity/DB uniqueness/runtime behavior not established.
- G-01/G-03/G-06/G-10 remain OPEN; G-02 remains OPEN/BLOCKER. Schema readiness NOT APPROVED. E1 only; no tests/live data/DDL/migrations/provisioning/production changes.


## Phase 2 — Task 2.8 Increment 156 (2026-11-23)
- Procurement event integration configuration, form-response quantity source validation and central project-information linkage to material recalculation documented in `docs/audit/TASK-2.8-PROCUREMENT-EVENT-INTEGRATION-TRIGGER-CENTRAL-INFORMATION-TRACE-INCREMENT-156.md`.
- Observed tenant/event-type configuration and numeric single-value information type requirements; exactly-once response source validation and source-event-linked central information checks before calculation. Atomicity across event, central info, calculation and audit remains unverified.
- G-01/G-03/G-06/G-10 remain OPEN; schema readiness NOT APPROVED. E1 only; no tests/live data/DDL/migrations/provisioning/production changes.


## Phase 2 — Task 2.8 Increment 157 (2026-11-23)
- Event create/void orchestration, idempotency PROCESSING/COMPLETED markers, form persistence, project-state writes, integration failure handling and material-needs reconciliation failure paths documented in `docs/audit/TASK-2.8-EVENT-CREATE-VOID-ORCHESTRATION-PARTIAL-FAILURE-TRACE-INCREMENT-157.md`.
- Evidence shows sequential persistence and recoverable partial-failure boundaries; durable uniqueness, atomicity and runtime guarantees are not established. Void may persist before material reconciliation; failure is caught and logged for manual/recovery review.
- G-01/G-03/G-08/G-09/G-10 remain OPEN/pending discovery; schema readiness NOT APPROVED. E1 only; no tests/live data/DDL/migration/provisioning/production changes.


## Phase 2 — Task 2.8 Increment 158 (2026-11-23)
- Read-only FormService trace documented: response validation/snapshots, sequential response and central-information writes, amendment-event replay synchronization, ProjectInformation revision/history, and reference-sync failure boundaries.
- Amendment replay intentionally retries central-information synchronization after a response save, but writes across current-value/history/audit/reference-sync remain sequential and atomicity is unproven.
- G-01/G-03/G-04/G-09/G-10/G-11/G-12 remain OPEN/pending discovery; schema readiness NOT APPROVED. E1 only; no runtime tests, live data, DDL, migration, provisioning or legacy repo writes.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 159)
- Read-only attachment association, authorization, upload/download/thumbnail, Google Drive storage provider, copy and deletion lifecycle trace documented in `docs/audit/TASK-2.8-ATTACHMENT-ASSOCIATION-STORAGE-LIFECYCLE-TRACE-INCREMENT-159.md`.
- Source evidence identifies PROJECT/EVENT association patterns, inherited project visibility for linked events, sequential metadata/storage/audit operations, provider coupling, and recovery/orphan risks. Owner/FK coverage, storage reconciliation, delete/restore policy and runtime validation remain pending.
- G-04/G-11 remain open; G-01/G-10 and other readiness gaps remain unchanged. Schema readiness NOT APPROVED.
- No tests, live data/file access, SQL/DDL, migrations, DB provisioning, production modifications, deployment or VPS action.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 160)
- Added bounded attachment-validator and Router permission crosswalk in `docs/audit/TASK-2.8-ATTACHMENT-VALIDATOR-ROUTE-GATE-TRACE-INCREMENT-160.md`.
- Observed MIME/extension allow-list, filename and base64-size checks, PDF signature check in provider, ATTACHMENT_VIEW route gate on read/download/thumbnail/list surfaces, and additional tenant/owner checks in service paths. Upload/delete route gate details remain pending.
- G-04/G-11 and broader tenant/FK/mutation coverage remain open; schema readiness NOT APPROVED.
- No tests, live files/data, SQL/DDL, migrations, production modifications or deployment.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 161)
- Recorded attachment upload/delete Router permission gates and dependency-aware project cascade deletion source trace in `docs/audit/TASK-2.8-ATTACHMENT-MUTATION-PROJECT-CASCADE-TRACE-INCREMENT-161.md`.
- Observed ATTACHMENT_UPLOAD/ATTACHMENT_DELETE route gates, tenant-scoped dependency collection, shared procurement protection rules, sequential child soft-delete/Drive trash/event-void and conditional physical project deletion. Lock does not provide atomicity; partial-failure recovery remains unproven. Preview path includes a schema ensure call in the sampled service.
- G-04/G-09/G-10/G-11 and mutation coverage remain open; schema readiness NOT APPROVED.
- No runtime actions, tests, live data/files, SQL, migration or production modifications.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 162)
- Documented bounded discovery boundary for file references in FormService/EventDataService at `docs/audit/TASK-2.8-FORM-ATTACHMENT-LINK-DISCOVERY-BOUNDARY-INCREMENT-162.md`. This scan did not establish explicit file-reference tokens in FormService; front-end field serialization and related source paths still require discovery. This is not proof of absence.
- G-03/G-04/G-11 remain open; schema readiness NOT APPROVED.
- No runtime tests, production data/files, modifications to production, SQL or migration.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 163)
- Added bounded tenant qualification and partial-failure boundary findings for project deletion collector in `docs/audit/TASK-2.8-CASCADE-TENANT-RELATION-FAILURE-BOUNDARY-INCREMENT-163.md`.
- Event-data collector selection appears keyed by active collected event IDs without an independent TenantID filter in the sampled code; this is a reliance on ID/relationship integrity, not proven cross-tenant safety. Attachments use tenant filter plus entity links. Collector reads fail closed on sheet read errors, but deletion remains sequential and non-transactional.
- G-01/G-04/G-09/G-10/G-11 remain open; schema readiness NOT APPROVED.
- No tests, live data/files, production modifications, SQL, migration or deployment.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 164)
- Traced actual frontend file-field upload/reference semantics from read-only legacy frontend source and recorded `docs/audit/TASK-2.8-FRONTEND-FORM-FILE-REFERENCE-SEMANTICS-TRACE-INCREMENT-164.md`.
- Event configured form file fields upload through project-scoped `/projects/{projectId}/attachments`, then embed AttachmentID/FileName/MimeType/FileSize/AttachmentType metadata in response values. This indicates a project-owned file with form-response reference in this observed path; direct binary payload is not persisted in form value. Independent-event upload is rejected in this helper.
- Reuse, clear/removal, amendment and orphan lifecycle policies remain unproven. G-03/G-04/G-09/G-10/G-11 remain open; schema readiness NOT APPROVED.
- No runtime tests, live files/data, production modifications, schema, migration or deployment.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 165)
- Added crosswalk of project attachment upload UI and lifecycle in `docs/audit/TASK-2.8-PROJECT-ATTACHMENT-UI-LIMIT-LIFECYCLE-CROSSWALK-INCREMENT-165.md`.
- Project edit/create sample saves project before upload and uses a 5 MB client-side limit; configured event form upload uses a 10 MB limit (from Increment 164). Server settings remain authoritative. Project save and attachment upload are sequential, so failure may leave saved project but upload incomplete.
- G-04/G-09/G-11 remain open; schema readiness NOT APPROVED.
- No runtime tests, live data/files, production modifications, schema or migration.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 166)
- Read-only trace of repeat-project image/reference copying in backend ProjectService.js recorded at `docs/audit/TASK-2.8-REPEAT-PROJECT-IMAGE-REFERENCE-COPY-TRACE-INCREMENT-166.md`.
- The helper validates source Attachment tenant, PROJECT owner and source project and image MIME; copies attachment to target project and replaces reference with new target-owned attachment metadata. Active image-typed information columns are traversed in scalar/table values. Lock/status/retry persistence exists in sampled orchestration; atomicity/compensating cleanup for multi-file copying is unproven.
- Copy-vs-share policy and durable integrity/recovery remain design gaps. G-04/G-09/G-10/G-11 remain open; schema readiness NOT APPROVED.
- Source trace only; no runtime tests, live data, production modification, migration or deployment.


## Phase 2 — Task 2.5 checkpoint (2026-11-23, Increment 167)
- Added revision/history/soft-delete trace of ProjectInformationService.js in `docs/audit/TASK-2.8-PROJECT-INFORMATION-REVISION-HISTORY-DELETE-TRACE-INCREMENT-167.md`.
- Observed current ValueJSON/revision/status/source-event metadata plus history snapshots, ExpectedRevision stale-edit guard, sequential update/history/audit/live-sync; soft-delete increments revision and creates history/audit. Atomicity remains unproven. File-based live-sync conflict USE_SOURCE is rejected in sampled conflict resolution.
- G-01/G-03/G-04/G-09/G-10/G-11/G-12 remain open; schema readiness NOT APPROVED. Source-only, no tests/data/production changes/migration.
