# TASK-2.8-WORKFLOW-UPDATE-MASS-ASSIGNMENT-OF-MUTABLE-FIELDS-INCREMENT-98

Date: 2026-11-23
Evidence: E1 source inspection
Source: PRSPRO2BACKEND-PRODUCTION/WorkflowService.js SHA eb8373c14085975a9eb521d5ba20ec2e5146bfad; WorkflowValidator.js SHA 8e1bcbc5edf08ed1252ef8b0eaa2200624c73c1e
No runtime tests or production changes.

## Workflow update service trace
- validates workflow ID, update payload, workflow existence and tenant ownership.
- duplicate workflow Code checked within tenant if changed.
- repeat-reference policy and WorkflowType validated when supplied.
- generic loop copies data properties into updateData except ID, TenantID, CreatedAt, RepeatReferencePolicyJSON and PublishedRepeatReferencePolicyJSON.
- Thus fields such as Status, Version, PublishedVersion, DraftVersion, LifecycleState (and other unlisted fields) are not explicitly excluded by the generic loop in the sampled service.
- service writes updateData to WorkflowRepository directly.

## Potential control boundary
This creates a credible service-layer mass-assignment concern for lifecycle/version metadata. Whether public routes allow these fields, or authorization/route validation strips them, remains pending route-level trace. Not a confirmed exploitable vulnerability.

## V2 controls
- Explicit allowlist of mutable business fields.
- lifecycle/version state changes via dedicated transition services only.
- DB/API validation and permission tests for protected fields; tenant immutable.
- Negative tests: lifecycle, published/draft versions, tenant, audit fields, status bypass and unexpected fields.
- Record route and permission crosswalk.

E1 only, no runtime tests. G-01/G-10 and lifecycle integrity G-03 remain open.
