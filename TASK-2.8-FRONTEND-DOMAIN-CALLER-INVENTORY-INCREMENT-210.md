# Task 2.8 Increment 210 — Frontend domain caller source retrieval checkpoint

Date: 2026-11-24

## Exact frontend source blobs fetched (read-only)
- `js/modules/events.js`: `7929b8c81914a1f776519df9ad8efb6a4c503a1b`
- `js/modules/forms.js`: `36a5abaaa146398205f0d597f1ee2511d37c3fd7`
- `js/modules/projects.js`: `d8d5ec0c1c576b9c34ca4af0547158dc28710841`
- `js/modules/workflows.js`: `309d90e8f03cc738507dd02a1cf4cdc01a2d23a4`
- `js/modules/procurement.js`: `fc99a49efc6e7666d4da0b4aa34b9f0161ca76e2`
- `js/modules/attachments.js`: `b457af7af93edd56061c38786fdec8e3ff3bf14b`
- `js/modules/project-detail.js`: `8ac1ea017671f3363250cf8cb92f7e1e24919192`
- `js/modules/material-planning.js`: `6c9a405dceb92dd38d1680387e698656b1b8c19a`

## Caller/path observations from static string extraction
- Events module: project/gallery/attachment/event/event-type route families.
- Forms module: form/information-type/event-type assignment/form field route families.
- Projects module: projects/project-card-images/attachment route families.
- Workflows module: workflow and event-type route families.
- Procurement module: procurement workspace/dashboard/needs/orders and batch receipts.
- Attachments module: attachment route family.
- Project-detail module: project/event/attachment route families.
- Material-planning module: project/material-specification route family.

Extracted path strings include dynamic prefixes and form input names; they are preliminary caller navigation, not a precise call graph. Some paths are discovered by other modules or dynamic route construction.

## Crosswalk progress
Frontend domain caller retrieval is now available for eight major domain modules. The next step is line/operation-level reconciliation with backend route branches, permissions/tenant checks, payloads, side effects and test assertions. No coverage percentage or route completeness claim is made.

## Status and safety
G-01 remains open pending precise call graph and source disposition. G-10 tenant/security verification and G-02/G-03/G-04 remain open. No tests or production modifications; no SQL/DDL, migration, live data, VPS or deployment.
