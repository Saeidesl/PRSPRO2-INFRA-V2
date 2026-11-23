# Task 2.8 — EventType workflow-step reference integrity crosscheck — Increment 185

Date: 2026-11-23. Read-only trace of EventTypeService reference validation and EventTypeRepository helper/header.

## Observations
- EventType header contains TenantID, DepartmentID, WorkflowStepID, and target/allowed department/user fields (confirmed in bounded DatabaseSetup header).
- EventTypeService validates WorkflowStepID existence and step TenantID in sampled reference validator.
- The bounded validator does not visibly check that referenced step belongs to the EventType's selected WorkflowID, where a workflow context is otherwise applicable. Current header excerpt doesn't establish a WorkflowID field on EventType itself, so semantics of WorkflowStepID must be discovered rather than assumed.
- Department and target department refs validate same tenant; permitted users/departments are validated in subsequent bounded code.
- Repository helper findByWorkflowStepId uses global lookup semantics; code relies on service validation/tenant filtering.
- No tenant-qualified FK, relationship uniqueness, or lifecycle behavior when a step is retired is established.
- This is a discovery gap, not a proven security defect.

## V2 implications
- Establish exact EventType-to-step relationship meaning and whether step belongs to a specific workflow or is a global tenant-scoped step reference.
- Use composite tenant-safe FK and validate lifecycle/status semantics.
- Define behavior for event types whose referenced step is inactive/removed or workflow version changes.
- Add negative tests for cross-tenant and wrong-workflow step references.

## Status
G-01/G-10 remain open. Source-only.
