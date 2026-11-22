# PMS V2 — Authoritative Phase Status

Updated: 2026-11-22

## Phase 0 — Discovery & Repository Preparation
COMPLETE and accepted (Tasks 0.1, 0.2, 0.3).

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
