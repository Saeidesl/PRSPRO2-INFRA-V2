# Task 2.8 Increment 208 — Router guard and service-target reconciliation snapshot

Date: 2026-11-24

## Evidence
Static source analysis of the same Router.js snapshot (blob SHA `f2542a17993b852c7dd2968a8fefe57f70b991f3`) counted guard-call patterns and service-return target names by dispatch section.

| Dispatch section | AccessControlService.require calls | Router.requireAny calls | explicit isAdmin checks |
|---|---:|---:|---:|
| GET | 40 | 18 | 4 |
| POST | 37 | 1 | 0 |
| PUT | 17 | 0 | 0 |
| DELETE | 9 | 1 | 2 |

These are static pattern counts only; helper guards, service-internal authorization, early branches and checks expressed differently may not be captured.

## Service target families observed
Across dispatch sections, return-target names include authentication, project deletion/status/project/information, departments, attachment, event/event-data/event-type, material planning, workflow and step department, print templates, forms, information types, procurement and procurement integration, user/user-role, permission/role/role-permission, audit and reports.

## Risk/usefulness
- Router appears to centralize a significant portion of authorization dispatch, but guard presence cannot establish correct tenant isolation or object ownership.
- A number of route families are deeply nested or relationship-specific, so guard-to-object and service-internal check tracing is necessary.
- Pattern counts should not be converted into coverage percentages or security assertions.
- Negative tenant/object authorization tests are absent from this analysis; source tracing and test evidence are still required.

## Work package
For every extracted route family, construct a trace row: HTTP method + path/pattern, target service method, permission/admin gate, tenant/object predicate, validator, writes/read projections, audit/history, attachment or notification effects, frontend caller, test references, disposition and confidence.

## Status
This increment is an inventory/navigation aid, not closure. G-01/G-10 remain OPEN; G-02/G-03/G-04 remain OPEN; schema readiness NOT APPROVED.

## Safety
No production modification or tests; no SQL/DDL, migration, live data, provisioning or deployment.
