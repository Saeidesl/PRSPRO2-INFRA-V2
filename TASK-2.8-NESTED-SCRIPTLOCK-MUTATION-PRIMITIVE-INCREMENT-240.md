# Task 2.8 — Nested ScriptLock and mutation primitive crosswalk (Increment 240)
Date: 2026-11-24
Read-only source trace: ProjectDeletionService.remove and GoogleSheetsRepository.updateById/deleteById/insert.

## Observed lock pattern
- ProjectDeletionService.remove acquires LockService.getScriptLock() using tryLock(10000), then holds it across cascade processing until finally releaseLock().
- Its softDelete helper calls GoogleSheetsRepository.deleteById or updateById for each row. GoogleSheetsRepository.deleteById delegates to updateById.
- GoogleSheetsRepository.updateById independently obtains the same ScriptLock and calls waitLock(10000), then releases it in its own finally.
- This creates a nested acquisition of the ScriptLock within a cascade path that already holds the script lock.

## Risk hypothesis (not confirmed)
- Depending on Google Apps Script LockService re-entrancy semantics, nested acquisition may block/time out, potentially causing cascade per-row failures or long delays. No runtime reproduction was performed; do not treat as confirmed defect.
- Repeated per-row updates each acquire/release locks and perform row reads/writes; even absent re-entrancy issues, potential performance/timeout cost for large cascades should be measured in isolated fixtures.
- Other service paths may intentionally use locks and Repository primitives, so broad conclusions must await call-graph inventory.

## Verification needed
- Isolated GAS test/development environment only: outer lock held while updateById called, measure whether inner lock acquisition succeeds and duration.
- If non-reentrant/timeout behavior confirmed, design a lock-aware internal mutation primitive or bulk cascade write path that preserves validation/cache invalidation and avoids lock nesting.
- Capture latency and failure behavior for varied dependency counts; never experiment on production.

## Status
Static source evidence only. Hypothesis unconfirmed. Task 2.8 IN PROGRESS; G-01/G-10 open; schema readiness NOT APPROVED.
