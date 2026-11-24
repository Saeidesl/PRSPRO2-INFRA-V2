# Task 2.8 Increment 204 — Expanded read-only service retrieval checkpoint

Date: 2026-11-24

## Access recovery
Increment 203 corrected the owner spelling (`Saeidesl`) and restored successful API retrieval. This checkpoint expands selected source fetches without modifying legacy production repositories.

## Selected backend production source retrieval verified
Successful exact-path fetches:
- `Router.js` — SHA `f2542a17993b852c7dd2968a8fefe57f70b991f3`
- `Code.js` — SHA `03055705453640d8410faa8afacbcda8a7d32de2`
- `WebApp.js` — SHA `5592e6527e08397e05f431e99edf282eb7a3c917`
- `DatabaseSetup.js` — SHA `f8e341a5c220105792eab030ea5de920c9921320`
- `EventService.js` — SHA `1fe31ea9ea2a048a870ecbc47c11512bc2942108`
- `WorkflowService.js` — SHA `eb8373c14085975a9eb521d5ba20ec2e5146bfad`
- `ProcurementService.js` — SHA `c0e16aa769cd88f85ba8d65ce12e093b636accf0`
- `AttachmentService.js` — SHA `74492d147499024b6416555a31dd1ee294a746cb`
- `ProjectService.js` — SHA `5c9b5bb3f79a2f85472d2cc2ec3d8c3d1e31321f`
- `FormService.js` — SHA `abddf1851d5721347ac07618e85bfa75cef61589`
- `AccessControlService.js` — SHA `4672948ff7246e56c1493996df6317281fc73b1d`
- `PermissionService.js` — SHA `15a33c07e50a409e69e938418c0e4b2d4f73977b`
- `ReportService.js` — SHA `ee945d6abb113da269f85e01337a9f70c052f6d6`

Frontend production retrieval sanity-check:
- `index.html` — SHA `174ce0be55ed63facc93136b450aa02dd9a84533`

These fetches verify selected file paths and exact blob hashes only. They do not establish a full tree manifest or reviewed repository commit ref. No production changes.

## Source-family evidence summary
Selected files are consistent with the previously documented domain families: tenant-scoped services, workflow, procurement locking/idempotency, attachment lifecycle, project copy/deletion, form revision/amendment, permissions and reporting. Keyword counts were used only as a navigation aid, not proof of behavior or exhaustive coverage. Detailed behavioral conclusions must be grounded in specific traced paths, not counts.

## EP-01 status
Improved: stable selected-file hashes recorded. Still incomplete: full source-tree manifest and exact reviewed repository commit refs, plus exhaustive branch/consumer/caller mapping. EP-01 cannot be closed from sampled retrieval.

## Safety
Read-only source retrieval only. No production edits/commits/branches/PRs; no tests, live data, DDL, migration, VPS or deployment. G-01/G-02/G-03/G-04/G-10 remain OPEN; Schema Readiness NOT APPROVED.
