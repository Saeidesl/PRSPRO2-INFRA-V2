# Task 2.8 — Increment 244: Form amendment and reference-copy partial recovery crosswalk
Date: 2026-11-24
Scope: read-only inspection of FormService form-response amendment and direct template update paths, ProjectService reference-copy processing.

## Static findings
- Direct form-field template update path acquires ScriptLock, checks event/form version and response timestamps, writes response then synchronizes project information/project code, then audit. Failure between response and synchronization/audit can produce partial state; no transaction in inspected path.
- Amendment-event form response path acquires ScriptLock, re-reads current response and checks replay against LastAmendmentEventID and payload consistency. It persists response (or reuses on replay) then synchronizes project information field-by-field and audits. Comments explicitly attempt finishing synchronization on replay after an earlier partial sync. This is an explicit retry design, but recovery/failure-injection coverage not verified.
- Project reference-copy path acquires ScriptLock and checks policy/status. Per-information copy can create information row, history, sync-log (for LIVE_SYNC), and update project copy status sequentially. Error handler attempts mark the affected plan item FAILED and persist copy status; failure of that recovery write is swallowed. No operation-wide rollback evidenced.
- Profile image copy branch updates item status after AttachmentService copy; unlike main path excerpt, it is handled as a distinct branch. Failure recovery needs specific test.
- LIVE_SYNC idempotency key is composed from source project, target project, information type and source revision. Duplicate/conflict handling and persistence atomicity require further repository/test tracing.

## Risk candidates
1. Form response saved before central-info synchronization; replay/repair path is present in amendment flow, but direct template path differs and lacks an explicit recovery replay in inspected snippet.
2. Reference-copy operations write multiple records sequentially; recovery status write may fail and is swallowed.
3. Reference-copy idempotency/conflict protection under retry needs tests.

## Verification boundary
Static source only. No tests run or failure injection. No production changes. G-01/G-10 OPEN; schema readiness NOT APPROVED.
