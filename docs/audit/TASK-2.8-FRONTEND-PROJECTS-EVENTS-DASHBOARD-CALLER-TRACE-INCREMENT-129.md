# Task 2.8 Increment 129 — Frontend Projects, Project Detail, Events and Dashboard caller trace

Date: 2026-11-23  
Evidence: E1 — read-only frontend source inspection

## Files inspected
- js/modules/projects.js
- js/modules/project-detail.js
- js/modules/project-images.js
- js/modules/events.js
- js/modules/dashboard.js

## Observed caller/API families
### Projects list and creation/edit
- Projects module gates list/create UI by permission state and avoids project-list request for create-only users. List uses cache-first API retrieval, filtering/rendering and project-card image metadata/content caching; image content cache uses IndexedDB, metadata uses client cache.
- Create/edit workflow uses project-form lookups or dashboard lookups, workflow step lookups and status choices. Project create/edit calls /projects POST/PUT; attachments are uploaded via project attachment endpoint; selected attachment deletion uses DELETE with reason; repeat/reference information copy is a multi-step client workflow with progress and retry statuses.
- Project settings and notification/status fields are sent in update payload. Cache invalidation is coordinated across project list/detail/dashboard data.

### Project detail
- Project and event/detail requests use cache-first and refresh paths, request-version guards, and event-detail bundling. Information fetch/save/delete/history/approval/reference conflict resolution and file upload/download are integrated with permissions and domain APIs.
- Project detail uses project/event audit APIs, event form responses and attachment gallery/download. Print designer/output modules share project data loading and print template calls.
- Rendering uses escaped dynamic data in many output paths; UI contains multiple specialized form/table/modal renderers and data normalization adapters.

### Event queue/registration/context
- Events module permission-gates event/project/type loading, uses cached GET flows with TTL and refresh controls, and maintains token-scoped dependent-data caches for event forms, project information, workflow progress and picker data.
- Project picker includes event-picker query projection; event-type prefetch requests includeFormDefinitions=true and uses generation/token guards to reject stale responses.
- Event registration supports project context, image gallery/thumbnail paths, workflow-progress completion, amendment source event selection, configured form visibility/row collection and file upload, plus POST /events.
- Event details retrieve event, dynamic data and form responses in parallel; attachment viewing is permission-gated in the caller.
- UI refreshes/invalidation are event-driven; cache generation guards help prevent stale context from overwriting a newer modal/session.

### Dashboard
- Dashboard uses cached dashboard/lookup data, project lists and selected-project detail/events. Selected project detail requests use cache-refresh callbacks and request version/selected-project guards. Workflow steps, event data and event field labels are loaded separately with cache and per-event request deduplication.

## V2 implications / unresolved verification
1. Preserve create-only vs view/edit permission UX but rely on backend enforcement; map caller permissions to explicit API policy.
2. Consolidate project/event cache invalidation and user/session scoping; cache keys and stale-refresh behavior need test matrix.
3. Define event picker projection and project dashboard projection contract to avoid full-list payloads and repeated detail reads.
4. Reference-copy workflow is multi-request and should be durable server-side operation with idempotent resume, not UI-driven orchestration.
5. Event form prefetch, amendment selection and visibility rules should be API contracts with form-version snapshots and predictable error isolation.
6. Attachment thumbnail/download and event-context access should be verified against tenant/project permissions and sensitive data filtering.
7. Client render/version guards, duplicate listener/module cleanup, browser behavior, responsive accessibility and performance require runtime tests.
8. Source audit does not confirm any defect; only architecture/contract observations.

## Limits
- Read-only source inspection; no browser tests, production changes, live data or runtime access.
- E1 only; no tests executed; G-01/G-10 remain open; schema readiness NOT APPROVED.
