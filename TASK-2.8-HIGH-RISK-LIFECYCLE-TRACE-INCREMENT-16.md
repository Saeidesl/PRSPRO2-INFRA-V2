# Task 2.8 — High-Risk Lifecycle Relationship Trace Increment 16
Date: 2026-11-22
Source: targeted read-only snippets of EventService, FormService, AttachmentService, ProcurementService, ProjectDeletionService and ProjectService.

## Event/Form
- EventService.create has an idempotency-key branch, tenant/user checks and a lock; older callers without keys follow legacy unlocked behavior. Source snippet indicates fallback path, not full replay payload equivalence.
- EventService.workflowProgress validates ProjectID, user tenant, project tenant and ProjectService project-availability helper (previous increment evidence).
- FormService includes a snapshot builder capturing form ID/code/name/version and field-definition metadata; amendment path snippet validates linked amendment event against same tenant/project and inspects event type configuration.
- Evidence remains incomplete for complete correction chain, write atomicity, event/form payload mutation, immutable history and all mutation callers.

## Attachment
- AttachmentService._isAttachmentVisibleToUser_ checks attachment tenant, typed EntityType (PROJECT/EVENT), entity tenant, and project availability for project attachments; event attachments linked to project are checked through project tenant/availability.
- Registry attachment owner remains polymorphic. Previous evidence notes storage and metadata delete sequence and project-reference image copy; restore/orphan/purge behavior not exhaustively traced.

## Procurement
- ProcurementService has tenant-scoped idempotency handling in order/receipt paths (sampled snippets); createReceiptsBatch exists and uses a script lock. Previous increment sampled receipt atomicity risks and replay/receipt path assertions.
- Full batch semantics, rollback/compensation, payload equality and cross-project allocation invariants remain unverified.

## Project deletion
- ProjectDeletionService dependency collector filters active rows and tenant/project ownership for project-linked entities. Collector source includes event data and procurement-related sheets, including receipts, plus attachments and calculation/spec domains (prior source review).
- Header comment says admin-only invocation expected via Router. This is a documented expectation, not independent proof of complete route enforcement.
- Error handling, rollback/recovery, physical storage restore and complete indirect cross-project graph remain unresolved.

## Reference copy
- ProjectService source includes operation-ID replay handling for prior copy operations and visibility validation for the previously created project. Bounded snippet only.
- Exact all copied entities, partial-failure cleanup, storage and information sync, and transaction boundaries remain unverified.

## Risk classification
Positive evidence: selected tenant/availability checks, configured form amendment linkage, event/order/receipt idempotency hooks, deletion dependency collection and copy-operation replay are present in inspected source paths.
Not proven: exhaustive tenant relationship enforcement, atomicity, concurrency correctness, comprehensive caller coverage, data integrity, runtime behavior, or test execution.

## Gate impact
G-01 OPEN (no complete source/caller/operation trace).
G-02 OPEN (procurement business semantics and complete transaction evidence pending).
G-03 OPEN (policy and history/transaction evidence incomplete).
G-04 OPEN (owner/storage lifecycle and recovery incomplete).
G-10 OPEN (partial tenant checks do not prove all relationship-edge enforcement).
Schema readiness NOT APPROVED.

## Safety
Read-only source inspection only. No tests run, no live data, SQL/DDL, migration, provisioning, deployment, VPS or production modifications.
