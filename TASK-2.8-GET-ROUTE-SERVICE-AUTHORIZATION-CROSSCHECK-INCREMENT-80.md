# TASK-2.8-GET-ROUTE-SERVICE-AUTHORIZATION-CROSSCHECK-INCREMENT-80

Date: 2026-11-23
Evidence level: E1 bounded Router.js and service source inspection
Reference repo: PRSPRO2BACKEND-PRODUCTION
No runtime tests, production changes, data operations, migrations or deployments.

## GET route observations (Router.js SHA f2542a17993b852c7dd2968a8fefe57f70b991f3)
- Project information history/item/list routes have PROJECT_VIEW route checks; service-level project visibility and tenant/type validation were sampled.
- Project/event attachment routes use ATTACHMENT_VIEW; attachment service performs entity, tenant and project visibility checks.
- Project image gallery/count routes require PROJECT_VIEW or EVENT_VIEW as applicable plus ATTACHMENT_VIEW.
- Event GET subroutes include form responses and EventData; excerpts do not show explicit Router-level permission calls for each route, so correctness depends on service-level user/tenant/project validation. This is a verification dependency, not a confirmed authorization defect.
- Attachment download/thumbnail/get route paths are gated by ATTACHMENT_VIEW and delegated to AttachmentService; exact download storage and permission behavior requires fuller method-by-method review.
- Procurement workspace/report/dashboard routes use explicit requireAny checks in sampled Router excerpts. Full endpoint inventory and service checks still not reconciled.

## Risk and V2 requirements
1. Maintain declarative route permission registry and generate/validate route-to-service coverage.
2. Authorization tests for every read/write endpoint, including independent events, project visibility, forms/EventData, downloads and procurement.
3. Avoid treating Router permission as sufficient: tenant-qualified service/data authorization required.
4. E1 bounded observations only. No endpoint test execution; G-01 remains BLOCKER and G-10 remains BLOCKER.

No production writes or testing. Schema readiness NOT APPROVED.
