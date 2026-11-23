# Task 2.8 — User identity uniqueness and tenant access query trace — Increment 174

Date: 2026-11-23. Read-only trace of UserService and UserRepository.

## Observations
- UserService.list loads all user rows then filters TenantID in memory.
- UserService.get validates user TenantID against current user's TenantID.
- Create validates username duplication across all users globally, case-insensitively, then validates optional department tenant.
- UserRepository.findByUsername and findAllByUsername use global case-insensitive matching; findByUsername returns a row only when exactly one match, otherwise null.
- UserRepository.findByTenantId also retrieves all then filters.
- Username uniqueness is described in UserService as system-wide because login does not select tenant. However, normalization differences between service duplicate scan and repository helpers (trim/lowercase vs underlying exists check exact) and DB uniqueness/transaction guarantees remain unknown.

## V2 implications
- Identity/account uniqueness scope must be explicitly confirmed; legacy source suggests global normalized username scope.
- Enforce uniqueness at database level using normalized username key, not pre-check only, to prevent concurrent duplicates.
- Tenant memberships should be separate relational entity; avoid loading global user tables to filter for tenant.
- Login and identity migration strategy (credential upgrade/reset, duplicate remediation, legacy hash conversion) remains pending; never import credentials directly without secure design.
- User queries should be paginated and tenant-aware; global user lookup restricted to identity service.

## Status
G-05/G-08/G-10 remain open. Source-only; no tests, live data, migration or production changes.
