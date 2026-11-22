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
