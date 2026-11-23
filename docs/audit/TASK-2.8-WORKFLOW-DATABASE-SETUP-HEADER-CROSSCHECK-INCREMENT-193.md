# Task 2.8 — Workflow database setup/header and validation crosscheck — Increment 193

Date: 2026-11-23.

## Header evidence
Read-only DatabaseSetup.js crosscheck confirms:
- `09_Workflows`: ID/TenantID/name/code/version/status plus WorkflowType, LifecycleState, PublishedVersion, DraftVersion and repeat-reference policy snapshots.
- `10_WorkflowSteps`: ID/TenantID/WorkflowID/name/code/StepOrder/status/timestamps/WorkflowVersion.
- `17_WorkflowStepDepartments`: ID/TenantID/WorkflowID/WorkflowStepID/DepartmentID/status/timestamps.
- `37_WorkflowStepEventRules`: tenant/workflow/version/step/event-type IDs, requirement/data-source/sequence/group/order fields, status/timestamps and AllowRepeat.
- Status list validations exist in setup code for workflow and workflow-step sheets; assignment sheet validation includes ACTIVE/INACTIVE/DELETED.

## Cross-source consistency notes
- Header names match the reviewed repository field access and the WorkflowStepEventRuleRepository required-column manifest for the fields examined.
- Header manifest presence is not proof of relational integrity, unique constraints, cascade behavior, tenant-composite constraints or enforced FK relationships in Google Sheets.
- Assignment validation includes DELETED in setup dropdown, while service removal currently writes INACTIVE; source comments also indicate soft-removal compatibility. This is recorded as a status-policy consistency question, not a confirmed defect.
- Step status validation allows ACTIVE/INACTIVE, but direct repository implementation remains inaccessible; the service and header evidence do not prove repository write/validation semantics.

## Limits
No tests, live data or production writes. E1 only. G-01/G-10 remain blockers; schema readiness NOT APPROVED.
