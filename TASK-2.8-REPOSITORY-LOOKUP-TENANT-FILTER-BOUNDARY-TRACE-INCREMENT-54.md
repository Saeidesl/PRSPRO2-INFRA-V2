# Task 2.8 — Repository Lookup & Tenant Filter Boundary Trace (Increment 54)
Date: 2026-11-22
Status: READ-ONLY source inspection; bounded repository paths.

## Sources inspected
- EventRepository.js
- AttachmentRepository.js
- ProjectRepository.js
- GoogleSheetsRepository.js (findById path)
All from READ-ONLY backend production reference.

## Confirmed observations
- EventRepository.findById, AttachmentRepository.findById and ProjectRepository.findById delegate to the generic GoogleSheetsRepository.findById by ID.
- The inspected generic repository lookup is ID-based, with a request-record cache and a row lookup by the ID column; tenant filtering is not part of that generic method signature or visible lookup excerpt.
- EventRepository.findByProjectId and other filter methods retrieve sheet records then filter by relation IDs. The inspected findByProjectId filter compares ProjectID but does not itself filter TenantID.
- AttachmentRepository.findByEntity filters by EntityType, EntityID and non-DELETED status, but does not itself apply TenantID in the inspected method.
- These repository functions are adapters, not tenant-aware relational repositories; sampled services add tenant and visibility checks above them. This does not establish that all callers consistently do so.

## Risk and V2 design implications
1. Do not treat repository ID lookup as an authorization boundary.
2. V2 repositories/query methods should support tenant-qualified lookup and relation constraints where tenant-owned data is involved; services still perform business authorization.
3. Cross-tenant direct ID and indirect relation negative tests should include generic repository bypass attempts at service/API boundaries.
4. Composite FK and query tenant filtering are both required; neither alone substitutes for authorization.
5. Reconcile all repository methods/callers before declaring G-01 or G-10 closed.

## Evidence limits
Bounded source review only; no runtime tests, live records, DDL, migrations, provisioning, production changes or deployment. G-01/G-10 remain OPEN; schema readiness NOT APPROVED.
