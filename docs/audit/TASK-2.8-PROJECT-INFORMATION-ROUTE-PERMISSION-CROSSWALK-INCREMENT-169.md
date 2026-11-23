# Task 2.8 — Project-information route permission and operation surface — Increment 169

Date: 2026-11-23. Read-only Router.js crosswalk for ProjectInformationService.

## Observed route surface
- GET `/projects/:projectId/information`, item GET and history GET are guarded by PROJECT_VIEW, then service validates project availability and tenant and validates InformationType tenant.
- PUT/save route for `/projects/:projectId/information/:typeId` is guarded by PROJECT_EDIT and invokes saveWithLock.
- DELETE same item path is guarded by PROJECT_EDIT and invokes deactivate (soft-delete path).
- POST approval route and reference conflict resolution are guarded by PROJECT_EDIT.
- Service-level project availability and type tenant validation remain present in inspected paths; route gate and service check are separate layers.

## V2 implications
- Preserve clear read/edit separation, including permission enforcement at service/API boundary.
- History, approval and conflict-resolution endpoints need explicit authorization, tenant scoping and version-state transition rules.
- Soft-delete operation should be explicit and auditable, with transactional consistency; consider API method/route semantics distinctly from save.
- Build a route-to-service-to-domain mutation matrix and authorization tests. This bounded crosswalk does not establish complete route coverage.

## Status
G-01/G-09/G-10 remain open; no test run, production modification, migration, live-data access or deployment. Source audit only.
