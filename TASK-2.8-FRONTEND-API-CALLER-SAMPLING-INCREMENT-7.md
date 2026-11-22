# Task 2.8 — Frontend/API Caller Reconciliation Increment 7

Date: 2026-11-22
Scope: READ-ONLY caller sampling from production frontend modules. Repository PRSPRO2-PRODUCTION was accessed read-only. No edits to production repos, no tests executed, no runtime or production data accessed.

## Inspected files
- js/core/api.js (request wrapper; API base URL and token behavior)
- js/core/router.js (page route map and route permission map; bounded excerpts)
- js/core/permissions.js (client-side permission helper)
- js/modules/events.js (sample endpoint calls)
- js/modules/forms.js
- js/modules/procurement.js
- js/modules/projects.js
- js/modules/attachments.js

## Caller-surface evidence (sampled)
- Event module callers: event/event-data/event-form reads, project events and workflow progress, complete-step, event creation, amendment form update, project information/history, event-type/form-definition reads, project attachments/image-gallery and attachment thumbnail/download.
- Forms module callers: forms and form fields CRUD, event-type/form mappings read/create/delete, information-types and event-types reads.
- Procurement module callers: workspace/dashboard/needs/orders read paths, fallback legacy reads, order creation and batch receipt posting; workspace fallback is explicitly client-observed.
- Projects module callers: project reads/create/update, project attachment reads/upload, attachment delete, reference-copy, project card images and attachment download.
- Attachments module callers: attachment download and generic attachment upload, entity list reads for PROJECT/EVENT.
- Router/page permission map and Permissions helper are client-side navigation/UI gates only. They are not evidence of server-side enforcement; backend route/service authorization must remain authoritative.
- The sampled API paths show mixed resource families, compatibility/fallback calls, and cross-domain endpoints. This increment does NOT prove endpoint coverage, exact handler binding, server permissions, response contracts, caller completeness or test coverage.

## Reconciliation implications / next
1. Build a canonical endpoint registry by backend Router dispatch branch and service method, then map frontend call-site patterns to each endpoint (caller IDs, verbs, permission enforcement, tenant checks, tests).
2. Separate read-only projection endpoints from mutations, including side effects and idempotency/replay behavior.
3. Resolve compatibility fallbacks (e.g., procurement workspace vs dashboard/needs/orders) and determine whether each legacy fallback is supported, deprecated or required.
4. Map polymorphic attachment and cross-domain routes to explicit target domain contracts.
5. Client-side permission maps must not be treated as authorization evidence.
6. G-01 remains OPEN: this is sampled reconciliation only, not a full caller/route/mutation/test matrix.

## Limits
No tests executed, no live data, no SQL/DDL/schema/migration, no DB provisioning, no deployment, no production changes. The production repositories remain read-only.
