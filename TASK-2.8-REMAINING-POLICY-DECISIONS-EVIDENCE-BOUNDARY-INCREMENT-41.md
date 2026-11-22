# Task 2.8 — Remaining Policy Decisions, Unknowns & Evidence Boundary Increment 41
Date: 2026-11-22
Purpose: Identify which decisions are technical defaults suitable for the V2 architecture proposal versus unresolved business policy, source evidence or external verification.

## Proposed technical defaults (safe to carry into design proposal; not approval)
- DB-local domain mutations, version/history and audit/outbox records use a single PostgreSQL transaction.
- External storage is asynchronous, recoverable, idempotent and reconciled through durable operations/outbox.
- Idempotency is scoped to tenant and actor/client context as applicable, key plus payload hash; key reuse with changed payload is conflict.
- Tenant-qualified references and FK/unique constraints for owned relations; polymorphic ownership represented by explicit owner type + owner ID with validated owner registry/strategy.
- Soft deletion/archive for history-bearing business facts; hard purge separately governed by retention policy.
- Optimistic revision checks for editable/versioned facts; serialization/locking only for specific quantity/state invariants.
- Audit append-only semantics, actor and operation correlation; no sensitive payloads/credentials in audit.
- Unknown business rules must remain explicit requirements, not guessed constraints.

## Business-policy decisions still unconfirmed
1. Receipt/need/order semantics when an original source event is voided after allocations, orders or receipts exist; decide whether preserve, reverse, review or compensate each downstream fact.
2. Project deletion policy: retention, archive versus soft delete, allowed states, dependency-changing race behavior and confirmation requirements.
3. Attachment retention, purge eligibility, restore window, supported owner types and sensitive-file access policy.
4. Audit retention period, privacy redaction, actor identity rules and export/access scope.
5. Tenant model: which entities are tenant-owned versus globally shared (permission registry, system schemas, templates or other references); no assumed global exceptions without source/business confirmation.
6. Business uniqueness rules (project code, item/material codes, event keys, workflow identifiers) and whether they are tenant-scoped.
7. Whether amendments/approvals create new immutable revisions for every form/info type and which historic snapshots must be queryable.

## Source/test evidence closure boundaries
- Selected critical source and test assertion traces exist across increments 1–40.
- Test execution, runtime behavior, PostgreSQL constraint enforcement, migration fidelity, full route/caller coverage and live-data behavior are not established.
- Production repository references remain read-only.
- E4/live-data evidence intentionally excluded; it must be a later gated task after test readiness and explicit approval.

## Decision gate
The evidence/decision pack can be delivered as a discovery artifact, but it cannot declare schema readiness. Business decisions above must be confirmed or explicitly deferred and schema design must remain blocked for unknown constraints. Runtime verification and migration rehearsal are separate tasks.

## Limits
No tests, live data, DDL, migration, provisioning, deployment or production modifications. Schema readiness NOT APPROVED.
