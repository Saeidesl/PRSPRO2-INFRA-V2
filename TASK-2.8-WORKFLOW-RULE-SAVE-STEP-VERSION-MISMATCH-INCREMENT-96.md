# TASK-2.8-WORKFLOW-RULE-SAVE-STEP-VERSION-MISMATCH-INCREMENT-96

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/WorkflowService.js SHA eb8373c14085975a9eb521d5ba20ec2e5146bfad; WorkflowStepEventRuleRepository.js SHA 299492048c8d52a5040a4d9ed5ea27cea63983f9
No runtime tests, no production changes.

## saveStepEventRules boundary
- validates workflow and step existence, tenant ownership, and step.WorkflowID matches workflow.
- Determines draft target version from workflow metadata and optionally rejects data.WorkflowVersion mismatch.
- Validates event type tenant/status/connection.
- For published workflow without draft, clones prior-version rules.
- Creates rule rows with WorkflowVersion=target version but WorkflowStepID=requested step ID.
- Replaces rules for that step and target version, then updates workflow draft metadata and logs audit sequentially.

## Confirmed source-level version binding omission (runtime effect unverified)
The sampled validation does not explicitly compare the selected step's WorkflowVersion to the resolved target draft version. It validates workflow ownership and relationship, then stamps new rules with target version while retaining stepId. Combined with publish's sampled step membership check against the tenant/status-filtered steps list without explicit version filter, this creates a credible cross-version step-reference inconsistency. Runtime effect, reachability and any external safeguards remain unverified.

## Atomicity
Clone, rule replacement, workflow metadata patch and audit are sequential; rule replacement itself locks its sheet operation, but the encompassing flow is not shown as transactional. Clone/replace or metadata/audit failure recovery remains unproven.

## V2 controls/tests
- Require exact step WorkflowVersion == target version in save API.
- Composite FK rule (TenantID, WorkflowID, WorkflowVersion, WorkflowStepID) to versioned step.
- Transactional clone/replacement/draft metadata/audit/outbox.
- Test stale-step IDs, published-step IDs used in draft save, tenant negatives, concurrent draft saves, partial failures and publish afterward.

E1 only; no tests. G-01/G-03/G-10 remain open.
