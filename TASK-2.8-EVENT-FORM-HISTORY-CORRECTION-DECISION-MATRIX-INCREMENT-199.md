# Task 2.8 Increment 199 — Event/Form history and correction decision matrix

Date: 2026-11-23 | Design synthesis only; no runtime testing.

## Recommended invariants
1. Completed events and submitted responses remain immutable originals.
2. Submitted responses preserve exact form-definition/version snapshots; definition edits affect future submissions unless approved migration/reinterpretation.
3. Amendments are linked records, not destructive edits: target, predecessor revision, reason, actor/time, changed-value evidence, tenant-scoped idempotency and optimistic revision checks.
4. Sequential amendment chain by default; stale concurrent amendment conflicts. Original and effective revisions remain independently queryable.
5. Void is permission controlled, reason required, original preserved, effects recorded and reconciliation tracked; no hard delete.
6. Event-specific effects on project state, workflow, material planning, procurement, reports, attachments and notifications must be explicit, never inferred from event-name text.
7. DB-local domain/history/audit/projection writes are transactional; external integrations use durable outbox and idempotent reconciliation.
8. Operational reports default to effective state; audit views show original, amendment/void lineage, reasons and actors. Exact inclusion policy remains to be determined.
9. Retention/purge requires approved schedule and authorization; no destructive purge by default.

## Decision matrix
| Topic | Recommended default | Pending policy/evidence |
|---|---|---|
| Amendment vs edit | linked amendment, preserve original | permitted fields/form types |
| Reason | required, categorized and explanatory | reason catalog/approval |
| Chain | sequential, references original and predecessor | branching exceptions |
| Concurrency | optimistic revision check | UX conflict handling |
| Void | permission/reason/impact record | role, timing, approvals |
| Downstream impact | domain-specific reconciliation | event-type impact matrix |
| Form definition | durable version/snapshot | snapshot format/versioning |
| Reports | effective view plus audit view | report inclusion rules |
| Attachments | lineage-preserving, owner-validated | lifecycle/owner inventory (G-04) |
| Idempotency | same payload replay, changed payload conflict | key retention |
| Audit | actor/time/tenant/reason/revision | access/redaction/retention |
| Retention | preserve pending approved schedule | business/legal schedule |

## Downstream safeguards
- Project information/status: conflict-safe transitions; no blind restoration of stale state.
- Workflow: retain version context; do not roll back unrelated later transitions.
- Material planning: provenance-bound deterministic recalculation; supersede rather than overwrite.
- Procurement: void never erases receipt facts; explicit correction/reconciliation.
- Reports: effective projection and complete audit lineage.
- Attachments: validated typed-owner access and non-orphaning lineage.
- Notifications: outbox, idempotent retry and defined correction/void notifications.

## Conceptual commands (not finalized APIs)
EventAmend (target, expected revision, reason, changed values, actor, idempotency); EventVoid (target, revision, reason, effect policy); FormDefinitionPublish (version/validation/change summary); EventReconcile (tenant/event/revision/effect scope).

## Test candidates (not run)
- Historical values remain unchanged after form-definition changes.
- Amendments preserve original and create linked revisions with reasons.
- Stale concurrent edits conflict; one revision advances.
- Replay is idempotent; changed payload conflicts.
- Void preserves history and effects; later unrelated facts are not erased.
- Tenant-crossing references denied.
- Effective/audit reports follow documented inclusion rules.
- Transaction failure rolls back DB-local domain/history/audit/projection writes; outbox retries observable/idempotent.

G-03 remains OPEN; G-01/G-10/G-04 also remain blockers. Source coverage is bounded. No schema approval, SQL/DDL, migration, runtime tests, live data, provisioning, deployment or production change. Product Owner approval is required for business policy.
