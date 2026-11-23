# Task 2.8 Increment 108 — Department lifecycle and tenant boundaries (2026-11-23)

## Scope
Read-only inspection of DepartmentService.js, DepartmentRepository.js and DepartmentValidator.js from backend production.

## Source observations (E1)
- list filters departments by current user's TenantID; get verifies department tenant.
- create requires current user TenantID, generates ID and tenant ownership server-side, checks duplicate Code case-insensitively within tenant, inserts then audits.
- update validates target tenant and uses an explicit mutable field set (Name, Code, Status, UpdatedAt); TenantID is not set through this path. Duplicate code check is tenant scoped; update then audit are sequential.
- DepartmentRepository.findById uses a request-local cache; insert/update reset that cache. No broader cache behavior or concurrent mutation effects are asserted.
- Repository code helpers findByCode/existsByCode are global-code lookups, not tenant-aware by themselves; service tenant checks govern sampled paths.
- Storage-level uniqueness, locking for create/update, atomic audit, and behavior on audit failure are not demonstrated. Router endpoint checks were not re-audited comprehensively here.

## V2 implications
Tenant-aware relational FK/unique constraints; service/API validation and explicit field allowlists; transactional audit/outbox; tests for duplicate-code races, cross-tenant identifiers, status changes, stale cache and audit failure recovery.

## Status
E1 source-only; no tests executed; no production changes. Not a confirmed defect. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 remain blockers.
