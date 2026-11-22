# Task 2.6 — Resolve Discovery Blockers Before Schema Design

Status: IN PROGRESS (started 2026-11-22)

Purpose: resolve critical domain contracts and discovery blockers from Task 2.5 before any physical schema design. Work is read-only against legacy repositories. Decisions below are V2 design recommendations derived from sampled source evidence, not approved business policy unless explicitly marked.

## Workstream 1 — Procurement correction/reversal contract (G-02)

### Increment 1 — bounded source review (2026-11-22)
Reviewed legacy ProcurementService createOrder, receipt creation and batch receipt paths, plus ProcurementEventIntegrationService save/validate/apply and ProcurementRepository context.

Evidence:
- Order creation uses lock/idempotency replay, tenant/project/need validation, order-item normalization and allocation-related calculations; writes span multiple sheets and are not shown to be transactionally atomic.
- Existing orders/items/allocations influence ordered-quantity projections. Statuses CANCELLED, VOID, DELETED and NEEDS_REVIEW are excluded from certain allocation/projection calculations, showing status semantics affect derived quantities.
- Receipt creation and batch receipt paths have idempotency checks, tenant/order/item/project validations, allocation constraints and source-event association. The sampled paths are write-oriented and their full write/reversal/status semantics require further source review.
- Procurement event integration currently maps a configured event to confirmed-quantity recalculation. Sampled comments/code explicitly treat legacy receipt mappings as inert and Receipt APIs as independent. Applying integration recalculates project material needs from confirmed quantity.
- No general correction/reversal contract for order, item, allocation or receipt was proven in the bounded sample. Existing partial compensation/review status semantics must not be assumed to be accounting-grade reversals.

### V2 recommendation — proposed contract pending further discovery
1. Persist procurement actions as auditable domain operations; distinguish cancellation before fulfillment from correction after fulfillment.
2. Do not physically delete procurement orders/items/allocations/receipts once they affect fulfillment or reporting. Use explicit lifecycle states and compensating records/adjustments.
3. Receipts should be immutable ledger-like records after posting; correction should be a linked reversal/adjustment, preserving original record, reason, actor, timestamp and references.
4. Order/item cancellation should be allowed only when received/allocated/fulfilled quantities and downstream links are reconciled; otherwise require controlled adjustment or review workflow.
5. Derive quantities from valid status-scoped operations and enforce invariants within database transactions; never rely on cached spreadsheet projections as correctness source.
6. Idempotency keys must be tenant-scoped, operation-scoped and backed by uniqueness constraints; retries should return the prior operation.
7. Maintain audit and reconciliation reports for discrepancies and partial legacy migration states.

Open questions requiring additional source coverage before finalizing: exact receipt void/deletion paths, allocation creation/reassignment/adjustment, order status transitions, over-receipt/surplus policy, procurement event dependencies, reports/dashboard formulas, and project deletion behavior for all procurement states.

No tests, live data, SQL/DDL, migration, DB provisioning, production modification or deployment.


### Increment 2 — projection/report/deletion semantics (2026-11-22)

Additional bounded source review of ProcurementService dashboard/reports/listOrders/workspace, ProcurementRepository, and ProjectDeletionService dependency collection.

Evidence:
- Procurement workspace composes needs, visible orders and dashboard. Report dataset includes needs, orders, projects, material specs and calculations.
- Dashboard aggregates receipt quantities by PurchaseOrderItemID, then caps displayed received quantity at ordered quantity and computes pending as max(ordered-received,0). Surplus is separately aggregated. In the sampled dashboard path, receipt aggregation does not visibly filter receipt status; this requires explicit confirmation against status conventions and other consumers before V2 semantic equivalence is defined.
- Report/workspace and visibility logic can omit orders associated with inaccessible projects; order visibility is evaluated across linked projects. Shared order/item allocations therefore have cross-project visibility and dependency consequences.
- Project deletion collector links procurement to project material needs and allocations. Shared order lines are preserved where another project's allocation remains. Orders are cascade candidates only when all active lines are project-linked and no other allocation remains. Receipt collection also includes links via item, project or order. Soft-delete/cascade writes and Drive trash operations are multi-step, not a demonstrated transaction.
- The sampled service exposes create-order, receipt create and batch receipt, list/workspace/reports/dashboard. No explicit order-cancel, receipt-void, allocation-adjustment or reversal API appears in the sampled ProcurementService function inventory. This is bounded negative evidence, not proof no such path exists elsewhere.

### Increment 2 decisions and unresolved G-02 items
- V2 projections must define receipt-status eligibility centrally, with one shared calculation rule used by dashboard, workspace and reports.
- Receipts, allocation changes and cancellations must not be physically removed when they affect shared orders or reports; reversals/corrections should be linked records and the projection should derive net eligible quantities.
- Project deletion should be a policy-driven archive/retention operation with a dependency preview that includes shared orders, allocations, receipts and retained audit/history. A project delete must not erase fulfillment history belonging to another project.
- G-02 still BLOCKER: over-receipt, surplus, item/order state transitions, receipt correction and allocation rebalancing require further coverage and policy decisions.

No tests, live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 3 — receipt batch invariants and API surface (2026-11-22)

Reviewed batch-receipt implementation, Router procurement read endpoints and existing test files (tests were read, not executed).

Evidence:
- Batch receipts normalize and validate all entries before the insertMany call; duplicate idempotency keys within a batch are rejected, and previously recorded keys are replayed.
- Receipt batch enforces item ordered-quantity ceiling, allocation-to-project limits and unallocated general-warehouse limits. It rejects receipts against CANCELLED orders; NEEDS_REVIEW orders are treated specially during order status recalculation.
- Batch inserts receipt rows, recalculates order status (ACTIVE/PARTIALLY_RECEIVED/RECEIVED) based on aggregated quantities, then audits each receipt. These steps are multi-write and not demonstrated atomic. Status recalculation is skipped for NEEDS_REVIEW orders.
- Single receipt and batch receipt paths have slightly different implementation details and replay handling; both require a shared canonical policy in V2.
- Router sampled read routes expose workspace, reports, dashboard, needs and orders under permission checks. EventType procurement settings route is separate. Procurement create/receipt write endpoints were not fully enumerated in this increment, so route coverage remains pending.
- Existing tests assert idempotency/quantity allocation behavior, workspace caching and event integration separation, but were not run. Test presence is evidence of intended legacy behavior, not a successful test result.

### V2 design direction refined
- Receipt posting should validate and post in one DB transaction; order status should be a derived projection or updated within the same transaction.
- The same receipt eligibility, allocation and idempotency rules must apply to single and batch paths.
- Order lifecycle states should be explicit and mutually coherent, with NEEDS_REVIEW treated as a controlled state rather than a generic active/inactive flag.
- Implement reconciliation for status/quantity drift and preserve audit links for all corrections.

G-02 remains BLOCKER: correction/reversal paths, allocation mutations, route write surface, surplus/over-receipt policies and report semantics still require source coverage.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 4 — order creation recovery and single-receipt parity (2026-11-22)

Reviewed remaining order creation writes, single receipt writes and Router write-path inventory.

Evidence:
- Order creation validates/normalizes full payload before writing, then inserts order, each item and its allocations sequentially. On failure after header insertion, it attempts to mark the order NEEDS_REVIEW and adds a failure note rather than deleting partial records. This is a compensating recovery marker, not atomic transaction rollback; update failure/recovery guarantees remain unproven.
- Order creation allocations are derived from need quantity, prior counted allocations, surplus and explicit allocation payload; total allocation is validated against ordered quantity. Need/project tenant and visibility validation is applied in sampled code.
- Single receipt checks replay by source-event/item and idempotency key at outer method, then validates project/item/order/allocation and ordered quantity, inserts receipt, updates order status and audit. These writes are sequential and no atomic transaction is demonstrated. The single path's quantity aggregation includes prior receipt rows without a clearly demonstrated common status filter, unlike the replay predicate that excludes VOID in one branch.
- Router inventory confirms write service calls for createOrder, createReceipt and createReceiptsBatch; sampled read endpoints include workspace, reports, dashboard, needs and orders. Route-level write permissions were not fully captured from this limited excerpt and remain to verify in later security/tenant work.

### G-02 contract consolidation — still provisional
- Legacy failure recovery leaves order header NEEDS_REVIEW when item/allocation write sequence fails; V2 should transactionally prevent partial graph writes, while maintaining a durable failed-operation/reconciliation record when external side effects are involved.
- Single and batch receipt flows must share identical status eligibility and validation policy. Replay by source-event and idempotency key must be standardized and DB-enforced.
- Status drift and partial legacy graph recovery are explicit migration/reconciliation cases; not a reason to silently discard records.

G-02 remains BLOCKER until receipt corrections/reversals, allocation mutation/deallocation, over-receipt/surplus, lifecycle states and dependency policies are sufficiently discovered and decision-ready.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 5 — lifecycle surface inventory and allocation/surplus semantics (2026-11-22)

Read-only source inventory of ProcurementService public method list, ProcurementRepository, event-integration service and test-source assertions.

Evidence:
- ProcurementService public method inventory includes createOrder, createReceipt, createReceiptsBatch, listNeeds, listOrders, workspace, reports and dashboard; no allocation-update/deallocation, order-cancel, receipt-void or receipt-adjustment public method appeared in the sampled service inventory.
- ProcurementRepository is read-only collection access for needs, orders, items, allocations and receipts; it exposes no dedicated mutation/lifecycle API. Mutation logic is embedded in ProcurementService.
- Existing test source includes allocation cap/surplus behavior: cumulative order allocations against a need are capped at requested quantity and remaining ordered quantity is recorded as surplus. Estimated and confirmed need records share allocation key semantics for existing procurement.
- Tests are only inspected, not executed. These are bounded observations, not complete proof that no lifecycle operations exist in another file or indirect route.

### Proposed procurement lifecycle matrix — provisional
- Order: DRAFT/ACTIVE -> PARTIALLY_RECEIVED -> RECEIVED; CANCELLED allowed only before posted receipt or after explicit reconciled adjustment; NEEDS_REVIEW is an operational exception state, not an ordinary lifecycle transition. Confirm whether draft state exists in product requirements before adopting.
- Receipt: POSTED (immutable) -> linked REVERSAL/ADJUSTMENT; no silent deletion or overwrite after posting. Void semantics, if allowed before posting, need an explicit state and reason.
- Allocation: explicit allocate/reallocate/release operations with quantity invariant: active allocation sum <= order-line quantity; project-specific received quantity <= active allocation quantity unless approved surplus/exception path. Release/reallocation must preserve receipt history and reconcile downstream needs.
- Surplus: separate unallocated quantity from need-allocated quantity. Do not treat surplus as negative need or silently alter material requirements. Confirm warehouse/general procurement policy and how surplus can be allocated later.
- NEEDS_REVIEW: controlled repair/reconciliation workflow with reason, actor, timestamp, prior/current snapshots and resolution record.

### G-02 resolution status
G-02 is NOT resolved. The source inventory suggests the legacy service primarily supports creation and read projections; corrective lifecycle contracts and operational policy remain insufficiently discovered. Next steps should inspect project/business requirements, downstream event/report consumers, and any deletion/void/adjustment routes in other services before treating the matrix as finalized.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 6 — cross-service consumers and project deletion retention review (2026-11-22)

Reviewed MaterialPlanningService method inventory, ProcurementEventIntegrationService and ProjectDeletionService collector/preview context.

Evidence:
- MaterialPlanningService versioned specification and calculation-history functions create/revise specs, record/recalculate needs and mark source-event voids. Its header and sampled logic intentionally leave existing orders/receipts intact; calculation updates can be linked to material-need records and source event status.
- Procurement event integration explicitly treats receipt mappings as inert and Receipt APIs independent; its active integration recalculates confirmed material needs from project central information. V2 should not couple event integration directly to posted receipt ledger.
- Project deletion dependency collection includes events/event-data, needs, order/items, allocations, receipts, specs, calculations and attachments. It is allocation-aware for shared procurement items/orders and receipt association through item/project/order. However, delete collection is a legacy destructive cascade, not a universal retention policy.
- A project-delete cascade should not be copied literally into V2 for procurement history: V2 should preserve posted fulfillment and audit records, anonymize/archive project data only under approved retention policy, and use referential restrict/soft-archive or explicit retention-aware workflow when linked procurement history exists.

### G-02 decision refinement
- Procurement needs are recalculated from central project information/event integration, but posted receipts must remain independent from need recalculation. Changes to needs should not automatically reverse received quantities.
- Need/spec/calculation revisions and procurement allocations are separate concepts; preserve their linkage without treating a revised need as an order/receipt mutation.
- Project deletion and order deletion are not interchangeable; fulfillment records must survive project archival/deletion policy.

G-02 remains BLOCKER. The lifecycle contract is now sufficiently drafted for internal technical design but requires remaining discovery (write routes/other consumers and report calculations) and final consistency review before this gap can be marked resolved.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 7 — configuration entity lifecycle and version semantics (2026-11-22)

Read-only sample of WorkflowService, InformationTypeService, EventTypeService, ProjectStatusService and PermissionService method/lifecycle paths.

Evidence:
- Workflow service supports management bootstrap/data, create/update, steps, step-event rules and publish. Publishing validates active step/rule/event references and records a published version; editing rules can clone published configuration into draft version. Workflow versions and published references must be preserved rather than overwritten.
- Information types have logical-deactivation paths; type deactivation checks active form field usage. Column deactivation is logical but sampled path does not demonstrate comprehensive historical form-response or project-information usage guard. Revisions increment on type deactivation.
- Event types are tenant scoped and validate references, amendable forms and effective partial configuration before update. The sampled update mutates configuration, but a comprehensive immutable event-type revision snapshot policy is not established.
- Project statuses support ACTIVE/INACTIVE and tenant-scoped updates; status naming uniqueness applies among active entries. Code/ID/tenant/creation timestamp are preserved on update.
- PermissionService supports request cache reset, listing, lookup, user permissions, permission checks and requirePermission. Permission policy dependencies need relational integrity and audit/versioning in V2.

### V2 configuration decisions
- Workflow: immutable published versions; drafts may change, publish produces a new version. Event/workflow execution references the exact version used. Historical published versions cannot be cascaded away while referenced.
- Forms/Information types/columns: logical deactivation only; preserve definition snapshots with event/form response and project information. Deactivation must prevent new use but not invalidate historical payload interpretation.
- Event types and statuses: tenant-scoped IDs, controlled code/name changes, explicit active/inactive/deleted states and effective-version snapshots where event behavior depends on configuration.
- Permission: relational role-permission associations with tenant-compatible uniqueness and audit trail; cache invalidation tied to permission/config changes.
- Configuration deletion policy defaults to RESTRICT when history or active dependent references exist, unless an explicit retention/archive workflow is defined.

G-01 and G-10 remain BLOCKERS; this increment is bounded and does not prove exhaustive dependency coverage or DB-level relationship integrity.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 8 — identity/role/permission relationship constraints (2026-11-22)

Read-only review of UserRoleService, RolePermissionService, RoleService, PermissionService, AccessControlService and related repositories.

Evidence:
- User-role assignment validates user and role tenant match, active role status and duplicate association in sampled service path; association repository lookups are by UserID/RoleID and tenant filtering is largely applied in service.
- Role-permission assignment validates role tenant and permission reference, with tenant-aware association creation in sampled service. Repository-level lookup uniqueness is based on role/permission identifiers; a DB constraint is required to prevent duplicates and cross-tenant association races.
- User, Role and UserRole repositories are simple sheet access adapters; they do not independently enforce tenant-compatible relationship integrity. Role-permission repository/service contains batched locking paths, but these are legacy implementation mechanisms.
- AccessControlService resolves user/roles/permissions and request-scoped caches; cache reset is part of request handling. Permission resolution is distributed through route/service code, so V2 authorization must have a single policy contract while retaining service-level domain validation.

### V2 relational/security decision
- Composite tenant-compatible foreign keys/constraints should prevent associations across tenants, e.g. user-role, role-permission and domain-owner relations.
- Unique constraints should prevent duplicate role assignment and role-permission links, including normalized codes/user identifiers where policy permits.
- Permission cache invalidation should be event-driven or request-scoped only; database state remains authoritative.
- Authorization must deny by default, be centrally evaluated at route boundary, and retain explicit domain/tenant checks inside critical services. Admin bypass must be explicit, auditable and tenant-safe.

G-10 remains BLOCKER until complete entity relationship inventory and composite tenant integrity design are established; G-01 remains BLOCKER pending source coverage.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 9 — attachment metadata, ownership and storage boundary (2026-11-22)

Read-only review of AttachmentService/Repository/Validator, GoogleDriveStorage and StorageRepository.

Evidence:
- Attachment records are entity-linked to PROJECT or EVENT. Service visibility checks TenantID and parent project/event access. File upload validation restricts MIME/extension combinations and attachment type; upload/download/thumbnail/gallery/delete/copy paths are exposed.
- AttachmentRepository is a simple metadata adapter and findByEntity excludes deleted status. Storage is a separate GoogleDriveStorage adapter behind StorageRepository.
- Upload sequence includes storage write followed by metadata/audit; delete sequence may perform storage delete and metadata/status/audit writes. Multi-step recovery/transactional guarantees are not proven; prior audit noted partial failure risk.
- File storage key/Drive ID is used as the physical storage reference. File ownership derives from metadata plus parent entity and tenant, not from a demonstrated storage-level tenant isolation mechanism.
- Attachment ownership, metadata lifecycle and physical object lifecycle must be separate concepts in V2.

### V2 attachment decisions
- Store object metadata in PostgreSQL and object bytes in independent object storage; never expose storage object keys as public authorization tokens.
- Tenant/project/event-compatible relationship constraints must ensure attachments cannot reference cross-tenant or invalid parents.
- Upload should use staged object upload plus transactional metadata and a recoverable state machine/outbox; failure cleanup/retry must be explicit.
- Delete should be logical/retention-aware first, then asynchronous physical purge after policy and dependencies are verified. Audit/retention history remains.
- Enforce size, content type, extension and file-signature checks server-side; allowlist and scan/quarantine policy are defined independently from frontend controls.
- Attachment copy/move must preserve provenance and tenant ownership, and be idempotent.

G-04/G-11/G-10 remain BLOCKERS until file inventory, metadata/data migration coverage, lifecycle policy and tenant relationships are fully documented.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 10 — audit, reports and historical relationships (2026-11-22)

Read-only review of AuditService/Repository, ReportService/Repository, ProjectInformationRepository, EventDataRepository, EventRepository and ProjectRepository.

Evidence:
- Audit records capture tenant, actor, action, entity type/id, old/new data, reason and timestamp (bounded earlier sample). AuditRepository is simple access; append-only/tamper protection and transactional coupling to domain writes are not proven.
- Reports aggregate project, event, attachment, user, department, workflow, event-type and procurement/material planning sources. Report filtering depends on tenant/project visibility in service paths; underlying repositories are generic collection adapters.
- EventData is linked through EventID and central Project Information through ProjectID/InformationTypeID with separate history and reference-sync logs. These relationships have distinct history/retention requirements and cannot be treated as simple cascades.
- ProjectRepository supports generic update/delete; service-layer project deletion uses a dependency collector and confirmation workflow. DB integrity must make bypassing service paths safe.
- Legacy IDs are primarily string-based and cross-referenced by IDs across sheets. No canonical UUID mapping or full duplicate/orphan audit was proven in source sampling.

### V2 decisions
- Audit events should be append-only by application policy and protected from ordinary update/delete; domain mutation and audit insertion must be transactionally coupled. Sensitive payloads should be redacted/minimized.
- Report queries must apply tenant scope at query/data layer and use stable domain definitions; shared calculation logic must avoid divergent dashboard/report quantities.
- Preserve event/form definition snapshots, Project Information history and reference-sync relationships with explicit retention and linked-project policy.
- Migration requires stable legacy-ID mapping and orphan/duplicate detection before foreign-key enforcement; preserve IDs or store mapping table, do not silently regenerate/relink.
- Deletion policy: default restrict for historical/financial/fulfillment dependencies; archive/soft-delete with controlled retention and audit. Avoid broad cascades across shared records.

G-01/G-05/G-09/G-10 remain open. Full source/ID coverage and retention decisions still require completion.

No tests executed, no live data, SQL/DDL, migration, DB provisioning, production modifications or deployment.


### Increment 11 — cross-cutting technical baseline (2026-11-22)

Consolidated the recurring source-audit findings into a baseline for the next discovery/design gate.

#### Numeric and unit integrity
- V2 must store quantities/consumption/costs using exact decimal types, never binary floating-point as authoritative persisted values.
- Each material need, order item, allocation, receipt and adjustment must carry or reference a normalized unit; conversions require versioned conversion definitions and explicit precision/rounding rules.
- Rounding is applied only at documented calculation/display boundaries. Persist calculation inputs, algorithm/version, unit, precision and result so history can be reproduced.
- Legacy ceiling-to-four-decimals observation is not adopted as universal business policy; different quantities/costs require distinct policies.

#### Time and chronology
- Store instants in UTC; retain local date/time intent separately for date-only business fields, planned dates and timezone-sensitive schedules.
- Use tenant/system timezone configuration for display and operational dates, not implicit server timezone.
- Migration must parse legacy timestamp formats, report invalid/ambiguous values, and preserve source values in mapping/validation evidence.

#### Tenant and reference integrity
- For all tenant-owned entities, use tenant-compatible relational constraints for references wherever feasible (composite FK/unique keys), and query-layer tenant isolation as defense in depth.
- Polymorphic relationships (attachments/audit/reference-sync) require explicit validated relation models rather than unconstrained entity-type/entity-id pairs where a relational FK cannot apply.
- Soft-delete/deactivation semantics must be consistent; history-bearing records use restrictive/retention-aware deletion.

#### Readiness status
This baseline is a technical recommendation and does not close G-06/G-07/G-10. Actual legacy data format coverage, business unit conversion policy, timezone field inventory, precision requirements and relation coverage remain discovery items.


### Increment 12 — procurement projection divergence and shared-order visibility audit (2026-11-22)

**Read-only source:** ProcurementService.js lines covering listNeeds, listOrders, workspace, reports and dashboard (bounded source review).

**Verified evidence and risks:**
- Need projection uses a countable-order status filter that excludes CANCELLED, VOID, DELETED and NEEDS_REVIEW; receipt projection and dashboard/order listing use related but not identical filters. A unified V2 order/line/receipt status projection is required.
- Allocation-based received quantity is proportionally distributed across allocations using aggregate item receipts capped at allocation total. This is a legacy projection rule, not evidence that individual receipt records are project-allocated.
- Unallocated order items with a MaterialNeedID subtract SurplusQuantity from ordered quantity for need coverage; items without allocations and items with allocations take different calculation paths. Surplus quantity therefore needs explicit modeled semantics, not a generic receipt adjustment.
- Need projection caps displayed ordered/received quantities against requested need, while preserving OverOrderedQuantity. Dashboard totals are item-based and cap received per item at ordered quantity; report/workspace reuse these projection paths. These caps can conceal raw over-receipt or status inconsistencies in aggregates.
- Order list derives project associations from allocations, then fallback need and order project. It hides an entire order if any associated project is inaccessible. This visibility rule is important for tenant/project security and reporting semantics.
- Receipt records are summed by item without an explicit correction/reversal/status filter in the sampled projection, so any future reversal model must be consistently applied to every consumer.
- Projected order remaining quantity in order view is ordered minus all item receipts (floored at zero), while need and dashboard projections apply additional caps/filters; consumer parity is not established.

**Provisional V2 contract refinements (recommended, pending full review):**
1. Use one authoritative posted-receipt net quantity function, including explicit receipt state and correction/reversal links, shared by needs, order detail, workspace, dashboard and reports.
2. Treat order-level status as a lifecycle state and separately determine whether line receipts are countable; NEEDS_REVIEW must be explicit and reconciliation must not silently discard posted receipt history.
3. Model allocation shares as durable rows with exact decimal quantities and invariant checks; avoid proportional distribution of historical aggregate receipts as the primary accounting model unless the allocation-at-receipt policy is explicitly adopted.
4. Preserve over-order/surplus separately from fulfillment against need, and calculate need coverage from validated allocation/receipt accounting rather than display caps.
5. Retain whole-order hiding when a user's project visibility is incomplete, or redesign the response to avoid leaking inaccessible project/item/receipt labels or totals.
6. Dashboard/report/workspace must consume the same canonical domain projection and reconciliation status, with raw ledger totals available for audit.

**Scope and limitations:** This increment does not prove receipt reversal/correction pathways, every report consumer, race-safe uniqueness, or complete source coverage. G-02 remains BLOCKER; G-01/G-10 and precision/history constraints remain open. No tests executed, live data accessed, SQL/DDL, migration, database provisioning, production changes or deployment.


### Increment 13 — single/batch receipt mutation parity and replay audit (2026-11-22)

**Read-only sources:** ProcurementService.js single receipt, batch receipt, unlocked single receipt paths and Router.js receipt dispatch (bounded).

**Verified source evidence:**
- Router dispatch exposes POST-style receipt paths for single receipt and batch receipt; both reach separate service implementations.
- Single and batch validations differ: batch permits unallocated general-stock receipt for an item with project allocations only up to the unallocated portion; the single receipt path requires a project selection for allocated items and does not surface that general-stock branch.
- Batch receipt idempotency keys are checked against existing receipt keys and duplicate keys within the batch; single receipt checks key replay at wrapper level. Idempotency uniqueness is not demonstrated as a persisted/database constraint.
- Batch source-event replay and single source-event replay behavior differ; the single unlocked path checks existing SourceEventID+item and excludes VOID rows, while batch relies on idempotency-key handling. This is not equivalent.
- Batch receipt validation calculates prior quantities from receipt rows without a status exclusion in the sampled aggregation. Single receipt prior-item/project receipt sums similarly use broad receipt collections; reversal/void accounting parity is unproven.
- Batch inserts the prepared receipts in one repository insertMany call, but order status updates and audit writes occur afterward, across multiple writes; the operation is not shown to be transactionally atomic. Single receipt performs a receipt insert, then rereads and recalculates order totals, updates order, and logs audit; partial completion remains possible.
- Batch preserves NEEDS_REVIEW order status during recomputation; single path updates order status unless NEEDS_REVIEW, but the sampled status and aggregate logic are not identical. Receipt eligibility/replay/reconciliation contract remains divergent.
- No dedicated receipt correction, reversal, or void mutation API was identified in the sampled public service method inventory; this is a bounded finding, not proof of absence from all repository code.

**Recommended V2 decisions:**
1. One receipt command contract for single and batch submissions; batch is a wrapper over validated commands with one consistent policy, not a separate rules implementation.
2. Receipt idempotency scoped by tenant and durable unique constraint; define key requiredness, retention and payload mismatch behavior (same key with different payload must conflict rather than replay silently).
3. Source-event deduplication must be explicitly defined per event/item/tenant and unified across single/batch, including VOID/reversal behavior.
4. Introduce immutable receipt postings plus explicit reversal/correction postings; net accounting derives from these states and lineage, not mutable totals or ambiguous VOID filters.
5. Define allowed receipt destination semantics: project allocation vs unallocated/general stock; implement the same validation for single/batch.
6. Persist receipt command/posting, order-state projection and audit/outbox within one transaction in V2; reconciliation repairs projections from authoritative postings.
7. Model failure/recovery and duplicate-request outcomes explicitly, and test concurrency, partial failure, replay and batch atomicity in V2.

**Limitations:** Source-level bounded review only. No tests executed; no live data, SQL/DDL, migration, database provisioning, production changes or deployment. G-02 remains BLOCKER, and G-01/G-10 remain open.


### Increment 14 — order lifecycle, cancellation and history-retention audit (2026-11-22)

**Read-only sources:** ProcurementService.js createOrder flow, status/countability checks and receipt eligibility/update paths; Router.js procurement mutation dispatch; repository-level code search (bounded) for cancellation API.

**Verified findings:**
- Order creation initializes status ACTIVE. The reviewed service mutation inventory exposes order creation and receipt mutations but no order cancel/update lifecycle mutation in the bounded service excerpt.
- Need/order calculations exclude statuses CANCELLED, VOID, DELETED and NEEDS_REVIEW in some countable-order paths, while other projections have distinct filter rules.
- Receipt creation rejects orders with CANCELLED status, but sampled checks do not consistently reject all non-active states (e.g. NEEDS_REVIEW) at the same validation boundary. Batch and single paths have further differences noted in Increment 13.
- Order creation handles multi-sheet write failure by marking the order NEEDS_REVIEW and appending a recovery note; it intentionally preserves a partial record rather than deleting it. The evidence does not establish a complete recovery/reconciliation operation.
- Cancellation behavior for orders with existing receipts, allocations, supplier commitments, downstream stock movement or audit references was not found in bounded public service/API review. Do not assume cancellation means hard-delete or that receipts can be discarded.
- There is no demonstrated atomic transaction spanning order header, lines, allocations, receipt records, order projection and audit in the legacy Sheets implementation.

**Provisional V2 lifecycle contract recommendations:**
1. Explicit state machine: draft/active, partially received, received, review hold, cancelled (exact states and transitions to be finalized from business discovery); disallow arbitrary status mutation.
2. Cancellation must preserve all history. Define restrictions for any posted receipt; cancellation after receipt requires reconciliation/return/reversal policy and an authorized reason, not deletion.
3. Allocation/need reservation release on cancellation must be an explicit domain action and auditable; avoid silently releasing or double-counting quantities.
4. Validate receipt eligibility against order state and line/allocation state at a single authoritative command boundary; review-hold orders must require controlled resolution.
5. Order status is a projection from authoritative ledger/commands where appropriate, with explicit manual review state and reconciliation events.
6. Persist state-transition records (actor, timestamp, prior/new state, reason, source), and audit them transactionally.
7. Migration must classify legacy statuses and partial-write cases; unresolved records stay quarantined/reviewable, not silently normalized.

### Task 2.6 — audit closure assessment

Task 2.6 source audit and blocker-register consolidation is **AUDIT COMPLETE / DESIGN READINESS NOT APPROVED**. Increment 1–14 findings and recommendations have been documented. The unresolved blockers are not represented as solved: source-field/data coverage, complete relationship/tenant-integrity coverage, receipt lifecycle and reversal semantics, precision/unit policy, time-field inventory, configuration/attachment/audit retention, and validation of assumptions against real but non-production sample data or data dictionary evidence. This audit closure does not authorize physical schema, SQL, migrations or database setup. The next phase must address blocker closure with explicit evidence and product-owner decisions only where business policy is needed.
