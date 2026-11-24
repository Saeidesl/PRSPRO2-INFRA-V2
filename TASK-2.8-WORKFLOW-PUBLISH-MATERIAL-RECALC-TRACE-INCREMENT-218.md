# Task 2.8 Increment 218 — Workflow publication and material recalculation source trace

Date: 2026-11-24

## Sources
- WorkflowService.js blob `eb8373c14085975a9eb521d5ba20ec2e5146bfad`
- MaterialPlanningService.js blob `ff083babf9ef6c3e9e0763ae3c1773db83fc5485`

## Workflow publish observations
- Resolves current user and tenant-checks workflow.
- Requires active tenant-scoped steps.
- Uses draft/current version.
- Validates active event rules against tenant-active event types and active workflow steps.
- Publishes version, stores published repeat-reference policy snapshot, clears DraftVersion, updates lifecycle/status and audit.
- Atomicity between workflow version, rule snapshots and audit, publication rollback, concurrent draft edits and assignment invariants remain unverified.

## Material recalculation observations
- Recalculation acquires ScriptLock (10-second try) and releases in finally.
- Unlocked method resolves user/project, validates confirmed quantity and source event tenant/project.
- Uses active latest spec revisions grouped by SpecKey and requires active specs.
- Reads tenant/project material needs; marks prior confirmed need versions and estimated needs SUPERSEDED where applicable, then proceeds to generate/record confirmed needs (the method's remainder must be traced for full ledger/replay semantics).
- Exact precision/unit normalization, calculation provenance, locking scope, audit behavior, failure recovery and procurement projection linkage require further trace/tests.

## Evidence gaps
Workflow publishing and material recalculation have multi-step persistence and lifecycle rules; no runtime tests or fault injection were performed. G-01/G-10 and related G-02/G-03 remain open.

## Safety
Read-only source trace; no production modifications, tests, SQL/DDL, migration, live data, provisioning, VPS or deployment.
