# Task 2.8 Increment 285 — Role-permission mapping integrity trace

Date: 2026-11-24
Backend source pin: 8253eb3718d1a2a1bd5de03b6b5090ba69b94b67
Mode: read-only source inspection

## Scope
RolePermissionService assignment/list/removal paths and related role, permission and user-role repository/access-control sources. This continues Increment 284 and avoids repeating prior route and tenant-boundary traces.

## Static observations
- Role permission listing validates the role exists and belongs to the current tenant; returned mappings are filtered by TenantID.
- Single assignment validates role tenant, permission existence, checks the existing role-permission link and rejects duplicate or tenant-mismatched links. It then inserts and audits sequentially.
- Bulk assignment validates role tenant and non-empty/deduplicated input IDs; it checks permission existence and skips mapping rows already present for the same tenant. It then bulk inserts and audits each created row sequentially.
- Bulk assignment's inspected path does not visibly apply a permission active-status check. It also does not visibly reject an existing mapping with a different TenantID for the same role/permission pair (the tenant-filtered existing-map lookup ignores it), unlike single assignment. This is a candidate integrity gap requiring confirmation of the intended cross-tenant/global catalog semantics and tests, not a proven exploitable defect.
- Removal paths tenant-filter target mappings and audit sequentially after repository deletion(s).
- Repository and service consistency needs further validation: the inspected file named RolePermissionRepository.js contains RolePermissionService implementation, while references to the repository methods are unresolved in this bounded trace. This may be a source inventory/file naming issue; no conclusion about repository implementation is made.
- No runtime execution, race tests, failure injection, production access or mutations.

## Assurance targets
RP-01 verify permission catalog tenancy/global ownership and active/inactive semantics.
RP-02 negative test bulk assignment against inactive permissions.
RP-03 cross-tenant mapping collision behavior for bulk vs single assignment.
RP-04 concurrency/idempotency for simultaneous assignments.
RP-05 partial insert/audit failure recovery for bulk insert and removal.
RP-06 source inventory resolve repository implementation and test coverage.

## Status
Static evidence only. No test execution. G-01/G-02/G-10 remain OPEN; schema readiness NOT APPROVED.
