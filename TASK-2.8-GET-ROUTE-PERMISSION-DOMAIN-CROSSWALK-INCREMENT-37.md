# Task 2.8 — GET Route Permission & Domain Crosswalk Increment 37
Date: 2026-11-22
Scope: Read-only GET Router dispatch review covering project subroutes, event/form/data, template/form/type and procurement/report surfaces.

## Project and information GET routes
- `/projects/{id}/information` and `/information/{type}` plus history: PROJECT_VIEW → ProjectInformationService.
- `/projects/{id}/events`: PROJECT_VIEW gate on project prefix and EVENT_VIEW additionally.
- `/projects/{id}/workflow-progress`: PROJECT_VIEW prefix gate.
- `/projects/{id}/material-specifications` and `/material-calculations`: PROJECT_VIEW or PROJECT_EDIT.
- project details page/details: PROJECT_VIEW plus EVENT_VIEW.
- project attachments: PROJECT_VIEW plus ATTACHMENT_VIEW.

## Workflow, form, information-type and event-type reads
- `/workflow-management/bootstrap`: WORKFLOW_MANAGE.
- `/workflows`: WORKFLOW_MANAGE, PROJECT_CREATE or SYSTEM_SETTINGS.
- workflow event-rules/management-data: WORKFLOW_MANAGE; workflow steps: WORKFLOW_MANAGE or PROJECT_VIEW; department steps and generic workflow detail are WORKFLOW_MANAGE in sampled dispatch.
- `/forms`: SYSTEM_SETTINGS or EVENT_TYPE_MANAGE; `/forms/{id}` and form-event-type details: SYSTEM_SETTINGS.
- `/information-types`: SYSTEM_SETTINGS or EVENT_TYPE_MANAGE; individual information type/columns: SYSTEM_SETTINGS.
- `/event-types`: EVENT_TYPE_MANAGE, EVENT_CREATE or SYSTEM_SETTINGS; individual event type: EVENT_TYPE_MANAGE or EVENT_CREATE; forms subpath uses same requireAny; procurement config subpath additionally requires EVENT_TYPE_MANAGE.
- `/events`: EVENT_VIEW; `/events/{id}`, forms, data: EVENT_VIEW; attachments additionally ATTACHMENT_VIEW.

## Procurement and reports
- Procurement workspace/dashboard/needs/orders: PROCUREMENT_VIEW or PROCUREMENT_MANAGE.
- Procurement reports: REPORT_VIEW or PROCUREMENT_VIEW or PROCUREMENT_MANAGE.
- Project reports, report project list and dashboard: REPORT_VIEW.

## Interpretation
Route gates and service dispatch are observed source behavior. This crosswalk does not prove service-level tenant guards for every method, exhaustive API caller mapping, runtime route permission tests, or PostgreSQL constraints. The `PROJECT_VIEW` prefix guard for several project subpaths is supplemented by route-specific permission checks; service-level validation still needs crosswalk.

## V2 requirements
- Use explicit endpoint authorization and tenant-aware queries.
- Distinguish read permissions from mutation permissions and route-specific scopes.
- Cross-test nested project/event/form paths for cross-tenant access, permission combinations and hidden data.
- Preserve report visibility/business scope and procurement read permission semantics.

## Limits
Read-only route audit; tests not executed. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migration, provisioning, deployment or production modifications.
