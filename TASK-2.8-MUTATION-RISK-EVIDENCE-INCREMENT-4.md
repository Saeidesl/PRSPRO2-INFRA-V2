# Task 2.8 — Mutation Risk Evidence Increment 4 (Deletion, Attachments, Receipts)

Date: 2026-11-22
Scope: Read-only inspection of exact source segments in the production Backend reference. No execution.

## Project deletion — verified observations
- Admin and tenant checks occur in `_projectContext`; project is located and tenant matched.
- Dependency collector traverses project events/event data, needs, allocations, items, orders, receipts, specs, calculations and project/event attachments.
- Shared/general order and item dependencies receive preservation logic when other allocations exist.
- `remove` uses ScriptLock and re-collects dependencies after acquiring lock.
- The mutation then performs sequential soft-deletes/voids across multiple Sheets, trashes Drive files, and physically removes the project row only when no failures were recorded.
- Failure accumulation and an audit record are attempted; on failures, a conflict is raised. There is no multi-sheet transaction/rollback in the inspected source.
- The source comments and code confirm that some child sheets lack Status and ensureColumn is attempted; events use VOID due sheet validation.
- Receipts are included in dependency collection and soft-deleted in the project cascade. This conflicts with the proposed Task 2.8 policy that posted receipt facts should not be silently deleted and should instead be preserved/corrected through linked reversal. This is a design migration incompatibility, not a judgement on legacy operational suitability.
- Attachment bytes are deleted from storage during cascade before final project row removal; failures can leave a partially applied cascade and require explicit reconciliation.

## Attachment deletion — verified observations
- Service validates attachment existence, tenant, and visibility/access via parent entity.
- Storage deletion is invoked BEFORE metadata status update to DELETED and audit logging.
- This creates a potential consistency gap: storage deletion may succeed while metadata update/audit fails. The V2 contract should decouple durable metadata transitions from asynchronous physical deletion, with reconciliation and retry.

## Procurement receipt — verified observations
- Single receipt uses ScriptLock, optional idempotency lookup, and validation.
- Batch receipt validates entries, allocations/project scope, quantity bounds and replay keys, then inserts receipts in batch and updates order statuses.
- Receipt calculations use existing receipt rows and include records based on observed query behavior; the inspected fragments do not demonstrate a complete reversal/status filtering contract.
- Receipt creation is not a DB transaction across receipt insertion, order status updates and audit writes. Single receipt inserts the receipt before updating order status/audit.
- Idempotency replay by key appears to return an existing record without verifying that the replay payload matches the original payload; changed-payload conflict/fingerprint behavior is not established by inspected fragments.
- Receipt model in inspected fragments does not show a complete reversal/reason/immutable-posting lifecycle.

## Implications for V2
1. Project deletion must be a lifecycle command, not cascade physical destruction of immutable business facts. Prefer archive/closure and dependency impact report; hard purge only as separately governed retention process.
2. Receipts must be preserved as ledger facts. Project closure/deletion must not erase posted receipts; use reversal/correction records and explicit impact.
3. Attachment delete should first mark metadata and enqueue durable deletion; physical bytes deletion is asynchronous, retryable and reconciled.
4. Domain mutations, audit/history and transactional outbox should commit atomically in PostgreSQL; external storage effects handled after commit with idempotency.
5. Idempotency should bind tenant + command scope + key + payload fingerprint, and reject changed-payload replay.
6. Concurrency controls must prevent concurrent receipt over-allocation and race conditions; DB constraints and transactional locking should be authoritative.

## Status and limitations
Evidence is source-level and bounded to inspected code segments. No tests run. No changes to legacy repos. No database implementation, migration or deployment.
