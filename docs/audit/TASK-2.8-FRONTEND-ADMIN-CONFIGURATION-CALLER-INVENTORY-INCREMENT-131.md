# Task 2.8 Increment 131 — Frontend administration and configuration caller inventory

Date: 2026-11-23  
Evidence: E1 — read-only source inspection of administrative frontend modules

## Files inspected
Users, roles, permissions, departments, project statuses, event types, forms, information types, attachments, audit, admin and settings modules.

## API caller families observed
- Users: users list/create/update, role assignment/removal, lookup departments/roles.
- Roles: role list/create/update, permission catalogue, available permissions and bulk permission assign/remove.
- Permissions: permission catalogue read and display.
- Departments: list/create/update/deactivate flows.
- Project statuses: list/create/update lifecycle.
- Event types: type list/create/update, departments/users/project statuses, event-type forms, forms and procurement settings, information types for integration configuration.
- Forms: forms and information types lookup, event-type form assignment, form field add/update/remove and mapping lifecycle.
- Information types: list/create/update, column create/update/deactivate and lookup options.
- Attachments: list, project/event context, download and delete surfaces.
- Audit: audit list and filters.
- Admin: sessions list/revoke and project cascade-delete preview/confirm.
- Settings: workflow configuration lookups and settings UI.

## Cross-domain observations
- Modules use frontend permission checks and error/loading/confirmation feedback; these controls are UX safeguards and backend permission checks are authoritative.
- Configuration modules generally use direct API calls and page-local state. Exact cache/invalidation policy differs by module; runtime consistency is not proven.
- Admin deletion flows include preview and confirmation UI, while server-side authorization/confirmation remains mandatory.
- Forms/event-type mappings and information-type columns are interdependent configuration; caller UX may sequence separate requests, so partial persistence behavior requires backend recovery semantics.
- Attachment UI includes download and deletion paths; visibility, content-type and ownership enforcement must be validated at backend and runtime.

## V2 implications
1. Create a typed admin/configuration API catalog and consolidated permission matrix.
2. Configuration changes should use transactions and version/CAS guards for related entities (form fields/mappings/information columns/event types).
3. Admin destructive operations should use explicit preview, reason, strong confirmation, audit, idempotency and recoverable execution.
4. Tenant-scoped caches and state must be invalidated on configuration changes and user/session switching.
5. UI should expose safe status/error feedback and avoid permission bypass reliance on hidden controls.
6. Validate admin and configuration flows with tenant-isolation, permission-denial, concurrency, partial failure, accessibility and mobile tests.

## Limits
- Source-only inspection; no browser/runtime tests, live data or production changes.
- E1 inventory/trace; payload/permission crosswalk is not exhaustive. G-01/G-10 remain open; schema readiness NOT APPROVED.
