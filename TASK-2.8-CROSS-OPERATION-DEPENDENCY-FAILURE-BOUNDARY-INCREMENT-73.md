# Task 2.8 — Cross-operation Dependency and Failure Boundary Audit (Increment 73)

Date: 2026-11-23
Evidence: E1 bounded source inspection; not runtime verified.

## Inspected source snapshot
- ProjectDeletionService.js SHA 62482bfb42c2c7bb3329becbb2f821e1efbb4c8e
- ProjectService.js SHA 5c9b5bb3f79a2f85472d2cc2ec3d8c3d1e31321f
- EventService.js SHA 1fe31ea9ea2a048a870ecbc47c11512bc2942108
- ProjectInformationService.js SHA 965dfbb92b064f129aefaf38e5a8485bd836b2a8
- MaterialPlanningService.js SHA ff083babf9ef6c3e9e0763ae3c1773db83fc5485
- AttachmentService.js SHA 74492d147499024b6416555a31dd1ee294a746cb

## Newly confirmed bounded observations
1. Project deletion preview collects project events and EventData, material needs, specs/calculations, attachments, and procurement links via allocations/needs. It protects lines/orders that are shared or remain allocated to other projects.
2. Deletion acquires a script lock and re-collects dependencies. Child records are processed in multiple sequential operations; missing Status columns are dynamically ensured. Event rows use VOID due status validation, other dependencies soft-delete, storage files are trashed, and project row is physically removed only when no failures are collected. Partial failure can leave mixed state for reconciliation.
3. EventService void validates tenant, project visibility, event type and AllowVoid, department/admin rules, and later-event constraints (bounded excerpt). Voiding has downstream restoration/material reconciliation and audit boundaries already noted in prior increments; atomicity remains unverified.
4. ProjectService template updates are restricted to an allow-list, lock and UpdatedAt compare; project creation supports an operation identifier for replay recovery. Full copy aggregate flow is not re-audited in this increment.
5. ProjectInformationService saveWithLock delegates to save under ScriptLock; history reads are tenant-filtered in sampled path. Save mutation multi-write ordering remains non-atomic.
6. AttachmentService delete validates tenant and visibility, then storage delete, metadata status update, audit write sequentially. Failure after storage removal may leave metadata or audit incomplete.
7. No runtime test, actual execution, test pass, DDL or production change.

## V2 implementation/verification implications
- Project deletion should be an explicit persisted operation with immutable plan, transactional soft-delete state and external-storage tasks/outbox; no physical cascade unless retention policy authorizes it.
- For void/revision/copy workflows, model state transition and restoration/reconciliation as durable transactional jobs with idempotent retries.
- Keep shared procurement dependencies protected by DB constraints and transaction-level allocation checks.
- Attachment deletion should use tombstone plus outbox worker, retry, verification and restore/retention policy; metadata and audit must not depend on external storage being immediately available.
- Add fault-injection tests at each boundary: lock acquisition, child mutation, Drive/object delete, audit/outbox write, final project state; test replay and repair.
- Existing script locks/optimistic checks are observations in legacy code, not proof of V2 guarantees.

## Status
G-01/G-10 remain OPEN/BLOCKER. G-02/G-03/G-04 remain OPEN/HIGH. Runtime evidence E3 not established; schema readiness NOT APPROVED.