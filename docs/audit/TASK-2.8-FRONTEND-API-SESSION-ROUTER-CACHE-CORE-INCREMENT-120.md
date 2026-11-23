# Task 2.8 Increment 120 — Frontend API/session/router/cache core crosswalk

Date: 2026-11-23  
Evidence: E1 — source inspection; legacy repos read-only

## Files inspected
Frontend: `js/core/api.js`, `session.js`, `permissions.js`, `router.js`, `cache.js`, `app.js`.  
Backend request boundary: `RequestParser.js`, `Response.js`, `ErrorHandler.js`, `WebApp.js`, `Code.js`, `AppConfig.gs.js`.

## Observations
- Frontend API base URL is explicitly configured and has no fallback to another environment. Requests pass token through query parameters; API client supports retry on transient network/HTTP conditions, GET request deduplication, optional timing diagnostics and cache-backed GET flows.
- API token is maintained in memory and localStorage; session record also persists in localStorage. Session.clear removes session and API token, and permissions reset is coordinated by Router auth-expiry handling.
- Router loads server-derived permissions and server admin status, caches permissions for the active auth generation, checks route access and redirects to permitted fallback route. Auth expiry increments generation, resets page state/permission state and clears session. Frontend guards are UI navigation controls, not a replacement for backend route enforcement.
- Router preserves selected page UI state in memory during route transitions; page modules are loaded via HTML/script injection and mapping. Reentrancy/pending navigation logic handles route changes during loading.
- Cache uses versioned localStorage entries, optional TTL metadata and expiration cleanup; cache namespace and named cache keys are exposed. Storage failures are caught and cache operations may gracefully degrade.
- API GET in-flight deduplication key includes token, path, sorted query and retry setting. Cached GET supports stale display followed by refresh or no-refresh mode. Cache invalidation helpers are exposed, but correctness depends on caller invalidation policy.
- Backend request parser normalizes path, accepts GET query params and POST JSON, permits POST/PUT/DELETE method override from parameters/body, and transfers token from body to params. It rejects malformed/non-object bodies and unsupported methods.
- WebApp entry resets selected request caches, runs route access enforcement, then router dispatch; diagnostics opt-in is restricted to admin for GET, while event-create POST timing is conditionally attached. Error response builder exposes known error code/message and generic internal error for unrecognized exceptions; response shape is JSON.
- Telegram webhook/polling entry is separately routed by a query marker in doPost, with polling setup/trigger functions present.
- AppConfig contains production environment and file-type/size settings; spreadsheet ID is resolved via script property or configured value and production requires non-empty. A Drive folder identifier is present in source configuration; this is a legacy secret/configuration hygiene concern to avoid copying as V2 configuration.

## V2 implications / gaps
1. Token handling must move to secure backend session architecture; do not copy token-in-query or localStorage token persistence without explicit security design. Consider cookie-based sessions or short-lived token + refresh strategy and CSRF policy.
2. Frontend route checks must remain UX-only; backend authorization must be centralized and deny-by-default.
3. Define API request contract (method/path/body/query), versioning, error envelope, correlation IDs, rate limits, idempotency, and retry rules; ensure retries are safe for writes.
4. Define cache policy centrally by resource, auth scope, invalidation events, TTL, stale policy and storage sensitivity. User/tenant transitions must invalidate scoped caches.
5. API base URL/environment configuration and diagnostics must be explicit, secret-free, and production-safe.
6. Separate Telegram integration/API routes and credentials/configuration from application request boundary in V2.
7. Runtime behavior, browser security, storage failure and retry/deduplication edge tests remain unverified in this increment.

## Limits
- Read-only source inspection. No runtime tests, production changes, live credentials, or user data accessed.
- E1 only. G-01/G-10 remain open; PostgreSQL schema readiness NOT APPROVED.
