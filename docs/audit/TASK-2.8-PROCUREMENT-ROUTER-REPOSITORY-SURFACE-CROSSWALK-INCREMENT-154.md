# Task 2.8 — Procurement Router & Repository Surface Crosswalk — Increment 154

Date: 2026-11-23
Scope: Read-only retrieval of legacy ProcurementRepository.js and Router.js procurement route excerpts.
Evidence: E1 source evidence only.

## Observed API surface in the retrieved Router excerpt
- GET: `/procurement/workspace`, `/procurement/reports`, `/procurement/dashboard`, `/procurement/needs`, `/procurement/orders` with permission gates for PROCUREMENT_VIEW/PROCUREMENT_MANAGE (reports additionally REPORT_VIEW).
- POST: `/procurement/orders`, `/procurement/receipts/batch`, `/procurement/receipts`, all with PROCUREMENT_MANAGE.
- No dedicated receipt correction/reversal/cancel route was visible in the retrieved Router dispatch excerpt. This is bounded excerpt evidence, not proof of absence across every legacy path.
- ProcurementRepository exposes tenant-filtered collection reads for needs, orders, items, allocations, receipts using TenantID filtering. The repository itself does not expose mutation methods in the retrieved file; writes are performed via generic GoogleSheetsRepository operations in services.

## Implications
- V2 should expose explicit lifecycle commands rather than implicit generic mutations, with authorization centrally enforced and tenant context enforced in service/DB.
- Receipt correction/reversal needs an explicit API contract, lineage, authorization and audit model, rather than an assumed legacy endpoint.
- Procurement collections should have tenant-qualified relations/constraints and scoped indexes; projection access should be based on canonical domain accounting.
- Read route permission distinctions and report access should be deliberately designed, not copied without least-privilege review.

## Open gaps
- G-01 exhaustive route/caller/dependency coverage remains OPEN.
- G-02 correction/reversal receipt lifecycle remains OPEN/BLOCKER.
- G-10 tenant-safe relational constraints remain OPEN.
- No runtime tests, live data, schema, migration or production modification.
