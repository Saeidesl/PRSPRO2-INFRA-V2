# Task 2.8 — Increment 275: Event and procurement mutation idempotency/locking cross-check (2026-11-24)

## Scope
Read-only review of EventService.create idempotency handling and ProcurementService.createOrder flow.

## Observations
- Event create supports an optional IdempotencyKey scoped to TenantID and CreatedBy, with user-lock serialization. Existing PROCESSING keys fail closed; completed prior requests replay a response with visibility checks for project-linked records.
- Clients without idempotency keys retain non-keyed legacy behavior; retries without keys therefore are not deduplicated by this layer.
- Event idempotency replay derives form response presence and reports integration statuses from existing rows; replay does not repeat integration. This needs verification for partial/failed persistence states.
- Procurement order creation uses script lock, pre-validates the full item graph before writes, and supports idempotency replay scoped to tenant. Sheets cannot atomically commit multi-sheet order graph writes; rollback/compensation after partial persistence remains a test requirement.
- Idempotency replay lookup and ordering are static observations; duplicate key semantics, concurrent retries, replay of incomplete records, and key retention policy are not runtime verified.
- No confirmed defect.

## Test targets
ID-01..ID-12: missing-key retry duplicates; same key concurrent event requests; key scope across users/tenants; PROCESSING state crash recovery; event/form/central-info/procurement partial commit replay; project visibility replay; same-key order replay; concurrent order retries; multi-sheet write failure compensation; duplicate allocation prevention; audit-write failure; idempotency-key length and normalization.

## Limits
Static only; no tests/failure injection, production data or writes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
