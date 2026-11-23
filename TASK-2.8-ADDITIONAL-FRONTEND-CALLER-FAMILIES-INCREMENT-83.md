# TASK-2.8-ADDITIONAL-FRONTEND-CALLER-FAMILIES-INCREMENT-83

Date: 2026-11-23
Evidence: E1 read-only frontend source inspection
Reference repository: PRSPRO2-PRODUCTION
No tests, production changes, migration or deployment.

## Additional module caller trace
- material-planning.js SHA 6c9a405dceb92dd38d1680387e698656b1b8c19a: sampled endpoint families material specifications and project paths.
- project-detail.js SHA 8ac1ea017671f3363250cf8cb92f7e1e24919192: sampled event, attachment and project endpoint families.
- workflows.js SHA 309d90e8f03cc738507dd02a1cf4cdc01a2d23a4: sampled workflow and event-type endpoints.
- event-types.js SHA 7ed5e9921d0b010ddd41f01687e65efca5961ff4: departments, users, statuses, event type/form associations, forms and information-type endpoint families.
- reports.js SHA a626e4152830290b946a7eafbbdf989acd2590d2: report/project report endpoint families.
- admin.js SHA 29293f8c05dbfc10bef7a5db432772f5362c0e89: admin sessions and project/deletion endpoint families.

## Boundary
This extracts literal path families from bounded source snippets; dynamic paths, complete caller behavior, route permission mapping, request bodies and response contracts are not exhaustively reconciled. No mismatches confirmed from this sample.

## V2 requirements
- Route registry/OpenAPI contract and client generated or validated against it.
- Trace all endpoint paths including dynamic segments and permission variants.
- Test frontend caller behavior and route authorization.
- E1 only; G-01 remains blocker, no runtime tests and no production changes.
