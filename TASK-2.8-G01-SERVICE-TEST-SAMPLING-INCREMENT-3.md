# Task 2.8 — Service & Test Evidence Sampling (Increment 3)

Date: 2026-11-22
**Scope:** READ-ONLY source evidence sampling and test filename inventory. Not exhaustive.

## Service source sampling
Read-only source retrieval and inspection completed for:
- `AccessControlService.js`: request-level caches for user/admin/roles and current-user resolution via AuthService. Exact full access-control matrix remains pending.
- `EventService.js`: large service (retrieved source length ~66.8 KB); only opening logic sampled in this pass; operation-level review pending.
- `ProcurementService.js`: tenant-scoped needs/orders/receipts; lifecycle source review not complete.
- `AttachmentService.js`: source retrieved; owner/lifecycle operation mapping pending.
- `ProjectDeletionService.js`: dependency-aware deletion surface; listed affected sheet families include events/event data/attachments/material needs/orders/items/allocations/receipts/material specs/calculations. It explicitly avoids turning schema/read failures into empty dependency lists.
- `WorkflowService.js`: management bootstrap reads tenant-scoped workflows/departments and active project event types/information types.
- `ProjectInformationService.js`: information history/approval and file-based data type handling observed.

These are source observations, not conclusions of correctness or test coverage.

## Test inventory (tree evidence)
The backend tree includes tests with names covering:
- Access-control request cache
- Attachment service
- Dashboard performance contract
- Event form definition batching/cache
- Event idempotency and schema recovery
- Event project picker projection and form prefetch
- Forms API contract
- Performance diagnostics
- Google Drive storage folders
- Google Sheets cache invalidation and column index
- Information cardinality/file types/type foundation
- Material planning and schema cache
- Print template batching/revision
- Procurement event integration and ProcurementService
- Project timestamps/draft information/deletion/image gallery/reference copy
- Reports, reusable forms, schema contract, single project entry
- Telegram bot/service/notification
- User validator
- Workflow event enforcement/rules

**Important:** filenames alone do not prove tests pass or identify exhaustive test assertions. No test files were executed.

## Reconciliation observations and unresolved gaps
- Cross-domain project deletion potentially spans multiple procurement/event/attachment/material tables; exact transaction/rollback semantics and cross-tenant safety need operation-level review.
- Tenant scope is documented in service headers/comments for several sampled domains but must be verified for every operation and relational edge.
- Existing tests indicate some domain contracts are represented; route-to-test mapping and CI execution evidence remain unverified.
- Sampled source is not sufficient to close G-01 or any blocker.

## Next
Build focused operation-to-evidence matrix for mutation families, beginning with project deletion and attachment mutation, then procurement receipt/order lifecycle and event amendment/void, with exact source method and test mapping where evidence supports it. No runtime tests, SQL/DDL, migration, production writes or deployment.
