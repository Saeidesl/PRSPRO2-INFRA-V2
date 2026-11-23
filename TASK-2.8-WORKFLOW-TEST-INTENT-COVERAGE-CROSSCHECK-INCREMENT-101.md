# TASK-2.8-WORKFLOW-TEST-INTENT-COVERAGE-CROSSCHECK-INCREMENT-101

Date: 2026-11-23
Evidence: E1 source/test inspection
Sources: tests/workflow-event-enforcement.test.js SHA e7b6670ac2c211545a24d13c26f7d301a93508c; tests/workflow-event-rules.test.js SHA ec32d5cfe31c9fbc333514e17a92c1f612634127
Tests were inspected, NOT executed.

## Existing test intent observed
- workflow event rule schema/version awareness.
- Router permission and tenant-scoped API assertions.
- validator compatibility/order tests (including free display-order runtime test code).
- published workflow selection/project assignment.
- workflow event enforcement/progress/manual completion and source revalidation under shared write lock.
- JavaScript syntax parsing assertions for selected backend sources.

## Coverage gaps relative to source findings
- No sampled test directly asserts saveStepEventRules rejects a Step from a different version than target draft.
- No sampled test asserts publish filters steps to exact target version.
- No sampled tests for workflow update protected fields (Version/DraftVersion/PublishedVersion/LifecycleState).
- No sampled tests for concurrent workflow draft mutation/publish, audit failure, partial clone/replace or step insertion failure.
- No sampled tests for assignment duplicate race, cross-tenant repository helper usage, or version scope semantics.
- Source-pattern tests may assert implementation strings but do not prove runtime behavior or storage constraints.
- Tests were inspected only; not run.

## Next
Design targeted behavioral tests and transactional test harness for these gaps. G-01/G-03/G-10 remain open.
