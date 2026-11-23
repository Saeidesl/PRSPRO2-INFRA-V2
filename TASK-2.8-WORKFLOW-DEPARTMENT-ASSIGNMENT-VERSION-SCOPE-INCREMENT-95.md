# TASK-2.8-WORKFLOW-DEPARTMENT-ASSIGNMENT-VERSION-SCOPE-INCREMENT-95

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/WorkflowStepDepartmentService.js SHA 70bdf874af0a0fcd448d12e38497e7258c849f56; WorkflowStepDepartmentRepository.js SHA 0fc962c317dc2f9dac18f5a8ec67fab45a3310be; WorkflowStepEventRuleValidator.js SHA df4693dc0db92033cec2c547706c38b5d0d696d7
No runtime tests or production writes.

## Assignment trace
- listByWorkflow validates workflow TenantID and returns active assignments tenant-scoped.
- assign delegates to assignMany.
- assignMany checks workflow tenant/status, limits input batch size, loads all active steps for tenant/workflow (no explicit WorkflowVersion filter), all active departments, and active assignments.
- Rejects duplicate departments within input and department already assigned to another step; returns unchanged or inserts new assignments.
- InsertMany preferred, fallback sequential insert if repository lacks it.
- remove checks tenant and soft-deactivates assignment.

## Version-scope / consistency risk
Assignment records contain WorkflowID, WorkflowStepID, DepartmentID and TenantID, but no WorkflowVersion field in sampled service payload. Step identity is versioned, yet assignments appear workflow-global references to Step IDs. Whether this is intentional (assignments attached to immutable version-specific step IDs) or means draft assignments can mutate/impact published workflows must be resolved. A version-specific step ID may still implicitly scope assignment, but the business behavior is not established.

## Atomicity/concurrency
Batch validation is read-before-write; no explicit lock/CAS visible in sampled service. Duplicate department and assignment uniqueness under concurrent calls relies on storage constraints not established. Fallback per-row writes are non-atomic.

## V2 requirements
- Clarify whether department assignments belong to versioned steps or workflow-level shared assignments.
- Enforce tenant-qualified FKs and uniqueness constraints (e.g. department per workflow/version, step).
- Transactional batch insert and concurrency controls.
- Test assignment across draft/published versions, concurrent duplicate assignments, and fallback/partial failure.

E1 only. No tests. G-01/G-03/G-10 remain open.
