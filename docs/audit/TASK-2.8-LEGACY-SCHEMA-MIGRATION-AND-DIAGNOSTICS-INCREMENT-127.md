# Task 2.8 Increment 127 — Legacy schema setup, configuration migration and diagnostics

Date: 2026-11-23  
Evidence: E1 — read-only source inspection. No setup/migration functions executed.

## Sources inspected
DatabaseSetup.js, ProductionConfigurationMigration.js, PerformanceDiagnostics.js, LoginDiagnostics.js, PermissionDiagnostic.js, TelegramDiagnostics.js.

## Findings
- DatabaseSetup defines a broad set of spreadsheet schemas/header lists spanning tenant, user/role/permission, projects/workflows, events/forms, information, attachments/audit, procurement, material specifications/calculations, reference sync and print-template configuration. Headers are evidence of intended shape, not proof of constraints or relationships.
- DatabaseSetup includes explicitly destructive sheet-reset functionality, bootstrap/repair-admin operations, schema/seed/validation routines. These were inspected only and never executed. Reset workflow is guarded by user confirmation but deletes/rebuilds sheets, making it a high-risk administrative operation.
- ProductionConfigurationMigration provides preview/preflight routines comparing source and target headers, IDs, duplicate IDs, same-ID data conflicts, tenant identity and selected foreign-reference integrity. A safeToExecute result is source-level preflight logic, not proof of backup restore or transactionality.
- Configuration migration execution creates a backup and inserts missing configuration rows under script lock, leaving source unchanged in the inspected path; target mutation and audit/recovery guarantees are not database transactions.
- A separate migrateToProduction routine includes hard-coded spreadsheet identifiers and clears/replaces target configuration sheet contents after backup while preserving selected operational sheet data/header structure. It is destructive and must not be run or reused without explicit safeguards. This discovery audit did not execute it or disclose identifiers.
- Performance diagnostics aggregate request/repository timing, cache counters and errors; persistence is conditional on diagnostic logging configuration and writes to a bounded sheet. Diagnostic failure is caught to avoid failing user-facing requests. Source shows selected diagnostic response/persistence behavior but privacy, permissions and retention/runtime checks remain unverified.
- Login and permission diagnostics are read-only scripts that produce user/role/permission summaries; output may contain identifiers/usernames and sensitive administrative context if run on live data. They were not run.
- Telegram diagnostics checks secret presence, web-app endpoint behavior and Telegram webhook information; source can report status/errors but live configuration is not inspected.

## V2 implications
1. Replace spreadsheet setup/reset with versioned migrations, declarative schema, seed migrations, integrity checks and explicit destructive-operation gates.
2. Migration preflight must be read-only, repeatable, schema/data aware and validate every FK/unique relationship; execution must be idempotent, transactional where possible and produce structured results.
3. Backups must be independently validated/restorable; never rely on a copy being sufficient. Preserve source and test recovery on sanitized data.
4. Configuration/data migrations should avoid hard-coded production IDs and separate source/target configuration, secrets and environments. Define dry-run, approvals, audit, rollback/recovery and target isolation.
5. Diagnostics must be opt-in, admin-scoped, secret-free, privacy-minimized, bounded and retained under explicit policy.
6. Administrative diagnostics output should be anonymized or protected, and never committed with real identifiers/data.
7. Setup/bootstrap/reset actions in V2 must be inaccessible through ordinary app routes, narrowly authorized and auditable.

## Limits
- Read-only source inspection only; no migration/setup/reset/diagnostic execution, no live data or secrets accessed and no production changes.
- E1 only; G-01/G-10 remain open; schema readiness NOT APPROVED.
