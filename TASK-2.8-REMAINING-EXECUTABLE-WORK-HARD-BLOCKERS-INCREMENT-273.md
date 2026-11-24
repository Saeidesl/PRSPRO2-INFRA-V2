# Task 2.8 — Remaining Executable Work and Hard Blocker Register (Increment 273)

## Work still executable through read-only source review
- Continue exact source and caller/test crosswalks for accessible paths; preserve bounded evidence and commit-pin when repository metadata is available.
- Expand mutation/test crosswalk for additional accessible services, focusing on permission-negative and secondary write traces.
- Reconcile test suite inventories against the mutation risk matrix and avoid duplicate test blueprints.
- Read-back verify new audit artifacts and correct report numbering/references.

## Work blocked by environment or governance
### B-01 Runtime test execution
No execution environment/tool is currently available to run the production-reference test suite or scripted failure injection. GitHub file-reading/writing APIs cannot execute Node tests. The production-reference repository is read-only, so no test files, instrumentation or temporary changes may be committed there.

### B-02 Real concurrency/platform lock proof
Mocked source tests do not establish Google Apps Script lock semantics, Sheets behavior, or multi-request race behavior. An isolated staging environment and permission to run tests against it are required. Production experimentation is prohibited.

### B-03 Exhaustive branch/commit-pinned source evidence
Captured source tree inventories and selected retrievals do not provide exact immutable commit hashes and complete route/handler/caller/resource/tenant/write/test dispositions for all branches. Retrieval constraints and some exact-path sources remain inaccessible.

### B-04 Business policy decisions
G-02 procurement receipt/status/allocation/reversal policy; G-03 immutable form/event amendment and project-information history rules; G-04 file retention/purge/lifecycle; G-10 global-vs-tenant classifications require PO confirmation. Recommended defaults are documented but not approved.

### B-05 Migration schema readiness
Until B-01 through B-04 and existing critical gates are resolved, PostgreSQL schema readiness cannot be approved. No DDL/schema implementation/migration is authorized or justified by this audit alone.

## Next safe execution sequence when blockers clear
1. Run the exact existing test suite against isolated/sanitized environment and record commit/environment.
2. Implement the supplemental failure-injection tests in V2/isolated harness (not production reference).
3. Resolve PO policy decisions.
4. Complete G-01 crosswalk with immutable source refs and dispositions.
5. Run negative tenant/permission, race, replay/retry, data integrity and performance checks.
6. Reassess schema readiness and Task 2.8 closure; keep residual risks explicit.

## Status
Task 2.8 remains IN PROGRESS. The blockers above are practical and governance constraints, not test failures. No production writes or live data accessed.
