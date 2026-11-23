# Task 2.8 — Workflow publication lifecycle validation trace — Increment 190

Date: 2026-11-23.

## Source findings
- WorkflowService.publish verifies same-tenant workflow ownership and requires at least one active tenant step.
- It derives publication version from DraftVersion or Version.
- It loads active tenant event types and active rules for the target workflow/version, then validates each rule's event type exists in the active set and its WorkflowStepID corresponds to one of the active steps.
- It updates WorkflowRepository with Version/PublishedVersion, clears DraftVersion, marks LifecycleState PUBLISHED and Status ACTIVE, and snapshots repeat-reference policy into PublishedRepeatReferencePolicyJSON.
- It then emits an audit record for publication and returns the published version.

## Risks / limits
- Repository update and audit logging are sequential; atomicity/failure behavior and publication concurrency are not proven.
- The observed validation loop checks active rule references but this trace did not establish completeness of rule semantics, ordering, cross-step dependency validity, or assignment requirements.
- Step source/repository exact path remains unresolved; no runtime tests or negative-tenant tests performed.
- E1 source evidence only. G-01/G-10 remain blockers and schema readiness NOT APPROVED.

## Safety
Read-only source inspection. No production writes, tests, live data, migrations, DDL, deployment, or cutover.
