# Task 2.8 Increment 235 — Procurement integration/material-planning test review (2026-11-24)

## Reviewed
tests/procurement-event-integration.test.js and tests/material-planning.test.js, read-only.

## Coverage observed
- Procurement event integration tests include validation and central-information provenance, legacy receipt integration settings being inert, event order of validation/save/apply, and confirmation that event integration does not create receipts; standard order/receipt routes remain independent.
- Material planning tests include specification creation/versioning, validation, confirmed quantity events and replay, marking material needs for review when source event is voided (preserving history), and cross-tenant/invisible project rejection.

## Scope distinction
These tests concern event-driven material planning and event integration. They do not constitute direct receipt mutation integrity tests (batch/single receipts, allocation-based receipt ceilings, receipt void status totals, transaction failure/recovery) in the reviewed cases.

## Task 2.8 status
Static inspection only; tests not executed. The broader inventory reviewed so far still does not establish test coverage for the receipt transaction/failure scenarios. G-01/G-10 remain open; schema readiness NOT APPROVED.

## Next
Increment 236: inspect attachment/project deletion tests and cross-reference high-risk mutation integrity/rollback coverage, then consolidate Task 2.8 audit matrix.
