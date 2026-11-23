# Task 2.8 Increment 123 — Source-tree coverage reconciliation and remaining audit inventory

Date: 2026-11-23  
Evidence: E1 inventory from read-only GitHub source trees and current Infra audit index/TODO/ARCHITECTURE.

## Purpose
Reconcile the two production repository source trees against audit documents currently present in Infra V2, so future increments target uncovered areas rather than repeating completed traces.

## Backend coverage — already audited in focused increments
Auth/session/user, role/user-role (partial role-permission), departments, project statuses, information type/columns, forms/fields, workflow/service/version/assignment/rules, events and form response/amendment, project lifecycle/repeat-copy/information sync/delete, attachments/storage, material planning/specifications/calculations, procurement orders/allocations/receipts/projections, reports and print-template lifecycle. Some areas are sampled or service-only; do not interpret this as exhaustive route/test coverage.

## Backend source-tree items requiring focused audit/crosswalk or deeper validation
- Router route inventory and all GET/POST/PUT/DELETE route-to-service payload/permission crosswalk (previous inventory partial; route-level reconciliation remains an explicit Task 0.2 TODO).
- RolePermissionService and repository enforcement/permission mutation semantics (earlier source retrieval incomplete).
- EventType service/repository/validator and independent event-type connection/form mapping behavior, including route permissions and lifecycle transitions.
- EventData/EventField APIs, tenant/project scoping and attachment/deletion edge cases beyond sampled paths.
- Telegram integration lifecycle, polling trigger, notification selection, tenant/chat pairing, secret configuration and operational failure/retry (source-only; no live config).
- ProductionConfigurationMigration and setup/migration scripts, DatabaseSetup schema/header contracts, sheet relationships, generated IDs and uniqueness assumptions.
- PerformanceDiagnostics/diagnostic endpoints and privacy/permission limits; LoginDiagnostics/PermissionDiagnostic and diagnostic scripts.
- WorkflowStepRepository source path and all repository implementation coverage, including integrity gaps.
- Existing backend test suite coverage crosswalk to observed business rules; test files inventoried, not executed.

## Frontend source-tree items requiring focused audit/crosswalk
- All page modules and their API caller/payload/error/loading/cache behavior: dashboard/dashboard-redesign, projects/project-detail/project-images, events/event-types/forms/information-types, material-planning/procurement, workflows/workflow-department-assignments, reports/print-designer/print-output, admin/users/roles/permissions/departments/project-statuses/settings/audit/attachments/auth.
- CSS and responsive behavior crosswalk, including print-output and designer styles; tests/audit reports exist but not independently runtime validated here.
- Page script loader/module lifecycle and duplicate-listener/cleanup behavior across navigation.
- Frontend tests and CI crosswalk, plus browser-test coverage and artifacts, source-only inventory.

## Coverage-status rule
A source-tree inventory is not evidence of implementation behavior. Each uncovered family must be inspected in actual source, with unknown behavior recorded as pending. A focused audit may be consolidated only after its source and callers are verified. Runtime tests remain separately labeled.

## Limits
- This is a reconciliation inventory, not a claim that listed modules were inspected in detail.
- E1 inventory only; no tests, live data, credentials or production changes.
- Task 2.8 remains IN PROGRESS; G-01/G-10 remain open; schema readiness NOT APPROVED.
