# TASK-2.8-WORKFLOW-PUBLISH-VERSION-SCOPE-AND-CONCURRENCY-INCREMENT-92

Date: 2026-11-23
Evidence: E1 source inspection
Reference: PRSPRO2BACKEND-PRODUCTION WorkflowService.js SHA eb8373c14085975a9eb521d5ba20ec2e5146bfad; WorkflowRepository.js SHA 152e0748ec2af06bf268cf04c225b9e3c89831fd; WorkflowStepEventRuleRepository.js SHA 299492048c8d52a5040a4d9ed5ea27cea63983f9
No runtime tests or production changes.

## Publish validation/control observed
- Workflow tenant ownership validated.
- Active steps filtered by tenant and status.
- Draft version resolved from DraftVersion or Version.
- Active event types filtered tenant and status.
- Active rules for workflow/version filtered by tenant and status.
- Rule EventTypeID and WorkflowStepID are checked against active event types and the steps array.
- Published version, repeat-reference policy snapshot, draft cleared, lifecycle/status updated; audit then performed.

## Potential scope issue (requires validation)
The active-step array appears filtered by TenantID and Status but not explicitly by WorkflowVersion, while the active rules are selected for the resolved version. This could make publish validation's step-membership check accept a rule referencing a step from another version if other integrity controls don't reject it. Source inspection alone does not prove this can occur or be exploited. A version-scoped active-step set should be confirmed.

## Concurrency / atomicity
Publish writes workflow state then audit sequentially, with no explicit lock/CAS visible in sampled publish method. Draft cloning and workflow-step-rule replacement use locking in sampled repository paths, but publish itself appears not to coordinate those edits. Concurrent draft mutation/publish and audit failure scenarios remain unverified.

## V2 requirements
- Version-scoped step/rule constraints and FK.
- Publish validation bound to exact draft version and transaction.
- Optimistic concurrency or lock and version hash/check.
- Atomic publish state and audit/outbox.
- Tests: stale rule references, inactive/foreign-version steps, simultaneous edit/publish, audit fault injection, tenant negative tests.

E1 only; no runtime tests. G-01/G-03/G-10 remain open. No production changes.
