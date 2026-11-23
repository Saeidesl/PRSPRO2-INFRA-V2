# Task 2.8 — AuditService tenant filtering, query cost and write integrity trace — Increment 173

Date: 2026-11-23. Read-only trace of AuditService.js, AuditRepository.js, and Audit header inventory.

## Observations
- AuditService.log validates tenant/action/entity type/entity ID, serializes OldData/NewData, generates audit ID and timestamp, then inserts via AuditRepository. It is a separate insert call; coupling/atomicity to the business mutation is not established.
- AuditService.list reads all audit rows then filters tenant in memory.
- listByEntity fetches by entity type/ID globally through repository then filters TenantID after retrieval.
- listByUser validates target user's TenantID before fetching by UserID, then filters audit TenantID afterward.
- These methods logically filter results in sampled paths, but query efficiency, prefilter tenant scope and transaction coupling are not established. AuditRepository has no update/delete methods in the sampled source.
- JSON serialization may fail or oversize data handling is unspecified; no safe redaction/retention policy evidence in sampled path.

## V2 implications
- SQL query must tenant-scope in WHERE before returning data; indexes should cover tenant/time, tenant/entity type/entity ID, tenant/user/time.
- Auditing should be append-only with authorization and retention controls; sensitive data redaction/size limits must be explicit.
- Business mutation and audit record should commit atomically, preferably through transaction or durable outbox pattern.
- Provide pagination and bounded query response; do not retrieve all tenant logs for every request.

## Status
G-01/G-10 and audit integrity/retention/performance gaps remain. Source-only; no tests or runtime data.
