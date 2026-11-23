# TASK-2.8-INDEPENDENT-EVENT-AND-PROCUREMENT-TIMELINE-PROJECTION-INCREMENT-77

Date: 2026-11-23
Evidence level: E1 source inspection only
Reference repository: PRSPRO2BACKEND-PRODUCTION (read-only)
No tests, live data, production modifications or migrations.

## Observations
- EventService.list source excerpt filters events by TenantID; events without ProjectID are included in the visible list, while project-bound events require a tenant-visible project and user availability.
- Comments and source excerpts distinguish independent event rows from procurement timeline projections; procurement orders/receipts are synthesized into project timeline cards from source records rather than persisted as duplicate Event rows.
- Procurement timeline projection uses tenant-scoped repository collections, order/item/allocation/need/receipt joins, and project-specific allocation context. The inspected excerpt is a projection, not evidence of exhaustive consistency or access-control testing.
- Project-specific event listing and independent event mutation paths were only partially inspected. Full lifecycle validation for project-null event writes, event-type constraints, and all frontend callers remains unresolved.

## V2 implications / pending
1. Preserve explicitly independent events as first-class records with optional project relationship and clear tenant-qualified invariants.
2. Distinguish durable procurement records from read-only timeline projections; avoid duplicated mutable event records.
3. Define event projection/query model and consistency expectations (including receipt/order state changes and replay).
4. Reconcile independent-event create/edit/void routes, permissions, event data/forms, attachments and tenant-negative tests.
5. E1 evidence only; G-01/G-10 remain blockers, procurement integrity G-02 remains open, file/history gaps remain open.

No runtime test, migration, data access, deployment or production write. Schema readiness NOT APPROVED.
