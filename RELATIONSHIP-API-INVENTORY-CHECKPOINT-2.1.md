# Relationship and API Surface Inventory — Phase 2.1 checkpoint

Read-only source audit on 2026-11-22. This checkpoint is provisional and based on service/repository behavior and the Router file, not on live production data.

## Provisional entity relationships inferred from source behavior
- Tenant → Users, Departments, Roles, Projects, Workflows, EventTypes, Forms/InformationTypes and operational records; tenant boundaries enforced in service-layer checks, with some global permission concepts requiring confirmation.
- Role ↔ Permission via UserRoles and RolePermissions join entities; user-role grants are tenant scoped in inspected permission logic.
- Project → Workflow (published workflow version); Project → Events; project event data through Event → EventData; Project → ProjectInformation values and history.
- Workflow → Steps and step-department assignments; step ↔ event-type rules are version scoped.
- EventType → Fields; EventType ↔ Forms through EventTypeForms; EventFormData links Event + Form + version/snapshot.
- Project → MaterialNeeds; project → MaterialSpecs; MaterialNeedCalculation connects spec, need, source event, calculation key/revision and basis quantities.
- PurchaseOrder → Items; item ↔ project/material need via allocations (multiple allocations possible); receipts attach to order item and project/source-event context.
- Attachment metadata associates with PROJECT or EVENT entities; storage object identity is abstracted via StorageProvider/StorageKey.
- Project-reference sync log links source and target project information/revisions; policy and mapped information types govern copy/sync.
- PrintTemplates form a reusable library and resolve data from projects/forms/events (detailed resolver mapping still pending).
- AuditLogs represent actor/action/entity changes; exact retention and field coverage pending.

## API surface audit status
Router.js has been read and spot-checked for route permission families: project, event, attachment, workflow, event-type, user, role, permission, report, procurement, system setting, department and project-status management. This remains a spot-check, not a complete endpoint-by-endpoint inventory; the route list, methods, request/response contracts and deny-by-default coverage remain to be exhaustively mapped.

## Cardinality/constraints to preserve (provisional)
- Project uses a selected workflow version.
- Events are project-scoped in inspected project workflow path; event type/workflow/rules constrain valid event registration.
- Forms are assigned through event-type mapping, and response data retains a versioned definition snapshot.
- Material need calculations and specifications are revision/provenance linked; generated needs may require reconciliation.
- PO items can have multiple project/material-need allocations; shared items/orders must not be removed when other project allocations remain.
- Receipts are idempotent and quantities are constrained by ordering/allocation logic.
- Attachments may be attached to projects/events, and project deletion collects related objects.

## Open discovery items
1. Exhaustive endpoint list, method, payload, permission, tenant scope, status and idempotency matrix.
2. Confirm all key constraints and cardinality (especially project reference, information-type column cardinality, material allocations and receipt attribution).
3. Print template data resolver and dependencies.
4. Audit log semantics and field-level retention.
5. Complete attachment retention and project-reference copy rollback paths.
6. Inspect auxiliary backup sheet significance without production-data access or mutation.

## Gate
This is not a conceptual-model approval package yet. No schema or migrations have been created; production repositories/data remain unchanged.
