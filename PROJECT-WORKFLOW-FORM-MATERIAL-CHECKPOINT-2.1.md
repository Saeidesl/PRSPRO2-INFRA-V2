# Phase 2 / Task 2.1 — Project, workflow-version, form, material-planning source checkpoint

Date: 2026-11-22
Source: targeted read-only inspection of ProjectService.js, WorkflowService.js, FormService.js and MaterialPlanningService.js in PRSPRO2BACKEND-PRODUCTION.

## Project creation and reference-project behavior (observed)
- Creation validates workflow publication/version and captures the workflow version selected for the project.
- Repeat/reference projects require a source project and published workflow reference policy. The source cannot be the same project.
- Reference policy values observed include LIVE_SYNC, INITIAL_COPY and INDEPENDENT, tied to information types.
- Creation validates allowed departments and users and validates reference-policy information-type mappings.
- The creation flow uses a script lock in the inspected path.
- Reference-copy plan and status fields are persisted and a plan summary is returned in the inspected path.
- Source information and attachment copy plans are tenant/access scoped; exact recovery semantics for each copy type remain to audit.

## Workflow draft/version behavior (observed)
- When editing steps/rules of a published workflow without an existing draft version, the inspected paths create a new draft version and clone published rules.
- Rule edits are tied to a workflow version and validated against the current draft version.
- Workflow management selects draft or published version depending on state.
- This implies immutable published configuration and explicit draft/publish separation should be modelled in V2, while exact cloning of steps, department assignments and all configuration remains under validation.

## Form-definition behavior (observed)
- Forms and fields are soft deleted in inspected mutation paths.
- Changes to forms/fields increment form Version and timestamps.
- Field validation includes active information types, multiple-cardinality types and visibility dependencies; deleting or reordering fields is guarded against breaking dependent fields.
- Event form submission checks connected forms, requiredness, submitted form version, allowed fields and visibility. Hidden fields cannot carry submitted values.
- Event responses store response JSON and a definition snapshot; form amendment is a distinct path.
- Version/snapshot retention and transactional guarantees remain to audit.

## Material planning (observed)
- Material specifications are versioned; prior versions become SUPERSEDED in the inspected update path, and stale revision updates are rejected.
- Calculations are recorded as calculation history and are linked to source events and specification revisions.
- Calculated material needs are reconciled with existing needs; prior affected needs can be superseded.
- Event void handling marks related calculation rows SOURCE_EVENT_VOIDED and affected material needs for reconciliation.
- Legacy material needs are intentionally not assigned invented consumption rates (source comment).
- V2 should preserve calculation provenance, spec revisions, event source, need history and explicit reconciliation state; avoid silent recalculation or hard deletion.

## Provisional modelling implications
1. Projects should retain workflow-version references and model optional source/reference relationships with explicit policy.
2. Workflow configurations should be versioned and drafts separate from published immutable versions.
3. Forms should preserve definition versions/snapshots; submission should validate against a specific version.
4. Material specs/calculations/needs should preserve provenance and revision history.
5. Copy and reconciliation operations should be transactionally reliable, idempotent and observable in V2.

## Pending validation
- Complete repeat-project copy lifecycle and copy failure recovery, including attachments.
- Workflow cloning scope (steps, department mappings, rules, repeat policy, permissions) and publish transition.
- Form snapshots, response persistence, field visibility and amendment permission/eligibility paths.
- Material calculation algorithm, quantity rounding, surplus/allocations, and reconciliation resolution workflow.
- Actual test coverage and performance baselines.

No SQL/schema, migrations, tests, deployment or production data access were performed.