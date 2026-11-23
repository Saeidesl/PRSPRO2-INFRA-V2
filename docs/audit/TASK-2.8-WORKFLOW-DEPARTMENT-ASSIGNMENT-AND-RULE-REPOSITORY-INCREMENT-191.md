# Task 2.8 — Workflow department assignment and event-rule repository audit — Increment 191

Date: 2026-11-23.

## Source files recovered
- WorkflowStepDepartmentService.js
- WorkflowStepDepartmentRepository.js
- WorkflowStepEventRuleRepository.js
- WorkflowService.js saveStepEventRules path

## Verified assignment behavior
- listByWorkflow validates authenticated tenant ownership of workflow and filters active assignments by tenant.
- assignMany limits batches to 100, validates active workflow, active steps and active departments in the authenticated tenant; prevents repeated DepartmentID in a request and rejects assigning an already assigned department to a different step. Identical existing assignments are returned unchanged.
- Batch insertion uses insertMany when available, otherwise sequential inserts. No transaction is evidenced.
- Removal is soft: ACTIVE becomes INACTIVE. Tenant ownership is validated; already inactive rows return as-is.
- Repository findByWorkflowId/findByStepId and findByDepartmentAndWorkflow are raw filtered helpers and do not independently enforce tenant scope; callers must scope. findByDepartmentAndWorkflow also does not filter tenant and is active-status only.

## Verified rule-repository behavior
- WorkflowStepEventRuleRepository defines schema-required columns including TenantID, WorkflowID, WorkflowVersion, WorkflowStepID, EventTypeID and sequencing/grouping fields.
- cloneWorkflowVersion copies active tenant-scoped rules between versions, normalizes AllowRepeat, skips when an active destination version already exists, and inserts copied rows.
- replaceForStepVersion uses ScriptLock with 10-second wait, marks matching active rows inactive, writes replacements, invalidates cache and releases lock. Writes are not demonstrated to be atomic across old-row status replacement and new-row insertion.
- saveStepEventRules validates workflow-step tenant and relationship, max 100 rules, draft version mismatch, normalized rule fields and event type tenant/status/connection. It clones rules on published-to-draft transition, replaces rules, updates workflow draft lifecycle and logs audit sequentially.

## Risk / uncertainty
- Race/concurrent duplicate assignment, partial write recovery, atomic replacement, audit consistency, clone collision/duplicate behavior and constraints are not runtime-tested.
- Potentially stale/invalid references may be detectable only through comprehensive publication and tenant-negative tests. No defect asserted without tests.
- Repository-level helper methods are not tenant-safe by themselves; service callers' scopes require full call-site audit.
- E1 source evidence only; no tests, production changes or live data. G-01/G-10 remain blockers; schema readiness NOT APPROVED.

## Safety
Read-only inspection only; no tests, live data, migrations, DDL, VPS/deployment/cutover or production writes.
