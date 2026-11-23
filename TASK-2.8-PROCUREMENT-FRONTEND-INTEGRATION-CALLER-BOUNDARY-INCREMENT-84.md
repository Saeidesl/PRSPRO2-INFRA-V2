# TASK-2.8-PROCUREMENT-FRONTEND-INTEGRATION-CALLER-BOUNDARY-INCREMENT-84

Date: 2026-11-23
Evidence: E1 bounded read-only frontend source inspection
Reference repository: PRSPRO2-PRODUCTION
No runtime tests, production modifications or migrations.

## Findings
- index.html asset inventory includes no standalone procurement.js module in the sampled production bundle. Procurement caller integration appears in bounded excerpts across events, material-planning, project-detail and reports modules.
- events.js excerpts consume event submission result procurementIntegration status and surface failure handling in submit progress.
- project-detail.js filters synthesized procurement timeline records (IsProcurementTimeline) from ordinary event handling in the sampled excerpt.
- reports.js reads procurement needs/calculations/orders from project report payload and presents procurement information.
- material-planning.js endpoint-family extraction primarily shows material-specification and project paths; direct procurement mutation/read caller coverage is not exhaustive in this snapshot.
- These snippets indicate integration boundaries and projection handling but do not establish complete frontend/backend procurement contract or consistency.

## V2 requirements
1. Procurement UI/API contracts should be explicit, independent from event submission side effects, with durable status/reconciliation.
2. Reporting read model should join transactional procurement records and clearly identify projection/source.
3. Test failure and retry presentation for event-driven procurement integration; distinguish event success from downstream procurement failure.
4. E1 only, no tests. G-01 remains blocker; G-02 procurement integrity open/high; no production changes.
