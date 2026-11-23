# TASK-2.8-WORKFLOW-SHEET-RELATIONSHIP-AND-KEY-CONSTRAINT-CROSSWALK-INCREMENT-100

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/DatabaseSetup.js SHA f8e341a5c220105792eab030ea5de920c9921320
No runtime tests, no production modifications.

## Schema/header crosswalk
- 09_Workflows headers include ID, TenantID, Version, Status, LifecycleState, PublishedVersion, DraftVersion and repeat-reference policies.
- 10_WorkflowSteps includes WorkflowID and WorkflowVersion.
- 17_WorkflowStepDepartments includes TenantID, WorkflowID, WorkflowStepID and DepartmentID, but no explicit WorkflowVersion.
- 37_WorkflowStepEventRules includes TenantID, WorkflowID, WorkflowVersion, WorkflowStepID and EventTypeID.

## Integrity observation
The Sheets schema/header layer expresses the version relationship on Rule rows, while Department assignments point to versioned Step IDs without a dedicated version column. Headers alone establish neither foreign keys nor unique constraints. Enforcement and integrity are application-level and may be distributed; no database-equivalent relationship enforcement proven in this sampled source.

## V2 design crosswalk requirements
- Workflow PK and tenant ownership.
- Steps FK to workflow, tenant, exact version; uniqueness policy for Code and StepOrder documented.
- Rules composite FK to exact versioned Step; tenant-qualified event type FK.
- Department assignments composite FK to exact Step and tenant; explicit version policy (direct or derived) decided.
- Unique constraints for all defined uniqueness rules and atomic operations.

E1 only. No tests. G-01/G-10 remain open.
