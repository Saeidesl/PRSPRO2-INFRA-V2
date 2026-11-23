# Task 2.8 Increment 113 — Event form response, amendment and central-information synchronization (2026-11-23)

## Scope
READ-ONLY trace of FormService.js response preparation/persistence, direct template field update, amendment-based form update, and response retrieval.

## Observations (E1)
- Event response preparation checks active form mappings, tenant, form status, required mappings, submitted form IDs, current form version, field visibility/requiredness, value validation and allowed information-type IDs. Maximum 30 form responses per event.
- Responses include a definition snapshot and form version.
- SaveEventResponses upserts response records, then audit, then synchronizes non-empty values to project central information and project code; these steps are sequential.
- Empty values in initial response save do not erase existing central values (skip); response snapshot can therefore differ from central projection.
- Direct template field update validates event/project/tenant, response existence, expectedUpdatedAt and form version, validates all fields/visibility/required values and expected central information revision, then updates response followed by project-code or central-information mutation and audit. ScriptLock is used.
- Amendment response update requires an amendment event in same tenant/project and configured form-amendment flag or legacy marker in type name/code. Explicitly configured amendment type restricts allowed forms. Form version and values are validated; ScriptLock re-reads current response; replay is keyed by LastAmendmentEventID and conflicting replay payload is rejected. It writes response and then syncs central info and audit; replay can retry sync.
- Project-code handling attempts to preserve authoritative current project code unless amendment intentionally changes it.
- Response retrieval tenant filters and returns parsed values plus stored definition snapshot.
- The direct and amendment update paths are not demonstrated as a single transaction spanning response, central-information updates and audit. Lock scope and cross-repository atomicity are not demonstrated. In amendment path, no explicit expectedUpdatedAt check is enforced when the caller omits that value; concurrency behavior beyond ScriptLock is not proven.
- Repository/storage uniqueness, durable replay constraints and audit recovery are not established.

## V2 implications
- Response table normalized or JSON strategy with event/form/version FKs, unique event+form response, snapshot retention.
- Transactional response + project information synchronization + project-code update + audit/outbox; retry-safe orchestration.
- Explicit amendment-event relation, eligible-form rules, idempotency/replay and concurrency control.
- Preserve submitted definition snapshot for historical rendering; separate mutable central projection from event record.
- Tests: tenant crossing, stale form version, missing required fields, hidden values, stale central revision, duplicate response, concurrent direct edits, replay with same/different payload, sync partial failure and project code preservation.

## Verification
E1 source-only; no tests executed; no production changes. Potential boundaries are not confirmed defects. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.
