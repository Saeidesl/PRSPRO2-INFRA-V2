# Task 2.8 — Project Information repository tenant-filtering and history access boundary — Increment 168

Date: 2026-11-23. Read-only trace of ProjectInformationRepository.js and sampled ProjectInformationService.js call sites.

## Repository observations
- Repository-level `findHistory(valueId)` filters solely by ProjectInformationID and sorts ascending by Revision; it does not tenant-filter itself.
- Service call sites observed for `get`, `history`, and approval lookup apply tenant filtering after the repository returns rows; `_latestApproval_` filters by tenant and APPROVED action.
- `findSyncLog(source,target,type,tenant)` accepts an optional tenant argument. When omitted, its filter omits TenantID entirely.
- `findAnyByProjectAndType` and `findByProjectAndType` filter on project and information-type IDs only; tenant validation is performed at sampled service call sites before these reads.
- Repository APIs themselves therefore do not consistently enforce tenant isolation; correctness currently depends on caller-side validation and the proper optional arguments. No complete call-site inventory established.

## V2 design implications
- Tenant boundaries must be enforced structurally: tenant-aware composite FK/unique constraints where appropriate, query scoping, explicit tenant parameters required (not optional) in tenant-sensitive APIs, and authorization at service boundary.
- History/version queries must join or constrain through the tenant-owned parent; do not expose unscoped repository methods.
- Audit/history immutable and integrity rules need transaction-level protection.

## Evidence / status
Source-only; search did not return call-site results for repository functions in the bounded code search, so this is not exhaustive. G-01/G-09/G-10 and tenant isolation gate remain open; schema readiness NOT APPROVED. No production modifications, tests, runtime access, migration or real data.
