# Task 2.8 — Audit & Project Cascade Deletion Integrity Trace (Increment 60)
Date: 2026-11-22
Status: Bounded READ-ONLY source trace.

## Sources
AuditService.js, AuditRepository.js and ProjectDeletionService.js from READ-ONLY production reference.

## Observations
- AuditService creates audit records with tenant, actor, action, entity type/id, serialized old/new data, reason and timestamp. AuditRepository offers generic entity and user queries without tenant filtering at repository level; AuditService.list applies tenant filter in its sampled path.
- Audit records are append-style in sampled service, but no immutable/tamper-evident storage, retention, redaction, export or integrity verification mechanism is evidenced by these files.
- ProjectDeletionService gathers tenant/project dependencies and performs sequential soft-delete operations on multiple child sheets, trashes attachment storage, then physically deletes the project row if prior operations report no failures. It logs cascade deletion summary and partial failures.
- Cascading deletion is not transactional. Child status updates, file trashing and project physical removal can fail at different points. Some children are marked DELETED/VOID while the parent is physically removed; historical audit/lineage preservation relies on the audit record and existing non-cascaded references.
- The service requires admin check and project-code confirmation and revalidates under ScriptLock; the lock does not provide transaction/rollback.

## V2 requirements/recommendations
1. Project deletion should be a stateful soft-delete/retention workflow; physical purge only after retention, backup and legal/business policy approval.
2. Preserve immutable audit and lineage using typed entity links or registry and retention-safe identifiers; avoid FK cascade deleting historical evidence.
3. Cascade operation should be atomic or orchestrated by durable workflow with per-step status, idempotent retries, failure reporting and rollback/restore support.
4. Audit records should be append-only with restricted DB permissions, retention, redaction policy, optional hash-chain/tamper detection and transactional write with business changes.
5. Ensure audit query tenant filtering and direct-ID access are tenant-safe; test cross-tenant entity lookup and audit access.
6. Test cascade failure at every stage, recovery/retry, storage failure, backup restore and purge eligibility.

## Evidence limits
Bounded E1 source review only; no runtime tests, live data, SQL/DDL, migration, provisioning, production writes or deployment. G-04/G-10/G-01 remain OPEN; schema readiness NOT APPROVED.
