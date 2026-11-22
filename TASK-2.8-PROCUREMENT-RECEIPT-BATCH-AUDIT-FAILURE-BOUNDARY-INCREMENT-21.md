# Task 2.8 — Procurement Receipt Batch & Audit Failure Boundary Increment 21
Date: 2026-11-22
Scope: Read-only source trace of AuditService/Repository and ProcurementService order/receipt paths.

## Audit source observations
- AuditService.log validates basic fields and serializes OldData/NewData to JSON then delegates to AuditRepository.insert.
- AuditRepository.insert writes one row to the audit sheet. No demonstrated transactional coupling to originating mutation.
- AuditService.list filters returned rows by current TenantID. listByEntity and listByUser also tenant-filter returned rows after repository lookup. This is positive bounded tenant isolation evidence, not full audit-route authorization or immutable storage proof.
- Audit records are ordinary rows in the legacy store; sampled source does not establish append-only protection against edits/deletion, integrity hash, retention policy or guaranteed actor linkage.

## Procurement batch receipt path (direct source evidence)
1. Script lock acquired; user/tenant context established.
2. Receipt/order/item/allocation data loaded; existing receipts and idempotency keys sampled for replay handling.
3. Entire requested batch is validated and normalized before mutation, including tenant checks, order/item validity, quantity bounds, allocation/project matching, event validation and request-level duplicate keys.
4. New receipt rows are constructed and written with a single `insertMany` call.
5. Order statuses are recalculated and updated per affected order, sequentially.
6. Audit rows are logged individually after receipt insert and status updates.
7. Returns created and replayed receipts.

## Failure boundaries and risks
- Prevalidation before receipt write reduces validation-time partial writes.
- `insertMany` receipt write followed by sequential order-status updates and separate audit writes is not a cross-sheet transaction. If later order update or audit fails, receipt rows may already exist with partial status/audit completion.
- Idempotency-key replay is bounded source evidence; source does not prove database uniqueness or payload-equivalence conflict detection for reused keys. Some replay paths match existing key and return record(s) without validating that payload matches the original.
- Script lock reduces concurrent mutation risk within the Apps Script lock scope, but is not a durable DB transaction or distributed lock guarantee.
- The order creation source similarly validates before write and logs audit after order/item writes; atomicity and failure recovery are not established.

## V2 recommendations
- Use PostgreSQL transactions for receipt records, allocation effects, order status recalculation and durable audit/outbox entries.
- Enforce idempotency keys with tenant-scoped unique constraints; store payload hash and reject mismatched replay payloads.
- Persist a batch operation record with state, expected rows and recovery/reconciliation state for multi-item receipt posting.
- Recalculate order status in transaction from normalized receipt/allocation facts; status should not be the sole source of truth.
- Keep audit append-only and transactionally coupled via audit table or outbox; capture actor and operation context.
- Add migration/runtime validation for receipt quantity invariants, project/allocation correspondence, duplicated keys and cross-tenant references.

## Limits / blockers
- Source review only; not exhaustive routes/permissions, test execution, live data or runtime fault injection.
- G-02, G-10, G-01 remain OPEN; audit and procurement transaction policy not yet approved; schema readiness NOT APPROVED.
- No tests, live data, SQL/DDL, migrations, provisioning, deployment or production modifications.
