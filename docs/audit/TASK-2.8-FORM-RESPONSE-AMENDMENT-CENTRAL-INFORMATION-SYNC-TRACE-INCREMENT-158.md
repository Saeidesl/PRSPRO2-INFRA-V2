# Task 2.8 — Form Response Persistence, Amendment Replay & Central-Information Sync Trace — Increment 158

Date: 2026-11-23
Scope: Read-only trace of FormService.prepareEventResponses, saveEventResponses, updateEventFormResponse, direct template edits, and ProjectInformationService/repository persistence.
Evidence level: E1 source.

## Observed response persistence
- prepareEventResponses enforces tenant-scoped mappings, active forms, FormVersion, shape, visibility, required fields, allowed field IDs and validation; snapshots the form definition.
- saveEventResponses iterates responses and writes/upserts event response JSON and definition snapshot, then audit. It sequentially writes central project information for non-empty values (including special project-code handling); empty values do not clear prior central values in this create path.
- Response persistence, central information writes, and audit are sequential and no transaction boundary was established in sampled source.
- Form response record includes snapshot JSON, but persistent amendment lineage appears primarily represented by amendment-event ID on update, with separate central-information revision/history.

## Amendment path observations
- updateEventFormResponse requires a related amendment event for same project and checks configured amendment type/form allow-list or legacy name/code markers.
- validates form version, values, fields and visibility; then takes script lock and re-reads response.
- replay detection uses LastAmendmentEventID. On replay, it re-synchronizes project central information rather than writing response again; values differing on replay are conflict.
- project-code fields use authoritative project code if unchanged from the old response; changed values are specially synchronized.
- writes response first (unless replay), then sequentially synchronizes each central-information field via save/deactivate; audit is after those. Partial failure is possible but replay path deliberately attempts sync recovery.
- Lock protects this bounded edit path, but durability, relational constraints and cross-system transaction semantics are not proven.

## Central-information write observations
- ProjectInformationService save/deactivate path writes current value row, increments revision, appends history and audits, then live-reference synchronization side effects occur sequentially.
- The repository uses lookup by project/type and Sheets insert/update. Source sample does not establish DB-level unique constraint or atomic value/history/audit/live-sync behavior.
- Live-reference sync writes child current-value updates, history and sync log sequentially; conflicts and file-based values may be skipped. Recovery/atomicity across child rows is not established.
- File synchronization is explicitly skipped in some reference-conflict handling paths; requires separate safe storage migration/reconciliation policy.

## V2 requirements
- Store event-form responses with immutable definition snapshots and explicit amendment linkage/version lineage.
- Transactional response and central-information mutation with revision/history/audit and outbox; if distributed, durable saga and replay/reconciliation.
- Durable unique tenant/project/information-type constraints; safe idempotent amendment commands.
- Canonical value revisions and historical snapshots; explicit semantics for omitted, empty, cleared, deleted, restored fields.
- Reference sync must be versioned, auditable, conflict-aware, file-safe, and recoverable; use unique idempotency keys and transactions/outbox.
- Test partial failures at response, central value, history, audit and child-project synchronization writes; replay, concurrent edit, cross-tenant checks and file paths.

## Gaps
G-01, G-03, G-04, G-09, G-10, G-11, G-12 remain OPEN/pending discovery. Schema readiness NOT APPROVED.

## Limits
E1 only. No runtime tests, production data, physical schema, migrations, deployment or changes to legacy repos.
