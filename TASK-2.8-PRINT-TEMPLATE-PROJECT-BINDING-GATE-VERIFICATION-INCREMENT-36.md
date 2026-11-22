# Task 2.8 — Print Template Project-Binding Route Gate Verification Increment 36
Date: 2026-11-22
Scope: Follow-up to potential route gate gap noted in Increment 35. Read-only service trace.

## Finding
Router PUT `/print-templates/{id}/project-data` dispatches `PrintTemplateService.updateProjectBinding` without an explicit Router-level permission gate in the sampled branch. Service-level authorization is present and source-type specific:
- PROJECT_FIELD: requires PROJECT_EDIT before ProjectService.updateFromTemplate.
- PROJECT_INFORMATION: requires PROJECT_EDIT before ProjectInformationService.saveWithLock.
- EVENT_FORM_FIELD: requires EVENT_EDIT before FormService.updateEventFormFieldFromTemplate.
- EVENT_FIELD: requires EVENT_EDIT before EventService.updateFieldFromTemplate.
- Other source types rejected.

## Additional safeguards observed
- Tenant-scoped template lookup via current user's TenantID.
- Active template status requirement.
- Binding ID must match definition.
- Only LATEST selection allowed.
- Resolve project/source and compare ExpectedSource; stale source rejected with conflict.
- Mutation delegated to regular source services after specific permission checks.

## Assessment
This is not a confirmed authorization vulnerability based on inspected source; the service enforces source-specific permissions and tenant-scoped template lookup. Router-level gate omission is a defense-in-depth/route matrix note. Runtime tests, full resolver tenant/project validation and concurrency behavior remain unverified.

## V2
Implement explicit API authorization policy and service authorization where appropriate, tenant-qualified queries and optimistic concurrency. Route tests should verify unauthorized source types and cross-tenant template/project rejection.

## Limits
Read-only source review; no tests executed. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production changes.
