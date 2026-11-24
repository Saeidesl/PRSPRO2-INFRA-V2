# Task 2.8 Increment 217 — Reusable event-form response persistence trace

Date: 2026-11-24

## Source
Read-only trace of FormService.js blob `abddf1851d5721347ac07618e85bfa75cef61589`.

## Observed response persistence
- `saveEventResponses` iterates validated responses and inserts/updates EVENT_FORM_DATA rows.
- Captures FormVersion, ResponseJSON and DefinitionSnapshotJSON, tenant/event/form IDs and creator/time.
- Logs audit per response.
- For project-linked events, non-empty response values can update central project information; PROJECT_CODE uses dedicated ProjectService update path, other values use ProjectInformationService.save with SourceEventID.
- Empty/omitted values are intentionally skipped for central information in this create-event response path.
- Returns response count, central information count and project-code update status.

## Risks/evidence gaps
- Event, form response, history, central information and audit are multi-step operations without transaction primitive demonstrated.
- The snapshot is stored at event response level; exhaustive snapshot consumer/report semantics and amendment replay are not verified.
- Partial persistence and duplicate/retry handling across response rows, central-information updates and audit need fault injection/reconciliation.
- Cross-tenant, project visibility and form-definition version tests are not executed.

## Gate impact
G-03 event/form immutability, amendment and history design remains open; G-01 effects/caller coverage and G-10 tenant integrity remain open.

## Safety
Read-only source trace. No production modifications, tests, DDL/migration, live data, provisioning, VPS or deployment.
