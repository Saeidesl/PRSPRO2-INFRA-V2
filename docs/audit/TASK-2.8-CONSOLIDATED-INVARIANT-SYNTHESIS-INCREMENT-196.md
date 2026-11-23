# Task 2.8 — Consolidated invariant cross-domain synthesis — Increment 196

Date: 2026-11-23
Evidence level: E1/E2 synthesis of previously recorded bounded source traces; no new production source retrieval or runtime verification in this increment.

## Scope and evidence
This increment synthesizes increments 185–195 and the recovered Phase Status, DATA-MODEL-DRAFT, DOMAIN-MIGRATION-STRATEGY, TESTING-STRATEGY, and CI-BASELINE documents. Production source retrieval was not available through the current GitHub connector in this increment; no new legacy-source facts are claimed.

## Cross-domain invariant candidates (design requirements, not approved schema)
| Boundary | Invariant candidate | Evidence / caveat |
|---|---|---|
| Tenant -> tenant-owned entity | tenant identity must be consistent through relationship edges; tenant context server-derived | E1 service traces; G-10 remains open |
| Tenant -> membership/role | authorization paths must resolve active tenant membership and tenant-scoped roles; permission catalog may be global | sampled source; complete policy matrix not established |
| Project -> event | Project association is optional for independent events; when present, same-tenant/resource visibility validated | selected EventService traces |
| Event -> data/forms | preserve submitted response and definition/form snapshots as needed; amendment lineage and replay semantics must be explicit | source paths partially reviewed; G-03 remains open |
| Event -> workflow | published version/rules and event/step/tenant relationships must remain coherent; rule completeness and concurrency require proof | workflow traces; direct step repository retrieval unresolved |
| Project info -> revision/history | revision/approval/conflict history must not be silently overwritten or detached | sampled ProjectInformationService |
| Material specification -> needs/calculations | revision authority, source provenance, calculation replay key and supersession must be deterministic and transaction-safe | Increment 195; runtime/atomic uniqueness unverified |
| Order/item -> allocations/receipts | allocation/receipt quantity limits, cross-project scope and replay integrity must be enforced transactionally; reversal policy remains unresolved | procurement traces; G-02 open |
| File metadata -> owner/storage | finite owner types, authorization inheritance, lifecycle and compensation/reconciliation must be explicitly modeled | polymorphic owner boundary; G-04 open |
| Audit -> business mutations | audit lineage and redaction/retention policy; avoid inconsistent business write/audit partial commits | sequential writes observed; atomicity unproven |
| Template/report -> source data | bindings and access must be permission-aware; reporting should be read-oriented and not authoritative | sampled source evidence; coverage bounded |
| External delivery/storage -> DB | transactionally record intent; provider effects via outbox/retry/reconciliation, not DB transaction coupling | V2 design candidate |

## Architecture-wide controls to carry forward
1. Tenant-qualified relationship integrity (constraints and service policy), fail-closed resource authorization.
2. Explicit lifecycle/state transitions and immutable history/snapshot rules.
3. Transaction boundaries for multi-row mutations and durable idempotency keys with payload-equivalence semantics.
4. Concurrency protection through DB constraints/locking, not process-local locks alone.
5. Outbox/reconciliation for external storage and notification side effects.
6. Explicit deletion/archive/reversal/retention policy, protecting shared procurement and historical records.
7. Evidence traceability: source operation -> route/API -> permission -> validation -> persistence -> audit/side effect -> test assertion.

## Limitations and blockers
- This is a synthesis, not new source evidence. E1/E2 only; no E3 runtime or E4 live-data evidence.
- G-01 exhaustive operation/dependency/source coverage remains open.
- G-10 tenant-safe relational constraints remain open.
- G-02 procurement reversal/correction contract, G-03 event/form snapshot and payload contract, and G-04 attachment-owner/recovery contract remain open.
- WorkflowStepRepository direct source retrieval remains unresolved.
- Schema readiness remains NOT APPROVED. No DDL, SQL, migration, tests, production data, production modifications, deployment, or VPS action.

## Next audit focus
Resume source retrieval through an alternate authorized GitHub retrieval route. Once available, prioritize the missing direct repository/service traces and reconcile each domain mutation against route, tenant/resource authorization, persistence, audit/side-effect and tests. Do not repeat already-covered increments or infer unverified source details.
