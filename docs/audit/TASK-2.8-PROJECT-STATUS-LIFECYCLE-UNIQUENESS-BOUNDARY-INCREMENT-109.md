# Task 2.8 Increment 109 — Project-status lifecycle and uniqueness boundary (2026-11-23)

## Scope
READ-ONLY trace of backend production `ProjectStatusService.js`; repository/validator files at guessed root paths were not present. Router create/update route snippets were boundedly observed in Router.js previously; this increment focuses on service behavior.

## Source observations (E1)
- Context resolution obtains current user and TenantID; admins bypass the explicit permission-service check, while non-admins require PROJECT_STATUS_MANAGE.
- Listing filters to user's TenantID and sorts by Name.
- Create validates a non-empty name with a 100-character ceiling, checks for duplicate active names within tenant (case-insensitive), generates ID/Code and TenantID server-side, appends the row, then audit.
- Update resolves target by ID+TenantID, and allows Name and Status changes while preserving ID, Code, TenantID, CreatedAt. Audit follows the sheet update.
- Duplicate active-name validation during update is only triggered when Name is supplied. The code path changing an inactive record's Status to ACTIVE without also supplying Name does not visibly repeat the active-name duplicate check. This is a credible source-level consistency gap, not confirmed runtime behavior.
- Create append and update row write have no visible lock/transaction. Duplicate checks are read-before-write and race-prone absent underlying constraint evidence. Audit is sequential and failure recovery not demonstrated.
- Status rows are read directly through SpreadsheetApp and configured headers; no repository abstraction or cache is shown in this service.

## V2 requirements
- Define status naming/activation semantics and enforce active-name uniqueness with database constraint plus atomic service behavior.
- Revalidate duplicate-name constraint when reactivating a record, regardless of whether Name changed.
- Tenant-scoped FK/unique constraints and centralized audit/outbox transaction.
- Test concurrent create, reactivation collision, tenant-crossing IDs, audit failure, header/schema drift, admin vs permission behavior.

## Verification
E1 source-only; no tests executed; no production changes. Candidate issue unconfirmed. Task 2.8 remains IN PROGRESS; schema readiness NOT APPROVED; G-01/G-10 blockers remain.
