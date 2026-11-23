# Task 2.8 Increment 121 — Storage, audit, reporting and print-template boundaries

Date: 2026-11-23  
Evidence: E1 — source inspection; legacy repositories read-only

## Source areas inspected
- GoogleSheetsRepository.js (request cache, row lookup/cache invalidation, lock/write operations)
- GoogleDriveStorage.js and StorageRepository.js (storage abstraction, upload, tenant folders, copy/get/delete)
- AuditService.js / AuditRepository.js
- ReportService.js / ReportRepository.js
- PrintTemplateService.js / PrintTemplateRepository.js / PrintTemplateValidator.js

## Observed boundaries
### Data repository and caching
- Sheets repository maintains sheet/header caches and request-scoped list, record, find and column-index caches. Cache invalidation is performed after writes to a sheet; cache objects and row records are copied in some read paths but findById can return the cached record directly.
- Insert/update operations use ScriptLock and invalidate request caches after writes. Update-by-ID locates a row then replaces selected fields; database-style uniqueness and transactional constraints are not inherent to this storage layer.
- Row-number lookup builds a column value map for the request, preserving first-match behavior for duplicate values. The model is spreadsheet-based, not relational and cannot itself guarantee FK or unique constraints.

### File/object storage
- Drive storage validates required metadata, configured MIME types, base64 decoding, selected file signatures (PDF and other supported content checks), and size limits; tenant-specific upload path exists and sanitizes tenant folder names.
- Storage keys are Google Drive file identifiers; copy/get/thumbnail/base64/delete are delegated through the storage abstraction.
- Storage operations and domain metadata/audit are generally orchestrated by services in sequential steps. Cross-system transaction, object retention, and automatic orphan reconciliation are not inherent.

### Audit
- Audit records include tenant/user/action/entity, context/data/reason fields and timestamp; audit queries are tenant-filtered in service paths and entity/user filtering is implemented.
- Audit is a separate write from the domain mutations in sampled service code; transaction/outbox semantics and immutable storage guarantees are not established.

### Reports
- Project reports validate tenant and project visibility, and assemble events, procurement, material needs and project data. Dashboard/report aggregation builds visible-project indexes and grouped event collections to reduce repeated scans.
- Reports are assembled from multiple sheet collections; stable as-of snapshot and transactional consistency are not demonstrated.

### Print templates
- Reusable templates are tenant scoped and versioned, definitions hold bindings rather than embedded project values, and preview/resolve paths validate project visibility and allowed source bindings.
- Data resolution covers project fields, central information, event fields, event form responses and selected form fields; latest-source edits use source revision/timestamp checks and domain-specific permissions.
- Batch resolve limits template count, combines bindings into one shared project data pass and falls back to individual resolution where necessary.
- Template images/PDFs are tenant uploads, validated by MIME/content and size; storage metadata updates, cleanup of unreferenced files and audit are sequential. Cleanup/recovery and atomicity are not proven.
- Template schema is lazily ensured additively; no storage-level uniqueness or transactional version/CAS is demonstrated for all mutation paths.

## V2 implications
1. PostgreSQL should enforce PK/FK, tenant-scoped uniqueness and referential integrity; eliminate spreadsheet row-position identity.
2. Use object storage with tenant-aware policy, immutable/opaque storage keys, checksum, metadata, upload staging, scanning, durable lifecycle and reconciliation.
3. Domain mutation plus audit should be transactional or use an outbox; audit should be append-only with restricted access and tamper-resistance strategy.
4. Reports should use explicit transaction snapshots/as-of semantics or defined materialized projections; verify project visibility before aggregates.
5. Print templates should use typed/versioned binding schemas and explicit permissions, optimistic concurrency, snapshot/revision contracts and safe file lifecycle.
6. Define request-scoped cache and DB query performance policies rather than emulating spreadsheet caches; audit cache invalidation and projection freshness.
7. Preserve source behavior where business rules are verified, while recording unresolved semantics rather than inferring.

## Verification limits
- Source-only; no runtime tests, production changes, live files or user data accessed.
- E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.
