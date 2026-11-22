# Task 2.8 — Project Information, Forms, Event Amendment & Workflow Mutation Permission Trace Increment 33
Date: 2026-11-22
Scope: Read-only focused Router.js crosswalk for central information writes/conflict resolution/approval, form definition/assignment, event form amendment, event void and workflow event-rule configuration.

## Central project information mutations
- POST-like project information save path `/projects/{projectId}/information/{typeId}`: `PROJECT_EDIT`, dispatches `ProjectInformationService.saveWithLock`.
- reference conflict resolution path: `PROJECT_EDIT`, dispatches `resolveReferenceConflict`.
- approve path: `PROJECT_EDIT`, dispatches `approve`.
- deactivate central information path: `PROJECT_EDIT`, dispatches `deactivate`.
- ProjectInformationService behavior was sampled in prior reference-sync audit; full operation-specific tenant validation and route test coverage not exhaustive.

## Reusable forms / event type configuration
- Form create/add-field and form definition mutations: `SYSTEM_SETTINGS` in sampled Router handlers.
- Event-type form assignment uses `EVENT_TYPE_MANAGE` or `SYSTEM_SETTINGS`.
- Event type creation/update uses `EVENT_TYPE_MANAGE`.
- Event-type procurement configuration save uses `EVENT_TYPE_MANAGE`.
- Event-form amendment route uses `EVENT_EDIT` and dispatches `FormService.updateEventFormResponse`; its validator, tenant/event provenance, concurrency and replay behavior are sampled in earlier test/source review.

## Event and workflow mutation
- Event creation POST `/events`: `EVENT_CREATE`.
- Event void action `/events/{id}/void`: `EVENT_EDIT`.
- Manual workflow step completion `/projects/{id}/complete-step`: `EVENT_CREATE`.
- Workflow step event rules update: `WORKFLOW_MANAGE`.
- Workflow update prefix: `WORKFLOW_MANAGE`.

## Positive evidence and limits
Explicit route gates are present for these sampled mutations. The service checks and route method/path details are only bounded to the above source locations and related sampled tests. No runtime permission test, exhaustive dynamic route matrix, or PostgreSQL tenant constraint evidence is established.

## V2 requirements
- Maintain route and service/domain authorization matrix; least-privilege tests for each operation.
- Central information write/approve/deactivate/conflict resolve should transactionally preserve revision, source provenance, history and tenant-qualified links.
- Form definition versioning, event-type mapping and amendments must preserve immutable snapshots and optimistic concurrency in normalized schema.
- Event void must transactionally reconcile workflow and source-linked domain effects, with durable outbox/recovery for external operations.

## Limits
Read-only route trace. Tests not executed. G-01/G-03/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migrations, provisioning, deployment or production modifications.
