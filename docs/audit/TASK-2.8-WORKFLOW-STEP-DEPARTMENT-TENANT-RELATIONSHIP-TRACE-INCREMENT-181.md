# Task 2.8 — Workflow-step department assignment relationship trace — Increment 181

Date: 2026-11-23. Read-only source trace of WorkflowStepDepartmentService and Repository, plus WorkflowService management reads.

## Observations
- Assignment service first tenant-checks workflow and active state.
- It collects active workflow steps and departments constrained by TenantID and workflow, then validates each submitted Step and Department against those lookup maps.
- Existing assignments are tenant/status filtered and keyed by DepartmentID. Duplicate departments in a batch are rejected; an existing department mapped to a different step blocks reassignment unless the old assignment is removed first.
- New assignment rows contain TenantID, WorkflowID, WorkflowStepID, DepartmentID, status and timestamps; insertMany is used when available, with sequential insert fallback.
- Removal is soft: status becomes INACTIVE. The repository lookup methods themselves are ID/workflow-based and do not tenant-scope; the service applies filtering/checks.
- No database FK/unique constraint, transaction guarantees for batch insert, or consistency constraint linking TenantID across workflow/step/department are evidenced.
- Workflow management read path filters steps and assignments by tenant and version context.

## V2 implications
- Relational constraints should enforce tenant-consistent references from assignment to workflow, step and department.
- Consider unique active mapping rules (e.g., department/workflow) only after validating legacy semantics and workflow-version policy.
- Batch assignment should be transactional; soft-deactivation/reassignment history policy should be explicit.
- Direct repository/service bypass and negative cross-tenant tests are needed.

## Status
G-01/G-10 remain open. Source-only; no tests/live data.
