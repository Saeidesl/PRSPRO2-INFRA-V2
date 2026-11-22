# Task 2.8 — Targeted Tenant-Scoped Service Validation Increment 10
Date: 2026-11-22

## Scope
Read-only inspection of exact EventService.workflowProgress and selected ProjectService and ProcurementService tenant/visibility helper patterns in legacy backend. No endpoint execution, live data, tests, or writes.

## Workflow-progress tenant and visibility evidence
The inspected `EventService.workflowProgress(projectId, params)`:
- Validates project ID.
- Resolves current user.
- Loads project by ID.
- Rejects when project is absent, TenantID differs from current user's TenantID, or ProjectService._isProjectAvailableToUser_ returns false.
- Calculates workflow-rule progress using tenant ID and derives event-type names from records filtered to the user's tenant.
- Computes current step name and canCompleteStep indicator.

Project visibility helper source indicates admin or creator access, otherwise project access is based on parsed AvailableDepartmentIDs and AllowedUserIDs; it fails closed when neither grants access.

## Procurement helper evidence (bounded)
ProcurementService._isProjectVisible_ loads/indexes a project and then requires both matching TenantID and ProjectService project availability. The sampled _validateProject_ helper similarly checks tenant ID and, when user supplied, project availability. _validateEvent_ checks event TenantID and linked project visibility when applicable.

## Interpretation
These are positive, source-observed service-level tenant/visibility checks for the inspected helpers. They do not establish that all procurement methods use these helpers consistently, nor that all repository access, cross-entity relations, event visibility, mutation handlers, or polymorphic attachment paths are safe. No test or runtime validation was performed.

## Status
G-10 remains OPEN pending exhaustive tenant-qualified relationship and authorization disposition plus design/validation evidence. G-01 remains OPEN. Schema readiness NOT APPROVED.
