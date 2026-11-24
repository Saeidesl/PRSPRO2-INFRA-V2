# Task 2.8 Increment 221 — Print-template project-data authorization trace

Date: 2026-11-24

## Scope and evidence
A targeted read-only re-fetch of the production backend Router.js and PrintTemplateService.js examined the PUT route for `/print-templates/:id/project-data`.

## Findings (source-only)
- Router.js dispatches the matching path directly to `PrintTemplateService.updateProjectBinding(...)` without a route-level AccessControlService.require call in that branch.
- The service obtains the current user, scopes the template lookup to that user's TenantID, rejects inactive templates, resolves project data and validates the binding and source freshness.
- For PROJECT_FIELD and PROJECT_INFORMATION writes, the service requires PROJECT_EDIT before applying the update. EVENT_FORM_FIELD and EVENT_FIELD paths require EVENT_EDIT. The service therefore contains source-type-specific authorization gates, rather than a missing authorization gate established by this Router observation.
- The route/service design is nevertheless a centralization/fail-closed consistency candidate: route-level authorization is absent, while service-level checks determine the permission based on binding type. This is not classified as a confirmed vulnerability.
- Authorization/resource checks, tenant-negative cases and source freshness/conflict behavior have not been executed in a safe test environment.

## Evidence grading and disposition
Evidence: E1 (direct source retrieval) for Router branch and service authorization paths. Runtime evidence: none. Security disposition: PARTIAL / review candidate, not a defect finding.

## Risks and follow-up
- Add API contract and permission-negative tests in V2 for each binding source type.
- Ensure binding source type cannot bypass required resource/tenant checks and stale-source protections.
- V2 should centralize route authentication and authorization policy while retaining resource-specific service checks.

## Safety
READ-ONLY source inspection only. No production modifications, tests, data access, SQL/DDL, migration, provisioning, deployment or release action. G-01/G-10 remain OPEN; schema readiness NOT APPROVED.
