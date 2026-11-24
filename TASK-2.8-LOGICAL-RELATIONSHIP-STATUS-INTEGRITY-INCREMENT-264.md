# Task 2.8 — Logical Relationship and Status Integrity Test Blueprint (Increment 264)

## Basis
The audited Google Sheets model uses logical relationships rather than database-enforced foreign keys in many domains; prior source audit mapped projects/events/event-data/forms/information/workflow/attachments/audit/procurement/material planning. Selected schema-contract tests focus on additive schemas, material schema/history, sync guard and event idempotency. This increment formalizes integrity checks not evidenced by the selected mutation-focused tests; not an exhaustive absence claim.

## Referential integrity test matrix
- I-01 Events.ProjectID: tenant-matched existing project or allowed independent-event blank; reject invalid/cross-tenant references.
- I-02 EventData.EventID: active or appropriately retained historical event; no orphan write.
- I-03 EventFormData EventID/FormID: event and form tenant/project/version context match; snapshot retained.
- I-04 EventTypeForms: valid event-type/form mapping, active/allowed form and tenant scope.
- I-05 FormFields.FormID and InformationTypeID: both references exist, tenant-bound and compatible type.
- I-06 ProjectInformation ProjectID/InformationTypeID: tenant/project/type references and uniqueness/cardinality rules.
- I-07 Information history links to parent record and maintains revision/approval state.
- I-08 ReferenceSyncLog source/target project and information type: tenant scoped, stable operation key and outcome.
- I-09 WorkflowSteps/Department/Rules: published workflow version and stable references; prevent dangling/deleted dependencies.
- I-10 Events workflow/step/type snapshot: retain historical version context; current workflow changes do not reinterpret past event.
- I-11 Attachment polymorphic EntityType/EntityID: entity exists, belongs to tenant and is visible; disallow unsupported type.
- I-12 Audit polymorphic EntityType/EntityID: validate actor/tenant and correlation; preserve audit even if entity later archived.
- I-13 MaterialSpec.ProjectID and calculation SourceEventID/spec: tenant/project coherence; revision lineage.
- I-14 MaterialNeed.MaterialSpec/ProjectID/SourceEventID: matching project and tenant; status-aware countability.
- I-15 PurchaseOrderItem.OrderID/MaterialNeedID: tenant coherence and independent/general order semantics.
- I-16 Allocation.ItemID/ProjectID/NeedID: allocation graph consistent; enforce caps and shared-item rules.
- I-17 Receipt.ItemID/ProjectID/SourceEventID: coherent tenant and allocation/quantity constraints.
- I-18 Cascade archive/void: retained child references remain valid as history; shared/general procurement graph not destroyed.
- I-19 Status normalization: null/blank/legacy/mixed-case and ACTIVE/VOID/DELETED/SUPERSEDED/NEEDS_REVIEW handling are explicitly specified.
- I-20 Reconciliation report detects orphan records, invalid tenant edges, duplicate operation keys and inconsistent aggregates without automatically destructive repair.

## V2 database design implications
- Convert logical edges to FK constraints where appropriate; polymorphic edges should be modeled as typed relation tables or constrained domain references, not unconstrained pointers.
- Use composite tenant keys/constraints to enforce tenant alignment.
- Specify delete behavior per relationship: restrict, archive, detach, or outbox-backed external cleanup; do not blanket cascade.
- Preserve version snapshots and audit lineage.
- Implement migration validator that reports pre-existing orphan/duplicate/inconsistent records and requires explicit remediation policy.

## Verification
Blueprint only. No data scan or runtime test was run; production data was not accessed.
