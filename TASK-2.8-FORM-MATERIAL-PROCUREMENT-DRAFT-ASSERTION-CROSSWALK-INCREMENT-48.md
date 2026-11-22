# Task 2.8 — Form, Material Planning, Procurement Integration & Draft Project Assertion Crosswalk (Increment 48)
Date: 2026-11-22
Status: assertion-source inspection only; no tests executed.

## Reviewed test sources
- `tests/forms-api-contract.test.js`
- `tests/material-planning.test.js`
- `tests/procurement-event-integration.test.js`
- `tests/project-draft-information-flow.test.js`
- `tests/reusable-forms.test.js`

## Assertion scope
### Forms API contract
Source assertions cover reusable form schema/API wiring, active form dependency protection, event response persistence before idempotency completion, permission-guarded amendments, optimistic concurrency and replay indicators, and amendment configuration. The assertions are source-order/text checks, not live route or persistence tests.

### Material planning
The inspected test harness models repositories, a script lock and audit stub. Assertions cover specification creation/versioning, disabled early-order behavior and validation, with revision/history cases in the suite. The source harness cannot establish actual concurrent writes, data integrity or live calculations.

### Procurement-event integration
Mock-based assertions cover quantity/form validation and configured integration pathways, including rejection of invalid information-type shapes. The actual integration with event persistence, project central information, calculation history and downstream order/receipt lifecycle remains unverified by this source inspection.

### Draft project lifecycle
Source assertions cover schema lifecycle state, draft validation/activation, API creation/completion, own-draft access, draft event-type allowance, draft event status behavior, project code updates from events and tenant-scoped duplicate project-code checks under a lock. They do not exercise actual API requests, authorization or storage concurrency.

### Reusable forms
Mock harness assertions cover malformed response rejection, required form enforcement, validated values/definition snapshots, stale or unassigned forms, and response/amendment behaviors in the suite. Mocked repositories and project-access helpers limit conclusions; tenant isolation, DB constraints, failure atomicity and real amendment event effects remain unverified.

## Evidence limits
E2 assertion source inspection only. No runtime test execution or E3 evidence. No exhaustive route-to-assertion mapping. G-01/G-03/G-02 remain open.

## Safety
Production reference repos are read-only. No test execution, live data, DDL, migrations, provisioning or deployment.
