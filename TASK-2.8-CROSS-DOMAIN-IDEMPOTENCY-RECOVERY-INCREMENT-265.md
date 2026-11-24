# Task 2.8 — Cross-Domain Idempotency and Stuck-Operation Recovery Contract (Increment 265)

## Existing bounded evidence
Event idempotency tests cover replay, creator/tenant scope, PROCESSING refusal, COMPLETED persistence, and failure to persist completion. Repeat-project tests cover stable operation-key recovery for a lost response. Procurement and receipt mutation coverage gaps were separately documented in increments 251–252 and 246–250. These selected tests do not establish a cross-domain durable repair contract or recovery of stranded processing states.

## Common operation contract
Every mutating request with replay risk should use:
- OperationID and idempotency key, tenant and actor scope.
- Payload fingerprint: same key/same payload replay; same key/different payload conflict.
- Durable states: RECEIVED, PROCESSING, COMPLETED, FAILED_RETRYABLE, NEEDS_REVIEW, FAILED_TERMINAL (exact state model subject to V2 design).
- Result pointer and response snapshot sufficient for replay.
- Recovery owner/attempt count/next retry time/error class.
- Atomic claim and domain transaction with constraints.
- Explicit reconciliation for external side effects via outbox.
- Audit/correlation identity and immutable transition history.

## Test cases
- O-01 Key absent: operation behaves per API contract.
- O-02 Same key and same payload: replay result; no duplicate mutation.
- O-03 Same key and different payload: conflict.
- O-04 Tenant/actor scope collision: cannot replay another user's/tenant's operation.
- O-05 Crash before domain transaction: safe retry.
- O-06 Crash inside transaction: atomic rollback.
- O-07 Crash after transaction before response: replay yields committed result.
- O-08 External side effect queued but not executed: retry worker executes once effectively.
- O-09 External action executes but acknowledgment lost: worker replay reconciles safely.
- O-10 Processing lease expires: recovery claims operation and revalidates state.
- O-11 Stale worker cannot overwrite newer operation state.
- O-12 Retry exhaustion moves to manual review with diagnostics.
- O-13 Completed operation cannot be reset or duplicate domain writes.
- O-14 Concurrent claims: only one active claim; uniqueness enforced.
- O-15 Key retention/expiry policy: old key cannot cause unsafe duplicate.
- O-16 Partial legacy operation marker recovery: inventory, reconciliation, manual review; no blind replay.

## Domain-specific operation identifiers
- Event create: tenant+actor+key and payload fingerprint.
- Project repeat/reference copy: project-create operation key, copy plan identity, per-type progress and target/source revisions.
- Procurement order/receipt: tenant operation key, source-event/item constraints and payload digest.
- Material calculation/revision: calculation identity, spec revision and source event.
- Attachment/storage: upload/delete operation identity and external storage key mapping.
- Project deletion: deletion operation identity, dependency manifest snapshot, durable cleanup state.

## Verification
Blueprint only; no database, runtime or worker tests. No production writes.
