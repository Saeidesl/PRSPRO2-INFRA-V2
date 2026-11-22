# Task 2.8 — Schema Registry, Auxiliary Lifecycle & Reset Safety Evidence (Increment 6)

Date: 2026-11-22
Scope: bounded READ-ONLY source review of production backend DatabaseSetup.js and the legacy frontend API/feature modules already sampled. No execution of setup/reset routines, no data access, no production writes.

## Evidence verified
- DatabaseSetup.js declares the configured sheet registry and header arrays for tenant/security, project/workflow, event/form, information/history, attachment/audit/print, material planning and procurement entities.
- Two distinct sheet names use the numeric prefix 18: `18_MaterialNeeds` and `18_ProjectStatuses`. The source defines distinct literal names; prefix numbering is not a reliable identity, order, or key.
- Several relationship-bearing entities use JSON/list fields or polymorphic IDs: Project AvailableDepartmentIDs/AllowedUserIDs, event prior-state snapshots and VariantDetailsJSON, definition/validation JSON fields, attachment EntityType/EntityID and StorageKey, and workflow/event-rule configuration. These need explicit target representations and migration reconciliation, not mechanical conversion.
- Project/Event/Form and material/procurement entities contain source IDs, version/revision, idempotency, status, provenance and snapshot fields. Preserve source identity and map lifecycle/lineage before normalization.
- DatabaseSetup.js includes a destructive reset routine that deletes/rebuilds sheet structure after a user confirmation dialog. It is a legacy setup utility; it was NOT executed. V2 must not carry forward destructive reset behavior as a routine migration/setup path. Rebuild/recovery must be explicit, isolated and backed by validated backups.
- The registry is source metadata only. It does not prove actual sheet contents, live header drift, row counts, data quality, whether every configured entity is used, or consumer completeness.
- The frontend API and procurement/event module excerpts show runtime API configuration and client-side request/cache behavior, but this increment did not map every endpoint caller or test. Client code cannot be treated as authoritative permission enforcement.

## V2 implications / follow-up
1. Maintain a source-to-target manifest using full literal source names and stable IDs; never derive identity from numeric prefixes.
2. Reconcile configured headers with service/repository consumers and classify every structure as active, legacy, duplicate, auxiliary, unused (evidence required), or unresolved.
3. Treat JSON/list and polymorphic fields as a migration discovery concern with explicit typed models, validated relationships and preservation of source payload evidence.
4. Setup/reset operations in V2 must be non-destructive by default; migrations versioned, transactional where DB-local, tested on disposable/sanitized fixtures, with backup/restore gates.
5. G-01 remains OPEN: consumer/mutation/caller/test coverage and live header drift are not established. G-10 remains OPEN; tenant-safe relational constraints require full relationship reconciliation.

## Limits
Source-only, bounded. No tests executed; no live production data or configuration accessed; no schema, SQL/DDL, migration, provisioning, production modification, deployment or VPS action.
