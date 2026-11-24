# Task 2.8 Increment 222 — Attachment access and delete ordering trace

Date: 2026-11-24

## Scope
Read-only retrieval and inspection of AttachmentService.js, focusing on visibility, upload, download and deletion.

## Evidence (E1 source)
- `_isAttachmentVisibleToUser_` checks attachment tenant, entity existence/tenant, and project visibility; for project-linked events it also checks parent project tenant and project visibility. Independent event visibility returns true after event tenant checks.
- Upload validates entity type, loads project/event, checks tenant and project visibility. Event attachment upload also checks parent-project access when event is project-linked.
- Download repeats tenant/visibility checks, rejects DELETED records, then retrieves the stored object and returns Base64 content.
- Delete requires ID and reason, checks tenant/visibility and status, calls StorageRepository.delete first, then marks metadata DELETED, then audits. This order risks orphaned metadata/status if storage delete succeeds but metadata update/audit fails; conversely retry behavior and recovery are not demonstrated. A storage-delete failure appears to interrupt before metadata mutation, but runtime behavior was not tested.
- Copy-project-image path has tenant/source/target/project visibility and source image type/status checks; its persistence and cleanup/recovery were not re-audited in this increment.

## Findings / design implications
Storage lifecycle should use durable, recoverable state transitions (e.g. tombstone intent, idempotent storage delete, reconciliation), retain deletion audit, and specify retention/purge, malware scanning, file ownership taxonomy, limits and failure handling. These are design recommendations only, not decisions adopted.

## Coverage status
Source-only E1. No tests, runtime fault injection, storage reconciliation, retention or purge policy evidence. G-04 remains OPEN; G-01/G-10 remain OPEN. Schema readiness NOT APPROVED.

## Safety
READ-ONLY. No production changes, tests, live data, SQL/DDL, migration, provisioning, deployment or release.
