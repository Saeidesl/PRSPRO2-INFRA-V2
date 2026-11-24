# Task 2.8 Increment 280 — CI workflow definition and alerting scope audit

Date: 2026-11-24  
Audit mode: READ-ONLY  
Reference repository: `Saeidesl/PRSPRO2BACKEND-PRODUCTION` (main; workflow definitions fetched from current main)

## Evidence inspected
- `.github/workflows/agent-monitor.yml`
- `.github/workflows/backend-check.yml`
- `.github/workflows/pms-agent-suite.yml`
- `.github/workflows/procurement-performance-diagnostics.yml`

## Findings (static workflow review)
1. **Agent monitor scope is limited:** scheduled daily at 08:15 UTC and manual; it reports completed failures in a rolling 24-hour window, excludes its own run, and caps report list at 20. It does not alert on cancelled, timed-out, skipped or queued runs, and no-failure runs leave any prior issue open for human review. This is an alerting design limitation, not a verified defect.
2. **Alert permissions:** monitor workflow declares `actions: read`, `issues: write`, `contents: read`; issue creation/update is expected. Static review did not verify token runtime behavior or repository policy.
3. **Path-trigger redundancy:** backend-check has the `sync-apps-script.yml` path repeated. PMS agent suite likewise repeats `scripts/**`. These are harmless-looking redundant entries, not a functional failure established by execution.
4. **Trigger coverage differs:** source-check and agent-suite workflows use path filters, so changes outside listed paths may not trigger their checks. The workflows also expose manual dispatch. This is a coverage/design observation; scope adequacy requires maintainer decision.
5. **Procurement diagnostics are bounded:** explicit files/paths are listed; no broad whole-repository trigger. Static review did not validate whether all related production changes are included in these filters.
6. **CI execution applicability remains commit-specific:** this workflow review does not establish successful execution on the later audited snapshot SHA `8253eb3718d1a2a1bd5de03b6b5090ba69b94b67`. Previously confirmed historical run results on `4a131aed6f0e1f7e0e05aad7dc2baacb35671dd7` remain bounded evidence.

## Suggested test/assurance targets
- WF-01: confirm trigger behavior for representative included/excluded paths on a disposable branch or test repository.
- WF-02: verify monitor issue update/create, permissions, pagination, and duplicate alert handling.
- WF-03: clarify policy for stale open alert issue after a subsequent no-failure day.
- WF-04: audit trigger path lists against an authoritative source inventory; document intentional exclusions.
- WF-05: verify timeout/cancellation alert policy and whether failure-only monitoring is sufficient.

## Restrictions and status
No production repository modification, workflow dispatch, deployment, data access, or tests performed. Findings are static configuration observations only. G-01/G-10 remain OPEN; E3/runtime assurance NOT VERIFIED; schema readiness NOT APPROVED.
