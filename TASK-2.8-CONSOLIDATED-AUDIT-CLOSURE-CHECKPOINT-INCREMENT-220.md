# Task 2.8 Increment 220 — Consolidated audit closure package and PO decision checkpoint

Date: 2026-11-24

## Scope
This package consolidates the currently evidenced audit state and next closure requirements. It does not imply acceptance, readiness, or migration approval.

## Evidence advances since Increment 202
- Corrected repository-owner spelling and restored access (203).
- Selected source retrieval hashes (204).
- Non-truncated recursive path inventories of both production default-branch trees, 147 backend and 172 frontend blob entries with 41/46 test files, respectively (205).
- Preliminary route/path and frontend permission crosswalk (206).
- Static HTTP operation dispatch inventory (207).
- Guard-call/service target pattern counts (208).
- Core service method inventory (209).
- Eight frontend domain modules retrieved/path-family inventory (210).
- Source traces: Procurement receipt (211), Attachment lifecycle (212), Event void (213), Project cascade deletion (214), Project Information revisions/live sync (215), Event creation/persistence (216), reusable Form response persistence (217), Workflow publication and Material recalculation (218).
- V2 implementation repo metadata readiness observations (219).

## Evidence status by gate
### G-01 — Source/route/caller/dependency coverage
Advanced to non-truncated path inventories and static operation families. Still open: precise parent commit refs; every path disposition; every route/branch mapped to caller, handler, permission, tenant predicate, validator, side effects, tests and target-domain mapping; exclusions justified.

### G-02 — Procurement policy and correctness
Source evidence shows locking, idempotency, project/allocation and receipt quantity checks, projection/status update and audit sequences. PO decisions remain: over-receipt, stock handling for general items, correction/reversal model, order cancellation semantics, allocation/receipt batch policy, surplus, NEEDS_REVIEW disposition. V2 recommendations remain draft only.

### G-03 — Event/Form history and amendment
Source evidence shows event snapshot fields, idempotency lifecycle, form-definition snapshots, revision/history and amendment-event-linked form response updates. Still open: immutable event/form model, allowed correction/amendment scope, void policy, reason catalogue, report effective/audit views, downstream impacts, atomicity/recovery and test evidence.

### G-04 — Attachment lifecycle/storage
Source evidence confirms tenant/owner visibility checks and storage/metadata/audit sequencing, plus project copy and deletion. Still open: complete owner taxonomy, independent storage target, reconciliation/orphan/missing objects, restore/retention/purge/scanning and failure-recovery tests.

### G-10 — Tenant/global and relationship integrity
Selected source contains tenant predicates and project visibility checks, but exhaustive relationship classification and negative tenant tests remain open. Define cross-project/global event types and reference-sharing policy, tenant-scoped IDs/constraints and orphan prevention.

## Consolidated PO decision set (deferred until business context review)
1. Procurement: over-receipt; general stock; receipt correction/reversal; order cancellation/reopen; allocation handling; batch semantics; surplus treatment; NEEDS_REVIEW.
2. Event/Form: amendment scope and authority; void permissions/timing; reason catalogue; audit/effective report semantics; event-type effects.
3. Attachment: retention/purge; owner taxonomy; external upload scanning; restore and storage reconciliation responsibilities.
4. Tenant/global: classification of independent events, workflow/event-type sharing, reference copies and cross-project access.

## Recommended technical defaults for later discussion (not adopted)
- Append-only canonical ledgers for receipts, amendments and event/form history; corrections as linked compensating records.
- Explicit tenant-bound relationships, restrictive FK/unique/index constraints and object-level authorization.
- Transactions for core mutations, audit and outbox; idempotency keys with payload fingerprints.
- Separate effective-state reporting from immutable audit chronology.
- Storage reconciliation/restore tools, retention policy and deletion tombstones.
- Migration dry-run validation, rollback, backup, cutover and read-only legacy retention.

## Completion criteria
Task 2.8 may be submitted for PO acceptance only after G-01/G-02/G-03/G-04/G-10 evidence closure or explicitly accepted risk disposition. The PO must approve the grouped business decisions; technical recommendations must not be silently adopted. Runtime tests remain pending until a safe non-production V2/test environment exists.

## Safety
No production modifications, tests, SQL/DDL, migrations, live data, provisioning, VPS or deployment. Schema readiness NOT APPROVED.
