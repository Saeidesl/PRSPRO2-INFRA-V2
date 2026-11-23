# TASK-2.8-WORKFLOW-STEP-CREATE-DRAFT-CLONE-AND-UNIQUENESS-INCREMENT-94

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/WorkflowService.js SHA eb8373c14085975a9eb521d5ba20ec2e5146bfad; WorkflowStepRepository.gs.js SHA 49e45437d80c86e44df187b1cea94e85e75114c8; WorkflowStepEventRuleRepository.js SHA 299492048c8d52a5040a4d9ed5ea27cea63983f9
No runtime tests, no production modifications.

## createStep source trace
- Validates workflow tenant ownership and Name/Code.
- Reads existing steps for the workflow; duplicate Code detection scopes TenantID but not WorkflowVersion.
- StepOrder default uses existingSteps.length+1, also across versions.
- Resolves version from DraftVersion or publishedVersion+1 / Version.
- If published without an existing draft, clones event rules from published version into next version, then updates Workflow DraftVersion/LifecycleState to DRAFT, then inserts the new step.
- Step insert follows cloning and workflow state update sequentially.

## Findings / questions
- Global-per-workflow Code uniqueness across versions may be intentional, but it must be confirmed as a business rule. If versioned steps should permit codes reused in separate versions, current duplicate check prevents it. Conversely, if codes are global, DB uniqueness should enforce that rule.
- Default StepOrder is derived from all historical/versioned steps rather than the selected version. Could lead to gaps or unexpected ordering; impact requires validation.
- Failure boundaries: cloning rules, workflow draft state update, step insert are sequential and not visibly atomic; failures may leave draft state or cloned rules without step (or partial cloning). No recovery/fault-injection proof.
- createStep does not visibly use lock/CAS across the clone+draft+step sequence, so concurrent step creation may race.

## V2 requirements
- Decide code uniqueness scope (workflow-global or per-version), enforced by DB.
- Version-specific StepOrder and unique ordering strategy (if applicable).
- Transactional draft creation, rule cloning, draft metadata and step insertion.
- Optimistic concurrency / lock.
- Test failure at every sequence boundary, concurrent creates, clone consistency, code uniqueness and ordering.

E1 only. No runtime tests. G-01/G-03/G-10 remain open.
