# Task 2.8 — V2 Transaction & Integrity Test Matrix Draft Increment 40
Date: 2026-11-22
Status: Draft derived from bounded legacy evidence; not approved business policy or executable schema.

## Matrix
| Domain | Transaction boundary candidate | Integrity constraints | Idempotency/replay | Fault/concurrency test targets | Evidence/gap |
|---|---|---|---|---|---|
| Tenant/user/role/permission | role assignment + audit; permission update | tenant-qualified role/user links; global-vs-tenant scope; actor validation | operation key for bulk changes if retryable | revoked permissions, concurrent assignment, cross-tenant identifiers | G-10/G-01 |
| Project create/update | project row + central info + event/projection effects where applicable | tenant-qualified refs, unique tenant project code if policy confirmed, draft owner | create key bound to payload | duplicate create, draft fallback, cross-tenant write, revision conflict | G-01/G-10 |
| Central information/history | value/revision/history/audit in one transaction | tenant/project/type relation, version monotonicity, active type rules | key + expected revision | concurrent saves, approval conflict, deactivate/reactivate policy | G-03/G-10 |
| Event/form response | event + response snapshots + source references + audit | tenant/project/event/type/form relation, snapshot/version provenance | event creation key payload-bound; amendment key | replay, stale version, failure between response and audit, void transition | G-03/G-10 |
| Workflow | published snapshot + event progress + step completion | workflow tenant/active/published/version; project workflow reference | step-completion operation ID | simultaneous completion, stale workflow version, replay | G-03/G-10 |
| Material planning | specification revision + calculation history + need generation | tenant/project/spec lineage; source-event relationship; quantities/units | calculation/revision keys | duplicate calc, lock contention, void source, superseding spec, rollback | G-02/G-03/G-10 |
| Allocation/procurement order | need allocation + order line + audit/outbox | quantity conservation, tenant/project, shared order references | payload hash + key + uniqueness | allocation overflow, cross-project shared order, partial batch failure | G-02/G-10 |
| Receipt | receipt fact + order/need status + audit/outbox | received quantities, item/order/tenant links, immutable receipt facts | payload-bound receipt operation; replay equivalence | multi-line rollback, audit failure, concurrent receipts, partial fulfilment | G-02/G-10 |
| Attachment metadata/storage | metadata row + object operation outbox | tenant/entity owner polymorphic relation, checksum/size/type, active/deleted states | upload/copy operation ID, object key determinism | storage success/DB failure, DB success/storage failure, delete restore, orphan reconciliation | G-04/G-10 |
| Project deletion/archive | dependency preview snapshot + archival/status changes + outbox | protect shared facts and cross-project allocations; preserve history | deletion operation ID and confirmation bound to project/revision | dependency changed after preview, storage failure, partial child mutation | G-01/G-02/G-03/G-04/G-10 |
| Audit | domain transaction + append-only audit/outbox | tenant, actor, operation correlation, redact sensitive fields | same operation correlation, no duplicate audit side effect | audit insert failure rollback, immutability, concurrent writes | cross-cutting |

## Transaction strategy proposal (not approval)
- PostgreSQL transaction for all DB-local state changes and audit/outbox records.
- External object storage effects are orchestrated via durable outbox/workers with idempotent operation IDs and reconciliation, not held inside DB transactions.
- Use tenant-qualified FK/unique constraints and check constraints where semantics are confirmed.
- Optimistic revision checks for mutable versioned facts; locking only where invariant requires serialization.
- Store operation/idempotency records with tenant, actor/client scope, payload hash, outcome and recoverable state.
- Preserve immutable fact/history tables and soft-state lifecycle where deletion would destroy traceability.

## Test layers required before schema readiness
1. Unit/domain invariant tests.
2. PostgreSQL integration tests with real constraints and transactions.
3. Route/API authorization tests, including cross-tenant negative cases.
4. Failure injection and transaction rollback/reconciliation tests.
5. Concurrent execution tests.
6. Migration rehearsal on synthetic fixture data with reportable validation.
7. Frontend contract/E2E tests for critical workflows.

## Limits
Derived from source/test-assertion audit, not exhaustive and not approved business rules. No implementation, PostgreSQL DDL, tests, live data, migrations, provisioning or deployment performed. G-01/G-02/G-03/G-04/G-10 remain OPEN; schema readiness NOT APPROVED.
