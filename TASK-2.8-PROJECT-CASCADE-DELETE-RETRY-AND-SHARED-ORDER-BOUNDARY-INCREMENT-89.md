# TASK-2.8-PROJECT-CASCADE-DELETE-RETRY-AND-SHARED-ORDER-BOUNDARY-INCREMENT-89

Date: 2026-11-23
Evidence level: E1 source inspection
Reference: PRSPRO2BACKEND-PRODUCTION ProjectDeletionService.js SHA 62482bfb42c2c7bb3329becbb2f821e1efbb4c8e
No runtime tests, production writes, migrations or deployment.

## Cascade deletion mechanics
- Preview reports collector counts and confirmation code.
- Remove validates project code and obtains script lock; revalidates project and dependency graph under lock.
- Soft-delete helper attempts to ensure Status column for legacy sheets, then records per-record failures.
- Processing sequence includes EventData, receipts, allocations, order items, orders, material needs, calculations, specifications, attachments, storage trash and finally events (VOID marker).
- Project physical row deletion is attempted only if no earlier dependency/storage failures were recorded.
- Audit is logged with deletion counts and failedCount, then operation returns conflict if any failures remain.

## Shared procurement safeguards
- Orders/line items are considered through project material needs and allocations.
- Items allocated to another project are excluded from cascade.
- Order is only removed when all active items are selected for removal and no other allocation is present.
- General/shared order preservation logic is visible in collector.

## Failure/retry boundary
- Operations are sequential and individually caught. Failure may result in a partial cascade; the project row is retained if failures before project deletion remain, allowing retry but not transaction rollback.
- If project row deletion succeeds and audit log fails (audit invocation is outside a try/catch), operation may throw after the project row has been physically removed. This is a source-level control-flow observation; actual outcome depends on repository/audit behavior and has not been tested.
- Collector omissions previously documented (project information/history, event-form responses, project-reference sync logs) remain in this inspected dependency graph.

## V2 requirements
1. Transactional DB cascade or explicit deletion-state machine, retaining project tombstone and operation record.
2. Durable deletion checkpoints and retry/restore semantics for storage and external files.
3. Shared-order FK/allocation constraints and deletion-preview completeness.
4. Audit/outbox transaction so delete completion cannot precede durable audit.
5. Fault-injection tests for every step and restore tests for partially deleted graph.

E1 only; no runtime test. G-01/G-02/G-03/G-04/G-10 remain open/blocking as applicable. No production changes.
