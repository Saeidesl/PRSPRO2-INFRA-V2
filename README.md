# PMS V2 — Independent Production Management System

## Project Goal
Build an independent PMS for garment manufacturing, replacing the current Google Apps Script + Google Sheets architecture only after the new system is complete, validated, approved, and safely migrated.

## Target Architecture
- Backend: Node.js + TypeScript
- Database: PostgreSQL
- Frontend: existing HTML/CSS/JavaScript adapted to the new API
- Infrastructure: Ubuntu, Docker, Docker Compose, Nginx (deployment stage)
- CI/CD: GitHub Actions
- Storage: independent file/object storage (selection pending discovery)

## Safety
- Existing production repositories and systems are read-only references.
- No production data experimentation, uncontrolled dual-writing, or premature migration.
- No VPS purchase or deployment before development and testing justify it.
- Secrets and real user data must never be committed.

## Project Documents
- TODO.md: roadmap and historical task status
- ARCHITECTURE.md: architecture decisions, discovery findings, and pending questions
- CONTRIBUTING.md: repository collaboration, branch/PR, quality, security and documentation conventions
- PHASE-STATUS.md: authoritative reconciled status for accepted milestones and current task
- ARCHITECTURE-STANDARDS.md: draft standards for architecture, API, security, logging and errors
- CI-BASELINE.md: CI inventory and proposed baseline

## Status
Phase 0 — Discovery & Repository Preparation is COMPLETE and accepted (Tasks 0.1, 0.2 and 0.3). Phase 1 Task 1.1 — Repository collaboration conventions is COMPLETE following Product Owner acceptance on 2026-11-22. Task 1.2 — CI baseline inventory/readiness review is COMPLETE (inventory documented in CI-BASELINE.md; no pipelines implemented). Task 1.3 — architecture/API/security/logging/error standards is IN PROGRESS (draft in ARCHITECTURE-STANDARDS.md). No implementation, live data access, migration, deployment or production changes.
