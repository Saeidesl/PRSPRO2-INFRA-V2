# Task 2.8 — Event data access and audit repository scoping — Increment 171

Date: 2026-11-23. Read-only trace of EventDataService/Repository and AuditRepository/EventRepository.

## Event data
- EventDataService obtains current user, fetches event by ID, rejects missing or tenant mismatch as not found.
- For project-linked event, it also validates project tenant and project availability to user.
- Event data row is fetched by EventID only from repository; event validation is the tenant gate in the sampled path.
- Stored JSON is parsed and validated as an object; malformed or array payloads fail.

## Audit repository
- AuditRepository.findByEntity(entityType, entityId) and findByUser(userId) scan all audit rows and filter only entity type/ID or user ID respectively. Tenant filtering is not present in these repository methods.
- AuditRepository.insert is a plain insert; caller behavior, immutability and transactional coupling not established here.
- EventRepository has similarly broad ID and secondary filters (project/type/user/department) without tenant constraints at repository level; service/route context is relied upon.

## V2 implications
- Tenant-aware scoped repository/query interfaces should be mandatory; avoid global data scans and unscoped secondary filters in user-facing paths.
- EventData should be relationally constrained to tenant-qualified event parent, with unique EventID if domain cardinality is one-to-one (legacy source suggests a single row per event but constraint not proven).
- Audit events require immutable policy, tenant-safe access, append-only permissions, durable transactional linkage, and retention/exports controls.
- Resource authorization must be applied before any secondary or audit lookup.

## Evidence/status
Bounded source trace only. This reinforces G-01/G-10 and audit design gap; no tests/runtime data, production changes or migration.
