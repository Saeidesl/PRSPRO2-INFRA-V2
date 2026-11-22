# Task 2.7 — Blocker Resolution & Evidence Closure

**Status:** IN PROGRESS — discovery/review only  
**Started:** 2026-11-22  
**Gate:** Physical PostgreSQL schema design remains NOT APPROVED.

## Purpose
Convert open readiness blockers into a traceable evidence and decision-closure package. This task is not permission to create SQL/DDL, migration tooling, database provisioning, or access live/production data.

## Work plan
1. **Blocker traceability and coverage matrix:** map each gap to source evidence, unanswered questions, required decision/evidence, and closure criteria.
2. **G-03 event/form history contract:** trace submit, amendment, void, dynamic payloads, snapshots, revision and reporting dependencies; document confirmed versus unknown behavior.
3. **G-04 attachment ownership and lifecycle:** inventory owner associations, metadata, copy, upload/download, delete, retention, storage-side effects, and recovery boundaries.
4. **G-10 tenant-safe relationship matrix:** identify candidate tenant-owned parent/child relationships, polymorphic pointers, tenant scoping and integrity enforcement needs; avoid asserting SQL constraints without evidence.
5. **G-01 coverage reconciliation:** reconcile registry/header inventory against services, repositories, routes, mutation families and dependency collectors; explicitly classify bounded/unverified coverage.
6. **Closure assessment:** determine whether evidence is sufficient to recommend schema readiness or identify remaining discovery/business-policy gates. Product Owner approval remains required.

## Evidence and safety rules
- Existing production repositories are read-only.
- No live production data or credentials/configuration secrets are accessed.
- Source review does not count as runtime verification.
- Do not infer schema constraints, field precision, nullability, or deletion policies from names alone.
- Unknowns remain explicit and are not silently converted into business decisions.
- No tests are claimed unless executed.

## Initial baseline
Task 2.5 closed as an audit deliverable, with readiness NOT approved. Task 2.6 completed its bounded procurement lifecycle/correction audit and left G-02 open. Task 2.7 is a distinct traceability and evidence-closure work package; it does not assume those blockers are resolved.
