# Task 2.8 — PO Decision-Ready Recommendations for Remaining Policy Gates (Increment 267)

## Gate G-02 — Procurement receipt/allocation/status policy
**Recommended defaults (not approved):**
1. Count only ACTIVE receipts toward received/remaining quantities; VOID/DELETED excluded; legacy blank status require migration classification.
2. Reject receipt against CANCELLED/VOID/DELETED/NEEDS_REVIEW orders; allow only explicitly operational statuses.
3. Single and batch receipt APIs share the same validator and allocation policy.
4. For allocated items, receipts must identify project/allocation; general receipt is allowed only against explicitly unallocated remainder.
5. Receipt quantity, cumulative allocation/receipt, and surplus are checked transactionally.
6. Reversal/void requires reason, permission and auditable history; never delete receipt rows.
**PO decision needed:** confirm general-stock remainder behavior, status countability, and receipt reversal policy.

## Gate G-03 — Event/form/project-information history policy
**Recommended defaults (not approved):**
1. Submitted form response is immutable versioned history; amendment creates a new version/linked amendment event rather than rewriting history.
2. Amendment authorization is permission- and project-scoped; configuration restricts amendable forms and amendment reason.
3. Project-information revisions are versioned with explicit conflict-resolution and LIVE_SYNC baseline history.
4. Event snapshots retain exact workflow/event type/form versions at time of submission.
5. Event void is a status transition with immutable reason/history and conditional project-state restore; no cascading hard delete.
**PO decision needed:** confirm immutable amendment/history model, amendment eligibility, and project-information conflict authority.

## Gate G-04 — File/storage lifecycle
**Recommended defaults (not approved):**
1. DB attachment record is authoritative metadata; object/file storage deletion is asynchronous, durable and retryable.
2. Delete/archive preserves metadata, audit and retention evidence; purge requires explicit retention expiry and policy.
3. Storage delete uses idempotent job/outbox and reconciler; no synchronous assumption of atomic DB/storage.
4. Attachment ownership/category/type and retention are explicitly enumerated; unknown classifications default to restricted.
5. Project deletion archives/restricts project and queues dependent file cleanup; shared files are never removed without proven ownership.
**PO decision needed:** retention duration, categories, legal/operational exceptions, purge authorization.

## Gate G-10 — Tenant/global classification and security invariants
**Recommended defaults (not approved):**
1. Every business row is tenant-scoped by composite tenant key unless explicitly declared global immutable reference.
2. Global catalog data is read-only to tenant users; tenant overlays use separate records.
3. Polymorphic references include tenant ID and resource authorization; audit references retain tenant.
4. Unique keys include tenant unless global uniqueness is explicitly required.
5. Every route/resource mutation fails closed and has negative tests for cross-tenant and unauthorized access.
**PO decision needed:** confirm which catalogs (workflow definitions, form types, units/status catalogs, permissions, system roles) are truly global vs tenant-owned.

## Decision status
Recommendations are architecture proposals, not user-approved business rules. G-02/G-03/G-04/G-10 remain OPEN until explicit PO decisions and test/migration evidence.
