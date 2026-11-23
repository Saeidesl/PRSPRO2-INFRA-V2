# Task 2.8 Increment 143 — Backend source-file audit coverage reconciliation

Date: 2026-11-23  
Evidence: E1/E2 — repository tree compared with audit documents and source traces.

## Coverage map (source families; detailed route/test coverage remains partial)
| Backend source family | Audit increments/evidence | Status |
|---|---|---|
| AccessControlService/AuthService/SessionManager/UserService/UserValidator/UserRepository | 106, 120, 137, 141 | Source family traced; identity/session runtime and migration remain open |
| RoleService/RoleValidator/RoleRepository/RolePermissionService/Repository/PermissionService/Repository/UserRoleService/Repository | 107, 125, 141 | Source family traced; route coverage, uniqueness and transactional audit partial |
| DepartmentService/Repository/Validator; ProjectStatusService/Validator; setup scripts | 108, 109, 127, 141 | Source family traced; setup/runtime integrity partial |
| InformationTypeService/Repository/Validator; FormService/Repository/Validator | 110, 111, 113, 124, 127, 141 | Source family traced; exhaustive field/payload/route map partial |
| WorkflowService/Repository/Validator; WorkflowStepRepository; StepDepartment service/repo; StepEventRule repo/validator | 92–102, 128, 140 | Deep source trace; exhaustive mutation/caller/test mapping partial |
| EventService/Repository/Validator; EventDataService/Repository; EventFieldRepository; EventType service/repo/validator | 112–113, 124, 128–139 | Lifecycle trace; route mismatch candidate; exhaustive payload/report/tenant matrix partial |
| ProjectService/Repository/Validator; ProjectInformation service/repo; ProjectDeletionService | 114–116, 139 | Deep lifecycle trace; full dependency/recovery matrix partial |
| MaterialPlanningService; ProcurementEventIntegrationService; Procurement service/repo | 117–119, 128, 140, 136 | Deep calculation/order/receipt/projection trace; cancellation/correction, constraints and runtime partial |
| Attachment service/repo/validator; GoogleDriveStorage; StorageRepository | 103–104, 121, 134 | Upload/copy/delete/owner trace; storage recovery/retention partial |
| Audit service/repo/validator; Report service/repo; PrintTemplate service/repo/validator | 121, 128, 130, 140, 141 | Trace; audit coupling, snapshot consistency and route/test mapping partial |
| GoogleSheetsRepository; DatabaseSetup; ProductionConfigurationMigration; AppConfig; Code; RequestParser; WebApp; Response; ErrorHandler; Constants | 120, 121, 127, 138 | Boundary/schema/config trace; exact full config/schema/route contract partial |
| Diagnostics and setup scripts | 127, 141 | Source trace; no execution, privacy/retention/runtime partial |
| CI/tests/scripts | 132, 142 | Inventory/crosswalk; no execution |

## Reconciliation notes
- Backend repository source-tree inventory contains the service, repository, validator, routing, storage, configuration, diagnostics and test families above; audit evidence exists across increments but not as a complete one-file-to-one-route-to-one-test trace.
- Some increments refer to source file names with naming differences or historical retrieval limitations; tree retrieval resolved selected areas (material planning, role-permission, workflow step repository) but not every caller/test.
- Status means source-family evidence exists; it is not exhaustive behavioral coverage or runtime verification.

## Remaining G-01/G-10 closure work
1. Complete exact route branch/DTO/caller matrix for every nested Router path and HTTP method.
2. Complete service mutation/read effects matrix (create/update/delete/deactivate/archive/void/reconcile) and identify all secondary writes.
3. Reconcile all 41 backend test files against each critical route/invariant; explicitly mark no-test branches.
4. Validate candidate composite tenant FKs/uniques for each relation and indirect/poly links; runtime cross-tenant tests remain required.
5. Produce residual risk acceptance pack; no schema approval until G-01/G-10 evidence and PO acceptance.

## Limits
- Reconciliation inventory only, not closure of G-01/G-10.
- E1/E2 only; no runtime tests, live data or production changes. Schema readiness NOT APPROVED.
