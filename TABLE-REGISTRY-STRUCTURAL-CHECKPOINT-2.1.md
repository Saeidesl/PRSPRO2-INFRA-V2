# Table Registry and Structural Findings — Phase 2.1

Source: read-only inspection of APP_CONFIG.SHEETS in production DatabaseSetup.js on 2026-11-22.

## Registry
- 01_Tenants
- 02_Users
- 03_Departments
- 04_Roles
- 05_Permissions
- 06_UserRoles
- 07_RolePermissions
- 08_Projects
- 09_Workflows
- 10_WorkflowSteps
- 11_EventTypes
- 12_EventFields
- 13_Events
- 14_EventData
- 15_Attachments
- 16_AuditLogs
- 17_WorkflowStepDepartments
- 18_MaterialNeeds
- 18_ProjectStatuses
- 19_PurchaseOrders
- 20_PurchaseOrderItems
- 21_PurchaseAllocations
- 22_MaterialReceipts
- 23_EventTypeProcurementSettings
- 24_ProjectMaterialSpecs
- 25_MaterialNeedCalculations
- 28_InformationTypes
- 29_InformationTypeColumns
- 30_ProjectInformation
- 31_ProjectInformationHistory
- 32_Forms
- 33_FormFields
- 34_EventTypeForms
- 35_EventFormData
- 36_PrintTemplates
- 37_WorkflowStepEventRules
- 38_ProjectReferenceSyncLog

DatabaseSetup also references 08_Projects_Backup_UnusedColumns_ as an auxiliary/backup sheet name; its role remains to be investigated.

## Structural observations
1. Naming collision: two definitions share numeric prefix 18 (MaterialNeeds and ProjectStatuses). V2 identifiers must not derive semantics from sheet prefixes.
2. Non-contiguous numbering: 26 and 27 are absent from source definitions; do not infer missing domain entities.
3. Auxiliary backup sheet: investigate its data and significance before migration, without touching production data.
4. Registry contains tenant-scoped and apparently global entities. Ownership must be confirmed from service behavior, not headers alone.
5. DatabaseSetup source definitions do not prove deployed sheet state; this is a source audit only.

## Provisional V2 implications
- Use stable logical names and IDs independent of legacy sheet numbering.
- Make tenant ownership explicit and enforce tenant-aware relationships.
- Preserve legacy IDs where appropriate but isolate legacy formats from new key generation.
- Auxiliary backup data should not be migrated automatically without investigation and a PO decision.
- Schema design remains gated on completed and approved discovery.

No schema/migrations have been created and production has not been modified.
