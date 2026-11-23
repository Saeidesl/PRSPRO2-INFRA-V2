# Task 2.8 Increment 124 — EventType and historical EventData boundary

Date: 2026-11-23  
Evidence: E1 — read-only source inspection

## Sources
EventTypeService.js, EventTypeRepository.js, EventTypeValidator.js, EventFieldRepository.js, EventDataService.js, EventDataRepository.js.

## Observed behavior
- EventType configuration is tenant-scoped; list filtering for non-admin/non-manager users applies active status and department/user allowlists or default department restriction. Managers/admins can view broader tenant records.
- Event types support PROJECT/INDEPENDENT connection, target department/project status/step settings, allowed and available departments/users, void, attachment, variant quantity, draft, completion and form-amendment flags. Legacy removed fields are normalized away.
- Amendable form references are validated against active same-tenant forms; department/user/workflow-step references are checked for tenant scope. Project status validation directly reads the named sheet from active spreadsheet context and checks active status and tenant.
- Create requires EVENT_TYPE_MANAGE, validation and tenant-scoped duplicate code check, then references validation, insert and audit sequentially. Update verifies tenant, applies effective partial-update validation, checks code uniqueness only when code changes, validates references/amendable forms and sequentially updates/audits. Atomicity and storage-level code uniqueness are not demonstrated.
- EventTypeRepository uses short-lived ScriptCache for all event-type rows, filtering is done by service, and writes invalidate cache. Cache invalidation/recovery and race behavior are not fully tested.
- Optional list prefetch includes form definitions per event type and catches individual load failures, returning a flag rather than failing the entire list.
- Historical EventData service validates parent event, tenant, and project visibility where project-linked; independent events remain tenant-scoped. Stored JSON is parsed and shape-validated; missing data returns null. EventData/EventField repositories provide historical lookup and request-scoped caching.

## V2 implications
1. Event type should be tenant-scoped relational configuration with FK constraints for departments/users/workflow steps/forms/status references and composite unique code.
2. Validate referenced project status through the same DB transaction/tenant scope, not a hardcoded sheet access path.
3. Define event type access policy precedence (allowlists vs department default) and tests for admin/manager/user, independent events and draft/variant/amendment options.
4. Mutation and audit should be transactional/outbox-backed; code uniqueness must be concurrency-safe.
5. Event type cache should be versioned and scoped, or rely on query caching with predictable invalidation.
6. Historical dynamic data should be migrated to a typed/versioned event snapshot, retaining old field-definition context rather than relying only on current labels.

## Limits
- Source-only inspection; no runtime tests, live data or production changes.
- Potential configuration-reference and concurrency issues are source observations, not confirmed defects.
- E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.
