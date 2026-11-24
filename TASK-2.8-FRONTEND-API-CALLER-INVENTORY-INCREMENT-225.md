# Task 2.8 Increment 225 — Frontend module/API caller inventory (selected modules)

Date: 2026-11-24

## Scope
Read-only inspection of PRSPRO2-PRODUCTION frontend modules: js/core/api.js, js/core/router.js, js/modules/events.js, forms.js, workflows.js and projects.js. File sizes recorded from retrieved source; no edits to production.

## E1 observations
- API core requires explicit PMS_API_BASE_URL and has token handling, in-flight GET request coalescing and optional diagnostic collection.
- Events module is large (219 KB retrieved), and contains calls to project image gallery counts, attachments, events, event-types, projects and event-form definitions. Static literal extraction alone does not capture dynamically assembled URLs.
- Forms module calls /forms, /information-types, /event-types, /event-type-forms, /form-fields and dynamic /forms/:id paths.
- Workflow module calls /workflows, workflow management/bootstrap and event-type routes; dynamic string construction means simple literal extraction is incomplete.
- Projects module calls /projects, project-card-images, attachments and project-related routes. Dynamic API construction and modules outside the inspected set prevent completeness claims.
- Router.js establishes frontend page routing/navigation, not API authorization; actual permission decisions reside in backend/endpoint layers.

## Inventory limits
This is a selected frontend caller/module inventory, not a complete route-to-caller crosswalk. It is not exhaustive of all modules, dynamically constructed API calls, caller permission branches, or backend dispatch. Backend path mapping and actual deployed source parity remain open.

## Recommendations recorded (not decisions)
Build V2 endpoint catalog from explicitly approved source discovery, with operation/route, caller, permission, tenant predicate, data contract, side effects, retry/idempotency, failure recovery and tests. Use generated/maintained contract tests where practical; avoid deriving APIs solely from frontend string scraping.

## Evidence and safety
E1 source. No tests run. No production changes, data, SQL/DDL, migrations, provisioning or deployment. G-01 and G-10 remain open; schema readiness NOT APPROVED.
