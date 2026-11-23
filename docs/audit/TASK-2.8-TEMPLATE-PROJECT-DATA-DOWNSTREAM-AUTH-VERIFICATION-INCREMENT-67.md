# Task 2.8 — Downstream authorization verification for template project-data PUT (Increment 67)

Date: 2026-11-23
Source: Read-only review of PrintTemplateService.js, ProjectService.js, EventService.js and FormService.js in the production backend.

## Finding
The Router PUT branch for /print-templates/{id}/project-data delegates to PrintTemplateService.updateProjectBinding. That service:
- obtains the authenticated user;
- validates the template belongs to the user's TenantID and is not deleted;
- requires ACTIVE status;
- resolves the project and verifies tenant and user availability;
- checks binding ID, latest-source selection, and source consistency (ExpectedSource);
- dispatches by source type and performs explicit permission checks:
  - PROJECT_FIELD and PROJECT_INFORMATION require PROJECT_EDIT;
  - EVENT_FORM_FIELD and EVENT_FIELD require EVENT_EDIT.

The delegated handlers further enforce tenant/project availability, field allow-lists, locking/version checks and relevant event restrictions (source-level observations). Therefore the missing explicit Router-level guard on this branch is not, based on the reviewed downstream code, evidence of an authorization bypass. It is a defense-in-depth observation, not a confirmed vulnerability.

## Evidence boundaries
This review is limited to the identified methods. It is not runtime testing, and it does not validate every authorization edge, service method, repository, or caller. No production changes or data access were performed.
