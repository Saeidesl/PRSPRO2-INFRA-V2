# Task 2.8 Increment 216 — Event creation/idempotency and downstream persistence trace

Date: 2026-11-24

## Source
Read-only trace of EventService.js blob `1fe31ea9ea2a048a870ecbc47c11512bc2942108`.

## Create validation and captured context
- Requires current user and tenant.
- Non-admin performed-by identity is constrained to current user; admin selection may be supplied.
- Event type tenant/status, project linkage/tenant/availability, workflow rule and draft-allowed checks are performed where applicable.
- Department authorization and tenant department checks; performed user tenant checked.
- Reusable form responses are prepared/validated before persistence; project-only restrictions apply.
- Variant rows are validated, bounded (up to 500), duplicate color/size pairs rejected and quantity rules enforced.
- Event records capture project/workflow IDs/version, event-type version, department, occurred/created timestamps, variant details and previous project-step/status/department snapshot.
- Repository insertion revalidates project step/availability/workflow under repository lock in project-linked path.

## Downstream sequence
- Form responses and central project information are persisted after event insertion.
- Procurement-event validation is performed before event insert; apply integration occurs afterward. Integration failure is caught and recorded as FAILED with an audit entry, leaving event persisted.
- Project state updates based on configured event changes and workflow progression; project update and audit follow.
- Event audit and project audit are separate writes.
- Idempotency key starts with PROCESSING; final marker becomes COMPLETED only after event/form/integration/project/audit steps. If final marker fails, response warns not to resend and to inspect existing event.
- Telegram notification occurs after successful persistence; best-effort failure is swallowed/logged.
- Response surfaces form persistence, central-information and procurement integration status.

## Potential partial-write and recovery surfaces
The sequence is multi-step and no transaction primitive is demonstrated. The source explicitly handles procurement integration failure, and idempotency PROCESSING state protects against blind retries, but recovery/compensation and stuck-Processing reconciliation are not fully demonstrated.
- Form/central information may be partially persisted after event.
- Procurement integration may fail.
- Project mutation/audit or final idempotency completion may fail.
- Notification may fail (best effort).
These are source-based failure surfaces, not observed failures.

## Gate impact
G-03 immutable event snapshots/amendments and G-01 complete effect/caller mapping remain open; G-10 tenant/permission negative tests remain open.

## Safety
Read-only source trace. No production modification, tests, DDL/migration, live data, provisioning, VPS or deployment.
