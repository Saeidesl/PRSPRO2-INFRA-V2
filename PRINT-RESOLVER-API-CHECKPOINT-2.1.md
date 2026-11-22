# Print Template Resolver and API Surface — 2.1 audit checkpoint

Source read-only review dated 2026-11-22 of PrintTemplateService.js and Router.js.

## Print-template resolver source types observed
- PROJECT_FIELD: allow-listed project fields (ID, ProjectCode, Title, Description, Status, Quantity, CreatedAt, UpdatedAt, RecordState).
- PROJECT_INFORMATION: project information-type values and revisions.
- EVENT_FIELD: event properties including event type name, occurred/created time, description, creator, performer, department and status.
- EVENT_FORM_FIELD: form-response fields for linked events.
- Other binding types may be event/form-scoped; exact binding definition and resolver output path still require completion of the remaining resolver source.

Resolver behavior verified in inspected section:
- Tenant/project availability checked before resolving.
- Events exclude VOID/CANCELLED/CANCELED.
- Only required sources are read; reads are reduced to once per resolve request.
- Forms and project information are tenant-filtered; project info revision values collected.
- Event type source excludes INDEPENDENT connection types in the inspected path.
- Template metadata is tenant scoped; templates have version, definition JSON, and separate storage references for PDF and images.
- Image upload validates MIME, extension and file signatures, bounds file size, max image count, and cleans up stored object when metadata update fails.
- PDF upload validates PDF MIME, page count/definition consistency; replace/delete storage only when unreferenced; cleanup failures are deferred/logged.
- Deactivation is a soft status transition to DELETED with audit record.
- Resolver is an on-demand source-data assembly, not a stored project-value snapshot. V2 must preserve this distinction and explicitly decide output snapshot/audit needs.

## Router read-only route families verified in inspected sections
GET families: auth/me and permissions; admin project deletion preview and session list; project statuses, departments, galleries, projects and project information/history; project events/workflow progress/details/attachments/material specs/calculations; workflows and workflow-step rules/management; print-template list/revision/source/get/image/PDF; forms/event-type links; information types/columns; event types/forms/procurement settings; events/form responses/data/attachments; attachments/thumbnail/download; users/roles/permissions; audit/entity/user; dashboard/project dashboard; procurement workspace/reports/dashboard/needs/orders; project reports.
POST sections inspected: login/logout, print-template create/resolve/preview/image/PDF uploads, project-status create, department create, role create, bulk role-permission routes. The remaining Router mutation routes and HTTP PUT/DELETE sections are not yet exhaustively enumerated.

## Security/contract observations
- Authorization is generally route-specific; some GET routes use requireAny combinations. The V2 API needs explicit endpoint policies and test coverage, not a mechanical route translation.
- Print-template sources endpoint has SYSTEM_SETTINGS permission while list/get/download endpoints accept SYSTEM_SETTINGS or PROJECT_VIEW; create/upload require SYSTEM_SETTINGS; preview/resolve require PROJECT_VIEW. Verify route-to-service data access and tenant scope as part of V2 contract review.
- Resolver outputs are project-dependent and tenant-scoped; template assets should not leak storage keys in public metadata except internal definition/source paths.

## Remaining work
- Finish resolver source bindings, output assembly and related template routes/contracts.
- Extract complete Router GET/POST/PUT/DELETE endpoint matrix and permission requirements.
- Continue auxiliary-sheet, reference-copy recovery, attachment retention, audit coverage, and cardinality audit.
- Then integrate into conceptual model and review with Product Owner; no schema or migrations until approval.
