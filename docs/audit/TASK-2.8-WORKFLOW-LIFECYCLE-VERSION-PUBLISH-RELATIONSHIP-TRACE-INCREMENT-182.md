# Task 2.8 — Workflow lifecycle/version publication relationship trace — Increment 182

Date: 2026-11-23. Read-only trace of WorkflowService create/update, management reads, event-rule persistence cross-references and publish operation.

## Observations
- Workflow create/update service performs tenant context checks and tenant-scoped duplicate-code scan; update validates target workflow tenant before mutation.
- Workflow management reads tenant-filter steps and assignments, including draft/published version selection.
- Publish resolves workflow tenant, active steps, draft/version value, active event types within tenant, and active versioned rules; checks that rule event types and steps exist in those filtered sets.
- Publish updates workflow publication/version/lifecycle and repeat-reference policy fields, then audit logging is sequential. Atomic coupling not proven.
- Step/rule repository source path inventory not conclusively established; names and header-level FK guarantees remain unresolved.
- Version policy, immutable historical publication snapshot, transaction boundary and consistency constraints cannot be inferred from these service excerpts.
- Code matching/scans were source-limited; no runtime or negative-tenant tests.

## V2 considerations (not approved schema)
- Tenant-qualified FK constraints for Workflow, Step, Rule and EventType relationships.
- Version-scoped uniqueness and state transitions, plus publish transaction preserving snapshot integrity.
- Explicit rules for draft mutation, published immutable versions, assignment applicability, and publish rollback.
- Ensure referential validation cannot be bypassed by repository-level operations.

## Status
G-01/G-10 remain open. Source-only.
