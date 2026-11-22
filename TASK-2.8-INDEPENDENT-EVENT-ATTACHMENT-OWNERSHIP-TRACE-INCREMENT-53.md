# Task 2.8 — Independent Event & Polymorphic Attachment Ownership Trace (Increment 53)
Date: 2026-11-22
Status: Bounded READ-ONLY source trace; no runtime tests.

## Sources
- Legacy backend EventService.js
- Legacy backend AttachmentService.js
- Legacy backend DatabaseSetup.js
Fetched from READ-ONLY production-reference repository.

## Confirmed source observations
### Independent events
- In the sampled EventService listing path, records are first filtered by TenantID. Events without ProjectID are allowed through the tenant filter; project-linked events must resolve to a visible project in the current tenant.
- This supports an independent-event pathway at source level, but does not establish all create/update routes, operation-specific authorization or global policy. Procurement events have their own projection/timeline handling and are not evidence that every independent event is equivalent.
- Individual event retrieval checks event TenantID and, when ProjectID exists, validates parent project tenant and user visibility before returning the event.
- V2 must model event scope explicitly (project-linked vs independent) and enforce valid tenant-owned references; a nullable ProjectID alone should not be treated as the full policy.

### Attachment ownership and visibility
- Attachment upload accepts PROJECT or EVENT owner types in the inspected path and resolves the owner record.
- Owner TenantID must match the current user tenant. PROJECT upload checks project visibility. EVENT upload with ProjectID also validates the parent project's tenant and visibility.
- Download/get visibility similarly checks attachment TenantID, resolves PROJECT/EVENT owner, checks owner tenant, and validates project access for project-linked events; independent events are tenant-only within this sampled helper.
- Project-image copy source and target are both checked for same-tenant ownership and visibility in the inspected path.
- These are service-level legacy safeguards. EntityType/EntityID remains polymorphic and cannot be translated into a normal FK without a deliberate V2 typed-owner model/registry or equivalent integrity design.

## V2 implications (recommendations, not approvals)
1. Represent independent event scope explicitly and validate all event types/operations against the accepted scope policy.
2. Preserve tenant-consistency for event->project, event->event-type/workflow/department/actor references.
3. Attachment owner relation should be typed or registry-validated; enforce tenant consistency and parent visibility at authorization layer.
4. DB-level ownership constraints and centralized authorization are complementary, not substitutes.
5. Include negative tests for cross-tenant PROJECT/EVENT attachment IDs, project-linked event owner access, independent event access, copied attachment lineage and direct-ID bypass attempts.

## Evidence limitations
Bounded source trace only. No live data, secrets, runtime testing, SQL/DDL, migration, database provisioning, production write, deployment or VPS action. G-10/G-04/G-01 remain open; schema readiness NOT APPROVED.
