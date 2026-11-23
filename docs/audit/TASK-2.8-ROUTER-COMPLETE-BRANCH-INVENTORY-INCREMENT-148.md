# Task 2.8 Increment 148 — Router branch inventory by HTTP method

Date: 2026-11-23  
Evidence: E1 — complete inspected Router.js branch inventory. This list is route-dispatch inventory, not an exhaustive DTO/permission test matrix.

## GET branches (as inspected)
- `/auth/me`, `/auth/me/permissions`
- `/admin/projects/{projectId}/deletion-preview`, `/admin/sessions`
- `/project-statuses`, `/departments`, `/departments/{id}`
- `/project-image-gallery/counts`, `/project-card-images`, `/project-form/lookups`
- `/projects`, `/projects/{id}`, `/projects/{id}/image-gallery`, `/projects/{id}/information`, `/projects/{id}/information/{typeId}`, `/projects/{id}/information/{typeId}/history`
- `/projects/{id}/events`, `/projects/{id}/workflow-progress`, `/projects/{id}/material-specifications`, `/projects/{id}/material-calculations`, `/projects/{id}/details`, `/projects/{id}/details/page/{order}/{page}`, `/projects/{id}/attachments`
- `/workflow-management/bootstrap`, `/workflows`, `/workflows/{id}`, `/workflows/{id}/steps`, `/workflows/{id}/steps/{stepId}/event-rules`, `/workflows/{id}/management-data`, `/workflows/{id}/department-steps`
- `/print-templates/revision`, `/print-templates`, `/print-templates/sources`, `/print-templates/{id}`, `/print-templates/{id}/images/{assetId}`, `/print-templates/{id}/pdf`
- `/forms`, `/forms/{id}`, `/forms/{id}/event-types`
- `/information-types`, `/information-types/{id}`, `/information-types/{id}/columns`
- `/event-types`, `/event-types/{id}`, `/event-types/{id}/forms`, `/event-types/{id}/procurement`
- `/events`, `/events/{id}`, `/events/{id}/forms`, `/events/{id}/data`, `/events/{id}/attachments`
- `/attachments`, `/attachments/{id}`, `/attachments/{id}/thumbnail`, `/attachments/{id}/download`
- `/users`, `/users/{id}/roles`, `/roles/available-permissions`, `/roles`, `/roles/{id}/permissions`, `/permissions`
- `/audit`, `/audit/entity/{type}/{id}`, `/audit/user/{id}`
- `/project-dashboard/lookups`, `/procurement/workspace`, `/procurement/reports`, `/procurement/dashboard`, `/procurement/needs`, `/procurement/orders`, `/reports/projects`, `/reports/project/{id}`, `/dashboard`

## POST branches (as inspected)
- `/auth/login`, `/auth/logout`
- `/print-templates`, `/print-templates/resolve`, `/print-templates/preview`, `/print-templates/{id}/resolve`, `/print-templates/{id}/images`, `/print-templates/{id}/pdf`
- `/project-statuses`, `/departments`, `/roles`, `/roles/{id}/permissions/bulk`, `/roles/{id}/permissions/bulk-remove`
- `/procurement/orders`, `/procurement/receipts`, `/procurement/receipts/batch`
- `/projects/{id}/material-specifications`, `/projects/{id}/reference-copy/{informationTypeId}`, `/projects`, `/projects/{id}/complete-step`, `/projects/{id}/attachments`
- `/workflows`, `/workflows/{id}/publish`, `/workflows/{id}/steps`, `/workflows/{id}/department-steps`, `/workflows/{id}/department-steps/bulk`
- `/information-types`, `/information-types/{id}/columns`, `/projects/{id}/information/{typeId}/resolve-reference`, `/projects/{id}/information/{typeId}/approve`, `/projects/{id}/information/{typeId}`
- `/forms`, `/forms/{id}/fields`, `/event-types/{id}/forms`, `/event-types`, `/event-types/{id}/procurement`
- `/events`, `/events/{id}/attachments`, `/attachments`, `/users`, `/users/{id}/roles`, `/roles/{id}/permissions`

## PUT branches (as inspected)
- `/project-statuses/{id}`, `/roles/{id}`, `/departments/{id}`
- `/information-types/{id}/columns/{columnId}`, `/print-templates/{id}/project-data`, `/print-templates/{id}`, `/information-types/{id}`, `/material-specifications/{id}`, `/projects/{id}`
- `/workflows/{id}/steps/{stepId}/event-rules`, `/workflows/{id}`, `/forms/{id}`, `/form-fields/{id}`, `/event-types/{id}`
- `/events/{id}/forms/{formId}`, `/events/{id}/void`, `/users/{id}`

## DELETE branches (as inspected)
- `/projects/{id}/information/{typeId}`, `/information-type-columns/{id}`, `/print-templates/{id}`, `/information-types/{id}`
- `/admin/projects/{id}`, `/admin/sessions/{sessionId}`
- `/roles/{id}/permissions/{permissionId}`, `/users/{id}/roles/{roleId}`
- `/workflows/{id}/department-steps/{assignmentId}`, `/form-fields/{id}`, `/event-type-forms/{id}`, `/attachments/{id}`

## Reconciliation
- The branch inventory was cross-checked against source tree and caller family increments 123, 128–146.
- Branch-level permission, tenant/resource predicate, DTO validation, secondary persistence effects and tests are separately documented as PARTIAL in bounded matrices. This inventory does not declare full coverage.
- Nested branch overlap and route-method override parsing require contract/runtime tests; no tests were run.

## Limits
- E1 source inventory only; no runtime tests or production changes. G-01/G-10 remain blockers; schema readiness NOT APPROVED.
