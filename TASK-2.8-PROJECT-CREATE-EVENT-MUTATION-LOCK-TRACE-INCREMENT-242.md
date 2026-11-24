# Task 2.8 — Increment 242: Project creation and event mutation lock boundary
Date: 2026-11-24
Scope: read-only source trace of ProjectService.create (active and draft/repeat flows) and EventService event persistence/project-update/idempotency sequence.

## Findings (E1 static source)
- Active ProjectService.create acquires ScriptLock (30s), performs tenant-scoped duplicate ProjectCode scan, builds reference-copy plan, then ProjectRepository.insert -> GoogleSheetsRepository.insert, which acquires its own ScriptLock. This is a nested lock hypothesis analogous to nested lock paths previously traced. No isolated runtime verification.
- Draft creation branch inserts and audits without the same outer ProjectCode lock (draft code is blank); it still depends on individual insert/audit locks.
- Project creation audit is performed after the project row is inserted and after the outer lock is released. Audit failure could leave a persisted project while request error is surfaced; retry behavior depends on duplicate code/operation ID and requires test.
- Event creation inserts the event using a repository beforeWrite callback that revalidates project existence, tenant, current step, user access and workflow event rule under the repository write lock (concurrency guard observed for that insert).
- After event insertion, event form responses and central-information effects are persisted, procurement integration applied (failure is caught and logged), project state updated, event/project audit entries written, and idempotency status set COMPLETED at the end. The multi-write business operation is not transactional in inspected code.
- Idempotency key/status appears to guard retry behavior; if completion marker write fails, code raises an explicit warning that the event exists and must be checked rather than resubmitted. PROCESSING/exception recovery and stale-key recovery require further tracing/testing.
- Telegram notification is best-effort after successful persistence sequence and completion marker.

## Verification boundary
Static source only. No isolated runtime tests, concurrency tests, or failure injection performed. Nested-lock outcomes and partial-write recovery are hypotheses/candidates, not confirmed defects. No production writes.

## Follow-up
Continue tracing Event idempotency reservation/recovery and related mutation locks, then consolidate the mutation matrix and gap/test plan. G-01/G-10 remain OPEN; schema readiness NOT APPROVED.
