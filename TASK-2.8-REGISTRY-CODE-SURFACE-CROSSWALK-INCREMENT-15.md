# Task 2.8 — Registry-to-Code Surface Crosswalk Increment 15
Date: 2026-11-22
Sources: read-only recursive repository tree path inventories of legacy backend/frontend main branches, plus prior source sampling. This crosswalk maps registry domains to likely code surfaces by filename/domain alignment. It is NOT a proof of exact field usage, handler completeness, consumer dependencies or exhaustive caller mapping.

## Domain crosswalk
| Registry domain/entities | Backend surface candidates | Frontend surface candidates | Classification |
|---|---|---|---|
| Tenant/users/departments/roles/permissions | AuthService, AccessControlService, User/Department/Role/Permission repositories and services, UserRoleRepository, RolePermissionRepository, Router | auth, users, departments, roles, permissions, admin | Path-level crosswalk; detailed consumers PARTIAL |
| Projects/status | ProjectService/Repository, ProjectStatusService, ProjectDeletionService, Router | projects, project-detail, project-images, project-workflow-settings, project-statuses, dashboard | Path-level plus previous source sampling |
| Workflow/step/rules/department mapping | WorkflowService/Repository, WorkflowStepDepartment service/repository, WorkflowStepEventRuleRepository, Router | workflows, workflow-department-assignments, project-workflow-settings | Path-level; rule enforcement sampling available |
| Event types/fields/events/data | EventTypeService/Repository, EventFieldRepository, EventService/Repository, EventDataService/Repository, Router | event-types, events, project-detail | Path-level plus prior bounded caller/dispatch evidence |
| Forms and responses | FormService/Repository, EventFormData appears registry but no dedicated filename surfaced in sampled tree; may be handled in FormService/EventService | forms, events | PARTIAL; verify exact repository and route paths |
| Attachments/storage | AttachmentService/Repository, StorageRepository, Router | attachments, project-images, events, projects | Path-level plus previous delete/visibility samples |
| Audit | AuditService/Repository | audit | Path-level only |
| Information types/project information | InformationTypeService/Repository, ProjectInformationService/Repository | information-types, project-detail, events, forms | Path-level plus bounded source evidence |
| Procurement/material planning | ProcurementService/Repository, MaterialPlanningService, ProcurementEventIntegrationService | procurement, material-planning, events | Path-level plus bounded service/test sampling |
| Print/templates/reporting | PrintTemplateService/Repository, ReportService/Repository | print-designer, reports | Path-level only |
| Project reference-copy/sync | ProjectService and ProjectInformationService likely candidates; ProjectReferenceSyncLog is registry evidence | projects, project-detail | Candidate only; exact mutation/read consumers not reconciled |
| Telegram/integration | TelegramBotService | settings/admin possible | Integration path inventory only; scope not reconciled |

## Findings and caution
- Backend tree contains named Service/Repository candidates for most registry domains; filename correspondence is not evidence that all fields/operations are handled.
- Registry entries EventFormData and ProjectReferenceSyncLog do not map to obvious dedicated repository/service names in the sampled tree; candidate hosting paths are hypotheses pending source inspection, not missing-implementation claims.
- Crosswalk is filename/path based; G-01 remains open until exact caller/handler/entity operation trace and disposition is complete.
- No production files read beyond registry and tree path inventory for this increment. No runtime tests, live data, SQL/DDL, migrations, provisioning, deployment or production modifications.

## Next reconciliation
Prioritize relationship and consumer tracing for the high-risk clusters: event/form lifecycle, attachments/storage, procurement allocations/receipts, project deletion/reference-copy, and tenant scope. Record exact source method evidence and unresolved dependencies rather than infer them from filenames.
