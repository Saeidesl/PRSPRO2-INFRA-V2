# Task 2.8 Increment 282 — Sync workflow safety-test coverage cross-check

Date: 2026-11-24  
Audit mode: READ-ONLY  
Reference: `tests/schema-contract.test.js` and `.github/workflows/sync-apps-script.yml` from production backend repository main.

## Source evidence
The inspected test contains a test named `production Apps Script sync is manually triggered and guarded`. It statically asserts workflow dispatch, main ref, confirmation input, boolean/default false, job confirmation condition, deployment allow-variable condition, force push/deploy commands, two deployment-ID refusal string and absence of push trigger in the parsed trigger block.

## Coverage gaps (not verified defects)
- Assertions primarily verify exact strings and textual presence, not YAML semantics or execution of the workflow.
- It does not verify actual secret/variable values, credential permissions, configured target identity, dispatch authorization, force-push impact, rollback or recovery.
- The test verifies that the deploy command and refusal text are present, but not actual deployment behavior or the correctness of the hard-coded excluded deployment IDs.
- Trigger extraction/splitting by string and simple assertions may be sensitive to formatting and do not replace a parsed workflow or simulated GitHub Actions run.
- Two guessed candidate files `tests/deployment-workflow-safety.test.js` and `tests/sync-apps-script.test.js` were not found; this is not evidence that no related tests exist, only that these exact paths are absent.

## Assurance targets
- SYT-01: parse workflow with YAML-aware tooling and test evaluated conditions.
- SYT-02: simulate positive/negative dispatch condition cases in a disposable test workflow.
- SYT-03: validate workflow path/target checks in a disposable Apps Script project.
- SYT-04: verify protected dispatch policy, target identity and secret scopes without exposing secrets.
- SYT-05: recovery/rollback and force-push failure tests.

## Status
Static test-source inspection only; test execution and runtime assurance NOT VERIFIED. No production modifications or data access. G-01/G-10 remain OPEN.
