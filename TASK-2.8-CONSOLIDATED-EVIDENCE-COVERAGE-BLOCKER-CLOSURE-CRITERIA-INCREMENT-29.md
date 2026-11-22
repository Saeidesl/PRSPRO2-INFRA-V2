# Task 2.8 — Consolidated Evidence Coverage & Blocker Closure Criteria Increment 29
Date: 2026-11-22
Scope: Consolidation of bounded source and assertion evidence across increments 1–28. This is a decision/closure-criteria pack, not approval of schema readiness.

## Evidence classification
- E1: direct source inspection (service, repository, router, schema registry); not runtime proof.
- E2: actual test assertion inspected in repository; test not executed.
- E3: runtime/test execution evidence: none in Task 2.8 increments.
- E4: live-data/database evidence: none; intentionally excluded.

## Consolidated blocker register
### G-01 — Source coverage / implementation-to-test / cross-system completeness (OPEN)
Evidence: sampled Router/API/service/repository/schema, selected backend tests and selected frontend assertions across increments 1–28. Repository tree inventory broad but not exhaustive assertion mapping; not all mutation routes/services/test files traced. E1/E2 only.
Closure criteria: finish bounded service/route manifest for critical domains and map each critical mutation/read route to permissions, tenant guard, service invariants, persistence effects, tests; explicitly label untraced routes; owner acceptance of residual coverage risks.

### G-02 — Procurement integrity & lifecycle (OPEN)
Evidence: need/allocation/order/item/receipt and source-event integration trace, tests assertion samples; multi-sheet partial-failure boundary documented; no runtime failure injection. E1/E2 only.
Closure criteria: critical invariant manifest; reconciliation model for allocations/receipts/order status; idempotency and replay policy; explicit cross-project/shared-order handling; V2 transaction and constraint design requirements; test plan for rollback/concurrency/retry/fault boundaries.

### G-03 — Event/Form lifecycle and history (OPEN)
Evidence: event create/replay, workflow, reusable form response, amendments and project central info sampled; snapshot/version/replay tests inspected. E1/E2 only.
Closure criteria: bounded lifecycle/state-transition map including create/amend/void/approval/history/snapshot and source-linked effects; residual unknowns documented; V2 preservation and transaction/integrity tests defined.

### G-04 — Attachment/storage lifecycle (OPEN)
Evidence: polymorphic ownership, tenant/project visibility, selected attachment tests and upload/copy/delete source boundary. Storage failure/reconciliation not runtime verified. E1/E2 only.
Closure criteria: file state machine, metadata/object relationship, authorization/owner model, failure/recovery and orphan reconciliation requirements; explicit V2 object-storage migration/test plan.

### G-10 — Tenant integrity (OPEN)
Evidence: selected service checks, relationship matrix, tenant filtering and tests of project/event visibility. No exhaustive routes or live rows; no DB constraint proof. E1/E2 only.
Closure criteria: tenant-qualified relation manifest for all critical entities, access matrix, cross-tenant rejection tests and V2 composite FK/constraint design; distinguish global vs tenant-owned records and system actors.

## Additional cross-cutting blocker classes (not numbered gaps)
- Audit durability, immutability, mutation-failure policy, correlation and actor integrity.
- Transactional boundary and compensation/reconciliation for multi-step mutations.
- PostgreSQL schema/migration constraints, data reconciliation and migration verification.
- Runtime execution evidence and test harness fidelity.
- Complete auth/session/security controls and route matrix.

## Readiness decision
- Legacy production source remains read-only.
- Evidence is limited to source inspection and assertion inspection. Task 2.8 tests have not been executed.
- No live data, PostgreSQL DDL, migrations, migration rehearsal, deployment or production mutation was performed.
- Schema readiness remains NOT APPROVED.
- Task 2.8 is not declared complete; this pack defines measurable closure criteria and next bounded review steps.

## Next bounded work
1. Build a critical-route mutation/read matrix from Router route families and service dispatch.
2. Close high-risk domain trace gaps only where source/test evidence is accessible; avoid claiming exhaustive coverage.
3. Prepare a proposed V2 domain transaction/integrity test matrix tied to each blocker.
4. Read back updated docs and continue to Task 2.8 completion pack; PO acceptance still required to mark task complete.
