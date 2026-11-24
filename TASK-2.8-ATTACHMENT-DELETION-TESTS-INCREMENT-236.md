# Task 2.8 Increment 236 — Attachment and project deletion test inventory (2026-11-24)

## Reviewed
tests/attachment-service.test.js and tests/project-deletion.test.js (read-only).

## Attachment test coverage observed
- Event attachment listing denies access for users without project access.
- Upload to event denies users without project access.
- Listing remains available for visible-project event.
- In the inspected file, no deletion/storage failure/audit failure injection tests were found.

## Project deletion test coverage observed
- Collects event/project dependencies without tenant/project boundary crossing.
- Preserves shared purchase order if it contains lines for other projects.
- Ignores already deleted rows and avoids relying only on ProjectID for order relationship.
- Treats VOID events as inactive to support retry of failed project deletion.
- Test source includes dependencies across attachments, procurement records, material specs and calculations.

## Gaps
No evidence in inspected tests of attachment delete storage/meta/audit failure injection or post-failure repair. Project-deletion test coverage is meaningful for dependency collection and retry semantics, but this pass does not establish full mutation rollback/transaction behavior.

## Status
Static source inspection only; no tests executed. Task 2.8 remains incomplete; G-01/G-10 open; schema readiness NOT APPROVED.

## Next
Increment 237: review ProjectDeletionService actual orchestration and attachment storage/deletion internals, map their mutation ordering, and consolidate findings.
