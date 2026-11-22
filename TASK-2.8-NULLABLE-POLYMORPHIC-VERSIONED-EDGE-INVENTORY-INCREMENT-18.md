# Task 2.8 — Nullable, Polymorphic & Versioned Edge Inventory (Increment 18)
Date: 2026-11-22
READ-ONLY inspection of declared DatabaseSetup headers and prior service/repository evidence.

## Header-grounded edge inventory

| Entity | Fields/edges present in declared headers | Interpretation / caveat |
|---|---|---|
| Events | TenantID, ProjectID, WorkflowID/Version, WorkflowStepID, EventTypeID/Version, PerformedBy, CreatedBy, DepartmentID, prior-state snapshots | Multiple reference edges and historical snapshots; header alone does not establish nullable rules or FK enforcement |
| EventData | EventID, DataJSON | Parent event edge is direct; tenant field absent from header, so tenant derives from event, not independent row attribute |
| ProjectInformation | TenantID, ProjectID, InformationTypeID, SourceEventID, Revision, Status | Multi-parent references; SourceEventID appears optional in workflows but nullability requires policy/source validation |
| ProjectInformationHistory | TenantID, ProjectInformationID, ProjectID, InformationTypeID, Revision, Action, SourceEventID, ActorUserID | Repeated parent references allow cross-edge consistency checks; append-only semantics are candidate, not fully enforced |
| ReferenceSyncLog | TenantID, SourceProjectID, TargetProjectID, InformationTypeID, revisions, idempotency and actor | Cross-project dual-reference edge; must validate both projects/types share tenant and explicit sync policy |
| Attachments | TenantID, EntityType, EntityID, StorageProvider, StorageKey, SourceAttachmentID | Polymorphic entity owner and source-copy pointer; no conventional FK; declared provider list is currently legacy-specific |
| AuditLogs | TenantID, UserID, EntityType, EntityID, OldData/NewData | Polymorphic entity pointer and actor edge; immutability and actor retention need policy |
| MaterialNeeds | TenantID, ProjectID, SourceEventID, MaterialSpecID/Key, CalculationID, NeedType, CalculationKey | Candidate nullable/optional edge combinations: general/project need, source event, spec/calculation; source paths validate some combinations |
| PurchaseAllocations | TenantID, PurchaseOrderItemID, ProjectID, MaterialNeedID, quantity | Explicit multi-edge allocation; cross-project consistency is critical |
| MaterialReceipts | TenantID, PurchaseOrderItemID, ProjectID, SourceEventID, WarehouseDepartmentID, ReceivedBy, IdempotencyKey | General stock vs project receipts and optional event/department paths; exact nullable policies unresolved |
| FormFields | TenantID, FormID, InformationTypeID | Definitions link form and information type; both tenant consistency required |
| WorkflowStepEventRules | TenantID, WorkflowID, WorkflowVersion, WorkflowStepID, EventTypeID | Versioned multi-edge rule; enforce same tenant/version relationship and history policy |

## Constraint and policy implications
1. Rows without TenantID (notably EventData) require tenant-scoped parent access, or V2 should consider denormalized TenantID plus composite FK where beneficial.
2. Repeated references to same logical parent (ProjectInformationHistory ProjectID/InformationTypeID alongside ProjectInformationID) are useful for audit/reporting but can drift; use transactional write and relational consistency constraints.
3. Polymorphic pointers (Attachment/Audit) should not be represented by unrestricted text-type/entity-ID pairs in V2 without an ownership validation mechanism.
4. Optional edge combinations must be documented and validated by domain (general stock, project-specific need/receipt, source-event optionality); avoid blanket NOT NULL or permissive NULL assumptions.
5. Workflow and event/form versions should preserve historical interpretation rather than always FK to current mutable definition.
6. Idempotency columns are evidence of replay-related operations, not proof of uniqueness constraints or replay equivalence.

## Proposed validation checklist for future schema design
- Validate every tenant-bound multi-edge against the same TenantID.
- Decide and document each nullable relation and allowed edge combination before DDL.
- Preserve event/form/workflow definition snapshots and revision lineage.
- Resolve duplicate/redundant parent reference semantics, uniqueness and deletion behavior.
- Establish typed attachment/audit owner approach, storage cleanup and reconciliation.
- Add migration-time orphan, cross-tenant mismatch, invalid status, duplicate key and quantity-invariant reports.

## Limits / blocker state
This is header-grounded and source-informed only. No live data, tests, DDL, migration, provisioning, production changes or deployment. G-01/G-10/G-02/G-03/G-04 remain OPEN; Schema Readiness NOT APPROVED.
