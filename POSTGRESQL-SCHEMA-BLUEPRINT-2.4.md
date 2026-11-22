# Phase 2 — Task 2.4: PostgreSQL Schema Blueprint (Design-only)

**Started:** 2026-11-22  
**Status:** IN PROGRESS — DESIGN DISCOVERY  
**Inputs:** Accepted Task 2.1 conceptual model, Task 2.2 source-to-target mapping, Task 2.3 field semantics/constraint validation.  
**Boundary:** Design-only. No SQL/DDL, migration files, database provisioning, live legacy data access, production changes, deployment or VPS action.

## Objective
Translate accepted discovery evidence into a coherent PostgreSQL logical schema blueprint: domain/table groupings, tenant-aware relationship strategy, key/constraint candidates, lifecycle/history modeling, transaction boundaries and unresolved decisions. This is a design proposal, not approved implementation.

## Design principles
1. Tenant boundaries must be represented and enforced at the DB/API boundary; evaluate tenant-scoped composite keys/FKs where they prevent cross-tenant relationships.
2. Preserve historical snapshots and version context for workflows, forms, event responses, information revisions, specifications/calculations and audit records where evidenced.
3. Use normalized relational structures for core entities and associations; use JSONB only for bounded snapshots/configuration or payloads whose structure is variable/versioned, with validation policy.
4. Model polymorphic attachment/audit references explicitly; do not pretend conventional FKs enforce polymorphic targets. Evaluate typed association tables or explicit owner/reference tables.
5. Define durable idempotency scopes and unique constraints; design transactions for multi-record operations and concurrency-sensitive aggregate limits.
6. Status transitions and reversal/void predicates must be explicit; preserve evidence uncertainty and do not invent legacy capabilities.
7. Separate core schema from external object storage. File metadata, storage keys, associations, retention and cleanup/outbox responsibilities must be modeled.
8. Candidate SQL types, precision, nullability and constraints are proposals pending domain and migration validation.

## Proposed work packages
- 2.4-A: Logical schema/domain inventory and table grouping.
- 2.4-B: Tenant-scoped key/FK strategy, association cardinalities and unique constraints.
- 2.4-C: Historical, snapshot, soft-delete, audit, polymorphic and storage models.
- 2.4-D: Transaction/idempotency/concurrency strategy, unresolved decision register and consistency review.

## Proposed domain groups (initial; names provisional)
- Tenant, user, membership, department, role, permission and association tables.
- Project, project-information current values/revisions/approvals and reference-sync/copy logs.
- Workflow definition/version/step/department assignment/event rules and project execution context.
- Event, event data, event types, form definitions/fields/associations, form response snapshots and amendment/void context.
- Material specification revisions, material needs, calculation records and reconciliation states.
- Procurement orders, items, allocations, receipts and explicit operational status/history where verified.
- Attachment metadata, typed associations, storage lifecycle/outbox.
- Audit records, print templates/versions/assets and integration configuration as confirmed.
- Operational idempotency and migration-run/validation records where appropriate.

## Decision gates / unresolved items
- Exact tenant composite-key implementation and global catalog scope.
- ID strategy and preservation/translation of legacy identifiers.
- User/password/session migration policy.
- Numeric precision, quantity units, currency and rounding policy.
- Timestamp/timezone and Jalali display-vs-storage policy.
- Exact uniqueness scopes for codes, keys, event responses, revision numbers and replay identifiers.
- Receipt VOID/reversal semantics and whether any unsupported mutation path exists.
- Attachment polymorphism strategy and object-storage provider.
- Audit retention, soft-delete/archive and recovery policy.
- Snapshot retention and event/form amendment representation.
- Migration source anomalies and live-data validation (deferred until separately approved).

## Deliverables
- This blueprint document, expanded with evidence-backed proposals.
- A schema decision register, separating proposed decisions, accepted legacy behavior, unknowns and business choices.
- Updated TODO.md and ARCHITECTURE.md.
- Comprehensive consistency review and presentation for Product Owner approval.

## Verification
Read back all changed documents from GitHub. No schema or migration tests are in scope; no such tests will be claimed. Task 2.4 will remain IMPLEMENTED/READY FOR REVIEW until explicit acceptance.
