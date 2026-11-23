# Task 2.8 Increment 110 — Information types and dynamic columns lifecycle (2026-11-23)

## Scope
READ-ONLY trace of InformationTypeService.js, InformationTypeRepository.js and InformationTypeValidator.js from backend production.

## Observations (E1)
- Service resolves current user and scopes type access through TenantID in _type(); list filters tenant and excludes DELETED.
- Create validates definition (name/code format, data type, cardinality, JSON rules/options), checks tenant-scoped code duplicate, creates revision 1 and inserts then audits.
- Update validates merged definition; blocks structural DataType/Cardinality change if type is used in project information or active form fields. Checks deactivation status against active form fields. Writes a limited patch and increments Revision; audit follows repository update.
- Code changes are duplicate checked tenant-scoped. Invalidation of form-definition cache is called after update/deactivate and column mutations.
- Deactivate type soft-deletes and checks active form-field references; project-data usage is not visibly checked in this specific method.
- Column creation validates type ownership and checks active duplicate codes within type/tenant. Column update validates type and column linkage, duplicate code, then writes a patch. Column deactivate validates column tenant but does not visibly require the supplied parent type (no parent-type argument) or check active form/project usage in that method.
- Type/column revision increments and duplicate checks use read-before-write; storage-level uniqueness, atomicity, locking/CAS, and audit recovery are not demonstrated. Repository methods use generic sheet CRUD and do not show tenant enforcement intrinsically on all ID lookups; service paths provide contextual checks.
- Validator supports a defined data-type set and cardinality; options capped at 500 and pattern length capped at 200. Validation rules include numeric fields and regex parseability but regex complexity behavior is not established.

## V2 implications
- Relational FK/tenant constraints, atomic duplicate-code uniqueness, optimistic concurrency (revision/CAS) and transactional audit/outbox.
- Referential lifecycle policy for types/columns used by project data/forms; deletion and migration/retention policy explicitly defined.
- Revalidate parent type and dependencies for column deletion/deactivation; make service paths tenant scoped and idempotent.
- Tests: cross-tenant IDs, structural change while in use, type/column deactivation, stale revision conflict, duplicate code concurrency, JSON/regex adversarial validation, audit failure recovery, cache invalidation.

## Verification
E1 source-only; no tests executed; no production changes. Potential boundaries are not confirmed defects. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 blockers remain.
