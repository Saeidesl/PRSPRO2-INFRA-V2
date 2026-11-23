# Task 2.8 Increment 200 — Consolidated Product Owner decision register

Date: 2026-11-23
Status: Decision-preparation artifact. No policy is accepted or implemented by this document.

## Purpose
Consolidate business decisions across G-01/G-02/G-03/G-04/G-10, minimize decision burden and clearly separate Product Owner choices from technical implementation decisions.

## A. Decisions requiring business/PO confirmation

### Procurement (G-02)
1. Over-receipt: default recommendation is block; exception only with authorization/reason.
2. General stock: explicit unallocated stock destination vs project allocation; no implicit project attribution.
3. Receipt correction: reversal/return-to-supplier/correction classification and operational disposition.
4. Cancellation after receipts: allowed only after facts and allocations are explicitly resolved; confirm operational rules.
5. Allocation across projects: approval/permission level and when reallocations are allowed.
6. Batch receipts: default all-or-nothing; confirm preference if partial-success batch is operationally needed.
7. NEEDS_REVIEW: confirm who may resolve and which actions remain blocked.

### Event/Form (G-03)
8. Amendment scope: which fields/form types may be amended, and whether any require approval.
9. Void policy: allowed time window, permissions and approval level.
10. Reasons: controlled reason categories for amendment/void.
11. Operational reports: define which effective states/counts include amended or void events; audit view preserves full lineage.
12. Event-type impact: classify effects on project/workflow/material planning/procurement/reporting per event type; never generic inference.

### Attachments (G-04)
13. Retention: business retention and restore window per category.
14. Purge: authorized role, approval, evidence/backup gate and schedule.
15. Attachment owner categories: confirm exhaustive legitimate ownership and any shared/global files.
16. External uploads: decide whether malware-scanning is a mandatory operational requirement or assessed deployment control.

### Tenant/global classification (G-10)
17. Confirm tenant-owned vs global catalog classification for users/membership, departments, permission catalog, workflows, forms, event types, information types and templates.
18. Confirm independent event classes that legitimately have no project link.
19. Confirm whether tenant sharing across projects is ever permitted for specific resources and how visibility should be calculated.

## B. Architect/engineering decisions (do not require PO choice unless they alter business behavior)
- PostgreSQL tenant-qualified relationship constraints and tenant-aware defaults.
- Transaction and lock boundaries; optimistic revision conflict handling.
- Durable idempotency with payload fingerprint and conflict semantics.
- Outbox/reconciliation for external side effects.
- Exact decimal representation and unit conversion implementation, after business units/precision rules are known.
- Typed attachment ownership model and provider-neutral storage interface, subject to owner/retention decisions.
- Versioned immutable event/form snapshots and audit lineage representation.
- Canonical projections and API contracts, traceable to approved policies.
- Automated test strategy, CI, security tests, observability and recovery mechanisms.
- Physical schema, migrations and migration validation design only after readiness gates close.

## C. Evidence/coverage gates (not discretionary business choices)
- G-01: exhaustive active structure, route, mutation, consumer and dependency mapping; disposition unused/unknowns with evidence.
- G-03: exhaustive form payload, snapshot, amendment, report and caller coverage.
- G-04: attachment owner/type inventory, storage-object recovery/reconciliation and lifecycle evidence.
- G-10: tenant/global classification mapped into relationship constraints and cross-tenant negative tests.
- Repository retrieval blocker: source coverage remains incomplete until production repository tree/content access is recovered and independently crosschecked. Existing production repositories remain read-only.

## D. Decision-efficiency recommendation
Avoid asking the PO for decisions before the relevant business context is clear. Group decisions into a single short review, use recommended conservative defaults for discussion, and record explicit accept/reject/defer outcomes. Do not silently adopt defaults. If no approval is available, mark the affected design assumption as pending and continue unaffected technical discovery.

## E. Status / guardrails
- G-01/G-02/G-03/G-04/G-10 remain OPEN.
- Schema readiness remains NOT APPROVED.
- No SQL/DDL, migration, tests, live data, provisioning, deployment, VPS or production modification.
- This register is preparation, not a request for immediate user answers.
