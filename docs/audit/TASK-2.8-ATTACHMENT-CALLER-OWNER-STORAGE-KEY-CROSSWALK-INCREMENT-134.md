# Task 2.8 Increment 134 — Attachment caller, owner and storage-key crosswalk

Date: 2026-11-23  
Evidence: E1 — read-only source and caller inventory.

## Source families cross-checked
Backend AttachmentService/Repository/Validator and GoogleDriveStorage (previous increments 103/104/121); frontend projects/project-detail/project-images/events/attachments/print-designer and material/information/form flows (increments 129–131).

## Observed owner/caller patterns
- Legacy Attachment metadata owner types are observed in sampled backend upload/visibility paths as PROJECT and EVENT; service validates parent/tenant visibility in sampled paths. This finite set is not yet proven for all historical data or unindexed callers.
- Project and event attachment callers use entity-specific upload endpoints or generic upload with EntityType/EntityID; gallery/project-card image views and event context reuse metadata IDs/storage keys for thumbnail/download.
- Project information file values and event form file values can reference attachment identifiers within typed value/response structures; the parent metadata entity is not necessarily a separate INFORMATION or FORM owner type in the inspected pathways. Relationship semantics require field-level mapping.
- Print-template image and PDF assets are managed directly by PrintTemplateService/GoogleDriveStorage and metadata stored in template asset fields, not necessarily through the generic Attachment rows. This is a distinct object-storage relationship family.
- Project-image copy workflow preserves source attachment linkage in metadata in sampled paths; duplicate/copy idempotency and cross-project ownership/recovery need explicit constraints.
- Storage keys and Drive file IDs are opaque references; deletion path storage-first then metadata status/audit, and upload path object-first then metadata/audit. Cross-system atomicity, orphan reconciliation and retention are not demonstrated.
- Frontend download/thumbnail UI is permission-aware in selected callers but backend visibility and direct object access behavior remain the enforcement boundary to test.

## V2 implications
1. Avoid polymorphic EntityType/EntityID without explicit typed owner relations or a well-defined polymorphic integrity model.
2. Distinguish general attachments, project/event attachments, project-information/form value references and print-template assets; define parent relationship, visibility, lifecycle and deletion policy for each.
3. Object storage key should be opaque, immutable and separate from metadata identity; record checksum, size, MIME, owner, uploader, timestamps, scan status and retention.
4. Staged upload and transactional metadata/outbox plus reconciliation must address partial object/database writes and orphan detection.
5. Copy operation should have durable idempotency and ownership/revision checks, and deletion should be recoverable rather than storage-first irreversible.
6. Test direct download/thumbnail, tenant/project visibility, embedded value references, copied images, file deletion, orphan reconciliation and storage failures.

## Remaining G-04/G-10 scope
- Exhaustive historical owner/reference inventory and field-level mapping remains incomplete.
- Attachment-related tenant-consistent constraints, owner lifecycle, retention/purge and migration validation remain blockers.

## Limits
- Source-only; no live file metadata, Drive access, runtime tests or production changes.
- E1 only; G-04/G-10 remain open; schema readiness NOT APPROVED.
