# Task 2.8 Increment 135 — Legacy identifier generation and code crosswalk (G-05 evidence)

Date: 2026-11-23  
Evidence: E1 — source-only sampling of Constants and selected services.

## Observed identifier patterns
- Constants.ID_PREFIX defines prefixes for several core entities (tenant/user/department/role/permission/project/workflow/step/event type/field/event/attachment/audit/session), but services do not uniformly consume this registry.
- Sampled service-generated IDs use UUID-derived strings with differing conventions: full UUID without hyphens, shortened UUID fragments, prefixes, and a mixture of uppercase/lowercase suffixes. Examples of patterns observed include project/event/attachment IDs, role/department IDs, project status short IDs and procurement/material item codes.
- Material/procurement human-facing item codes use separately generated random code fragments and are distinct from row IDs; order/receipt/allocation IDs also use prefix conventions.
- Project/status codes and generated material codes are business/display identifiers in addition to internal ID. Some workflows and event types have tenant-scoped Code duplicate checks in services, but storage-level uniqueness is not demonstrated.
- Copy/repeat-project and reference-sync operations generate new IDs for copied records and preserve source linkage in selected entities. A comprehensive map of IDs that are intentionally preserved versus regenerated is not yet complete.

## V2 implications
1. Separate immutable internal primary keys from business codes/labels; establish consistent UUID strategy and human-readable code strategy.
2. Define tenant-scoped unique constraints for business codes and global uniqueness for PKs; do not rely on read-before-write duplicate checks.
3. Build migration mapping from legacy IDs to V2 IDs only where necessary; preserve legacy IDs as alternate keys or explicit mapping table when downstream/reference compatibility requires.
4. Inventory all generated IDs, copy/repeat operations, external references, event snapshots, file IDs/storage keys and human-facing material/order/project codes before migration.
5. Define canonical casing/normalization, length limits, collision handling, concurrency-safe code generation and retry/idempotency scope.
6. Preserve provenance/source IDs in migrated history without conflating them with new V2 primary keys.

## Limits
- This increment is a bounded identifier-generation sample, not exhaustive entity inventory or live-data collision analysis.
- No live IDs/data, tests, migration or production changes. G-05 remains HIGH/open; G-01/G-10 remain blockers; schema readiness NOT APPROVED.
