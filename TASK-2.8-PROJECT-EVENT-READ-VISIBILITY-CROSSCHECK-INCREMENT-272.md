# Task 2.8 — Increment 272: Project/event read visibility cross-path cross-check (2026-11-24)

## Scope
Read-only review of ProjectService._isProjectAvailableToUser_, selected EventService list/get/project-detail paths, and Router GET permission gates.

## Observations
- Project visibility helper grants visibility to tenant admin, creator, users listed in AllowedUserIDs, or users whose DepartmentID matches AvailableDepartmentIDs. Parsing malformed list fields fails closed to empty lists, but creator/admin bypasses are intentional-looking policy semantics requiring confirmation only if policy review requests it.
- EventService.list is tenant-filtered and project-linked events are constrained by a visible-project index. EventService.listByProject and get/project-detail page verify project tenant and visibility before returning project-linked records.
- EventService.get checks tenant and inherited project visibility for project-linked events; independent/general events remain tenant scoped in inspected path.
- Router includes route-level EVENT_VIEW/PROJECT_VIEW gates for selected event read routes; service-level checks are also present in the inspected functions.
- Static source crosswalk only; route enumeration is not exhaustive and does not prove denial test coverage. No confirmed defect.

## Test targets
PV-01..PV-10: creator/admin policy, department/user-list access, malformed ACL fields fail closed, project tenant isolation, event inherited visibility, independent-event tenant isolation, visibility revocation across requests, direct service invocation, paginated timeline access, and consistent list/get behavior.

## Limits
No tests executed, production data or writes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
