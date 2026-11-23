# Task 2.8 — Workflow service source trace recovery — Increment 189

Date: 2026-11-23.

## Source access recovered
- Exact-path read-only retrieval succeeded for root-level `WorkflowService.js`, `WorkflowRepository.js`, and `EventTypeService.js` in `Saeidesl/PRSPRO2BACKEND-PRODUCTION`.
- The earlier checkpoint's claim that WorkflowService exact-path retrieval failed is superseded: it was resolved during this increment. WorkflowStepRepository path remains unresolved.

## Verified WorkflowService findings (source-only)
- Workflow management bootstrap scopes workflow, department, active PROJECT-connected event-type, and active information-type selections to the authenticated user's tenant.
- Management-data reads validate workflow ownership, selects draft/published/version fallback, filters step rows by tenant and version, and active department assignments to visible steps.
- Workflow list/get reads tenant-filtered data. Workflow get and getSteps similarly select steps up to the requested version.
- Workflow creation validates repeat-reference policy against active tenant information types and allowed policy values; checks tenant-scoped duplicate workflow Code; creates tenant-bound ID, DRAFT lifecycle, DraftVersion and initial version.
- Workflow update verifies tenant ownership and tenant-scoped duplicate Code; validates workflow type and repeat-reference policy. TenantID is excluded from mutable input. A sequential repository update and timestamps are used.
- Step creation validates name/code, parent workflow tenant ownership, and tenant-scoped duplicate step Code. It derives step order and version; when creating a draft from a published workflow, it attempts to clone workflow-version event rules and advances workflow draft state before step insertion. These are sequential sheet operations, not a proven atomic transaction.
- Step event-rule retrieval and saving validate parent workflow and step tenant/link ownership, derive requested draft version, constrain rule count, validate referenced active tenant event types and PROJECT connection, and clone rule version when necessary.

## Limits and open questions
- WorkflowStepRepository source, full workflow deletion/update-step behavior, department-assignment implementation, and version clone completeness remain unresolved.
- This is source evidence (E1), not runtime evidence; no tests were executed.
- Concurrency, partial-failure recovery, tenant-negative tests and relational constraints remain unverified.
- G-01/G-10 remain blockers; G-02/G-03/G-04 and G-05 through G-12 remain open or policy-dependent as recorded. Schema readiness NOT APPROVED.

## Safety
Read-only access only. No production repository writes, test execution, live data, migrations, DDL, VPS/provisioning, deployment, or cutover.
