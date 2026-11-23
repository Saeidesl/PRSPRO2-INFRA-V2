# Task 2.8 Cascade Collector Tenant-Link Qualification & Failure Boundary — Increment 163

Date: 2026-11-23. Read-only source trace.

## Additional bounded findings from ProjectDeletionService._collect
- Events are selected by tenant and project ID and active status; event IDs are then used as the collector's key for event data and attachment associations.
- Event-data collection uses the active predicate and event-ID membership, but the inspected filter does not independently qualify TenantID for event-data rows. This relies on event-ID uniqueness/relationship integrity and is not evidence of tenant-safe relational enforcement.
- Attachments are separately filtered by TenantID and then by PROJECT owner or collected EVENT ID. The collected event IDs similarly depend on uniqueness and integrity of EventID association.
- Collector reads dependency sheets and has fail-closed behavior for read errors (does not silently replace read failure with empty dependencies).
- Sequential deletion records per-row failures and prevents project-row deletion if earlier failures are collected; nevertheless, this is a partial-operation workflow, not transactional rollback. External storage trash failures can occur after metadata status changes.

## Risks / V2 design
1. Require tenant-qualified FK/relationship constraints for event data and attachments, or equivalent explicit relationship validation and negative cross-tenant tests.
2. Ensure cascade collector queries are qualified by tenant through joins/constraints and are not reliant solely on opaque identifier uniqueness.
3. Test collector completeness and cross-tenant boundary for each dependent relation, including orphaned/invalid association rows.
4. Use durable deletion job state, transactionally capture dependency snapshot, progress and retries, and reconciliation for external storage.
5. Do not directly reproduce destructive cascade semantics until preservation/retention policies for procurement and files are explicitly modeled.

## Status
G-01/G-04/G-09/G-10/G-11 remain open. Schema readiness NOT APPROVED. Source-only; no runtime tests, live data/files, production modifications, migration or deployment.
