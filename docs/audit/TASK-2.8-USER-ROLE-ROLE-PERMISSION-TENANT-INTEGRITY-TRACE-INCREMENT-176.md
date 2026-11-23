# Task 2.8 — User-role and role-permission assignment tenant integrity trace — Increment 176

Date: 2026-11-23. Read-only trace of UserRoleRepository/UserRoleService and RolePermissionService/Repository plus headers.

## Observations
- UserRoleRepository.findByUserId/findByRoleId/find and exists are ID-based scans without tenant filtering.
- UserRoleService validates target user tenant, role tenant and active role state on assignment; listing by user filters tenant after repository read.
- RolePermissionService validates role tenant and permission existence; sampled assignment checks existing relationship and TenantID, then inserts tenant-tagged association. Permission itself is global catalog (05_Permissions headers lack TenantID), while RolePermission associations include TenantID.
- Service checks provide defense in sampled paths but repositories are unscoped; unique and FK constraints are not evidenced. Assign/delete/audit atomicity not established in bounded source.
- UserRole header includes TenantID; target design should constrain user and role membership to same tenant.

## V2 implications
- UserRole tenant-qualified FKs/composite constraints should prevent assigning a user to a role from another tenant even if service validation is bypassed.
- RolePermission should reference tenant-owned Role and global Permission, with unique constraints preventing duplicates and FK integrity.
- Repository query APIs should require tenant context for user-facing access; enforce tenant scope in SQL.
- Permission catalog should be global/static or explicitly tenant-customizable only after policy confirmation; preserve legacy header evidence rather than assume.

## Status
G-01/G-05/G-10 remain open. Source-only; no runtime/tests/live data.
