# Task 2.8 — Attachment Upload/Delete Recovery Boundary (Increment 103)

Date: 2026-11-23  
Scope: READ-ONLY source review of legacy AttachmentService upload/delete lifecycle.  
Evidence level: E1 — source observation only.

## Observed upload sequence
1. Validate attachment input.
2. Resolve current user and parent entity (PROJECT or EVENT).
3. Verify tenant and visibility; event-linked uploads also check parent project visibility.
4. Upload binary object to storage.
5. Create attachment metadata.
6. Insert metadata row.
7. Write audit record.
8. Return attachment and storage URL.

### Boundary
The inspected path does not visibly compensate by deleting the uploaded object if metadata insertion or audit writing fails. This is a potential orphan/reconciliation boundary, not a confirmed failure.

## Observed delete sequence
1. Validate ID and reason, resolve user.
2. Read attachment and enforce tenant/entity visibility.
3. Delete storage object.
4. Update metadata status to DELETED.
5. Write audit record.

### Boundary
Storage deletion precedes metadata status update and audit. If a later step fails, the metadata may remain active or audit may be incomplete while the storage operation has already occurred. Runtime behavior and storage deletion semantics were not verified.

## V2 requirements
- Stage uploads and persist operation state so metadata/object failures can be reconciled.
- Couple metadata mutation with durable audit/outbox transactionally where possible.
- Implement deletion lifecycle states and retry/reconciliation; don't assume object deletion is atomic with DB updates.
- Idempotent cleanup and orphan detection.
- Retention policy and recovery/restore path.
- Tenant-scoped object keys/ownership and explicit authorization checks.
- Fault-injection tests across each persistence boundary.

## Verification limits
- Source review only; no tests run and no runtime failures established.
- No production modifications, live data, SQL/DDL, migrations, or deployment.
- G-04/G-09/G-11 remain open; G-01/G-10 blockers remain.
