# Task 2.8 — Consolidated Coverage, Evidence & Readiness Matrix (Increment 64)

Date: 2026-11-23  
Scope: Read-only legacy source audit consolidation; no legacy code/data changes.

## Evidence classification
- **E1 Source-observed:** bounded source inspection; not proof of all execution paths.
- **E2 Test-source observed:** assertions exist in test files; tests were not executed here.
- **E3 Runtime verified:** test actually executed and passed (none established in this increment).
- **D Design requirement:** target V2 requirement derived from source behavior/risk.
- **U Unresolved:** needs further source review, testing, workload evidence, or policy decision.

## Consolidated coverage matrix

| Domain | E1 observations | E2 evidence | V2 design requirements | Open verification |
|---|---|---|---|---|
| Tenant / identity / RBAC | Tenant-aware checks at selected services; ID-only generic repository lookups also observed; assignment + audit separate writes | Validator/access-control tests exist in inventory | Tenant-qualified FK graph; tenant-aware unique scopes; authz checks at API and service; transactional role changes + audit | Exhaustive endpoint/mutation map; tenant-isolation negatives; session lifecycle |
| Workflow/version/rules | Versioned rules, clone/replace, locking, selected validation and rule sequencing observed | Workflow-event-rule test sources inspected | Immutable published versions; transactional aggregate changes; historical event snapshots; composite tenant constraints | Full publish aggregate, runtime enforcement and all mutation callers |
| Projects/status | Visibility/tenant checks and workflow state transitions observed in selected paths | Project deletion and draft-project test files in inventory | Tenant/project constraints; safe deletion orchestration; lifecycle states and recoverability | All project mutation paths, deletion/cascade/recovery runtime |
| Events / idempotency | Project-linked and independent event paths; tenant/visibility validation sampled; void/reconciliation semantics sampled | Event idempotency tests and selected event test surfaces inventoried | Idempotency scope constraints; immutable event lifecycle/audit; transaction and retry policy | Exhaustive event/form/amendment path; runtime replay/failure tests |
| Forms / EventData | Header and selected form/event-data paths mapped; validation/visibility sampling | Form/API tests listed | Versioned definitions and responses; integrity constraints; transactional event + form response where required | Complete lifecycle, field semantic rules, concurrency tests |
| Project Information/history | Revision guards, history, approval/conflict/sync paths sampled; sequential writes observed | Cardinality/data-type test files listed | Revision concurrency control; immutable history; atomic current-state/history/audit; sync conflict model | Full approval/amendment path, concurrency/recovery tests |
| Material planning/specs | Calculation revisions, void-event invalidation and review flags sampled | Material-planning tests listed (not run) | Immutable calculations/revisions; transaction boundaries; event-source links and tenant FKs | Reconciliation behavior, concurrency/lock tests, exact quantities/unique scopes |
| Procurement | Tenant/project checks, idempotency, locks, sequential order/item and receipt writes sampled; partial failure states | Procurement tests listed | Atomic order/items/allocation/audit; receipt ledger transaction; idempotency and tenant-qualified constraints | All cancellation/adjustment paths; concurrency and failure injection tests |
| Attachments/storage | PROJECT/EVENT owners; validation; Drive lifecycle; sequential upload/delete writes observed | Attachment tests listed | Typed owner integrity; storage lifecycle/recovery; scans; checksum; soft delete/retention; secure access | Orphan handling, backup/restore, retention policy, provider migration |
| Audit | Append-style logging; tenant filtering in selected service paths, generic repository not scoped | Audit-related test evidence limited | Immutable append-only log; transactional audit for critical mutations; tenant-safe queries | Full mutation coverage and retention/export policy |
| API/permissions | Selected route branches permission checks mapped; coverage bounded | Test inventory includes contracts and permissions | Declarative route authorization; defense-in-depth service checks; error contract; tenant isolation | Exhaustive route/method/permission matrix; endpoint-level negative tests |
| Migration/runtime | No live data or runtime DB access in audit | Legacy tests are source inventory only | Repeatable migrations, validation/reconciliation, backups, rollback, observability | PostgreSQL constraints, RLS decision, load tests, migration rehearsal |

## Critical gap register consolidated
1. **G-01 — Complete mutation/route/dependency coverage:** OPEN/BLOCKER. Evidence remains bounded and sample-based.
2. **G-02 — Procurement accounting integrity:** OPEN/HIGH. Transaction and ledger design must cover orders/items/allocations/receipts/cancel/adjustment.
3. **G-03 — Lifecycle/revision/void/history consistency:** OPEN/HIGH. Full event/form/project-information/workflow/material planning sync behavior not yet exhaustively validated.
4. **G-04 — File lifecycle/recovery:** OPEN/HIGH. Storage ownership, orphan cleanup, retention, backup/restore, scanning and recovery policy not established.
5. **G-10 — Tenant integrity:** OPEN/BLOCKER. Tenant-qualified FK/unique scopes and cross-tenant test coverage are not implemented/verified.
6. **Runtime validation — OPEN:** no E3 evidence. Legacy test sources and CI workflow definitions do not establish execution/pass results.
7. **Performance baseline — OPEN:** no measured workload or target DB benchmarks established.
8. **Policy decisions — OPEN:** retention, deletion/recovery, notification behavior, conflict resolution and operational RPO/RTO need explicit design/approval as applicable.

## Evidence boundaries and safety
- This matrix consolidates prior increments 52–63; it does not imply exhaustive source coverage.
- Test-file existence is E2 and is not a test pass.
- No legacy repository writes, live production data access, SQL/DDL, migration, DB provisioning, deployment or VPS purchase occurred.
- This matrix is an audit/readiness artifact only. It does **not** approve schema implementation.

## Readiness decision
**Task 2.8 audit remains IN PROGRESS and NOT READY for executable schema/migration.** G-01 and G-10 remain blockers. Further work should focus on filling evidence gaps with targeted source review, rather than repeating already documented traces, and then produce a defensible final Task 2.8 acceptance package.
