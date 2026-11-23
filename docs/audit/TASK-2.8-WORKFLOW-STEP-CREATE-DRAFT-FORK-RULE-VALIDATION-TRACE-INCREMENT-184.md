# Task 2.8 — Workflow step creation, draft fork and rule-save validation crosscheck — Increment 184

Date: 2026-11-23. Read-only focused trace of WorkflowService createStep/getStepEventRules/saveStepEventRules.

## Observations
- createStep validates workflow existence and tenant, required name/code and duplicate step Code within workflow/tenant (source uses a scan).
- If workflow is published and no draft version exists, it clones published rules to the new draft version and patches workflow draft/lifecycle state before inserting the new step; operations are sequential and not transactionally evidenced.
- Step order defaults using existingSteps.length+1, with existingSteps loaded by workflow ID and duplicate code scan tenant-filtered.
- get/save step event rules check both workflow and step tenant, and step.WorkflowID matching supplied workflow.
- save rules caps at 100, validates version consistency and event type tenant/status/connection type, clones published rule version when needed, replaces records under repository lock, then patches draft lifecycle and audit sequentially.
- The bounded trace does not establish unique constraints for step code/version/order or a tenant-qualified FK between step/rule/workflow. Concurrency duplicate prevention and recovery across clone/state/step insert remain unverified.
- Step update/delete/retire semantics were not located in bounded source; absence is not concluded.

## V2 implications
- Transactionally create/fork draft, clone rules, insert step and update workflow state.
- Tenant-consistent composite FK and version-qualified constraints; duplicate-code and ordering semantics require business validation.
- Document explicit step edit/deactivate/delete and historical published-version policies.
- Tests: cross-tenant step/rule IDs, concurrent duplicate step code, publish/draft fork atomicity, rule replacement rollback, idempotent retries.

## Status
G-01/G-10 remain open; source-only.
