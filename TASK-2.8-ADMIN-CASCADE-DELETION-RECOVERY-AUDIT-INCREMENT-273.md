# Task 2.8 — Increment 273: Admin cascade deletion recovery/partial-failure audit (2026-11-24)

## Scope
Read-only inspection of ProjectDeletionService preview/remove cascade behavior.

## Observations / risk points
- Deletion is admin-only and tenant-scoped, uses a project-code confirmation and script lock, and revalidates project context while locked.
- Dependency collection is tenant/project-scoped and accounts for shared purchase orders/items with other-project allocations; active dependencies are collected before cascade.
- Cascade is sequential, sheet-by-sheet soft-delete/VOID operations with per-row failure capture. A failure in an early step does not stop later dependency operations; final project row removal is gated on an empty failure list.
- Drive file trashing is attempted after attachment record deletion attempts, but the file loop does not appear to require each attachment soft-delete to have succeeded. This presents a recovery/consistency risk if attachment record mutation failed while Drive deletion succeeded. It is a candidate risk, not confirmed with live failure injection.
- Project row removal is physical rather than soft-delete, and occurs only when the accumulated failure list is empty. Nonetheless, dependency operations are not transactional; partial cascade failure may leave a mixed state that requires manual recovery.
- Preview counts and confirmation-code checks reduce accidental deletion but do not establish complete recoverability.
- No production action performed and no defect confirmed.

## Test targets
PD-01..PD-12: dry-run dependency completeness; shared PO cross-project allocation preservation; row-level soft-delete failure; attachment-record failure vs Drive trash; Drive trash failure; event VOID validation; project-row removal failure; retry idempotency after partial cascade; lock contention; dependency changes during lock; audit log failure; recovery/compensating action availability.

## Limits
Static review only; tests/failure injection not executed; no production data or changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
