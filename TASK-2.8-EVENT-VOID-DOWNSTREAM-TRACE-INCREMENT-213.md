# Task 2.8 Increment 213 — Event void downstream-effects source trace

Date: 2026-11-24

## Source
Read-only trace of EventService.js, blob SHA `1fe31ea9ea2a048a870ecbc47c11512bc2942108`.

## Observed guard and business rules
- Requires event ID and void reason.
- Resolves current user and checks event tenant.
- For project-linked events, checks linked project tenant and user project availability.
- Rejects already VOID events.
- Validates event type tenant and configured AllowVoid flag.
- Non-admin void must match registering department.
- Rejects void if a later event exists in another department.
- For project-linked state changes, reads configured expected changes and validates prior snapshot/current state consistency before restoring project snapshot fields.

## Mutation/downstream sequence observed
- May restore project snapshot and log project audit.
- Updates event status to VOID.
- May recompute current workflow-step completion and update/audit project status.
- Logs event VOID audit.
- Calls MaterialPlanningService.markEventVoided; errors are caught and surfaced as `materialNeedsReview.status=FAILED`, with a reconciliation audit entry, rather than failing the whole void response in the visible path.
- Returns event/project restoration and material-needs-review status.

## Risk/evidence gaps
- The event/project/workflow/material/audit sequence contains multiple writes and no transaction primitive is demonstrated in this source path.
- Material planning failure is explicitly treated as a separate reconciliation state; caller/UI persistence, retry/recovery and downstream procurement/report/form/attachment effects remain to verify.
- Snapshot restoration conflicts, event chain order and cross-department rule require exhaustive negative/positive tests.
- No tests were executed; this is source evidence only.

## Gate impact
G-03 (immutable event history/amendment/void semantics) and G-01 (operation/effect inventory) remain OPEN; G-10 tenant/department isolation remains OPEN.

## Safety
Read-only audit; no production changes, tests, DDL, migration, live data, VPS or deployment.
