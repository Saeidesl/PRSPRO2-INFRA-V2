# Task 2.8 — Workflow Router operation matrix crosscheck — Increment 192

Date: 2026-11-23.

## Router branch evidence
GET:
- `/workflow-management/bootstrap`: WORKFLOW_MANAGE.
- `/workflows`: requireAny WORKFLOW_MANAGE, PROJECT_CREATE, SYSTEM_SETTINGS.
- `/workflows/:id/steps/:step/event-rules`: WORKFLOW_MANAGE.
- `/workflows/:id/management-data`: WORKFLOW_MANAGE.
- `/workflows/:id/steps`: requireAny WORKFLOW_MANAGE, PROJECT_VIEW.
- `/workflows/:id/department-steps` and workflow detail fallback: WORKFLOW_MANAGE.
POST:
- `/workflows`: WORKFLOW_MANAGE.
- `/workflows/:id/publish`, `/steps`, `/department-steps` (single/bulk): WORKFLOW_MANAGE.
PUT:
- `/workflows/:id/steps/:step/event-rules`: WORKFLOW_MANAGE.
- `/workflows/:id`: WORKFLOW_MANAGE.
DELETE:
- `/workflows/:id/department-steps/:assignmentId`: WORKFLOW_MANAGE; branch verifies assignment tenant/workflow before service removal.

## Route boundary notes
- Router-level workflow-management permissions align with source/service functions reviewed to date; no runtime negative-permission tests.
- The PROJECT_CREATE/SYSTEM_SETTINGS exception for workflow list is read-only workflow-choice access at source boundary, while actual tenant filtering remains service behavior.
- Step GET PROJECT_VIEW exception and WORKFLOW_MANAGE are explicitly distinct.
- No Step deletion route or Workflow deletion route was found in inspected workflow Router branches. This is bounded to inspected Router.js, not proof of absent routes elsewhere (method override, aliases, indirect dispatch).
- Assignment removal path has tenant and parent workflow validation in Router branch, followed by soft removal service behavior.

## Remaining
Nested dispatch/callers, runtime permission negatives, method-override/alternate route surface, repository/DTO consistency and complete tests remain unresolved. E1 only; no tests/production writes/live data. G-01/G-10 remain blockers; schema readiness NOT APPROVED.
