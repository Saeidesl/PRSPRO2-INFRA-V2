# PMS V2 — Architecture, API, Security, Logging & Error Standards (Draft)

Updated: 2026-11-22
Status: Design baseline draft; Product Owner review required. This document defines engineering guardrails, not implementation or final technology configuration.

## 1. Scope and principles
- Independent PMS V2; legacy production repositories remain READ-ONLY references.
- Preserve verified business behavior and relationships; avoid mechanical porting of GAS/Sheets implementation.
- Modular monolith is the recommended initial backend shape unless evidence justifies distributed services. Keep modules separated by domain and contracts; avoid premature microservices.
- Database is the authoritative source for transactional business state.
- External operations (file storage, notifications) must not be falsely treated as atomic with PostgreSQL.

## 2. Initial domain/module boundaries (provisional)
Boundaries are logical and not yet a final schema or endpoint inventory:
- Identity & Access: accounts, tenant membership, roles, permissions, session lifecycle.
- Tenant & Organization: tenant scope, departments, organization settings.
- Projects: project identity, status, project information/history and reference relationships.
- Workflow: workflow definitions, versioning, steps, assignments and transition/rule policy.
- Events & Forms: event types, fields, forms, responses and immutable/versioned snapshots as required by confirmed behavior.
- Materials & Procurement: material requirements/specifications, orders, allocations, receipts and calculation history.
- Files: metadata, associations, access policy, lifecycle, storage abstraction.
- Reporting: report/query services, avoiding write-side business mutations.
- Audit: append-only activity records with access restrictions and redaction.
- Notifications & Integrations: transactional outbox, provider adapters, retries and delivery records.
- Print/Templates: template definition and rendering integration, subject to discovery.

Modules must own their business rules and persistence interfaces; cross-module operations should use explicit service contracts and transaction orchestration rather than direct uncontrolled table writes.

## 3. Backend architecture
Recommended initial layout is conceptual, not a scaffold:
- HTTP/API layer: routing, validation, authentication context, rate limits, response serialization.
- Application layer: use cases, transaction orchestration and authorization policy invocation.
- Domain layer: business rules, state transitions, domain validation.
- Persistence/integration adapters: PostgreSQL, file storage, notification providers and external APIs.
- Shared platform layer: configuration, logging, error model, observability, test utilities.
Do not allow HTTP controllers to contain domain logic or expose database entities directly.

## 4. API conventions (draft)
- REST-style resource URLs and native HTTP methods; avoid method-override transport.
- Version API contract under a stable prefix (candidate: /api/v1); exact URL prefix remains a project decision.
- JSON request/response contract; explicitly document schemas and validation.
- Use pagination for potentially large collections, with bounded page size and stable ordering.
- Filter/sort options must be allow-listed; avoid unrestricted client-supplied SQL/order expressions.
- Mutations should validate input and authorization before changing state.
- Safe/idempotent retry semantics: GET/HEAD may be retried; mutation retry requires idempotency support or an operation proven safe. Use idempotency keys for applicable create/side-effect requests.
- Do not place secrets or session tokens in query strings.
- Contract changes must be documented and coordinated with frontend/backend/database work.

### Candidate success response
Use consistent JSON responses. Exact envelope may be adopted or simplified during implementation:
- data: requested resource/result
- meta: pagination/request metadata when applicable
Do not return unnecessary internal implementation details.

## 5. Error handling and validation
- Errors should have stable machine-readable codes, human-readable safe messages and optional field-level validation details.
- Candidate shape: code, message, details (optional), requestId.
- HTTP status codes should accurately reflect the failure category: validation, authentication, authorization, missing resource, conflict, rate limiting, or internal failure.
- Never expose stack traces, SQL, internal paths, secrets, raw provider errors or sensitive data to clients.
- Validation should occur at API boundaries and again in domain rules where invariants require it.
- Unexpected errors must be logged with request correlation and translated to safe client responses.
- Distinguish expected business-rule failures from infrastructure failures.

## 6. Authentication, authorization and tenant isolation
- Authentication is distinct from authorization. Every protected operation must enforce both.
- Establish tenant context from a validated authenticated membership; never trust a client-supplied tenant identifier without membership and scope validation.
- Deny by default (fail closed); enforce policy at server-side application/service boundaries, not only UI or router.
- Resource access must validate tenant scope and relationship scope for every request.
- Roles/permissions must be explicit and testable; administrative privileges must be scoped and audited.
- Session/token storage, expiry, rotation, revocation and logout behavior must be documented.
- Recommended session approach for browser app: secure HttpOnly, SameSite cookies with CSRF protections when cross-site/unsafe requests require them; alternative Authorization-token approach needs documented threat model and storage policy. Final auth design remains pending.
- Avoid secrets in URLs, logs, client-side persistent storage, source code or test fixtures.
- Sensitive actions require audit logging and, if business risk warrants, additional confirmation/step-up controls (policy to be defined).

## 7. PostgreSQL and transaction policy
- DB constraints (FK, unique, check) should enforce core invariants in addition to application checks.
- Tenant-owned records should have explicit tenant scope and tenant-aware relationships/uniqueness where appropriate.
- Indexes must support observed access patterns; avoid speculative indexing before query review.
- Use transactions for logically atomic DB mutations; define transaction boundaries in use cases.
- External storage/provider actions cannot be made atomic with DB transactions: use outbox/saga, idempotency and reconciliation.
- Migrations must be repeatable/controlled, ordered, reviewed, tested on disposable DB and include recovery/rollback strategy.
- Avoid destructive migrations without explicit risk review and verified backup/recovery path.

## 8. Logging, audit and observability
- Structured logs (JSON or equivalent) with timestamp, severity, service, environment and correlation/request ID.
- Do not log credentials, tokens, cookies, sensitive form data, personal data beyond justified minimum, file contents or secrets.
- Redact sensitive fields centrally; test redaction.
- Operational logs and business audit records are distinct: audit records describe attributable business/security actions and require access/retention policy.
- Audit records should be append-only through application paths; changes to audit data require privileged controlled processes.
- Capture errors, latency, resource health, integration delivery failures and transaction/reconciliation outcomes.
- Define retention, access, alerting and metrics thresholds during operational design; no thresholds assumed here.

## 9. Files and integrations
- File access is authorized through metadata and tenant/resource scope; storage URLs must not bypass authorization policy.
- Validate file size, type and content; record checksums and lifecycle states.
- Storage operations and metadata changes use outbox/reconciliation patterns; detect orphaned metadata and objects.
- Notifications use durable delivery records/outbox, retry, deduplication and dead-letter handling where applicable.
- External provider adapters isolate credentials and provider-specific behavior; do not embed provider assumptions in domain logic.

## 10. Testing requirements for architecture-critical paths
- Unit tests for domain rules and state transitions.
- Integration tests for DB constraints, transactions and repository behavior.
- Authorization matrix tests including tenant isolation and resource scope.
- API contract tests for validation and response/error stability.
- Idempotency, concurrency, retry and outbox tests for applicable operations.
- File lifecycle, audit redaction, notification retry/reconciliation tests.
- E2E tests in isolated environments only; synthetic data and fail-closed target configuration.
- No test claims until tests actually execute.

## 11. Open decisions requiring evidence or Product Owner approval
- Final API version prefix and response envelope.
- Authentication/session model and CSRF policy details.
- Exact domain module and repository boundaries after detailed workflow discovery.
- File/object storage provider and upload architecture.
- Audit data scope, retention, access and privacy rules.
- Notification provider scope and delivery expectations.
- Rate limits, latency targets, availability, RPO/RTO and capacity requirements.
- Reporting/query strategy and any asynchronous jobs.
- Migration policy for IDs, history, snapshots and legacy edge cases.

## 12. Safety boundary
This draft authorizes neither implementation nor schema/migration work, live production access, VPS acquisition, deployment, data migration, or changes to legacy repositories. It is a reviewable design baseline only.
