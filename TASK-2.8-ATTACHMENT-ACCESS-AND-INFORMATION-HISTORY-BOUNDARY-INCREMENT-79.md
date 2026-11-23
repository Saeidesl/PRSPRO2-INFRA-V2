# TASK-2.8-ATTACHMENT-ACCESS-AND-INFORMATION-HISTORY-BOUNDARY-INCREMENT-79

Date: 2026-11-23
Evidence: E1 bounded source inspection
Reference repository: PRSPRO2BACKEND-PRODUCTION
No runtime tests, data operations, production changes, migrations or deployment.

## Attachment access and image-copy paths
- AttachmentService visibility excerpts require tenant match, entity resolution, tenant match for entity and project availability for project-bound attachments. Event attachments linked to projects inherit project availability; independent events remain tenant-scoped.
- copyProjectImage validates tenant/source/target, source entity/project availability, MIME image, active status, allowed image categories, and target project availability. It searches for an active duplicate by source attachment ID and target project.
- Copy path stages external storage then inserts metadata and audit; if insert/audit path throws, attempted StorageRepository.delete cleanup is performed.
- This is compensating cleanup, not proof of durable reconciliation, deletion success, idempotent storage key behavior or audit transaction atomicity.

## Project-information history/reference sync
- ProjectInformationService validates project visibility and type tenant; validates event source association; validates attachments against project/tenant/type; uses ExpectedRevision conflict checks and event/value replay checks in inspected save path.
- Save sequence persists current value/revision, history, audit and then live reference synchronization. Reference sync uses log/idempotency and revision conflict detection; file-based types are skipped in the inspected sync path.
- Logical removal also emits history/audit and changes status; approval is represented through history entries.
- Script lock wrapper exists for saveWithLock, but atomic current/history/audit/sync across failure boundaries is not demonstrated.
- Repository history and sync log methods have sampled filtering on service side; tenant filtering of every repository operation remains caller-dependent.

## V2 requirements
1. Tenant-aware composite keys/constraints and strict entity ownership across attachment polymorphic links.
2. Transactional current value + revision history + audit/outbox for project-information mutations; idempotent event replay and optimistic concurrency.
3. Durable file-operation outbox/reconciliation with storage cleanup retries and verified status; no reliance on best-effort compensation.
4. Audit and history immutable semantics/retention policy.
5. Cross-tenant negative, concurrency, storage fault injection and deletion/restore tests.

E1 only; G-01/G-10 blockers, G-03/G-04 remain open. No test claims. Schema readiness NOT APPROVED.
