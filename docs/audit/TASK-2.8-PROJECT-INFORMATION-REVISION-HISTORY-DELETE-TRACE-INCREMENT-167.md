# Task 2.8 Project-information revision/history/delete semantics — Increment 167

Date: 2026-11-23. Read-only backend trace: ProjectInformationService.js and ProjectInformationRepository.js.

## Observed behaviors
- Project information is stored as ValueJSON with Revision, Status, SourceEventID, UpdatedBy/At; associated history table is 31_ProjectInformationHistory.
- Save validates the information type/columns and tenant/project/source-event relationship, serializes values, enforces a bounded JSON cell size in the legacy Sheets implementation, and checks ExpectedRevision to detect stale edits.
- Save updates/inserts current information, then inserts history, logs audit and invokes live-reference synchronization sequentially. The trace does not prove transactional atomicity across those writes.
- History captures old/new values, revision, action, source event, reason, actor and time.
- Soft-delete path marks current information DELETED, increments revision, writes history and audit sequentially; physical purge not evidenced in sampled path.
- File-based reference conflict resolution explicitly rejects USE_SOURCE in the inspected path because secure attachment transfer is not implemented there.
- Revision approvals and conflict resolution records coexist with history/sync logs; snapshot/version retention model should be deliberately designed in V2.

## V2 implications
- Current-state row plus immutable revision/history model (or equivalent append-only version records) with atomic commit.
- Tenant-scoped FK integrity across project, type, source event, history and actor; validate indirect links.
- Define soft-delete, restore, retention, purge, amendment and approved-revision semantics; protect attachments referenced by retained versions.
- Replace legacy per-cell size limit with explicit DB and file-storage limits, while preserving payload structure.
- Live-sync should be a transactional/outbox or recoverable workflow; file sync conflict requires explicit handling.

## Evidence/status
G-01/G-03/G-04/G-09/G-10/G-11/G-12 remain open; schema readiness NOT APPROVED. Source-only trace; no tests/live data/production modifications/migration.
