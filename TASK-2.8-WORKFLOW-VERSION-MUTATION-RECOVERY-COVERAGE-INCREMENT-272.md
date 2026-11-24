# Task 2.8 — Workflow Version Mutation/Publication Recovery Coverage (Increment 272)

## Source trace
WorkflowService.createStep and saveStepEventRules clone published rule versions and transition workflows to draft; saveStepEventRules replaces rules and then updates workflow draft state and audit. publish validates active steps/rules and tenant/event type references, updates published version/lifecycle, then audit. These steps are sequential; transaction/recovery is not demonstrated.

## Selected tests
`tests/workflow-event-rules.test.js` covers additive/version-aware schema, tenant/permission, validator compatibility, published version lookup/assignment and free display order. `tests/workflow-event-enforcement.test.js` covers workflow enforcement, scoped progress, snapshots/repeatable rules, manual completion and event insert revalidation under shared lock. Selected tests do not establish comprehensive publication/create-step/rule-replacement/audit failure injection or concurrency.

## Test blueprint
- WF-01 Create step on published workflow: rule-version clone failure.
- WF-02 Workflow draft-state update failure after clone.
- WF-03 Step insert failure after draft/version mutations.
- WF-04 Duplicate code, tenant mismatch and published/draft status edge behavior.
- WF-05 saveStepEventRules validation rejects invalid event types/rules before mutation.
- WF-06 Clone rules failure; workflow state remains consistent.
- WF-07 Rule replacement succeeds, workflow DraftVersion/Lifecycle update fails.
- WF-08 Audit failure after rule/workflow writes.
- WF-09 Publish rejects invalid/deleted/cross-tenant rule references.
- WF-10 Publish workflow update succeeds but audit fails.
- WF-11 Publish idempotent replay and duplicate publication behavior.
- WF-12 Published snapshot remains stable while draft edits are in progress.
- WF-13 concurrent edits/publication and version conflict.
- WF-14 Lock/transaction/retry consistency for workflow and rule version rows.
- WF-15 Workflow assignment to project must use published version only; history unaffected by later draft edits.

## V2 requirements
Publish should be transactional across workflow version snapshot, step/rule rows, audit. Use immutable published versions, DB constraints, explicit draft-version concurrency control and durable operation IDs. Publish/retry returns stable result.

## Verification
Selected source/test review only; no runtime tests or failure injection. Production references remain read-only.
