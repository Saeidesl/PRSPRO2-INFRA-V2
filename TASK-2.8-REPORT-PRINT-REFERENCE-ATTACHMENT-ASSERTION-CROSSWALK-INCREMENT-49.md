# Task 2.8 — Reports, Print Templates, Reference Copy & Attachment Gallery Assertion Crosswalk (Increment 49)
Date: 2026-11-22
Status: assertion-source inspection only; no tests executed.

## Reviewed test sources
- `tests/report-service.test.js`
- `tests/print-template-batch-runtime.test.js`
- `tests/project-repeat-reference-copy.test.js`
- `tests/project-image-gallery-api.test.js`
- `tests/project-image-gallery-thumbnail.test.js`

## Assertions reviewed
### Report service
Harness assertions include tenant/visibility-filtered project listing, project report event filtering and selected-project procurement context. The mocked data provides cross-tenant/hidden project and void/cancelled events. This does not prove actual route authorization, live filtering, or report consistency.

### Print template batch resolve
Mocked runtime harness asserts batch resolution reuses reads, deduplicates template IDs, rejects templates outside tenant and inactive templates, and tests access behavior. Repository/access methods are mocks, not real database/tenant enforcement.

### Repeat reference project copy
Assertions cover persisted copy plans, per-information-type copy, history/sync logs, failure status, active value selection, operation-key recovery and live-sync conflict handling. These source assertions do not prove storage failure compensation, transactional consistency or real concurrent copy operations.

### Project image gallery
Mocked API harness models tenant-mixed projects/events/attachments, visibility, void/deleted states, image and non-image MIME, and private storage metadata. Assertion scope is limited to the service output and mocked repositories; it does not prove actual route/auth or storage exposure protection.

### Thumbnail
Assertions cover thumbnail-only reduced image content, MIME/image/status checks, attachment visibility, tenant-context presence and ATTACHMENT_VIEW route gate. The harness mocks storage/repositories; real storage privacy, access paths and lifecycle/recovery are not proven.

## Evidence limits
E2 assertion source inspection only. No test execution, real storage, runtime routes or DB integrity evidence. G-01/G-04/G-10 remain open.

## Safety
No production writes, live data, tests, DDL, migrations, provisioning, deployment or storage mutation.
