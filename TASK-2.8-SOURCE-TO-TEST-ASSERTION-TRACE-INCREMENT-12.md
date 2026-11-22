# Task 2.8 — Source-to-Test Assertion Trace Sampling Increment 12
Date: 2026-11-22

## Scope
Read-only assertion inspection in five backend tests: workflow-event-enforcement, procurement-service, attachment-service, project-deletion, schema-contract, and event-idempotency. Sampled tests are compared to source concepts, not run.

## Traceable assertion evidence
| Test file | Assertions visibly tied to source contract | Status |
|---|---|---|
| tests/workflow-event-enforcement.test.js | workflow rules validation; workflowProgress tenant/project visibility; event step/version persistence; manual step completion permission; lock revalidation; syntax parsing | Source/assertion trace visible; test NOT run |
| tests/procurement-service.test.js | Isolated service harness with tenant-filtered procurement repository stubs; visibility caching/bulk project reads; individual assertions not exhaustively traced in this sample | Partial |
| tests/attachment-service.test.js | Explicit denial for event attachments on inaccessible project; allowed listing for visible project | Source/assertion trace visible; test NOT run |
| tests/project-deletion.test.js | Cross-tenant event exclusion, event-data and attachment dependency collection, shared purchase-order preservation, allocations/receipts linkage, deleted-row handling, VOID retry cases (sample) | Source/assertion trace visible; test NOT run |
| tests/schema-contract.test.js | Schema fields/revisions and calculation history, append-only schema headers, guarded/manual Apps Script sync workflow, idempotency schema contract | Source/assertion trace visible; test NOT run |
| tests/event-idempotency.test.js | Test harness models replay, creator/tenant key scope and completion-update failure; assertion excerpts visible but full test suite not executed | Partial |

## Limitations
- This is a sampled test/source trace, not a complete matrix of all Router branches, service methods, repository calls, business invariants or schema entities.
- Static assertions and test harnesses can be brittle or incomplete; they do not prove production runtime correctness.
- Tests are not executed in this review. Production repository remains read-only.
- Remaining domains and negative cases need systematic inventory, particularly cross-tenant polymorphic relations, mutation authorization, attachments/storage lifecycle, transactional audit/history and procurement graph integrity.

## Status
G-01 remains OPEN, with better sampled traceability evidence. G-10 remains OPEN. Schema readiness NOT APPROVED.
