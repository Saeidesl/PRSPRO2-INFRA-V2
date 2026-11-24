# Task 2.8 Increment 214 — Project cascade-deletion source trace

Date: 2026-11-24

## Source
Read-only trace of ProjectDeletionService.js blob `62482bfb42c2c7bb3329becbb2f821e1efbb4c8e`.

## Preview
- Builds project context and dependency collection.
- Reports counts across events, event data, attachments, material needs, orders/items, allocations, receipts, material specifications and calculations.
- Returns project identity and confirmation code (project code).

## Remove path
- Requires confirmation code matching project code.
- Uses ScriptLock and revalidates project/dependencies inside lock.
- Soft-delete helper ensures Status columns for legacy sheets; failed operations are collected per row.
- Marks events VOID due sheet validation instead of invalid DELETED state.
- Processes dependency families sequentially: event data, receipts, allocations, order items, orders, needs, calculations, specs, attachments, then Drive storage objects, and events.
- Storage objects are trashed after attachment metadata is soft-deleted.
- If no dependency failures, physically removes project row and invalidates repository record cache.
- Logs cascade-delete audit including counts and failed count; if failures exist, raises conflict with failure detail rather than silently succeeding.

## Risk/evidence gaps
- This is a multi-step cascade, not demonstrated as transactional. Partial deletion can occur before later steps fail; retries require careful state/recovery.
- The ordering of metadata status updates and Drive deletion may leave recovery/reconciliation states; no compensation/restore workflow was demonstrated.
- Project row removal and cascade audit are separate operations.
- Preview/remove parity, stale preview, authorization/tenant negative tests, and injected failure recovery across each dependent collection/storage boundary need test evidence.
- Project deletion is distinct from ordinary soft deletion and needs V2 retention/restore policy and explicit PO approval.

## Gate impact
G-01 mutation/effect inventory and G-04 attachment/storage recovery remain open; G-10 tenant integrity remains open. No policy adopted.

## Safety
Read-only trace; no production changes or tests, no DDL, migration, live data, VPS or deployment.
