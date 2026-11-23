# Task 2.8 Increment 111 — Form definition and dynamic field lifecycle (2026-11-23)

## Scope
READ-ONLY trace of FormService.js, FormRepository.js and FormValidator.js. Focus on reusable form definitions, fields, conditional visibility, versioning, cache and mutation sequencing; response lifecycle covered only where visible in retrieved excerpts.

## Observations (E1)
- Forms are tenant-scoped by service helper; deleted forms are hidden. Form list filters tenant and DELETED.
- Create validates form Name/Code/Description/Status, tenant-scoped duplicate code, inserts version 1 then audit.
- Form update writes limited fields and increments Version, validates code uniqueness when code changes, invalidates form-definition cache, then audits.
- Form fields are tenant-scoped, linked to form and InformationType, and active fields are used to build definitions. Type tenant/status is validated. Multiple-cardinality types require active columns.
- Adding a field checks duplicate active InformationType in same form, assigns order, validates visibility reference to a preceding active field, inserts field, increments form version, invalidates cache, then audits.
- Field update validates ownership and dependencies; validates selected type and duplicate active field; protects referenced fields from order/status/removal changes and validates visibility reference. It writes field, increments form version, invalidates cache, then audits sequentially.
- Field removal is soft delete, guarded against active dependent visibility references; form version increment, cache invalidation and audit follow.
- Visibility operators supported in validator/service: EQUALS, NOT_EQUALS, FILLED, EMPTY; visibility references must point to a preceding active field in the same form.
- Definition cache uses ScriptCache with 60-second TTL and tenant-level version key invalidation (version token changed on writes). Cache invalidation errors are swallowed as cache optimization.
- Repository uses generic Sheet CRUD; no storage-level composite FK/unique constraints, transaction or locking proof.
- Service-level mutations are sequential; insert/update field, form version update, cache invalidation and audit may be partially completed if a later step fails. Form version increments are not shown with CAS/expected version for configuration mutations; concurrent edits remain unverified.
- Response paths shown in retrieved excerpts perform validation and tenant/project/event checks and use locks in some amendment/direct-response mutation paths; not a complete response lifecycle audit.

## V2 implications
- Relational FK and tenant composite constraints; transactional form+field+version+audit mutations.
- Optimistic concurrency for definition/field edits, idempotent configuration mutation, clear rollback/recovery.
- Central visibility rule validation and dependency graph, cycle prevention, safe order updates.
- Cache invalidation through durable versioning or outbox; no cache correctness reliance.
- Tests: cross-tenant IDs, duplicate type/field, visibility dependency/order, type deactivation, stale versions, concurrent field mutation, partial failure, cache invalidation and response contract.

## Verification
E1 source-only; no tests executed; no production changes. Potential failure boundaries are not confirmed defects. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.
