# Task 2.8 — Project Deletion, Event Idempotency & Workflow Assertion Crosswalk Increment 25
Date: 2026-11-22
Scope: Read-only inspection of actual assertions in selected backend tests.

## Project deletion assertions observed (`tests/project-deletion.test.js`)
- dependency collector scopes events/event data/attachments/material needs/specifications/calculations to project and tenant in sampled fixture;
- preserves shared orders containing lines associated with other project needs;
- derives order dependencies from items/allocations rather than relying solely on order ProjectID;
- ignores deleted orders and treats VOID events as inactive.
Limit: inspected file is short and sampled; does not establish runtime deletion orchestration, fault recovery, external storage behavior, full dependency graph or authorization coverage.

## Event idempotency assertions observed (`tests/event-idempotency.test.js`)
- replay returns prior event without repeated create;
- idempotency scoped by tenant and creator;
- interrupted PROCESSING request is not falsely reported complete;
- successful keyed creation sets COMPLETED;
- completion-state write failure does not return success.
Limit: harness stubs `_createUnlocked_` and models completion persistence; it does not test production-side effects, database uniqueness, concurrency beyond the stub lock, or recovery from every failure point.

## Workflow enforcement assertions observed (`tests/workflow-event-enforcement.test.js`)
- active workflow-step rules checked before persistence;
- progress tenant/project scoped;
- event preserves workflow/step/version context;
- repeat rules and manual completion semantics;
- manual step completion route and EVENT_CREATE permission;
- event insert revalidates workflow under shared spreadsheet write lock;
- selected sources are syntax-parse checked within test assertion (this test itself was not executed in this audit).

## Cross-cutting V2 implications
- Model workflow/event state transitions with transactional constraints and concurrency control.
- Preserve event idempotency operation state and replay identity while binding key to tenant, creator and payload hash.
- Deletion/archive needs full dependency preview, shared procurement graph protection, transactional state changes, durable external-storage outbox and reconciliation.
- Build test coverage for fault-injection, authorization, cross-tenant isolation, shared dependencies, recovery and migration validation.

## Limits
Source-only test inspection; no tests executed. G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED. No live data, DDL, migrations, provisioning, deployment or production modifications.
