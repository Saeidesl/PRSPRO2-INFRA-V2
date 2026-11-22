# Task 2.8 — Workflow Version/Event Rule Lifecycle Trace (Increment 62)
Date: 2026-11-22
Status: Bounded READ-ONLY source trace.

## Sources
WorkflowService.js, WorkflowStepEventRuleRepository.js, WorkflowStepEventRuleValidator.js, EventValidator.js and EventTypeService.js from READ-ONLY production reference.

## Observations
- Workflow management/list/get paths filter tenant-owned workflow, step, department and event-type records; workflow versions distinguish draft/published/version context.
- WorkflowStepEventRuleRepository stores tenant, workflow, workflow version, step and event type identifiers plus requirement/sequence/group/display fields and repeat policy.
- cloneWorkflowVersion copies active rules to a target version, and replaceForStepVersion deactivates existing rules for the same tenant/workflow/step/version then appends replacements under a script lock. This write sequence is protected against concurrent sheet edits, but not atomic across workflow-version tables/entities.
- Versioned rule repository queries do not directly tenant-filter in all helper methods; callers must filter. Rule-validator source has validation logic for requirement/group/sequence/display order, but full execution coverage not established.
- Event type configuration changes and workflow draft/publish lifecycle across steps/assignments/rules require reconciliation; source trace does not establish DB-level immutability or historical event snapshot binding.

## V2 implications
1. Workflow, steps, assignments and event rules must be versioned as one aggregate with transactional draft/publish and immutable published revisions.
2. Composite tenant-qualified FK constraints across workflow/version/step/event type and uniqueness rules for group/order semantics.
3. Historical events should reference exact published workflow/event type/step/form version used, rather than mutable current configuration.
4. Publish validation should detect orphaned/inactive rules, incomplete step assignments, invalid event types and sequencing conflicts.
5. Tests: draft clone, publish transaction failure, concurrent edits, tenant crossover, orphan rules, historical event consistency and rule-order/group semantics.

## Evidence limits
Bounded E1 source review only; no runtime tests, live data, DDL, migration, provisioning, production writes or deployment. G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED.
