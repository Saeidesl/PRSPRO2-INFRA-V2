# Task 2.8 — Consolidated project/event/procurement/storage boundary crosscheck — Increment 194

Date: 2026-11-23.

## Purpose
Reduce audit fragmentation by crosschecking the previously reviewed project, event, procurement, attachment and workflow boundary evidence in a single focused pass. Read-only; source excerpts retrieved from production backend.

## Cross-module findings
- Project availability is a shared access predicate: tenant equality plus admin/creator or project allowed-user/department conditions. Project list, project picker, procurement visibility and attachment visibility rely on this or equivalent checks; V2 should centralize resource authorization rather than replicate predicates.
- Project lookup/create forms assemble tenant-filtered departments/workflows/statuses and conditionally load repeat-reference projects. Dashboard lookups also retrieve tenant-filtered workflow steps/statuses. In the reviewed source, workflow-step data is referenced through WorkflowStepRepository, whose direct implementation remains inaccessible by exact-path retrieval.
- Project status/code mutation from an event validates project and event tenant/project relationship, project visibility, duplicate project code within tenant, and uses a script lock; update and audit are sequential, not transaction-proven.
- Event-service crosscheck confirms workflow destination-step resolution is based on a tenant-owned workflow, active tenant/workflow/department assignment and matching step tenant/workflow. Invalid strict mappings fail rather than silently moving. Event-driven project changes are suppressed for draft projects in the reviewed configured-change path.
- ProcurementService applies tenant-bound project/event visibility checks, numeric nonnegative/positive validation, script-lock order creation and idempotency replay lookup. Existing allocations are recalculated across needs/orders to avoid treating invalidated order states as outstanding allocation. This source excerpt reinforces transactional and uniqueness requirements for V2; detailed procurement rules already recorded in earlier audit increments and are not duplicated here.
- AttachmentService visibility checks attachment tenant, parent project/event tenant and project access. Project-image copy checks source and target visibility/type/status, uses a SourceAttachmentID duplicate check, copies storage first, then inserts metadata/audit; on insertion failure it attempts storage cleanup. This is a compensation pattern rather than transaction/outbox proof.
- Cross-module access/relationship patterns are consistent with previously recorded candidate composite tenant constraints, polymorphic attachment pointers, and durable idempotency needs.

## Limitations
- Source-only evidence (E1); no runtime tests or negative tenant tests were executed.
- Direct WorkflowStepRepository and material-planning repository source retrieval remain unresolved in this access path; no inference of defect.
- No database constraints, transaction guarantees, deployment behavior, or live data were verified.
- G-01/G-10 remain blockers; schema readiness NOT APPROVED.

## Safety
Production repositories remain read-only. No production data, test execution, schema/DDL, migration, VPS, deployment, cutover, or merges.
