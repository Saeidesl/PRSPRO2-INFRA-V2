# TASK-2.8-EVENTDATA-AND-ATTACHMENT-DELETE-BOUNDARY-INCREMENT-88

Date: 2026-11-23
Evidence: E1 source inspection
Reference repository: PRSPRO2BACKEND-PRODUCTION
No runtime tests, production writes, migrations or deployment.

## EventData
EventDataService.getByEventId requires a valid user, retrieves event, verifies TenantID, and for project-linked events verifies project tenant and project visibility. It then fetches EventData by EventID and parses DataJSON; malformed or non-object JSON returns an error. Independent events are tenant-validated without project visibility branch.
- This bounded read path is a positive tenant/visibility control observation, not complete route authorization proof.
- EventDataRepository findByEventId ownership relies on the validated event context and repository behavior; unique tenant/event composite enforcement was not proven.

## Attachment deletion
AttachmentService.delete validates ID/reason, current user, attachment existence, tenant, attachment visibility and active status; then calls StorageRepository.delete(StorageKey), updates metadata to DELETED, and finally AuditService.log.
- Ordering creates a multi-system failure boundary. If storage deletion succeeds but metadata update or audit fails, the system may have a partial state; if storage delete itself behaves ambiguously, the record may be difficult to reconcile. No durable delete/reconciliation operation was observed in the inspected path.
- Source does not prove storage delete is irreversible or that audit failure causes rollback.

## Dependency collector
ProjectDeletionService._collect was rechecked. It builds project dependencies from events, EventData, material needs, procurement order/item/allocation/receipt links, material specs/calculations and project/event attachments in the previously inspected excerpt. It does not visibly collect ProjectInformation, ProjectInformationHistory, EventFormData responses or ProjectReferenceSyncLog. This remains a bounded source-level dependency omission; orphaning is not runtime-proven.

## V2 design requirements
- Tenant-qualified EventData key and FK/composite constraints.
- Attachment lifecycle operation records and storage outbox/reconciliation (retry-safe), transactional metadata/audit.
- Project deletion graph registry and dependency coverage test; explicit retention policy for information/history/form responses/sync logs.
- Negative tenant access tests, storage fault injection, deletion rollback/recovery and audit durability tests.

E1 only; no runtime tests. G-01/G-10 blockers and G-04 remain; G-03 also open. No production changes.
