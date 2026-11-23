# TASK-2.8-WORKFLOW-PUBLISH-SCOPE-CHECK-REFINEMENT-INCREMENT-93

Date: 2026-11-23
Evidence: E1 source inspection
References: WorkflowService.js SHA eb8373c14085975a9eb521d5ba20ec2e5146bfad; WorkflowStepEventRuleRepository.js SHA 299492048c8d52a5040a4d9ed5ea27cea63983f9
No runtime tests or production changes.

## Refinement
- Rule repository query `findByWorkflowVersion` scopes WorkflowID and WorkflowVersion; tenant filtering is applied by service callers.
- Rule replacement `replaceForStepVersion` locks the sheet operation, deactivates existing active rows for the matching TenantID/WorkflowID/StepID/Version, and appends replacements.
- Publish path still appears to validate active rules of the target version against a step list filtered by TenantID and active status, with no explicit WorkflowVersion filter in that local step filter.
- Service get/list display logic uses version filtering or upper-bound semantics in sampled methods, suggesting the publish-validation filter deserves specific verification rather than broad assumptions.

## Conclusion
The suspected version-scope mismatch is a narrow, credible verification target, but remains unconfirmed. Step-repository implementation could not be fetched under the guessed path, so no claim about storage-level constraints is made. Publish still appears sequential (workflow update then audit) and no lock/CAS is visible in the inspected method.

## V2 tests/controls
- Test publish with stale-version/foreign-version step IDs and cross-tenant IDs.
- Exact-version filtered step/rule registry.
- DB foreign keys composite (tenant, workflow, version, step).
- transactional publish and audit/outbox; concurrency control.

E1 only; no tests; G-01/G-03/G-10 open; production unchanged.
