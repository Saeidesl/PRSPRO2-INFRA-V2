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
