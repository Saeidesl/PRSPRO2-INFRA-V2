# Task 2.8 — Workflow event-rule replacement/version clone transactional boundary — Increment 183

Date: 2026-11-23. Read-only source trace of WorkflowStepEventRuleRepository and WorkflowService step-rule save/version cloning paths.

## Observations
- Rule repository header list includes ID, TenantID, WorkflowID, WorkflowVersion, WorkflowStepID, EventTypeID, rule semantics/order fields, Status/timestamps, AllowRepeat.
- cloneWorkflowVersion filters tenant/status on source and target draft, creates copied rows and inserts in batch. Repository source lookup uses workflow/version then service-side tenant filtering.
- replaceForStepVersion acquires script lock, loads sheet rows, soft-deactivates active rows matching tenant/workflow/step/version, then appends replacement rows, invalidates record cache and releases lock.
- WorkflowService validates workflow/step tenant and relationship, derives requested draft/published version, builds normalized rule records; after repository replacement it may update Workflow draft/lifecycle state and then logs audit sequentially.
- This means the lock protects the repository replacement operation, but does not establish a transaction spanning replacement, workflow patch and audit.
- Tenant filters and version match are explicit in bounded operations; DB uniqueness/FK guarantees are not evidenced.
- Partial failure possibilities across cloned rule insert, workflow state changes and audit remain unverified; recovery semantics not established.

## V2 implications
- Enforce composite tenant-qualified relational consistency and version-key constraints.
- Preserve atomic replacement and version-cloning semantics; draft workflow patch and audit should join transaction.
- Validate event-type and step tenant/workflow/version associations at DB and service boundaries.
- Define retry/recovery and immutable published version behavior.

## Status
G-01/G-10 remain open. Source-only; no runtime testing.
