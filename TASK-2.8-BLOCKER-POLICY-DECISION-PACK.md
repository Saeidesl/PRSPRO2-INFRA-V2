# Task 2.8 — Remaining Blocker Policy & Evidence Decision Pack

**Started:** 2026-11-22  
**Status:** IN PROGRESS — design/policy preparation only  
**Scope:** Prepare evidence-grounded recommendations and decision checkpoints for open Phase 2 blockers. No SQL/DDL, migrations, DB provisioning, live-data access, production tests, deployment, VPS or production writes.

## Why this task follows 2.7
Task 2.7 completed a bounded evidence-closure audit and left G-01, G-02, G-03, G-04 and G-10 open. This task converts those gaps into proposed V2 policy contracts and evidence gates, separating recommendations from unresolved decisions and source-evidence requirements.

## Policy pack — recommendations (not accepted decisions)

### G-02 — Procurement lifecycle
**Recommended policy draft:**
1. Receipt is a posted fact; do not edit/delete posted receipts in place. Correct mistakes through linked reversal/correction records with reason.
2. Single and batch receipt share one canonical command and validation path; batch is orchestration, not an independent implementation.
3. Tenant-scoped idempotency key; identical replay returns prior result, changed payload conflicts.
4. Allocation/receipt accounting is explicit, quantity-precise and project/general-stock aware. Remaining quantities derive from canonical posted/reversed records.
5. Order cancellation is a state transition. Orders with receipts or allocations cannot silently disappear; cancellation specifies allocation/outstanding quantity and stock-fact disposition.
6. Cross-project allocations are explicit; visibility must not hide components that affect other projects.
7. Posting/reversal/allocation changes commit atomically in DB; external side effects reconcile.
**Required evidence/decision:** units, fractional quantities, surplus/over-receipt, general-stock semantics, allocation, cancellation and retention policy.

### G-03 — Event/form history
**Recommended policy draft:**
1. Completed events and original captured responses/snapshots are immutable facts; corrections are linked amendment/reversal records, not destructive rewrites.
2. Preserve definition/version snapshots with responses; definition updates do not silently reinterpret historical submissions.
3. Amendments explicitly link, scope allowed forms, reason, author/time and use concurrency/idempotency.
4. Void is controlled and preserves the original/reason; effects on workflow, project information, procurement/material planning and reports must be specified.
5. Domain writes, history, project state and audit commit transactionally where DB-local; integrations/projections reconcile durably.
6. Define retention and correction lineage.
**Required evidence/decision:** correction vs amendment vs void, repeat amendment policy, approvals and report inclusion.

### G-04 — Attachment lifecycle
**Recommended policy draft:**
1. Metadata and typed ownership are separate from object bytes; typed owner relations or a validated owner registry are preferred over unconstrained polymorphic IDs.
2. Upload uses staged objects, durable metadata and compensating/retry workflow; orphan states are reconciled.
3. Delete is logical lifecycle plus controlled purge; reconcile storage result before asserting success.
4. Copy creates new object/metadata with source lineage; idempotent retry and partial-copy recovery.
5. Define retention, restore, purge authorization, checksums, validation, provider migration and orphan reconciliation.
**Required evidence/decision:** retention by category, restore window, purge permission and exhaustive owner/types.

### G-10 — Tenant integrity
**Recommended policy draft:**
1. Tenant-owned entities carry TenantID; relationship edges prevent cross-tenant parent references.
2. Tenant-qualified composite constraints where appropriate; authorization is additive, not a substitute for relational integrity.
3. Classify global catalogs vs tenant-owned entities and nullable independent events.
4. Document/validate/reconcile every indirect or polymorphic relationship; prefer typed links.
5. Cross-tenant tests at API and DB levels: direct-ID, indirect-link, reports and files.
**Required evidence/decision:** global/tenant classification for users/departments/roles, workflows/forms/information/event types and independent events.

### G-01 — Coverage reconciliation
**Recommended policy draft:**
1. Machine-readable legacy manifest: file path, purpose, entities, consumers, routes, mutations, permissions and projections.
2. Reconcile each structure/route to source, target domain, operations, dependencies, retention, tests and migration mapping.
3. Classify unknown/deprecated/duplicate/unused structures; numeric sheet prefixes are not IDs.
4. Coverage threshold and exception sign-off before schema readiness.
**Required evidence:** complete repository tree and route/handler/caller inventory cross-checked with tests; previous bounded inventories are not exhaustive.

## Gate matrix
| gap | policy draft prepared | remaining required |
|---|---|---|
| G-01 | yes | exhaustive manifest/reconciliation and unknown disposition |
| G-02 | yes | business choices + evidence/test contract |
| G-03 | yes | distinctions, retention and transaction contract |
| G-04 | yes | retention/restore/purge choices and owner evidence |
| G-10 | yes | tenant/global classification and relational design approval |

## Next work
Continue evidence reconciliation and convert recommendations into decision records. Business-policy items require Product Owner approval; technical implementation decisions can be made by the architect within approved policy. Schema readiness remains NOT APPROVED.
