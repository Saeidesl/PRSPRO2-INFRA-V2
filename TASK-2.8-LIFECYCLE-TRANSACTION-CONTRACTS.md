# Task 2.8 — Lifecycle & Transaction Contracts (Draft)

**Status:** Technical design proposal; business-policy assumptions are flagged. No implementation/schema authorization.

## 1. Procurement lifecycle

Candidate purchase-order states: DRAFT, OPEN, PARTIALLY_RECEIVED, RECEIVED, CANCELLED.
- DRAFT -> OPEN: validation succeeds; items/quantities are frozen or versioned.
- OPEN -> PARTIALLY_RECEIVED: valid receipt posting records positive net receipt.
- OPEN -> RECEIVED: accepted net receipt reaches required quantity for all items.
- PARTIALLY_RECEIVED -> RECEIVED: remaining quantities received and validated.
- PARTIALLY_RECEIVED -> OPEN: reversal recomputation lowers net receipt below threshold.
- OPEN/PARTIALLY_RECEIVED -> CANCELLED: cancellation checks outstanding quantities, allocations and posted facts.
- RECEIVED -> earlier derived state only after linked correction/reversal and recomputation, never direct status edit.
- CANCELLED is ordinarily terminal; reopening would require a separately authorized command if accepted by policy.

Receipt states: DRAFT (optional staging) -> POSTED -> REVERSED (linked reversal). Posted receipt facts are immutable.
Allocation states: ACTIVE -> RELEASED/REVERSED; retain lineage and stock/project destination.

### Invariants
- Net receipt is calculated from canonical posted facts and linked reversals.
- Tenant-scoped idempotency key and persisted outcome.
- Same key + same normalized payload returns prior outcome; changed payload conflicts.
- Order, item, receipt, allocation and tenant relationships must agree.
- Unit conversion and precision are explicit.
- Batch is orchestration of canonical receipt commands; atomicity must match defined batch semantics.
- External storage/message operations are not performed inside DB transactions; outbox/reconciliation used.
- Cancellation cannot erase posted receipt/allocation facts.

## 2. Event/form history

Candidate event states: REQUESTED (only if required by existing workflow), COMPLETED, VOIDED.
- Completed original submission is immutable.
- Amendment references target response/event, predecessor revision, reason, actor/time and changed values.
- Amendment chain is linear; stale concurrent changes rejected; accepted change atomically advances effective revision.
- Void is an explicit, permission-checked command with reason and recorded downstream effects; reconciliation is durable where necessary.
- Definition snapshots remain attached to submissions; changes affect future submissions.
- Effective view and audit view are distinct; audit view exposes original and lineage.

## 3. Attachment lifecycle

Candidate lifecycle: STAGED -> ACTIVE -> DELETED (soft) -> PURGE_PENDING -> PURGED.
- Upload stages object, validates and checksums it, persists metadata and promotes; failures retry/reconcile.
- Download checks tenant, typed owner and permissions before streaming.
- Copy is idempotent; new metadata/object identity and source lineage; partial failure is recoverable.
- Delete soft-deletes and restricts access; purge is controlled after retention/backup gates.
- Provider abstraction defines put/get/delete/copy/verify; domain code stays provider-neutral.
- Typed owner relations or validated registry; no unconstrained polymorphic owner IDs.

## 4. Tenant integrity
- Tenant-owned relationship edges include TenantID validation and, where appropriate, composite constraints.
- Tenant identity derives from authenticated context, never arbitrary caller input.
- Cross-tenant direct IDs, indirect relationships, exports, reports, jobs and files are denied.
- Global entities require explicit classification.
- DB constraints complement central fail-closed authorization.
- Independent event scope requires an explicit model; nullable ProjectID alone is not sufficient until policy and constraints are settled.

## 5. Transaction/concurrency pattern
1. Authenticate/authorize; derive tenant.
2. Validate payload and same-tenant graph.
3. Resolve replay/idempotency and conflicts.
4. Begin DB transaction.
5. Lock required rows/unique keys or use optimistic revision checks.
6. Persist domain, history, audit and effective projections as appropriate.
7. Persist outbox/reconciliation record for external effects.
8. Commit.
9. Process external effects retryably/idempotently.
10. Monitor and reconcile failures.

Rollback DB-local work on failure. Avoid network/storage calls while holding DB locks. Define lock ordering and bounded retry for transient failures.

## 6. Candidate acceptance tests (design-level)

### Procurement
- Identical replay: one posting and stable outcome.
- Changed payload with same key: conflict.
- Cross-tenant reference: rejected.
- Concurrent receipts cannot exceed policy limit.
- Reversal changes net projection but preserves original.
- Batch atomicity matches policy.
- Cancellation after receipts requires resolution.
- Unit precision/conversion tests.

### Event/form
- Definition changes do not alter historical snapshots.
- Amendment chain retains original and rejects stale revision.
- Concurrent amendment attempts yield one success and stale conflict.
- Void preserves original, reason, lineage and reconciliation.
- Cross-tenant linkage rejected.

### Attachments
- Authorization before byte delivery.
- Failure injection at upload stages reconciles.
- Duplicate commands idempotent.
- Delete blocks normal access; restore within retention.
- Purge blocked before retention/backup gates.
- Orphan objects/metadata detectable.

### Tenant
- Negative tests for direct IDs, indirect relationships, report/export, attachment and background job.
- DB constraint tests bypass application service where feasible.

## 7. Evidence gaps and guardrails
- Draft only; not implemented or runtime-tested.
- Exhaustive source manifest, attachment owner registry, cancellation/void downstream semantics, retention, units and approval thresholds remain evidence/business gates.
- No SQL/DDL, migration, DB provisioning, production data, deployment or production changes.
