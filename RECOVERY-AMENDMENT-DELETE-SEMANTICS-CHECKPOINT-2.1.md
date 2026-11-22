# Recovery, Amendment and Delete Semantics — Audit checkpoint 2.1

Read-only source inspection on 2026-11-22.

## Project cascade deletion
ProjectDeletionService cascade handling includes event data, receipts, allocations, purchase-order items/orders, material needs, calculations, specifications, attachments and events. Some legacy child tables are missing Status columns; service attempts to add Status dynamically before soft-deleting them. Events are marked VOID rather than DELETED due to sheet validation. Attached storage objects are sent to StorageRepository.delete after attachment metadata soft-deletion. If all dependency processing succeeds, project row itself is physically removed. Failure collection and an audit entry record deleted counts and failed dependencies; partial failure raises a conflict. This is not a transactional cascade and the V2 design must provide atomicity or explicit durable orchestration, retention and recovery.

## Procurement recovery semantics
Purchase-order creation can partially fail after creating order/items/allocations; the source preserves the order with NEEDS_REVIEW and appends an explanatory note rather than deleting partial history. Receipt creation validates tenant, order state, ordered quantities and idempotency/replay keys. Cancelled purchase orders reject receipt paths observed. This confirms a recovery-state workflow, not a simplistic rollback.

## Form amendments
Amendment form updates require an amendment event from the same project and tenant. Explicit event-type configuration is preferred; legacy code/name markers are also recognized as a fallback. Configured amendments can restrict allowable form IDs. Form version is checked; values are validated against field definitions and visibility; locking and ExpectedUpdatedAt checks reject stale writes. Replay of the same amendment event is accepted if payload matches; a differing payload is rejected. Project-information synchronization is retried during replay to recover from partial synchronization. Auditing records changes.

## Attachment visibility / delete behavior
Thumbnail access validates tenant, user visibility, deleted/void status and image MIME type. Cascade deletion attempts metadata soft deletion followed by provider-side deletion of storage objects. Thus current source does not establish an assured recoverable retention window after physical storage deletion. V2 must define retention, recovery, object versioning and metadata/storage consistency explicitly.

## Unresolved
- Investigate purchase order cancellation and receipt-adjustment/return paths more comprehensively.
- Verify all cascade dependencies and failure/retry semantics, including source-event links.
- Confirm backup-sheet contents and whether any operational process depends on it (not yet inspected live).
- No schema or migration code has been created. No production system or data was modified.
