# Task 2.8 — Recommended Policy Defaults / Decision Register

**Date:** 2026-11-22  
**Status:** PROPOSALS ONLY — Product Owner acceptance required for business policy.  
**Purpose:** Reduce ambiguity by proposing conservative defaults. These are not implemented rules and do not authorize schema/migrations.

## G-02 Procurement
| decision | recommended default | rationale / impact |
|---|---|---|
| Quantity precision | Decimal quantities; precision and rounding by unit configuration; no binary floating-point accounting | Avoid precision drift; unit catalog must define precision |
| Over-receipt | Block by default; allow only explicit authorized exception with reason and audit | Prevent accidental excess |
| Receipts | Posted receipt immutable; correction through linked reversal and replacement | Preserves audit lineage |
| Receipt units | Receipt unit must match order item's base unit or have an explicit, versioned conversion | Prevent silent conversion |
| General stock | Separate stock destination; never implicitly assign to a project | Avoid misleading project accounting |
| Allocation | Explicit allocations; sum cannot exceed available received/ordered quantity unless separately authorized; cross-project allocation visible and auditable | Integrity and visibility |
| Cancellation | No hard delete; transition to cancelled only after explicit resolution of allocations, outstanding quantity, and posted facts; cancellation cannot erase receipts | Safe lifecycle |
| Replay | Tenant-scoped command idempotency; identical replay returns result, payload mismatch conflicts | Retry-safe |
| Atomicity | Order/receipt/allocation/reversal domain records transactional; external work through durable outbox/reconciliation | Reliability |

## G-03 Event/form history
| decision | recommended default |
|---|---|
| Completed events/responses | Immutable original facts; never destructive edit |
| Corrections | Linked amendment record with reason, author, time, target and changed-value evidence |
| Amendment chain | Allow sequential amendments; each amendment references immediate predecessor and original event/response; reject stale revision |
| Void | Permission-gated, reason required, explicit effects preview; preserve original and lineage |
| Definition snapshots | Retain exact version/snapshot captured at submission |
| Reporting | Default report view shows current effective state and offers original/amendment/void audit history; report behavior must be explicit |
| Retention | No automatic hard purge of business history pending approved retention schedule |
| Transaction | Domain/history/audit state writes in one DB transaction; integrations/projections via outbox |

## G-04 Attachments
| decision | recommended default |
|---|---|
| Ownership | Typed owner relations or validated owner registry; prohibit arbitrary owner IDs |
| Upload | Staged upload + checksum + metadata state machine + retry/reconciliation |
| Deletion | Logical delete first; physical purge separately, restricted and logged |
| Copy | New object/metadata ID; source lineage retained; idempotent copy operation |
| Restore | Restore window configurable by category; no purge until policy/backup gate is met |
| Purge | Admin/retention job with explicit policy, audit, reconciliation; no immediate purge on ordinary delete |
| Security | MIME/size validation, authorization per owner, safe filename, malware-scanning capability assessed before external uploads |
| Storage provider | Provider-neutral interface; provider migration must be tested before switching |

## G-10 Tenant integrity
| decision | recommended default |
|---|---|
| Tenant ownership | All tenant-owned rows contain TenantID |
| Relationship enforcement | Tenant-qualified composite foreign keys/unique keys where applicable; validate same-tenant edges |
| Tenant scope | Tenant-scoped by default; global catalog must be explicitly classified |
| Independent events | Explicit nullable-project type/constraint, not implicit absence |
| Attachments | Tenant + typed owner linkage enforced; polymorphic references must not rely only on application checks |
| Authorization | Centralized API authorization, fail-closed; database constraints are complementary |
| Testing | Cross-tenant negative tests for direct IDs, indirect joins, reports, attachments, exports and background jobs |

## G-01 Coverage reconciliation
| decision | recommended default |
|---|---|
| Manifest | Version-controlled machine-readable source manifest and mapping tables |
| Coverage | 100% of active/used source structures, routes, mutations and consumers mapped or explicitly dispositioned |
| Exceptions | Each exception has rationale, owner, risk, expiry/review date and PO approval |
| Unknowns | No schema decision from an unknown; track as blocker |
| Deprecated structures | Confirmed unused/legacy only after evidence, not naming assumptions |
| Validation | Repository tree, route/handler/caller inventory, source manifest, tests and cross-domain dependency matrix reconciled |

## Approval and implementation gate
- These recommendations require Product Owner acceptance before they become project policy.
- Some retention, unit, approval and accounting decisions may need factory/business input.
- After policy acceptance, technical design may translate them into relational constraints, state machines, API contracts and tests, but schema readiness still requires exhaustive G-01 evidence and other readiness gates.
- No production data, live configuration, schema, migration, deployment or production changes in this package.
