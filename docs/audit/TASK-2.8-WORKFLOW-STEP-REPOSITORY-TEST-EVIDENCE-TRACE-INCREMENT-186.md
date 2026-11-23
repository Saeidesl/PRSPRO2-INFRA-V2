# Task 2.8 — Workflow step repository and test-suite evidence — Increment 186

Date: 2026-11-23. Read-only crosscheck of WorkflowStepRepository.gs.js, WorkflowRepository.js and workflow-related test files in legacy backend.

## Observations
- WorkflowStepRepository exposes getAll/findById/findByWorkflowId/findFirstByWorkflowId/findByCode/insert/update; no delete method in the inspected file.
- findByWorkflowId sorts by StepOrder and does not tenant-filter in the repository; service paths filter tenant/version.
- findByCode also relies on the workflow ID selection but is not tenant-aware at repository level.
- WorkflowRepository provides basic CRUD-like getAll/findById/findByCode/existsByCode/insert/update; helper code lookup is global scan semantics.
- Workflow tests exist: tests/workflow-event-rules.test.js and tests/workflow-event-enforcement.test.js.
- Tests include static source assertions, rule-validator behavior in VM, rule/version lifecycle checks, tenant/project scoping assertions, event insert lock/revalidation assertions, and JavaScript syntax parsing for selected files.
- Test files were inspected but NOT executed in this audit environment. Runtime CI status and completeness of negative/transaction/concurrency tests remain unverified.
- Step removal/deactivation semantics and cascade effects remain pending discovery; no delete method found in inspected step repository.

## V2 implications
- Use tenant-aware repository query defaults and composite FK/tenant constraints.
- Explicitly model step lifecycle rather than infer deletion from repository absence.
- Add executable tests covering wrong-tenant IDs, workflow-version boundaries, step retirement/reference consequences, concurrency and rollback.
- Treat static source tests as evidence of guard presence, not runtime correctness.

## Status
G-01/G-10 remain open. Read-only source audit.
