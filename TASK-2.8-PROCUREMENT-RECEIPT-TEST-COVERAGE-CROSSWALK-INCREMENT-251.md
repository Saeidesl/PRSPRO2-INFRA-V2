# Task 2.8 — Procurement Receipt Test Coverage Crosswalk (Increment 251)

Date: 2026-11-24
Evidence: E1 test-source retrieval.

## Scope
Static inspection of tests/procurement-service.test.js and tests/procurement-event-integration.test.js in the read-only backend reference repository, reconciled with receipt mutation findings in Increments 246–250.

## Findings
- The inspected procurement-service test file covers workspace request-scoped collection reuse, project visibility read optimization, allocation caps/need-key compatibility and workspace reuse.
- The inspected file does not contain test cases exercising receipt creation, receipt void/status accounting, order eligibility for receipt mutations, single-vs-batch allocation parity, partial write recovery, or audit failure.
- The inspected procurement-event-integration test file covers central-information procurement configuration and legacy integration behavior; its assertions explicitly keep event-driven receipt integration separate from standard receipt routes.
- This is a bounded test-file coverage observation, not an exhaustive repository-wide absence claim. Receipt-specific tests may exist elsewhere; current audit has not yet exhaustively searched every test file or run tests.

## Test gap candidates
Prior increments identify test candidates: status filters/VOID, order state eligibility, single/batch parity, allocated vs general receipt, replay/idempotency, cumulative quantities, project allocation, tenant-negative checks, audit failure/recovery and concurrency.

## Verification
Static test-source inspection only; tests not executed. No production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
