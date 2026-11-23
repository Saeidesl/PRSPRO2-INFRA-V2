# Task 2.8 — Legacy event-data and audit header cardinality constraints — Increment 172

Date: 2026-11-23. Read-only cross-check of DatabaseSetup.js headers against service/repository traces.

## Observed headers
- `14_EventData`: ID, EventID, DataJSON, CreatedAt. No TenantID, event type, revision, or uniqueness declaration in the header inventory.
- `16_AuditLogs`: ID, TenantID, UserID, Action, EntityType, EntityID, OldData, NewData, Reason, CreatedAt.
- `13_Events`: includes TenantID, ProjectID, workflow context, event type version, actor/dept, occurrence/create times, status, previous-state snapshots, variant details and idempotency key/status.

## Relational and data integrity implications
- EventData tenant identity is inherited through EventID; target schema should use event-parent relational FK and enforce one-to-one cardinality if confirmed by full write-path evidence.
- AuditLogs have explicit TenantID but EntityType/EntityID polymorphic association lacks FK proof; design tenant-qualified entity association integrity or carefully bounded polymorphic model.
- Events store workflow version, event-type version, previous-state restoration snapshots and idempotency marker; these are important source fields but uniqueness, immutability and transaction rules are not established by headers.
- Header inventory is not proof of runtime schema completeness or constraints; sheet drift and all write paths remain unverified.

## Status
G-01/G-03/G-10 and audit integrity remain open. Source/header inspection only; no schema/migration/tests/runtime/live data.
