# TASK-2.8-MUTATION-ROUTE-PERMISSION-CROSSCHECK-INCREMENT-81

Date: 2026-11-23
Evidence level: E1 bounded Router source review
Reference: PRSPRO2BACKEND-PRODUCTION Router.js SHA f2542a17993b852c7dd2968a8fefe57f70b991f3
No tests or production operations.

## Explicit route permission observations
- Procurement order and receipt batch/single creation routes require PROCUREMENT_MANAGE.
- Project information approval and save routes require PROJECT_EDIT; save delegates to saveWithLock.
- Event form response update route requires EVENT_EDIT.
- Event/project attachment uploads require ATTACHMENT_UPLOAD; route assigns entity type/ID before service upload.
- Attachment deletion route requires ATTACHMENT_DELETE.
- EventData read and event form-response read Router excerpts did not show explicit permission checks at those route snippets; service-level validation is relied upon in sampled service code. This is a verification item, not a confirmed defect.
- Coverage of all mutation routes, service authorization, role matrix and frontend callers remains incomplete.

## V2 requirements
1. Explicit route policy inventory and automated route permission test matrix.
2. Tenant-scoped ownership checks in services/data access even when route permissions are present.
3. Role negative tests for procurement, project information approval, form responses and attachments.
4. API contract and permission matrix tied to route registry.

E1 only; G-01/G-10 remain blockers. No tests executed, no production changes, schema readiness NOT APPROVED.
