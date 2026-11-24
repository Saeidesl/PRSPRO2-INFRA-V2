# Task 2.8 — Increment 241: Mutation primitive and audit side-effect trace
Date: 2026-11-24
Scope: read-only inspection of production ProjectRepository, EventRepository, ProjectService update path, EventService void path, AuditService and GoogleSheetsRepository write primitives. No production files changed.

## Evidence (static source only)
- ProjectRepository.update delegates directly to GoogleSheetsRepository.updateById.
- ProjectService.update conditionally acquires ScriptLock for 30 seconds when ProjectCode changes, performs duplicate-code scan, then calls ProjectRepository.update (which reaches updateById and acquires ScriptLock again with waitLock(10 seconds)). Nested ScriptLock acquisition is a risk hypothesis requiring isolated GAS verification; no runtime assertion.
- ProjectService update writes the project first, then AuditService.log. Audit log failure could make the request appear failed after the project mutation already committed; retry/duplicate behavior needs testing.
- EventRepository.update delegates to GoogleSheetsRepository.updateById (with ensureColumn handling for idempotency-status fields).
- EventService.void in inspected code has no visible encompassing lock. Its mutation sequence can restore project state, update event status to VOID, write project audit, recompute project stage and audit, write event audit, and then invoke material-planning void/reconciliation. These are sequential operations, not a transaction in inspected path; partial-state windows remain hypotheses. Material-planning exception is caught and a failure response/audit is attempted.
- AuditService.log serializes OldData/NewData and inserts through AuditRepository; AuditRepository inserts via GoogleSheetsRepository.insert, which acquires its own ScriptLock. Audit failures are therefore another post-mutation boundary.
- The repository write primitives lock individual insert/update operations, not the whole multi-record business operation. Locks do not establish atomicity across related rows.
- AuditService.list/listByEntity/listByUser tenant-filter returned records; listByUser checks target user's tenant before lookup.
- Static findings only. No runtime tests, failure injection, concurrency tests or deployment checks run. Do not treat hypotheses as confirmed defects.

## Risk / verification candidates
1. ProjectCode change outer lock plus nested repository lock.
2. Cascade deletion outer lock plus inner repository locks (prior Increment 240).
3. Post-mutation audit insert failure boundary on Project update.
4. Event void sequential project/event/material/audit side effects without visible operation-level lock or transaction.
5. Recovery/idempotency semantics after partial event-void or project-update failure remain unverified.

## Next investigation
Continue tracing other mutation entrypoints that hold locks while calling repository primitives, and reconcile exception/rollback patterns. Then assemble the cross-domain mutation matrix and test plan. G-01/G-10 remain open; schema readiness NOT APPROVED.
