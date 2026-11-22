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
- TODO.md: roadmap and task status
- ARCHITECTURE.md: architecture decisions, discovery findings, and pending questions
- CONTRIBUTING.md: repository collaboration, branch/PR, quality, security and documentation conventions

## Status
Phase 0 — Discovery & Repository Preparation is COMPLETE (Tasks 0.1, 0.2 and 0.3 accepted/closed). Phase 1 Task 1.1 — Repository collaboration conventions is IN PROGRESS. CONTRIBUTING.md has been added. No implementation, live data access, migration, deployment or production changes.

Repository availability and privacy checks are verified.
