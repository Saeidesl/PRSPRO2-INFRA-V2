# Task 2.8 Increment 141 — User-role association, validators and setup scripts boundary

Date: 2026-11-23  
Evidence: E1 — read-only source inspection; setup functions not executed.

## Sources inspected
UserRoleService.js, UserRoleRepository.js, RoleValidator.js, AuditValidator.js, Validator.js, ProjectStatusPermissionSetup.js, ProjectStatusSetup.js and SidebarPermissionsSetup.js.

## Observed behavior
- UserRole assignment validates current-user tenant, target user and role tenant/status, then read-before-write duplicate check and insert; audit follows sequentially. Removal resolves the existing link, validates tenant and physically deletes the association before audit. Unique/race-safe association constraint and transactional audit are not demonstrated.
- UserRoleRepository is ID-oriented and find/exists methods do not intrinsically filter tenant; service validation and route checks are relied on. Tenant filter is applied in selected listByUser pathway.
- RoleValidator validates name/code and optional status/tenant ID, but uniqueness/tenant immutability is service responsibility. AuditValidator validates action/entity/user/tenant/reason shape, not authorization or tenant correctness itself.
- Generic Validator provides required/string/number/boolean/enum/ID/object validation. Number validation checks JS numeric type/NaN, not precision/range; ID validation is prefix/underscore pattern rather than entity existence.
- ProjectStatusSetup is an operational sheet migration/bootstrap script: lock, additive sheet/header preparation, tenant discovery and seed status rows, plus clears project-status column data validations on the projects sheet. No runtime execution was performed.
- ProjectStatusPermissionSetup and SidebarPermissionsSetup add permission catalogue entries if codes are absent, but the inspected scripts rely on active spreadsheet context, header/index assumptions and generated ID helper. They are setup operations rather than application routes.

## V2 implications
1. User-role link requires composite uniqueness and tenant-qualified FK; role assignment/removal should be transactional with audit/outbox and preserve security-sensitive actor/reason.
2. Prefer soft-history or explicit temporal role membership if audit/migration requirements need reconstructable membership changes; current physical removal may lose direct link history apart from audit.
3. Validator layers should distinguish DTO shape, domain policy, tenant/resource authorization and DB constraints. ID format checks are not FK checks.
4. Seed/setup scripts should become versioned migrations or guarded administrative seed tasks; no production spreadsheet context, hardcoded IDs or destructive validation changes.
5. Permission catalogue seed is idempotent by code and needs conflict-safe DB upsert/unique constraint.
6. Test cross-tenant user-role links, inactive role rejection, duplicate/concurrent assignment, physical removal/history, seed idempotency and setup failure recovery.

## Limits
- Source-only inspection; no setup/migration execution, live data, tests or production changes.
- E1 only; G-01/G-10 remain blockers; schema readiness NOT APPROVED.
