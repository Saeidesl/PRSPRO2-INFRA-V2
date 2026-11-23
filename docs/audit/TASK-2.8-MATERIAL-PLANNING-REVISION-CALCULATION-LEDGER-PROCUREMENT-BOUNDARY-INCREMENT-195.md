# Task 2.8 — Material planning revision, calculation ledger and procurement boundary audit — Increment 195

Date: 2026-11-23
Evidence level: E1 static source review only.

## Reviewed source
Production backend read-only files: MaterialPlanningService.js, ProcurementRepository.js, EventRepository.js; related prior ProcurementService and AttachmentService reviews.

## Findings
- Material specifications are project- and tenant-scoped. Specifications carry SpecKey and Revision; listing selects highest revision per key and filters DELETED. Creation validates project access and numeric consumption/waste inputs.
- Revision inserts a new record with the next revision before marking the prior record SUPERSEDED. The code comment states the new highest revision is authoritative even if marking the old row fails. This is a recoverability strategy, but uniqueness and concurrent revision behavior require database constraints and transactional locking in V2.
- Calculation history is an append-only ledger with CalculationKey replay lookup. It records source event/version, spec and need references, basis, consumption, wastage, calculated/previous/variance values and calculation type. The replay lookup is not independently proven atomic; enforce tenant-scoped unique CalculationKey and transactional calculation/need updates in V2.
- Recalculation uses a ScriptLock and verifies project/event tenant and project relationship, derives latest active specification revisions, supersedes older confirmed needs and estimated needs, and persists new calculation and need records. Current lock is process/script scoped; V2 must use PostgreSQL transactional locking/unique constraints and protect against retries and concurrent requests.
- ProcurementRepository provides simple tenant-filtered read methods over needs/orders/items/allocations/receipts. Integrity is chiefly service-level today; V2 should enforce tenant-aware FK/composite constraints, quantity invariants and referential integrity.
- EventRepository is generic sheet-backed CRUD/query access. Several query helpers filter by ProjectID, EventTypeID, PerformedBy or DepartmentID without tenant predicate at repository level; services must scope and validate callers. In V2 use tenant-aware repository/query defaults and explicit authorization, avoiding unsafe unscoped helpers.
- These patterns confirm migration requirements for immutable/versioned records, unique idempotency keys, concurrency control, audit events and transactional writes. They are architecture implications, not findings of runtime failure.

## Risks / pending validation
- No runtime concurrency, rollback, retry or migration tests performed.
- No PostgreSQL constraints or DDL designed in this task.
- Exact WorkflowStepRepository source remains inaccessible through current path retrieval.
- G-01 and G-10 remain open; Schema readiness NOT APPROVED.

## Safety
Read-only production source review. No changes to production code/data, tests, schema, deployment or migration.
