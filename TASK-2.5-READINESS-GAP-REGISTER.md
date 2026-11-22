
# Task 2.5 — Evidence Gap Register & Source Audit Increment 1
Updated: 2026-11-22
Status: IN PROGRESS — read-only source audit; readiness not established.

## Audit scope completed in this increment
Production backend source files re-read from the default branch of PRSPRO2BACKEND-PRODUCTION: DatabaseSetup.js, ProcurementService.js, AttachmentService.js, EventService.js, ProjectInformationService.js and FormService.js. This audit used source only; no live records, operational configuration, secrets, test execution or production changes.

### Evidence confirmation
- DatabaseSetup.js confirms registry headers for Projects, Workflow, EventTypes, Events, EventData, Attachments, AuditLogs, MaterialNeeds, PurchaseOrders, PurchaseOrderItems, PurchaseAllocations, MaterialReceipts, material specification/calculation, ProjectInformation/history, Forms/FormFields/EventTypeForms/EventFormData, print templates, workflow event rules and reference-sync logs. Registry/header evidence does not establish deployed sheet contents or nullability.
- ProcurementService exposes distinct create-order, single-receipt, batch-receipt, request-row calculation, needs/orders listing, workspace/report/dashboard operations. This corroborates separate write paths requiring a unified target contract; it does not prove parity between single and batch behavior or correction/return behavior.
- The existing procurement cancel/adjustment audit (2.1) remains consistent: the inspected service has no identified dedicated order cancellation or receipt-return/adjustment operation. Cancellation is observed as a state check in receipt validation, not proof of a complete cancellation lifecycle.
- AttachmentService exposes upload, download, thumbnail, list/by-entity, project-image/gallery/count, copy-project-image and delete operations. This establishes lifecycle breadth but not exhaustive finite owner-type coverage, storage-provider migration contract, scanning/retention, orphan recovery or authorization matrix.
- EventService source has create, void, workflow validation/progress, project-detail/list, event data enrichments and field/template update pathways. The audit must still systematically map payload mutability, snapshot authority, amendment/void and attachment interactions across all paths.
- ProjectInformationService and FormService are present and reviewed at file level in this increment; this is not yet a complete write-path/field mutation map.

## Readiness gap register (initial prioritized state)
| ID | Priority | Gap / question | Evidence status | Next evidence or decision |
|---|---|---|---|---|
| G-01 | BLOCKER | Exhaustive table/entity-to-write-path coverage and mutation matrix | Partial registry + selected services | Trace all repositories/services/routes and classify create/update/delete/archive |
| G-02 | BLOCKER | Procurement cancellation, receipt correction/return, reversal, replay and quantity reconciliation contract | Partial source evidence; no standalone cancellation/adjustment method identified in inspected service | Trace all receipt/order pathways and document PO business decision |
| G-03 | BLOCKER | Event/form response payload structure, query/report/search/filter expectations, immutable snapshot and amendment model | DataJSON/ResponseJSON evidence; behavior partially reviewed | Cross-map payload schema, mutation paths, reporting use and performance workload |
| G-04 | BLOCKER | Attachment owner types and relationship integrity | PROJECT/EVENT evidence and generic EntityType/EntityID observed; finite inventory incomplete | Trace all callers and list owner types, tenant inheritance, visibility, lifecycle |
| G-05 | HIGH | Identifier strategy and legacy mapping | IDs generated/consumed in source; formats and migration scope incomplete | Enumerate entity ID generation, reference/copy IDs, tenant scope, external references |
| G-06 | HIGH | Decimal precision, quantity/unit, currency and rounding semantics | Material/procurement quantities present; exhaustive definitions unknown | Trace validators/calculations and units, bounds, rounding, conversion |
| G-07 | HIGH | Timestamp semantics, timezone and Jalali display policy | Tenant timezone configured (Asia/Tehran default); occurrence/create times exist | Trace date conversion, user input/output and storage representation; PO policy |
| G-08 | HIGH | Identity, password and tenant-membership migration | User/role/permission structures source-visible; migration semantics unresolved | Trace login/password hashing, membership and session/token handling; migration decision |
| G-09 | HIGH | Deletion, retention, archive, recovery and purge policy | Project deletion dependencies and attachment trash partial paths audited | Map all entity lifecycle and storage recovery windows; PO retention decisions |
| G-10 | HIGH | Tenant-safe constraints on indirect links and polymorphic entities | Logical relations source mapped; integrity matrix not exhaustive | Trace all indirect paths and confirm FK/unique candidates |
| G-11 | MEDIUM | File storage-provider migration, checksum, size limits, scanning and orphan reconciliation | Google Drive provider and metadata paths observed | Detail source validation, storage operation failure/retry, access policy |
| G-12 | MEDIUM | Configuration/catalog scope and revision semantics | Workflows/forms/event types/information types versioned in places | Establish global vs tenant-local catalogs and mutable/published revision contract |

## Prioritized continuation
1. Continue route/service/repository mutation tracing to close G-01 and G-10 (read-only).
2. Trace procurement receipt/order pathways end-to-end and enumerate all fields/state/replay and dependency edges (G-02).
3. Trace event payload, form response, amendments, voids, reports and attachments together (G-03/G-04).
4. Subsequently close identifier, precision/timezone, identity and retention policy gaps (G-05–G-09), recording decisions separately from source facts.

## Guardrails and limitations
No executable SQL/DDL, migration code, database provisioning, live production data access, production writes, deployment, VPS action or tests were performed. This increment is evidence progress only; it does not declare schema/migration readiness or Task 2.5 completion.
