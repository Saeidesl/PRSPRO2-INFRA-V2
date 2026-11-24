# Task 2.8 — Router mutation/read route authorization trace (Increment 283)

Date: 2026-11-24
Production backend snapshot: 8253eb3718d1a2a1bd5de03b6b5090ba69b94b67
Scope: static READ-ONLY review of Router.js GET/POST/PUT/DELETE dispatch and selected contract tests. No production repository writes or runtime actions.

## Evidence reviewed
- Router.js full dispatch paths (GET, POST, PUT, DELETE) at the pinned snapshot.
- AccessControlService.js and PermissionService.js.
- tests/schema-contract.test.js, tests/forms-api-contract.test.js, tests/workflow-event-enforcement.test.js.

## Findings
- Enforcement is distributed between route-level checks, requireAny and service-level checks; route gates alone do not prove tenant/resource-level enforcement.
- GET families include project, information/history, events/details/workflow progress, material planning, workflows, reusable forms, information types, event types, events/form data, attachments, users/roles/permissions, audit, dashboards and procurement/report projections.
- POST/PUT/DELETE cover projects/information, workflow assignments/rules, event types/forms, events/amendment/void, attachments, procurement, user/role/permission operations, templates, material specs, admin deletion and session revocation.
- Draft project fallbacks may permit same-tenant creator DRAFT access with PROJECT_CREATE where normal VIEW/EDIT is absent; reference-copy similarly uses ownership fallback. Adversarial negative tests are needed.
- Explicit admin-only gates exist for project deletion preview/removal and session listing/revocation; SYS_ADMIN/ADMIN detection is tenant scoped.
- Candidate requiring service-level trace: PUT /print-templates/:id/project-data dispatches to PrintTemplateService.updateProjectBinding without a visible route-level permission guard in that branch. Not classified as vulnerability until service guard, scope and intended permission are verified.
- Additional cross-resource ID boundary targets: role permission assignment, user-role assignment, workflow department assignment deletion, event-type-form unassignment, and project/event attachment routes.
- Examined contract tests are mostly source-pattern assertions, not dynamic authorization or cross-tenant tests.

## Verification
Static inspection only (E1). No tests executed, runtime, integration, concurrency, or adversarial requests. No implementation changes.

## Next
Trace service-level guards/resource scope for project template bindings, role/user assignment, workflow assignment deletion and attachment/event/project resources; map negative-test gaps.
