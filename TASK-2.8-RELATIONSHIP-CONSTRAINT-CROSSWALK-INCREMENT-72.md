# Task 2.8 — Relationship & Constraint Crosswalk (Increment 72)

Date: 2026-11-23
Evidence classification: E1 — bounded source/header/repository inspection. Not runtime verified.

## Scope and source evidence
- Read DatabaseSetup.js header definitions and sampled repositories: ProjectRepository, EventRepository, ProjectInformationRepository, ProcurementRepository, AttachmentRepository, AuditRepository.
- DatabaseSetup headers confirm identifiers and logical reference columns across tenant, project, event, workflow, information/history, forms, attachment/audit and procurement domains.
- Repository samples reveal some generic ID accessors and collection filters rather than tenant-qualified constraints; tenant safety is predominantly enforced by services in previously sampled paths.
- MaterialPlanningRepository retrieval failed (NOT_FOUND); relationships inferred only where header/source evidence from other retrieved material exists and explicitly bounded.

## Crosswalk (logical edges, not approved DDL)
| Parent / key | Child / key | Constraint candidate / risk | Status |
|---|---|---|---|
| Tenant.ID | most tenant-owned rows TenantID | tenant-qualified FK/composite unique keys | candidate; tenant enforcement E1 only |
| Project.ID | Event.ProjectID | tenant-qualified FK; nullable for independent events pending discovery | logical edge; independent-event policy needs discovery |
| Event.ID | EventData.EventID | FK, likely payload uniqueness/cardinality unknown | candidate; pending cardinality |
| Event.ID | EventFormData.EventID | FK; snapshot/version preservation | logical edge confirmed header |
| EventType.ID | EventTypeForms.EventTypeID | tenant-qualified FK and uniqueness | candidate |
| Form.ID | EventTypeForms.FormID, FormFields.FormID | tenant-qualified FK; field/form lineage | candidate |
| InformationType.ID | FormFields.InformationTypeID; ProjectInformation.InformationTypeID; columns.InformationTypeID | FK plus active/deleted semantics | candidate |
| Project.ID + InformationType.ID | ProjectInformation | cardinality varies; enforce unique only after Cardinality rules are verified | unresolved uniqueness |
| ProjectInformation.ID | History.ProjectInformationID | FK; history retention/immutable ledger | candidate |
| Project.ID / Event.ID | Attachments.EntityType/EntityID | polymorphic owner; cannot safely use one ordinary FK | design blocker: normalize owner/link or constrained registry |
| Audit.EntityType/EntityID | entity pointers | polymorphic, archival durability | design decision pending |
| Workflow.ID + version | WorkflowSteps / rules / assignments | composite version-aware FKs; published version retention | candidate, full lineage unresolved |
| WorkflowStep.ID | StepDepartments / EventRules | composite tenant/workflow/version context needed | candidate |
| EventType.ID | EventFields / EventTypeProcurementSettings / rules | tenant FK, effective version/history rules | candidate |
| Project.ID | MaterialSpecs / MaterialNeeds / calculations | tenant FK; source event nullable/versioned | candidate |
| MaterialSpec.ID | Need.Calculation/spec refs; Calculation.MaterialSpecID | revision-aware FK, immutable calculation history | candidate |
| MaterialNeed.ID | PurchaseOrderItems.MaterialNeedID; allocations | partial/general procurement semantics and tenant composite FK | candidate, G-02 open |
| PurchaseOrder.ID | PurchaseOrderItems.PurchaseOrderID | FK; order status/partial receipt | candidate |
| PurchaseOrderItem.ID | Allocations/Receipts.PurchaseOrderItemID | FK; allocation/receipt quantity constraints | candidate, transactional enforcement needed |
| Project.ID | Allocations/Receipts.ProjectID | tenant FK; shared/general order protection | candidate |

## V2 constraint recommendations (not approved schema)
1. Use tenant-qualified composite keys for tenant-owned references (e.g. (tenant_id,id) unique and (tenant_id,parent_id) FKs) where modeling permits; do not rely solely on application checks.
2. Keep polymorphic attachment/audit pointers out of unconstrained FK design; consider owner-link tables or a typed ownership registry.
3. Make transaction-local business invariants (allocation sums, receipt totals, uniqueness/idempotency, revision compare-and-swap, project copy/deletion state) database-enforced where feasible and service-validated as well.
4. Retain immutable/versioned snapshots for workflow, form definitions/responses and material calculations; projections/current-state rows must be reconciled to their ledger.
5. Independent events, project-information cardinality, cascade/soft-delete retention and shared procurement semantics remain explicit discovery/policy gates; do not infer executable constraints from this matrix.

## Gaps and verification
- E1 only. No SQL/DDL, database provisioning, tests, live data, production changes or deployment.
- G-01, G-10 remain BLOCKER; G-02/G-03/G-04 remain HIGH/open. Schema readiness NOT APPROVED.
- Source access gap: MaterialPlanningRepository fetch NOT_FOUND; other modules and edge cases remain incomplete.
