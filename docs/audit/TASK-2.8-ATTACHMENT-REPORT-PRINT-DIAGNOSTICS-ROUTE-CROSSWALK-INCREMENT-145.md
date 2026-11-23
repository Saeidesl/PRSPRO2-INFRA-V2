# Task 2.8 Increment 145 — Attachment, report, print and diagnostics route-to-evidence crosswalk

Date: 2026-11-23  
Evidence: E1/E2 — bounded Router/service/caller/test-source mapping; tests not run.

## Route families
| Route family | Permission observed | Service/tenant evidence | Persistence/side effects | Coverage |
|---|---|---|---|---|
| GET /attachments and nested get/download/thumbnail | ATTACHMENT_VIEW | selected attachment tenant/owner/project visibility validation | read metadata/object | PARTIAL |
| POST /attachments and project/event attachment routes | ATTACHMENT_UPLOAD | owner type/project/event/tenant validation | object upload, metadata insert, audit sequential | PARTIAL |
| DELETE /attachments/{id} | ATTACHMENT_DELETE | attachment ownership/visibility/reason checks sampled | storage delete, metadata state/audit sequential | PARTIAL |
| Project image gallery/counts/project-card image endpoints | PROJECT_VIEW or EVENT_VIEW plus ATTACHMENT_VIEW; PROJECT_VIEW for card image | visible project image filtering and owner checks sampled | image metadata/count projection | PARTIAL |
| GET /reports/projects and /reports/project/{id}; dashboard | REPORT_VIEW | visible-project/report context validation | aggregate/report data projection | PARTIAL |
| GET audit/entity/user | REPORT_VIEW | tenant/entity/user filters sampled | audit projection | PARTIAL |
| Print-template list/revision/source/get/download | SYSTEM_SETTINGS or PROJECT_VIEW for read; SYSTEM_SETTINGS for source listing | tenant/version/source binding checks | cache/revision/data resolution | PARTIAL |
| Print-template create/update/deactivate, images/PDF upload, project-data update | SYSTEM_SETTINGS; source-binding update delegates permission/source validation | tenant/template/project/source checks sampled | template/asset/source writes and cleanup sequential | PARTIAL |
| Diagnostics endpoints/scripts | Admin/opt-in gates in sampled paths | selected permission and request context checks | diagnostic timing/log sheet writes | PARTIAL |

## V2 implications
1. Attachment download, thumbnail and direct-object authorization must validate tenant/owner and parent visibility for every operation; opaque storage key must not be authorization.
2. Report and audit query predicates must filter before aggregation; establish snapshot semantics and privacy-safe projection.
3. Print templates and bindings require typed/versioned contracts, source permissions, transaction/revision control and recoverable asset lifecycle.
4. Diagnostic data needs strict access, redaction, retention and bounded persistence.
5. Map every route branch to test IDs and execute negative tenant/permission cases; file existence alone is not proof.

## Remaining gaps
- Nested route combinations, direct object access, permission denial, audit/report snapshot, template concurrency, storage recovery and diagnostics privacy tests are incomplete.
- Routes are marked PARTIAL, not fully closed.

## Limits
- Bounded crosswalk only; E1/E2. No runtime tests, live data or production changes. G-01/G-10 remain blockers; schema readiness NOT APPROVED.
