# Task 2.8 Increment 116 — Admin cascade project deletion boundary (2026-11-23)

## Scope
READ-ONLY inspection of ProjectDeletionService.js in production backend.

## Observations (E1 source-only)
- Deletion is explicitly admin-only, tenant-scoped and requires exact project code confirmation.
- Preview collects dependency counts. Remove revalidates under script lock.
- Dependency collection spans project events/event data, project material needs/calculations/specs, procurement orders/items/allocations/receipts and attachments; shared order/item dependencies are retained where other project allocations exist.
- Rows are soft-deleted where supported, with sheet Status columns ensured for legacy child sheets. Events are marked VOID due to sheet status validation.
- Attachment metadata is marked deleted before storage file deletion attempts; failed storage deletes are reported.
- Project row is physically deleted only after dependency handling reports no failures.
- Audit is attempted with cascade deletion counts and failure count.
- Operation is not atomic: individual records/files are handled sequentially and failures accumulated. Project deletion may proceed to physically remove the project row only when no prior failures were collected, but audit failure after deletion is not shown to have rollback. Some records are soft-deleted and files removed; recovery/restore strategy not demonstrated.
- MaterialPlanningService._ensureSchema_ is invoked before preview/remove when available, meaning the deletion flow may trigger schema setup/maintenance; behavior was not examined.
- Lock acquisition uses tryLock with 10 seconds and fails if another operation is active. Lock coverage and shared-lock conventions are not fully established.
- Dependency collector ignores rows deemed inactive/deleted/void; completeness of cross-system dependencies not independently proven.

## V2 implications
- Prefer archive/deactivation as normal lifecycle operation; hard delete restricted, delayed and governed.
- Dependency graph and preview must be transactionally consistent, with exact counts and explicit retained shared-resource rules.
- Deletion orchestrator: durable job, idempotent checkpoints, failure/retry/compensation, object-storage trash/retention, audit/outbox and recovery.
- Referential integrity and tenant-safe cascading policy should be enforced in PostgreSQL; avoid physically deleting project root before full verified dependency handling.
- Test shared procurement allocations, concurrent dependency changes, partial soft-delete failures, storage failure, audit failure, restore and completeness of dependency graph.

## Verification
E1 source-only; no runtime tests executed; no production changes. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain.
