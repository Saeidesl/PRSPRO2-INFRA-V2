# Task 2.8 — Workflow Service Publish and Rule Validation Trace (Increment 102)

Date: 2026-11-23  
Scope: READ-ONLY review of the legacy production backend WorkflowService.js  
Evidence level: E1 — source observation only; no runtime verification

## Files inspected
- `Saeidesl/PRSPRO2BACKEND-PRODUCTION/WorkflowService.js` — methods `createStep`, `getStepEventRules`, `saveStepEventRules`, and `publish`.

## Observations

### 1. Step creation
- Validates required Name and Code and checks the Workflow tenant.
- Duplicate Code check searches the steps associated with the workflow, but the uniqueness guarantee is application-level; no storage constraint or concurrent-create protection was established.
- When the workflow is published and no draft version exists, cloning rules, updating workflow draft state, and inserting the new step are sequential operations.
- StepOrder defaults to the count of existing steps plus one, not a proven concurrency-safe sequence.

### 2. Rule retrieval
- Checks workflow and step tenant and parent workflow relationship.
- Version is derived from request parameters or workflow draft/published/version fallback.
- Repository lookup is by step and requested version, then tenant and active-status filtering. This review did not establish a database-level composite integrity constraint.

### 3. Rule save
- Validates submitted WorkflowVersion against the computed draft version.
- Validates active, tenant-owned, non-independent event types.
- The selected step is checked for tenant and WorkflowID, but visible code does not explicitly assert that the step's WorkflowVersion equals the target draft version. Rule records are stamped with the target version.
- If a published workflow has no draft, the method clones published rules; then performs replacement, workflow patch, and audit sequentially. Atomic rollback/recovery is not demonstrated.

### 4. Publish
- Checks workflow tenant and requires at least one active step.
- Loads active rules for the target version and checks event-type validity and membership of each rule's step ID in the active steps set.
- Writes publication/version/status state then logs audit in a separate sequential operation.
- A lock/CAS guard against concurrent publish/draft mutations is not proven by this inspected method.

## V2 implications
- Model workflow version as a relational aggregate with composite tenant/workflow/version integrity.
- Enforce step-code uniqueness and assignment/rule integrity at database level, with explicit version membership.
- Make draft cloning, rule replacement, step mutations, publish state, and audit/outbox coupling transactional or recoverably orchestrated.
- Define concurrency control (optimistic revision/CAS or aggregate lock) and deterministic StepOrder behavior.
- Add tests for mismatched step-version saves, duplicate concurrent creation, failed clone/insert/replace/audit, concurrent publish, and invalid/inactive step/event references.

## Limitations
- Source review only. No tests were run, no runtime exploit or defect is claimed.
- No production repository changes, live data access, SQL/DDL, migration, or deployment.
- G-01, G-03 and G-10 remain open; schema readiness is NOT APPROVED.
