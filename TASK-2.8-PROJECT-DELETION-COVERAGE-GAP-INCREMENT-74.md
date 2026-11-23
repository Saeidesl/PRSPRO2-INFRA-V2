# Task 2.8 — Project Deletion Dependency Coverage Findings (Increment 74)

Date: 2026-11-23
Evidence classification: E1 source inspection of ProjectDeletionService.js (SHA 62482bfb42c2c7bb3329becbb2f821e1efbb4c8e) and FormRepository.js (SHA 4052d7e420cdf7c00d4f6d751494aa718d044cb2), DatabaseSetup header evidence.

## Confirmed scope of collector (source bounded)
The deletion dependency collector explicitly gathers: project Events, EventData, material needs, orders/items/allocation subset, receipts, material specs, calculations, and PROJECT/EVENT attachments. It protects shared order items with allocations to other projects.

## Not represented in the sampled collector/deletion sequence (coverage gaps, not asserted as orphaned)
- ProjectInformation (30_ProjectInformation) and its revision ledger (31_ProjectInformationHistory)
- EventFormData responses (35_EventFormData) — FormRepository exposes findResponsesByEvent and getEventResponses
- Project reference sync log (38_ProjectReferenceSyncLog)
- Other potentially project-linked records outside the collector require further inventory.
This is an evidence-backed gap in the deletion dependency coverage visible in the inspected collector; the impact on deletion is not runtime verified. Since project rows are physically deleted after the listed dependencies succeed, V2 must not replicate this behavior without a complete dependency registry and policy.

## Additional failure boundary
- AuditService.log is invoked after dependency handling and, if no dependency failures, after physical project row deletion. The sampled code does not show a transactional guarantee between physical deletion and audit persistence.
- Failed dependency handling can leave a partially changed aggregate and returns a conflict. A safe retry is not established in this source excerpt.

## V2 mandatory safeguards
1. Generate dependency deletion plan from centralized dependency registry; require all owned and shared dependencies accounted for.
2. Default archival/soft-delete project aggregate; avoid physical deletion until retention policy and audit durability are satisfied.
3. Include ProjectInformation/history, event-form responses, reference-sync log and all discovered children in explicit disposition rules (retain, archive, cascade, detach, block).
4. Durable operation ledger and idempotent repair/retry; transactional audit/outbox.
5. Negative tests: nonzero counts for omitted classes, cross-tenant records, shared procurement, storage failure, audit/outbox failure, partial retry, and concurrent delete/update.

## Status
Source-only observation; no test execution, production change, data mutation, migration or deployment. G-01 and G-10 remain BLOCKER; G-02/G-03/G-04 remain open/high. Task 2.8 remains incomplete and schema readiness NOT APPROVED.
