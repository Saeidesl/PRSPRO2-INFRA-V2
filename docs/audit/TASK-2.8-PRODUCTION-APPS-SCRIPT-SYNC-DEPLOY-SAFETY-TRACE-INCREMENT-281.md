# Task 2.8 Increment 281 — Production Apps Script sync/deploy workflow safety trace

Date: 2026-11-24  
Audit mode: READ-ONLY  
File inspected: `.github/workflows/sync-apps-script.yml` in `PRSPRO2BACKEND-PRODUCTION`

## Workflow control sequence
- Manual `workflow_dispatch` only; user-selected phase is `push_only` or `push_and_deploy`, with a required boolean confirmation.
- Job-level condition requires manual dispatch, confirmation true, and either push_only or deploy mode with repository variable `ALLOW_APPS_SCRIPT_DEPLOY=true`.
- Serial concurrency group prevents overlapping sync jobs and does not cancel in progress.
- Checks required secrets/variable, source syntax, manifest JSON, duplicate file names and minimum source count, then installs clasp and validates credentials.
- Creates isolated `deploy` source and `.clasp.json`, then executes `clasp push --force`.
- Web-app deployment step is conditional on deploy mode and the allow-variable, with explicit deployment ID secret.

## Material safety observations
1. **Push-only is not read-only:** `push_only` still runs `clasp push --force` to the Apps Script target configured by secret `APPS_SCRIPT_ID`. It updates script source; it is not merely a validation or dry run.
2. **Target isolation depends on configuration:** checks require script ID and, for deploy phase, deployment ID; static code explicitly refuses two hard-coded deployment IDs. This does not prove the configured secrets currently target an isolated environment.
3. **Deploy phase has layered gates:** explicit user input, confirmation, repository variable, deployment ID secret and source validation. Static review cannot prove GitHub variable/secret values, target isolation, OAuth account scope or current deployment identity.
4. **Credential handling:** workflow writes clasp credentials with restrictive mode and performs format checks, but runtime token exposure, OAuth scope and secret lifecycle cannot be verified statically.
5. **Force push:** clasp source push is forceful; recovery/versioning and pre-push snapshot/reconciliation are not established in this workflow file.
6. **No dry-run or automatic test-to-deploy chain is evidenced in this file:** validation checks and deployment target controls are present, but this review does not establish a runtime deployment rehearsal, rollback, or deployment audit trail beyond description.

## Assurance targets
- SY-01: verify actual configured Script ID/deployment ID mapping via secure, non-disclosing environment inspection (do not reveal secret values).
- SY-02: confirm authorization and scope of clasp credentials, secret rotation and protected workflow dispatch permissions.
- SY-03: verify pre-sync backup/snapshot and recovery procedure before `clasp push --force`.
- SY-04: confirm that the workflow's intended production source target is explicitly documented and that push_only semantics are understood.
- SY-05: test failure behavior in a disposable Apps Script project, including interrupted push and deployment rollback.
- SY-06: review protection/approval policy for workflow dispatch and repository variable toggling.

## Status
Static configuration only. No workflow run, credential access, source push, deployment, live data access, or production modification occurred. This increment is an audit observation, not a safety certification. G-01/G-10 remain OPEN; E3 runtime assurance NOT VERIFIED.
