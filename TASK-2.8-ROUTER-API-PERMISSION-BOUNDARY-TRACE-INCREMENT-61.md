# Task 2.8 — Router/API Permission Boundary Trace (Increment 61)
Date: 2026-11-22
Status: Bounded READ-ONLY route trace.

## Source
Router.js from READ-ONLY backend production reference.

## Observations
- Router-level permissions are explicitly enforced for sampled procurement reads (workspace, reports, dashboard, needs, orders) and writes (orders, receipts, batch receipts) using PROCUREMENT_VIEW/MANAGE or report alternatives.
- Project material specification creation routes allow PROJECT_EDIT, with a fallback for project creator using PROJECT_CREATE after tenant/creator validation. Material calculation/specification listing routes use project-level access checks; workflow-progress route uses a distinct route branch.
- Project information read/history routes require PROJECT_VIEW; attachment listing routes require ATTACHMENT_VIEW and related entity service calls.
- Audit endpoints require REPORT_VIEW, including entity/user path variants; sampled routes delegate tenant filtering and entity/user visibility to services.
- This trace only samples route branches. It does not establish complete coverage for GET/POST/PUT/PATCH/DELETE route families, route aliases, all permissions, or authorization consistency between Router and service.
- Router-level authorization and service-level validation are separate layers. Direct service invocation, alternate routes or parameter normalization bypass must be considered in isolated tests.

## V2 implications
1. Centralized route middleware should declare endpoint permission and scope metadata; services must also enforce domain ownership where needed.
2. Prefer API permission matrix generated from route registry and reviewed for coverage, avoid duplicated/inconsistent permission checks.
3. Test every endpoint method/route against authenticated roles, tenant scope, direct ID, encoded path variants, bypass and privilege escalation.
4. Validate route parsing/normalization before authorization; treat malformed and alternate path forms as deny-by-default.
5. Tenant-scoped query and DB constraints remain necessary in addition to route authorization.

## Evidence limits
Bounded E1 source review only; no endpoint execution or automated permission matrix tests. G-01/G-10 remain OPEN; schema readiness NOT APPROVED. No production changes.
