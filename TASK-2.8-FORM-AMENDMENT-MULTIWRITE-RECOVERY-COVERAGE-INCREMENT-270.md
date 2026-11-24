# Task 2.8 — Event-Form Amendment Multi-Write Recovery and Test Coverage Gap (Increment 270)

## Source trace
FormService.updateEventFormResponse validates source/amendment event, tenant/project match, amendment type marker/configuration, allowed form IDs, existing response and form version, submitted fields/visibility/required/type validation. Under ScriptLock it rereads the response, reconciles PROJECT_CODE behavior, checks LastAmendmentEventID replay and response conflict, persists the response for non-replay, then iterates fields to synchronize project information/deactivation/project code and finally writes audit for non-replay. Replay still attempts project-information synchronization, intended to recover partial writes.

## Bounded coverage observation
Selected `tests/reusable-forms.test.js` covers malformed responses, required forms, definition snapshots, stale/unassigned form, conditional visibility and idempotent response persistence per event/form. The inspected test file does not evidence amendment-event validation, amendment replay convergence after partial project-information writes, amendment audit failure or field-by-field failure injection. This is a bounded observation, not an exhaustive repository-wide absence claim.

## Test blueprint
- AM-01 Same-tenant/project amendment-event validation.
- AM-02 Explicit IsFormAmendment configuration and AmendableFormIDs.
- AM-03 Legacy marker compatibility behavior and false-positive candidate names.
- AM-04 Version stale conflict and validation of hidden/unknown fields.
- AM-05 PROJECT_CODE unchanged preserves current project code; changed code validates uniqueness and synchronization.
- AM-06 Non-replay response write failure: no downstream synchronization.
- AM-07 Response write succeeds, ProjectInformation save/deactivate fails: replay repairs safely.
- AM-08 Failure after first of multiple information-type synchronization updates: replay converges all fields without duplicate history.
- AM-09 Audit failure after response/information writes: recovery and audit completeness.
- AM-10 Same amendment ID/same payload replay; no duplicate audit/revision.
- AM-11 Same amendment ID/different payload conflict.
- AM-12 ExpectedUpdatedAt stale-response conflict.
- AM-13 concurrent amendment events against same response: deterministic conflict and preserved revision lineage.
- AM-14 lock timeout/failure and release behavior.
- AM-15 cross-tenant, inaccessible project, and amendment event in different project fail closed.

## V2 requirement
Represent amendments as immutable amendment operation/event with versioned response and transactional relational ProjectInformation updates plus audit/outbox. External reconciliation is durable and idempotent. A replay repairs only incomplete operation state, with operation-state tracking rather than blind per-field writes.

## Verification
Static source trace and test-file review only; no test execution, failure injection or production modification.
