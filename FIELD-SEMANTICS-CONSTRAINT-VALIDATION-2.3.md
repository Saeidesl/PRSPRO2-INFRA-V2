# Phase 2 — Task 2.3: Field Semantics & Constraint Validation (Read-only)

**Started:** 2026-11-22  
**Status:** IN PROGRESS  
**Scope:** Validate source-supported field semantics, data representations, nullability/optional behavior, enumerations, relationship rules, mutation invariants and constraint candidates against legacy source code. Record evidence and unresolved items to prevent unsupported SQL decisions.  
**Safety:** Production repositories are READ-ONLY. This task does not inspect live Sheets data, create SQL/schema/migrations, test against production, deploy, or alter operational systems.

## Why this task
Task 2.2 accepted the consolidated mapping package, but deliberately deferred translating source fields into PostgreSQL types, nullability, precise constraints and enforcement choices. Task 2.3 is the evidence-validation layer before a schema-design task.

## Work plan
- [ ] Validate source representation/field semantics by domain and record evidence.
- [ ] Separate required-at-create, optional, nullable, defaulted, derived and historical fields; do not equate validator behavior with database nullability.
- [ ] Identify enum/status/state families and their transition context.
- [ ] Validate tenant-aware relationship/association candidates and polymorphic links.
- [ ] Identify JSON payload/snapshot boundaries, numeric/date precision risks and representation ambiguity.
- [ ] Record verified candidates, unresolved decisions, evidence source and confidence.
- [ ] Conduct consistency review and present findings for Product Owner acceptance.

## Initial source evidence (verified read-only excerpts)

### Event validation
- Event creation validator permits ProjectID to be absent, null or blank; when present/nonblank it must be a string. This is consistent with independent events and means ProjectID cannot be assumed mandatory for all events.
- EventTypeID and PerformedBy are required strings at the validator boundary.
- CreatedBy and DepartmentID are optional string values in the examined create validator.
- OccurredAt is optional; if provided/nonblank it must parse as a valid date.
- Description is optional string; DynamicData is optional object.
- Event update validator permits Status only from ACTIVE/VOID in the inspected path.
- These are API validator observations, not final SQL NOT NULL or database enum conclusions.

### Information type validation
- Allowed data types observed: text, number, date, datetime, boolean, select, textarea, image, file.
- Cardinality values observed: SINGLE and MULTIPLE.
- Validation rule/options payloads are parsed as JSON and structurally validated. Options must be arrays (maximum 500 entries in the inspected validator); rule numeric members and pattern are constrained.
- PROJECT_CODE is explicitly constrained to text + SINGLE.
- ShowOnCard is boolean-validated when supplied.
- These findings imply a configuration-driven field/value model; SQL storage strategy and JSON payload schema remain design decisions.

### Form validation
- Form Name and Code are required on full validation; Code uses a constrained identifier pattern.
- Form Description is capped at 2,000 characters when supplied.
- Field InformationTypeID is required on full validation; FieldOrder must be numeric and non-negative when supplied; IsRequired is boolean.
- VisibilityRuleJSON is JSON/object validated; the observed rule model includes fieldId/operator/value and operators EQUALS, NOT_EQUALS, FILLED, EMPTY.
- Validator checks are API-level and do not independently prove database nullability or uniqueness.

### Event type validation
- ConnectionType observed values: PROJECT and INDEPENDENT.
- AllowOnDraft and IsFormAmendment are boolean-validated; AmendableFormIDs must be an array when supplied.
- INDEPENDENT event types cannot be configured for draft-project registration or variant quantity tracking in the inspected validator.
- TrackVariantQuantity has explicit boolean representation validation.
- Version is numeric-validated when supplied; DepartmentID/WorkflowStepID/TargetDepartmentID are identifier-validated when supplied.
- AllowedDepartmentIDs, AllowedUserIDs and AvailableDepartmentIDs are expected arrays when supplied.
- Exact storage representation, relationship constraints and complete lifecycle transition validation remain pending.

### Material planning (initial source review)
- The inspected service explicitly uses versioned material specification records and immutable calculation-history concepts.
- Its lazy schema helper creates missing sheets and appends missing headers; this is an operational legacy behavior, not a V2 migration recommendation.
- Quantity precision, units, conversion, rounding, status lifecycle and constraints remain unverified in this increment.

## Constraint and type risks surfaced
1. **Optional project link:** Event.ProjectID is conditional; independent event types must remain representable.
2. **JSON/configuration boundaries:** DynamicData, value/definition snapshots, options, visibility rules, validation rules and reference-copy policy payloads require deliberate typed JSON schema and versioning.
3. **Identifier strings:** Legacy IDs are string-based; preserve/mapping policy and tenant-composite relational enforcement need explicit design.
4. **Boolean and numeric normalization:** Validator boundaries sometimes accept boolean-like string representations; migration normalization needs a tested conversion specification.
5. **Date/time:** OccurredAt validation accepts parseable values; distinguish event occurrence time, creation time, update time and timezone semantics.
6. **Precision:** Quantity, percentage, unit price, allocation, receipt and calculation fields must not be mapped to arbitrary numeric precision before domain-specific precision/rounding evidence is established.
7. **Validator vs DB constraint:** Required/optional and enum checks observed in validators are not sufficient to determine database nullability, FK or uniqueness.

## Sources reviewed in this increment
- `PRSPRO2BACKEND-PRODUCTION/DatabaseSetup.js` (read-only; registry/header definitions)
- `EventValidator.js`
- `InformationTypeValidator.js`
- `FormValidator.js`
- `EventTypeValidator.js`
- `MaterialPlanningService.js` (selected excerpt)

## Not yet verified
- Field-by-field complete source mapping across all domains.
- Exact null/blank/undefined normalization at repository persistence boundary.
- Numeric precision, units and rounding rules for production/material/procurement.
- Unique-key scopes, deleted/inactive row semantics and complete FK/cardinality constraints.
- Complete status-transition/state-machine enumerations.
- Migration fixture behavior and legacy data quality (live data expressly excluded).
- Complete service path review and corroborating tests.

## Current checkpoint
**IMPLEMENTED (partial evidence package)** — Task remains IN PROGRESS. This checkpoint is not a finalized constraint matrix or schema approval. No SQL/schema/migration, tests, live-data inspection, deployment, VPS, or production modifications occurred.


## Additional persistence/validator evidence (2026-11-22)

### Generic Validator.js
- `required` treats undefined, null and whitespace-only string as missing.
- `string` checks the value's runtime type but does not itself require a non-null value.
- `number` checks JavaScript numeric runtime type and rejects NaN, but does not accept numeric strings in the inspected helper.
- `boolean` checks runtime boolean type (no coercion in the helper).
- `enum` returns success when value is undefined/null; requiredness must be enforced separately.
- `id` requires non-empty string matching the observed prefix/underscore pattern.
- `object` permits undefined/null, rejects arrays and non-object values.

### Repository persistence boundary
- GoogleSheetsRepository._rowToObject maps header values directly from row cells without type normalization in the inspected function.
- This confirms that API validators and Sheets cell representation are separate layers. Sheets may yield typed cell values (e.g. numeric/date cells) and blank-cell representations; conversion behavior must be validated per read/write path before designing migration normalization.
- No conclusion about all repository writes, date formatting, serialization or database-type equivalence is made from this excerpt.

### Additional caveats
- Validator.js is shared utility behavior, not a complete validator inventory for every domain.
- Numeric-string acceptance/rejection varies across individual domain validators and conversion code; normalize only with source-path evidence.
- A 404 was returned for guessed `ProcurementValidator.js` and `MaterialPlanningValidator.js` paths; this does not imply validators do not exist under other names or locations.


## Further domain-validator evidence (2026-11-22)

### Projects
- The inspected ProjectValidator supports a DRAFT create path with Title required and early return; regular project creation requires ProjectCode, Title and WorkflowID, and AvailableDepartmentIDs is an array with at least one entry (bounded to 100 in the excerpt).
- Quantity is optional but, if present/nonblank, converted and validated as an integer >=1. This illustrates domain-path-dependent requirements; DRAFT and regular project records must not be assumed to share identical requiredness.

### Workflow and step event rules
- Workflow creation requires Name and Code; Version is numeric-validated when supplied. WorkflowType observed values are INITIAL and REPEAT; Status values are ACTIVE/INACTIVE in the validator excerpt.
- WorkflowStepEventRuleValidator observed RequirementMode values REQUIRED, OPTIONAL, REFERENCE, NOT_REQUIRED; DataSource values NEW_EVENT, SOURCE_PROJECT, NONE; SequenceMode values STRICT, FREE, GROUPED. Combinations are validated (e.g. REFERENCE/source-project, NOT_REQUIRED/NONE). A step's rule list is required to be an array and sequence mode consistency is enforced across rules.
- These are configuration validation semantics; the full version-bound uniqueness and relational constraints still require service-level verification.

### Identity/reference domains
- User create validator requires Name, Username and Password (minimum length 8 in the examined excerpt); DepartmentID is optional and ID validated; Status accepts ACTIVE/INACTIVE.
- Role and Department validators require Name and Code; TenantID is optional at validator boundary; Status accepts ACTIVE/INACTIVE. Tenant assignment is expected to be enforced in service/access layers; validator-level optionality does not imply global entities.

### Attachments and audit
- Attachment validator requires EntityType, EntityID, FileName, MimeType and Base64; EntityType accepts PROJECT/EVENT in the excerpt. MIME type and file extension must both match configured allowlists. This upload DTO does not imply Base64 belongs in persisted attachment rows; registry maps storage metadata separately.
- Audit validator requires Action, EntityType and EntityID; UserID, TenantID and Reason are optional at validator boundary. Actor/tenant scope should be enforced by services and audit policy, not inferred solely from DTO requiredness.

### Print template definitions
- PrintTemplateValidator uses JSON definition structure, supports schemaVersion 1, arrays of elements/bindings and page defaults. Page-size values observed include A4, A5, LETTER and CUSTOM; orientation portrait/landscape. Treat definition as versioned JSON, not an untyped permanent blob without validation/version strategy.

### Caveat
These are selected validator excerpts. Service-level authorization, tenant constraints, lifecycle, uniqueness, and all branches were not exhaustively audited in this increment.


## Repository/service semantics validation — next increment (2026-11-22)

### Repository access patterns and integrity candidates
- ProjectRepository exposes lookup by ID and ProjectCode, and a separate existence lookup. These calls establish application-level lookup intent, but no evidence of database-enforced uniqueness was found in this repository wrapper. ProjectCode uniqueness scope (tenant/global/status) remains to be validated in service logic and data policy.
- WorkflowRepository similarly exposes Code lookup/existence. This is an application-level uniqueness candidate, not proof of DB uniqueness or scope.
- EventRepository exposes project/type/actor/department filtering; its ProjectID association is optional in the validator evidence. EventDataRepository.findByEventId uses findOne, implying the service expects one event-data record per event in this read path, but uniqueness and write semantics require further validation.
- AttachmentRepository filters by EntityType + EntityID and excludes DELETED records. A polymorphic association is used; this cannot be expressed as a conventional single FK without a chosen V2 pattern. Attachment status/lifecycle and physical-storage consistency require explicit handling.
- AuditRepository uses entity/user lookups; AuditService serializes OldData/NewData as JSON strings and records ISO timestamps. Audit is tenant-scoped at service access, but append-only enforcement and retention policy are not demonstrated.

### Project-information values and history
- ProjectInformationRepository findAnyByProjectAndType selects a record by ProjectID + InformationTypeID regardless of status; findByProjectAndType additionally requires ACTIVE (with ACTIVE default). This signals potentially multiple/inactive historical rows and active-selection semantics; uniqueness must not be assumed without service lifecycle review.
- History records are linked by ProjectInformationID and sorted by Revision. Approval selection is tenant-filtered and Action='APPROVED' in the inspected service helper. Preserve revision/history/audit distinction.
- Image/file project-information values are attachment references validated against tenant, project, active state, attachment type and MIME; V2 should model attachment IDs/references rather than embedding file bytes in field-value rows (subject to complete mapping).

### Procurement quantity and integrity
- ProcurementService helper converts values using JavaScript Number and validates finite, non-negative/positive quantities depending on allowZero. This is not evidence of decimal precision/rounding policy. PostgreSQL NUMERIC should be evaluated rather than binary floating-point storage for quantities, rates or allocation balances.
- Procurement source has multiple entities (needs/orders/items/allocations/receipts) and tenant-filtered repository access. Receipt/allocation reconciliation and protection against concurrent over-receipt remain transactional integrity requirements, not merely field constraints.

### JSON and temporal representation
- EventDataService parses DataJSON into an object and rejects arrays/non-object shapes and malformed JSON. Dynamic data should have schema/version strategy and preserve historical definition context where needed.
- AuditService serializes old/new snapshots to JSON strings; timestamp is ISO string. V2 should evaluate JSONB and timestamptz, while defining timezone/clock-source policy and preserving source timestamp semantics.

### Evidence limitations
- This is repository/service excerpt review only. Not all write paths, concurrency behavior, service transactions, constraints or tests were inspected. The items above are constraint/design candidates, not approved database decisions.


## Material-planning specification lifecycle evidence (2026-11-22)

- MaterialPlanningService maintains versioned project material specifications keyed by SpecKey and Revision; list resolution selects the latest revision for a key and filters deleted status.
- Create-spec validation requires ItemName and Unit, bounds their length, validates ConsumptionPerUnit as positive (minimum 0.0000001) and WastagePercent in [0,100], with default wastage 0. Initial EarlyOrderPolicy is DISABLED in the inspected create path.
- Create rejects an existing SpecKey within the inspected project/tenant selection; revise requires the supplied record to be the latest revision and rejects deleted/superseded records, indicating revision append semantics rather than in-place mutation for key specification values.
- IDs, generated item codes and timestamps are source-generated; generated ID format should not be copied blindly into PostgreSQL. Uniqueness scopes and revision race protection need service-path review.
- Material calculation history is described as immutable in service header, but its exact full write-path and invariants remain to be audited. No precision/rounding or calculation algorithm assumptions are finalized here.
- These findings support evaluation of decimal precision, immutable revision records, tenant/project-scoped key uniqueness, optimistic/concurrency-safe revision creation, and audit trail requirements, but are not approved constraints.


## Dependency-aware deletion, event transition and role assignment semantics (2026-11-22)

### Project deletion
- ProjectDeletionService performs dependency-aware soft deletion and is admin-gated at service context; project and dependency scope is tenant-filtered.
- Dependency collection traverses events/event data, material needs, purchase-order items, allocations, receipts, specifications and calculations. It deliberately protects order lines or orders still used by another project and avoids cascade-removing shared procurement objects.
- The service comments state a sheet-read/schema failure must not be interpreted as an empty dependency list. This is a critical fail-closed behavior.
- V2 deletion policy must model dependency graph and shared procurement relationships, use transactions, and distinguish logical deletion from file-storage cleanup. Exact cascade/retention policy remains pending business approval.

### Event transition semantics
- EventService uses event-time comparison (OccurredAt falling back to CreatedAt) to inspect later events in other departments, ignores VOID events in that comparison, and prevents/adjusts transition behavior based on configured event types and workflow assignments.
- Draft project events are explicitly treated as information collection only in inspected configured-change logic; lifecycle/workflow transitions wait until explicit completion.
- Event type configuration can set target project status, step status, current step, and available departments. These are service-level transition rules; complete state-machine constraints and all update/delete branches require further review.

### User-role association
- UserRoleService validates both user and role tenant matches, checks role ACTIVE, and checks duplicate assignment before insert. Remove validates tenant and deletes the association with audit logging.
- RolePermissionRepository exposes duplicate lookup and lock-protected bulk insert/delete, but UserRoleService.assign's check-then-insert path as inspected does not show a lock/transaction around the duplicate check. PostgreSQL should enforce unique user-role association in tenant scope and use transactional assignment to avoid races.
- Role/permission assignment and removal are relationship operations and should be represented as constrained association tables, with audit history retained.

### Caveats
- Excerpts are not a complete audit of every service branch, router authorization, repository write implementation or concurrency scenario. No DB constraint is finalized or approved based on these observations alone.


## Additional integrity review: procurement, project information, attachments, event data (2026-11-22)

### Procurement visibility and numeric handling
- ProcurementService includes tenant-scoped user context and validates project linkage/visibility when a project ID is supplied; a missing project ID is treated as independent/shared context in the inspected helper.
- Numeric helper converts values with JavaScript Number and checks finite values plus nonnegative/positive rules. This coercion is broader than strict numeric DTO validation and should be evaluated when designing API compatibility; persisted quantities should use explicit precision/scale and reject unsafe coercions.
- The inspected excerpt was not a full write-path audit; receiving, allocations, status transitions, and aggregate invariants remain pending detailed inspection.

### Project information and approval
- ProjectInformationService validates project visibility within tenant, and information types within tenant. For file/image values, it validates AttachmentID against tenant, project, entity type, attachment type, active status, and MIME policy, returning structured attachment metadata rather than raw file payload.
- Latest approval is selected by approved history and revision order. This indicates approval history is a distinct relationship from current information record. V2 should preserve history, revision and approved snapshot selection rather than overwrite history, subject to further full-path validation.

### Attachment access and copying
- Attachment visibility is checked against tenant, associated project/event and project availability; standalone events may follow separate visibility behavior in the inspected code.
- Project image copy checks source/target tenant, source project visibility, project attachment entity type, image MIME and non-deleted/void status. Copy lifecycle and storage cleanup transaction boundaries remain unverified.
- Attachment's polymorphic entity link (PROJECT/EVENT) and external storage object lifecycle require an explicit V2 integrity model; a conventional single FK cannot directly enforce both entity types.

### Event data historical read path
- EventDataService tenant-checks the event, checks project availability for project-linked events, returns null when event data absent, and rejects malformed/non-object JSON at read time. JSON object validation and optional absence are observed API semantics; database uniqueness/one-to-one remains to verify.

### Confidence boundary
- These are source observations from selected service excerpts, not comprehensive proof of every branch, and they do not authorize finalized schema constraints. Concurrency, transactional consistency and all status paths remain open audit areas.


## Procurement write-path integrity review (2026-11-22)

### Purchase order creation
- createOrder uses a ScriptLock and checks an optional IdempotencyKey within the tenant before proceeding. It validates the nonempty Items array and normalizes all line items/allocation relationships before the initial write.
- Line quantity must be positive; optional unit price is validated nonnegative. Project and material-need tenant/visibility relationships are checked. Per-line allocation sum is bounded by ordered quantity and project/need relationship consistency is validated in inspected path.
- The implementation explicitly notes Google Sheets has no multi-sheet transaction. It writes the order header, then item and allocation records. If downstream writes fail, it marks the order NEEDS_REVIEW and retains partial history rather than deleting it.
- V2 implication: transactionally persist header/items/allocations; retain recoverable status for failures only where an external side effect prevents rollback. Avoid legacy partial-write repair states when a PostgreSQL transaction can roll back the graph.
- Idempotency check is application-level; tenant-scoped uniqueness constraint and transactional insert/upsert semantics are candidates to prevent race duplicates.

### Receipt creation and batch receipts
- Single receipt path uses a ScriptLock, optional IdempotencyKey replay, and an additional SourceEventID + item replay check. It validates tenant/order/item, allocation-to-project, and total received quantity against ordered quantity.
- Batch receipt path takes a ScriptLock, preloads receipt/allocation totals, validates each entry including per-item ordered limits and per-project allocation/unallocated surplus rules, and normalizes the batch before write. It inserts the batch in one Sheets repository call and updates order status based on aggregate received quantities (ACTIVE/PARTIALLY_RECEIVED/RECEIVED), skipping NEEDS_REVIEW orders.
- Receipt creation audits each receipt. These paths implement both overall order-item ceiling and project-allocation ceiling; these invariants should be transactionally enforced in V2, with concurrency-safe locking/serialization where aggregate constraints are involved.
- IdempotencyKey handling is application lookup in inspected paths; uniqueness, key scope, replay semantics and status filters need to be formally specified for V2. Batch path's replay handling and receipt accumulation also need tests for duplicate keys, repeated entries, VOID receipts and project-allocation boundaries.

### Evidence limitation
- This excerpt review does not establish complete procurement status transitions, receipt VOID lifecycle, allocation mutation/delete path, repository uniqueness or all error recovery behavior. These remain pending.


## Procurement allocation lifecycle and test-coverage checkpoint (2026-11-22)

### Allocation behavior supported by inspected source/tests
- ProcurementService createOrder is the observed allocation creation path; no separate allocation mutation/delete or receipt-void service method was found in the inspected service method list.
- Existing allocation rows are used in need aggregation and receipt ceilings; need-key logic unifies legacy estimated and confirmed need rows by MaterialSpecKey and project.
- Inspected tests verify cumulative allocation capped at need quantity and record remainder as surplus. They also verify workspace request reuse/caching and tenant-scoped collections.
- The inspected procurement-service test file contains five named tests: project-visibility bulk-read behavior, workspace collection read reuse, estimated/confirmed need-key equivalence, allocation cap/surplus behavior, and workspace reuse of mapping/context/visibility checks.
- Tests were inspected but not executed during this source-audit increment; test presence is not evidence of runtime pass.

### Lifecycle and integrity gaps
- The inspected ProcurementService method list does not expose allocation update/delete or receipt void methods. This is a bounded finding for this service, not proof that no other router/repository or UI path exists.
- Receipt totals in the reviewed logic include rows from the receipt collection without a comprehensive VOID exclusion in the batch aggregate excerpts; a dedicated VOID lifecycle and reversal policy is not evidenced in the inspected service. V2 must define whether VOID is excluded, how reversals are audited, and whether order status recalculates atomically.
- Legacy allocation and receipt aggregate rules are service-calculated; PostgreSQL transaction locking and constraints must protect aggregate ceilings. A simple row CHECK alone cannot enforce sums across related rows.
- Task 2.3 remains incomplete: route-level auth, all allocation/receipt mutation paths, uniqueness scopes, exhaustive state transitions, and tests for idempotency replay, duplicate batch keys, void/reversal, over-receipt races, and allocation races remain to discover/specify.


## Router authorization and material-planning void/revision checkpoint (2026-11-22)

### Procurement route authorization observed
- Read routes `/procurement/workspace`, `/reports`, `/dashboard`, `/needs`, `/orders` use `requireAny` for procurement/report view permissions as appropriate.
- Write routes observed: POST-like `/procurement/orders`, `/procurement/receipts/batch`, `/procurement/receipts` each require `PROCUREMENT_MANAGE` before service dispatch.
- Route review confirms order creation and receipt registration/batch registration entry points; no receipt void or allocation edit/delete route was identified in the inspected procurement route segment. Scope remains bounded; additional routes may exist elsewhere.

### Material planning revision and event-void behavior
- `reviseSpecification` tenant-scopes current specification, project-checks it, refuses DELETED, and rejects a stale/superseded revision; creates revision logic rather than editing stale revision. SpecKey and latest revision calculations appear service-level, therefore race/uniqueness protection must be database-backed in V2.
- `recalculateProjectNeeds` acquires script lock and validates project, source event tenant/project match, and quantity; uses latest revision per SpecKey and active status.
- `markEventVoided` acquires script lock, marks matching calculation history as `SOURCE_EVENT_VOIDED`, and marks active confirmed material needs from the source event `NEEDS_REVIEW`, adding explanatory notes. This indicates a review/reconciliation state rather than automatic deletion or cancellation of related procurement records.
- `recordCalculation` performs tenant-scoped CalculationKey lookup and returns an idempotent replay if found; however lookup-then-insert remains susceptible to concurrent duplicate creation absent a database unique constraint and atomic write.

### V2 constraints/transaction implications
- Unique key for calculations should be tenant-scoped and enforced in DB, not only lookup logic.
- MaterialSpecKey/SpecKey revision numbering and latest-active semantics require transaction/locking and a unique revision constraint per scope.
- Voiding a source event does not, in the observed method, automatically reverse existing purchase orders or receipts; reconciliation policy must be a documented business decision. The legacy path puts affected confirmed needs into NEEDS_REVIEW.
- Authorization rules need to be modeled consistently in API middleware/service guards and tenant scoping, not assumed from route permissions alone.


## Receipt VOID status handling: additional evidence (2026-11-22)

### Confirmed from inspected receipt write/aggregate code
- `_createReceiptUnlocked_` uses a SourceEventID + PurchaseOrderItemID replay lookup that explicitly ignores receipts with Status `VOID`.
- However, the reviewed project-specific receipt aggregate (`projectReceived`) and item-level aggregate (`prior`) sum QuantityReceived across matching receipt rows without an explicit VOID exclusion.
- The batch receipt path's initial aggregates (`receivedByItem`, `receivedByItemProject`) also include receipt rows as shown, without explicit VOID filtering in the inspected excerpt.
- `listNeeds` receipt aggregation similarly sums receipt QuantityReceived values without explicit status exclusion in the inspected path.
- New receipt rows shown in this path do not set a Status field by default.

### Integrity risk (source-level finding)
If a receipt row is later marked VOID elsewhere, the VOID record may still consume order-level/project-level receiving limits and affect reporting/need aggregation unless those paths normalize or filter it elsewhere. This is a conditional source finding: no VOID mutation path has yet been found, so current operational impact cannot be asserted.
- V2 should define active-receipt predicate explicitly and reuse it across receipt ceilings, project ceilings, order status, need reporting, idempotency, and reconciliation. Reversal audit history should remain retained while void quantity is excluded from operational totals.


## Material specification revision atomicity and event integration boundaries (2026-11-22)

### Revision write order and partial failure
- `reviseSpecification` inserts the new ACTIVE revision first, then updates the previous revision to SUPERSEDED. The comment indicates the new highest revision is authoritative even if marking the old row fails. This is intentionally recoverable in Sheets but can leave multiple rows apparently ACTIVE if the supersede update fails. V2 should transact both changes and enforce unique revision number and one authoritative current revision per scoped SpecKey.
- Revision calculation is read-then-write and not atomic against concurrent requests in the inspected path.

### Calculation record and event integration
- `recordCalculation` performs tenant+CalculationKey lookup then insert, records calculation basis, consumption, wastage, calculated quantity, previous quantity, variance, type and status; lookup+insert has concurrency risk absent DB uniqueness.
- Event integration settings are tenant/event-type scoped and only permit `NONE` or `RECALCULATE_CONFIRMED_NEEDS`; legacy receipt mapping is inert. Quantity must come from one connected central numeric SINGLE information type, with no active columns; event response validation is performed before event insertion in the inspected EventService ordering.
- Event integration's `apply` calls MaterialPlanning recalculation, while receipt APIs remain separate; no automated receipt creation via this integration was evidenced.

### VOID confirmation
- `markEventVoided` updates calculations to `SOURCE_EVENT_VOIDED`, updates active CONFIRMED needs sourced by the event to `NEEDS_REVIEW`, and logs affected counts. It is a review/reconciliation path, not an automatic purchase-order or receipt reversal.


## Procurement lifecycle and repository constraints checkpoint (2026-11-22)

- Inspected ProcurementService method inventory: createOrder, createReceipt, createReceiptsBatch, listNeeds, listOrders, workspace, reports, dashboard; no order update/cancel, receipt void, allocation edit/delete methods observed in this service. This is a bounded source observation, not proof no external mutation exists.
- Router exposes read paths for dashboard/needs/orders and write paths for order creation and receipt single/batch creation guarded by PROCUREMENT_MANAGE. No route for order cancel/update, receipt void, or allocation mutation was observed in the searched Router sections.
- DatabaseSetup headers: PurchaseOrders includes Status and IdempotencyKey; PurchaseOrderItems includes quantities/prices/surplus; allocations include item/project/need/quantity; MaterialReceipts headers omit Status and include IdempotencyKey, SourceEventID and ReceivedAt. Therefore the receipt VOID status previously observed in aggregate filters may be legacy/injected/other-path data rather than a formally declared column; mutation path and schema usage remain unverified.
- MaterialNeed and Calculation rows are scoped by TenantID and associated IDs in inspected methods, but Google Sheets provides no demonstrated relational FK/unique enforcement. V2 should use FK constraints and tenant-aware uniqueness where domain invariants require it.
- No database-level unique constraints were evidenced in inspected DatabaseSetup (sheet headers only). Important candidates for V2 include tenant-scoped order idempotency, receipt idempotency/source-event-item replay keys, calculation keys, material spec revision and active spec-key semantics, and association uniqueness/ceilings. These need discovery and business validation before schema design.
