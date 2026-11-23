# Task 2.8 Increment 137 — Remaining frontend module traces: authentication, dashboard redesign and project-workflow settings

Date: 2026-11-23  
Evidence: E1 — read-only source inspection

## Files inspected
- js/modules/auth.js
- js/modules/dashboard-redesign.js
- js/modules/project-workflow-settings.js

## Findings
### Auth module
- Login submits through POST /auth/login, then resolves landing route using server-loaded permission state. If permissions cannot be loaded, frontend clears local session, permissions, cache and route/layout state rather than continuing with unusable auth.
- Logout attempts POST /auth/logout but clears local auth/permission/cache/route/layout state in cleanup regardless of request result. Auth expiry follows similar local cleanup.
- /auth/me is exposed by module as a caller; token/session handling is in core API/session modules audited in Increment 120.

### Dashboard redesign
- UI state (filters, expanded projects and scroll) is persisted/restored in local storage; state is local and user-specific keying/policy requires further verification.
- Project list and lookups use cached/fresh API calls with refresh/error callbacks and request control. Project histories are loaded in bounded concurrent batches, paginated through project detail page routes and cached by project/order/page.
- The paginated history payload includes event data and event-field context in the inspected frontend handling, reducing per-event lookup fan-out compared with the legacy dashboard path.
- Attachment indexes and project needs are used for project history/summary cards. The source shows multiple delegated API paths and client rendering; access and payload consistency require runtime tests.
- Scroll/search/filter persistence and expanded history restoration are UX features, not database state.

### Project-workflow settings module
- A small frontend helper reads a preferred workflow ID from localStorage and observes the project-create form DOM to apply a default workflow selection. It does not call a backend API itself in the inspected file; server-side validation and workflow availability remain authoritative.

## V2 implications
1. Authentication state transitions must be centrally managed and fail closed; cache invalidation should be auth-scoped and consistent across modules.
2. Dashboard pagination should have stable sort keys, snapshot/continuation semantics and tenant/project visibility predicates; avoid duplicate/missing events under concurrent updates.
3. User-facing filters/expanded state can persist locally, but sensitive data and tenant-scoped caches must not persist unboundedly or leak across sessions.
4. Dashboard projection should expose event data/field context in a defined contract, with appropriate historical snapshots and payload-size limits.
5. Local workflow preference is UX-only and must never override backend workflow/tenant/project validation.
6. Validate auth failure/cleanup, multi-account device switching, pagination under concurrent writes, search performance, local-storage failure, mobile and keyboard accessibility.

## Limits
- Source-only; no runtime/browser tests or production changes.
- E1 only; no tests executed; G-01/G-10 remain open; schema readiness NOT APPROVED.
