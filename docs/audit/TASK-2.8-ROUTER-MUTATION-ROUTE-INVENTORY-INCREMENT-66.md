# Task 2.8 — Router Mutation Route Inventory (Increment 66)

Date: 2026-11-23
Evidence: Read-only retrieval of production backend Router.js (blob SHA f2542a17993b852c7dd2968a8fefe57f70b991f3). Repository owner spelling verified as Saeidesl. No source changes made.

## POST routes observed
- Auth: login/logout.
- Print templates: create, resolve/preview, template image/PDF upload, individual resolve.
- Project status, departments, roles create.
- Role permissions: bulk assign/remove.
- Procurement: orders, batch receipts, receipts.
- Project material specification create (PROJECT_EDIT fallback to PROJECT_CREATE for tenant-scoped draft owned by user).
- Project reference-copy (PROJECT_EDIT fallback to PROJECT_CREATE under tenant + creator checks).
- Project create.
- Workflow create, publish, step create, department assignment (single/bulk).
- Information types and columns create.
- Project-information reference resolution, approval, save-with-lock.
- Forms, fields, event-type form association.
- Event types and procurement integration configuration.
- Event create, event attachment upload.
- Project complete-step.
- Project attachments, generic attachments.
- User create, role assignment, single role-permission assignment.

## PUT routes observed
- Project status, roles, departments update.
- Information-type column update.
- Print-template project binding update; print-template update.
- Information type update.
- Material specification revision.
- Project update with fallback restricted to tenant-matched draft owned by current user and PROJECT_CREATE permission.
- Workflow step event rules; workflow update.
- Form/form-field/event-type update.
- Event form response update.
- Event void.
- User update.

## DELETE routes observed
- Project-information deactivate.
- Information-type column deactivate.
- Print-template deactivate.
- Information type deactivate.
- Admin project deletion guarded by admin check and confirmation code.
- Admin session revoke guarded by admin check.
- Role permission removal; user-role removal.
- Workflow department-step assignment removal with parent/child consistency lookup.
- Form field removal; event-type form unassignment.
- Attachment delete guarded by ATTACHMENT_DELETE.

## Review limitations / risk notes
- Router permission checks and fallback conditions were inventoried; service/repository implementation and downstream integrity must be verified route by route.
- Print-template project-data PUT branch does not visibly perform an explicit AccessControlService.require in this Router branch; its service-side authorization must be independently verified. This is a review observation, not a confirmed vulnerability.
- The generic '/workflows/' POST and PUT branches require WORKFLOW_MANAGE; specific subroutes are dispatched after prefix matching.
- Project and event attachment upload branches set EntityType/EntityID before delegation.
- No runtime tests, mutation attempts, or production-data access were performed.
- This inventory is bounded to Router.js source and is not a complete route/dependency audit.
