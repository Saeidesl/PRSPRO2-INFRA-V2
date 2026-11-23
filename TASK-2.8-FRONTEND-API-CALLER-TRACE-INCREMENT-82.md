# TASK-2.8-FRONTEND-API-CALLER-TRACE-INCREMENT-82

Date: 2026-11-23
Evidence level: E1 bounded source inspection
Read-only repositories: PRSPRO2-PRODUCTION
No tests, production changes, migrations or deployment.

## Frontend source files inspected
- index.html SHA 174ce0be55ed63facc93136b450aa02dd9a84533
- js/core/api.js SHA 51a68b72f57696a83b4f3b5afb439afede541641
- js/modules/events.js SHA 7929b8c81914a1f776519df9ad8efb6a4c503a1b
- js/modules/forms.js SHA 36a5abaaa146398205f0d597f1ee2511d37c3fd7
- js/modules/attachments.js SHA b457af7af93edd56061c38786fdec8e3ff3bf14b
- js/modules/projects.js SHA d8d5ec0c1c576b9c34ca4af0547158dc28710841

## Observations
- Production frontend index explicitly sets PMS_API_BASE_URL to a production Apps Script URL; API module requires this explicit configuration and avoids Development/Staging fallback.
- API module provides in-flight GET request deduplication, retry logic (bounded excerpts), diagnostics opt-in and request-path diagnostics filtering.
- Events module callers sampled use event/project/gallery/attachment and event-type endpoints; forms module uses forms, information-types, event-types, mappings and field endpoints; attachments module uses attachment endpoints; projects module uses project and project-card-image/attachment routes.
- Frontend caller trace is partial: dynamic route construction, all modules, response contracts and every permission-dependent call were not reconciled. The direct file retrieval of several guessed root paths returned NOT_FOUND, but index asset references yielded valid module paths and these were fetched.
- No source-level endpoint mismatch confirmed from the sampled paths; the route/caller crosswalk is incomplete.

## V2 requirements
1. OpenAPI/API contract with shared typed frontend client and endpoint inventory.
2. Caller-route-service-permission tests and route migration coverage.
3. Frontend independent URL/environment configuration without production legacy API assumptions.
4. Request deduplication/retry semantics documented and bounded for idempotent operations.
5. Error/status contract and authentication/session behavior covered in automated integration tests.

E1 only; no tests. G-01 remains blocker; no production changes or schema approval.
