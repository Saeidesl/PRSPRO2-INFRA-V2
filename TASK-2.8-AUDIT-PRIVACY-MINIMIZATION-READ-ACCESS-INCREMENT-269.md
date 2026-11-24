# Task 2.8 — Audit Privacy, Payload Minimization and Read Access Boundary (Increment 269)

## Source trace
- AuditService.log serializes OldData and NewData directly as JSON when provided; there is no field-level redaction shown in the inspected method.
- AuditRepository stores audit records and queries by entity/user through collection filtering.
- Router audit list, entity and user routes require REPORT_VIEW. AuditService filters results by current tenant; listByUser additionally validates target user tenant.
- This selected trace is not exhaustive of every caller's audit payload or every audit-viewing path.

## Candidate risk (not a confirmed leak)
Broad snapshot serialization can capture sensitive/irrelevant data if callers pass full records, including potential file metadata, form answers, profile data, secret-like fields or unnecessary values. The source trace alone does not confirm that any credential or sensitive field is currently persisted in audit.

## Test specification
- PRIV-01 Audit snapshot allowlist: sensitive keys (password/token/secret/session/private storage URL) are excluded or redacted.
- PRIV-02 Nested arrays/objects redaction recursively applies.
- PRIV-03 Before/after diff snapshots are bounded by entity policy, not arbitrary full-object serialization.
- PRIV-04 Audit REPORT_VIEW negative permission test for all audit routes.
- PRIV-05 Cross-tenant audit list/entity/user tests.
- PRIV-06 Entity lookup with crafted polymorphic IDs cannot expose other-tenant records.
- PRIV-07 User audit path cannot enumerate or inspect another tenant's users.
- PRIV-08 Audit exports/print/report paths preserve redaction and tenant filter.
- PRIV-09 Audit data retention and purge are explicit and legally/business approved.
- PRIV-10 Audit payload length and truncation policy protects performance and avoids accidental disclosure.

## V2 requirements
- Use explicit audit DTO allowlists, recursive secret redaction and immutable operation metadata.
- Audit access must be permission-scoped and tenant-scoped; consider separate high-sensitivity audit permission for admin/security actions.
- Define retention, export, encryption at rest, access logging and audit-of-audit access.
- Audit snapshots should be structured change diffs and entity version pointers where possible.

## Verification
Selected source trace only; no runtime tests, penetration/privacy tests, data scan or production modifications. This is a risk hypothesis and test blueprint.
