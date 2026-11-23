# TASK-2.8-WORKFLOW-ASSIGNMENT-REPOSITORY-TENANT-AND-UNIQUENESS-INCREMENT-97

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/WorkflowStepDepartmentRepository.js SHA 0fc962c317dc2f9dac18f5a8ec67fab45a3310be; WorkflowStepDepartmentService.js SHA 70bdf874af0a0fcd448d12e38497e7258c849f56
No runtime tests or production changes.

## Repository/service crosscheck
- Repository findByWorkflowId returns rows by WorkflowID only (service filters tenant/status).
- findByDepartmentAndWorkflow searches active rows by DepartmentID + WorkflowID, but no TenantID condition.
- findByStepId filters active by StepID, but no TenantID condition.
- Service assignMany filters tenant and status before building its in-memory assignment map and validates step/department tenant.
- Service remove fetches by ID then checks tenant before soft removal.

## Risk / consistency
- Repository convenience lookup methods are not intrinsically tenant-safe and rely on callers to filter/validate. Their use by other callers requires exhaustive audit.
- Storage uniqueness and tenant-qualified FK/constraint enforcement not demonstrated. Service read-before-write duplicate detection can race.
- Department assignment relationship version scope remains unresolved from Increment 95.

## V2 requirements
- Tenant-qualified lookup interfaces or enforce caller contract centrally.
- Composite FK and uniqueness constraints; likely tenant/workflow/department uniqueness scoped according to resolved version policy.
- Concurrency-safe assignment operations and transaction batch.
- Caller inventory for findByDepartmentAndWorkflow/findByStepId and negative cross-tenant tests.

E1 only. No runtime tests. G-01/G-10 remain open.
