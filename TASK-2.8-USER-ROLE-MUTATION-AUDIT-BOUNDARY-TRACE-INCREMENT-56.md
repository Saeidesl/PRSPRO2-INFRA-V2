# Task 2.8 — User-role Assignment Mutation & Audit Boundary Trace (Increment 56)
Date: 2026-11-22
Status: Bounded READ-ONLY source trace.

## Sources
UserRoleService.js, RolePermissionService.js and PermissionService.js from READ-ONLY backend production reference.

## Verified source observations
- UserRoleService.assign validates target user and role against current tenant, checks role status, checks user-role tenant match and duplicate assignment, then inserts association.
- The assignment insert is followed by AuditService.log as a separate operation. In the inspected source sequence, no DB transaction is available in legacy Sheets architecture; a failure after insert may leave assignment without corresponding audit record.
- PermissionService.getUserPermissions uses user tenant, tenant-specific user-role resolution and active roles for that tenant, while permission catalog is read globally in the inspected path.
- RolePermissionService repository bulk insert path uses a script lock and checks required sheet columns, but the sampled code is not evidence of DB-level referential or tenant integrity.
- This source evidence is bounded; it does not prove all role-permission mutation paths or runtime behavior.

## V2 implications (proposals)
1. Assignment mutation, audit record and authorization-related state change should be transactionally persisted in DB.
2. Tenant-qualified constraints should enforce user and role tenant match and association TenantID.
3. Enforce uniqueness for user-role association at database level after policy confirmation.
4. Global Permission catalog versus tenant-specific permission records must be explicitly classified.
5. Test duplicate/concurrent assignment, inactive role rejection, cross-tenant IDs, audit failure atomicity and role removal/reassignment in isolated runtime.

## Evidence boundary
E1 source only; no runtime tests, live data, SQL/DDL, migration, provisioning, production modification, deployment or VPS. G-01/G-10 remain OPEN; schema readiness NOT APPROVED.
