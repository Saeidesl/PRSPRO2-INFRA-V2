# Task 2.8 — Tenant-owned Department and Role uniqueness/lifecycle trace — Increment 177

Date: 2026-11-23. Read-only trace of DepartmentService and RoleService, sampled repository behavior.

## Observations
- Department and Role list paths load all rows then filter by TenantID in memory; individual getters resolve by ID then verify TenantID.
- Creation validates a normalized (trimmed, case-insensitive) Code for duplicates within the tenant, then inserts record and audit sequentially.
- Update paths validate tenant ownership and check tenant-scoped duplicate code when code changes (bounded excerpts; exact full lifecycle and delete behavior not exhaustively traced here).
- Service-level duplicate checks are pre-insert scans; no DB uniqueness constraints or concurrency protection evidenced.
- Repository findByCode methods appear unscoped/global, while findByTenantId reads all then filters. Callers must verify tenant.
- Audit coupling to entity write remains sequential/non-atomic in sampled paths.

## V2 implications
- Department and Role codes need normalized composite uniqueness constraints scoped by TenantID (or explicit policy revision if approved).
- Query access should use tenant filters directly in SQL; unique/FK constraints enforce tenant-qualified relations.
- Status, deletion and code reuse semantics must be traced before deciding partial unique indexes or soft-delete policy.
- Audit should be transactionally consistent with create/update.

## Status
G-01/G-10 remain open. Source-only; no tests/live data.
