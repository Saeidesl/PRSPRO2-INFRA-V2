# PMS V2 — Domain Boundaries & Database Migration Strategy (Draft)

Updated: 2026-11-22
Status: Discovery-backed design draft; Product Owner review required. This document is not a final schema and does not authorize data migration.

## 1. Design goals
- Preserve confirmed PMS behavior, identifiers, tenant boundaries, history and relationships.
- Establish modular ownership and avoid cross-module uncontrolled persistence writes.
- Use PostgreSQL relational integrity and transaction semantics.
- Build migration as repeatable, observable, recoverable stages.
- Keep legacy PMS running and READ-ONLY to V2; no dual-writing.

## 2. Provisional domain ownership
Logical boundaries based on read-only discovery; detailed schemas/contracts remain subject to further inspection.

### Identity & Access
Authentication/session lifecycle, tenant membership, roles/permissions and policy evaluation. Enforce access at application/service boundaries and scope to tenant/resources.

### Tenant & Organization
Tenant context, organization configuration, departments and settings. Isolated across tenants.

### Projects
Project identity/code, lifecycle status, project information/history, metadata and links to associated domain objects. Existing IDs and history are preservation targets.

### Workflow
Workflow definitions, versioning, steps, assignments/rules and transitions. Preserve applicable workflow-version references in historical records.

### Events & Forms
Event types, event data, form definitions/fields, submissions and response snapshots. Preserve associations and historical form state; transition rules require mapping.

### Materials & Procurement
Material/specification requirements, procurement orders/items, allocations, receipts and delivery quantities/history. Preserve project-specific and general procurement and relationships.

### Files
Metadata, associations, permissions, lifecycle and storage adapter contract. Bytes reside externally; coordinate side effects through outbox/reconciliation.

### Reporting
Read-oriented query composition and projections, not uncontrolled copies of authoritative write data. Optimize based on measured reporting patterns.

### Audit
Attributable business/security records, separate from operational logs. Append-oriented policy, redaction, access and retention required.

### Notifications & Integrations
Provider adapters, durable records, outbox, retries, deduplication, delivery status and reconciliation.

### Print & Templates
Template definitions/configuration and rendering orchestration; detailed relationships remain pending discovery.

## 3. Cross-domain rules
- Tenant-owned entities have defensible tenant scope; cross-tenant access denied by default.
- Modules own domain invariants; cross-module access uses explicit contracts.
- Transactions cover bounded DB use cases, not external services. External side effects use outbox/saga and reconciliation.
- Avoid circular dependencies; keep shared primitives minimal.
- Preserve legacy IDs where feasible; explicit mappings are traceable when conversion is unavoidable.

## 4. PostgreSQL design principles (not a schema)
- Primary keys, unique constraints, verified foreign keys and deliberate delete/update policy.
- Tenant-aware keys/relationships where appropriate.
- CHECK constraints for simple invariants; domain validation for richer rules.
- Indexes based on measured access/query patterns.
- Explicit ownership of timestamps, status/history and snapshot data.
- Ordered migration files; released migrations immutable where practical.
- Avoid cascading deletion of critical history/audit/file associations without explicit review.

## 5. Proposed migration stages
### A — Inventory and mapping
Confirm source tables/sheets, headers, IDs, tenant scope, relationships, formulas/derived data, snapshots and data-quality exceptions. Produce mapping and transformation rules; record exceptions.

### B — Tooling
Controlled read-only extraction; versioned deterministic transformations; ID mapping ledger where needed; dry-run and synthetic fixtures.

### C — Validation
Counts by entity/tenant, identifier and relationship/orphan checks, domain-rule and required-field validation, agreed aggregates, file hashes/manifests and exception reports. Critical discrepancies must be explained and resolved.

### D — Rehearsal
Use isolated protected approved snapshots (scrub where feasible); measure time/capacity/failures; verify backup restore and rollback; repeat until deterministic.

### E — Controlled cutover (future, not authorized)
Agree freeze window; backup/restore verification; final extract/load/validate; old system read-only for retention; explicit go/no-go PO approval and rollback criteria. No uncontrolled dual-write.

## 6. Preservation targets identified by source audit
Tenant/user-role relationships; project IDs/codes/info/revisions; events and form snapshots; workflow version/progression history; procurement requirements/orders/item IDs/allocations/receipts; file metadata/associations/checksums; required audit lineage. Integration secrets must be securely reconfigured/rotated, not copied into repositories or logs.

## 7. Risks and unknowns
No live-data inventory; legacy inconsistencies possible. Volume, file volume, performance and migration duration unmeasured. Delete semantics, tenant relationships, formula-derived values, sensitive-data classification, retention, file-provider constraints, freeze window, RPO/RTO, rollback and read-only duration need discovery/decisions.

## 8. Migration testing
Synthetic transformation unit tests; isolated fresh-PostgreSQL migration; repeatability/idempotency and recovery tests; tenant, relationship, counts, aggregates, checksum and exception tests; rehearsal timing. No production-data testing without explicit authorization.

## 9. Safety boundary
No live data access/export, migration, dual-writing, VPS acquisition, deployment or changes to production repositories authorized. Detailed schema and tooling remain later work.
