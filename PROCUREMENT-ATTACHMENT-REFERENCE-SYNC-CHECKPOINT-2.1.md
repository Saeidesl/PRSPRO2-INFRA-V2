# Phase 2.1 — Procurement, attachments and reference-sync audit checkpoint

Date: 2026-11-22
Read-only source audit: PRSPRO2BACKEND-PRODUCTION ProcurementService.js, AttachmentService.js and ProjectInformationService.js.

## Procurement observations
- Purchase-order creation uses a lock and supports idempotency-key replay.
- Purchase-order items validate linked material needs and tenant/project relationships.
- Allocations can split order-item quantity among projects/needs; allocation total cannot exceed ordered item quantity.
- Allocation logic distinguishes linked need allocation and surplus quantities.
- Needs in review/superseded/cancelled/deleted states cannot be used for new order paths inspected.
- Order creation attempts to preserve partial records for recovery and marks an order NEEDS_REVIEW if partial work occurred (observed path).
- Receipt registration and batch receipts use lock/idempotency controls; batch processing validates duplicate request keys and calculates receipt totals across items/projects.
- Full status-transition and cancellation/receipt correction lifecycle remains pending.

## Attachment observations
- Attachment records store metadata and a storage key/provider; files are handled via StorageRepository abstraction in inspected paths.
- Upload validation and entity/project access checks occur; project files use server-generated canonical filenames.
- Attachments can be cloned for eligible project-information/profile-image categories; clone path creates storage copy and metadata, with cleanup on metadata failure.
- Attachment soft deletion is observed in a path, with physical storage deletion invoked in delete flow; full retention/recovery and event-void file semantics need validation.
- Sensitive storage internals are not exposed in lightweight project-gallery metadata path.
- V2 should separate binary storage objects from metadata, retain access policies and link lifecycle, and avoid destructive deletion without explicit retention design.

## Project information and reference sync observations
- Project-information values validate against InformationType and column definitions, including cardinality and data types.
- File/image values must reference validated attachments tied to the correct project and entity; multi-image values are restricted to images.
- Revisions and history records are maintained, including approval and source-event context.
- Save operations check expected revision for concurrency and idempotent replay of event/value combinations.
- Approval records are separate history actions; project-reference copies may not be approved as source.
- LIVE_SYNC writes sync logs/idempotency keys and checks local-target revisions for conflicts.
- Conflict resolution allows USE_SOURCE or KEEP_LOCAL and checks source/target revision expectations. File-based conflict resolution is explicitly unsupported in inspected path.
- Reference synchronization, history, and audit trails should be modelled separately and have recoverable, transactionally coordinated operations in V2.

## V2 design implications (provisional)
1. Procurement orders, items, allocations, receipts, material needs, and calculation records should be independently persisted, linked, and revision/status-aware.
2. Partial-operation recovery and NEEDS_REVIEW should be explicit, not hidden.
3. Idempotency should be first-class for order creation and receipt registration.
4. Attachments need metadata/object separation, ownership/entity linkage, permission checks, and a safe lifecycle.
5. Project-information revisions, approval, source-event provenance and live-reference sync logs/conflicts must be distinct concepts.
6. Transactional orchestration and reliable recovery are core V2 requirements.

## Pending
- Full procurement status-transition, cancellation and receipt adjustment/return logic.
- Storage abstraction implementation details, quotas, retention, physical deletion policy and failure recovery.
- Full reference-copy and attachment-copy orchestration.
- Test coverage and data constraints from repository/tests.
- No schema or migrations were implemented; no production changes or live-data access.
