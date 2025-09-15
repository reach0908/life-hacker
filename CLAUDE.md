# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a "life-hacking" project that implements a **Spec-Driven Development (SDD)** workflow system. The repository provides a structured approach to feature development through three main phases: specification, planning, and task execution.

## Core Commands

### Spec-Driven Development Workflow
```bash
# Phase 1: Create feature specification and branch
/.claude/commands/specify "feature description"

# Phase 2: Generate implementation plan from spec
/.claude/commands/plan "additional context"

# Phase 3: Generate executable tasks from plan
/.claude/commands/tasks "context for task generation"
```

### Development Commands
```bash
# Feature management scripts
scripts/create-new-feature.sh "feature description"    # Create new feature branch and structure
scripts/setup-plan.sh --json                           # Setup planning phase
scripts/check-task-prerequisites.sh --json            # Validate task prerequisites
scripts/update-agent-context.sh [claude|gemini|copilot] # Update agent context files

# Common operations
git status && git diff                                 # Check current changes
```

## Project Architecture

### Directory Structure
```
specs/                          # Feature specifications by branch
├── [###-feature-name]/
│   ├── spec.md                # Feature specification (/specify output)
│   ├── plan.md                # Implementation plan (/plan output)  
│   ├── research.md            # Technical research (/plan Phase 0)
│   ├── data-model.md          # Entity definitions (/plan Phase 1)
│   ├── quickstart.md          # Integration test scenarios
│   ├── contracts/             # API contracts and schemas
│   └── tasks.md               # Executable tasks (/tasks output)

templates/                      # Template files for generation
├── spec-template.md           # Feature specification template
├── plan-template.md           # Implementation plan template
├── tasks-template.md          # Task list template
└── agent-file-template.md     # Agent context file template

scripts/                        # Automation scripts
memory/                        # Constitutional principles and guidelines
├── constitution.md            # Core development principles
└── constitution_update_checklist.md

.claude/                       # Claude Code configuration
├── commands/                  # Custom Claude commands
│   ├── specify.md            # /specify command definition
│   ├── plan.md               # /plan command definition
│   └── tasks.md              # /tasks command definition
└── settings.local.json       # Local permissions
```

### Workflow Phases

**Phase 1 - Specification (`/specify`)**
- Creates feature branch with `###-feature-name` format
- Generates `spec.md` from user description
- Focuses on WHAT users need, not HOW to implement
- Must be business-stakeholder readable

**Phase 2 - Planning (`/plan`)**  
- Generates `research.md` (technical decisions)
- Creates `data-model.md`, `contracts/`, `quickstart.md`
- Updates agent context files incrementally
- Enforces constitutional principles
- Stops before task generation

**Phase 3 - Task Generation (`/tasks`)**
- Converts plans into executable, numbered tasks
- Follows TDD order (tests before implementation)
- Marks tasks for parallel execution with [P]
- Creates dependency-ordered task list

### Constitutional Principles

The system enforces strict development principles via `/memory/constitution.md`:

1. **Library-First**: Every feature starts as a standalone library
2. **CLI Interface**: Every library exposes functionality via CLI
3. **Test-First (NON-NEGOTIABLE)**: TDD mandatory - tests written and approved before implementation
4. **Integration Testing**: Focus on contract tests and real dependencies
5. **Observability**: Structured logging and unified error streams
6. **Versioning**: MAJOR.MINOR.BUILD format with breaking change handling
7. **Simplicity**: Maximum 3 projects, avoid unnecessary patterns

## Development Guidelines

### Working with Features
- Always work on feature branches created by `/specify`
- Follow the SDD lifecycle: specify → plan → tasks → implement
- Use constitutional checks to validate designs
- Update agent context files when adding new technologies

### Template System
- Templates are executable with embedded logic
- Use placeholder replacement for dynamic content  
- Templates reference constitution for compliance checks
- Self-contained - readable without external context

### Agent Context Management
- Run `scripts/update-agent-context.sh claude` after planning phase
- Keeps context files under 150 lines for token efficiency
- Preserves manual additions between `<!-- MANUAL ADDITIONS START/END -->` markers
- Updates incrementally (O(1) operation)

## Key Patterns

### Branch Naming
`###-feature-name` (e.g., `001-user-authentication`, `002-task-management`)

### Task Numbering  
`T001`, `T002`, etc. with dependency tracking and parallel execution markers `[P]`

### File Naming
- Specs: `spec.md` (business requirements)
- Plans: `plan.md` (technical approach)
- Tasks: `tasks.md` (executable work items)

### Testing Order
1. Contract tests (API schemas)
2. Integration tests (user scenarios)  
3. End-to-end tests (full workflows)
4. Unit tests (implementation details)

## Current Feature: LifeHacker Energy-First Productivity App

### Technology Stack
- **Backend**: NestJS (TypeScript) with Clean Architecture + DDD + Hexagonal Architecture
- **Mobile**: React Native 0.81.4 (TypeScript) with New Architecture (Fabric + TurboModules)
- **Database**: PostgreSQL + Prisma ORM (backend), MMKV + AsyncStorage (mobile offline storage)
- **State Management**: Zustand (client state) + TanStack Query (server state)
- **Authentication**: Google OAuth + JWT with dual storage strategy
- **HTTP Client**: Axios with interceptors for API communication
- **External APIs**: GitHub, Linear, Notion, Google Calendar, Apple Health (planned)
- **Testing**: Jest (backend), Jest + React Native Testing Library (mobile)

### Domain Architecture (NestJS)
```
life-hacking-api/src/domains/
├── auth/                   # ✅ Google OAuth + JWT authentication
├── user/                   # ✅ User profile and preferences management
├── energy-tracking/        # ✅ Energy level input and productivity tracking
├── routine-recommendations/ # ✅ Rule-based recommendation system
└── [future domains]        # Progress visualization, external integrations
```

### Mobile Architecture (React Native)
```
life-hacking-mobile/src/
├── api/                   # API client configuration
├── components/            # Shared UI components (atoms, molecules, organisms)
├── config/                # App configuration and constants
├── features/              # Feature modules (Container/Presentational pattern)
│   ├── auth/             # ✅ Google OAuth integration
│   ├── onboarding/       # ✅ User onboarding flow
│   └── [planned]/        # energy-tracking, routine-recommendations
├── navigation/            # React Navigation setup
├── services/             # Device services (storage, etc.)
├── state/                # Zustand stores + TanStack Query
├── types/                # TypeScript type definitions
└── utils/                # Utility functions and constants

Features Status:
├── auth/                 # ✅ Google Sign-In + JWT management
├── energy-tracking/      # 🔄 API types ready, UI components pending
├── routine-recommendations/ # ❌ Backend ready, mobile UI not started
└── progress-visualization/  # ❌ Not implemented
```

### Recent Changes
- 002-specs-001-prd: Documentation update to reflect actual implementation status
- Backend: 4 domains fully implemented with Clean Architecture + DDD + CQRS patterns
- Mobile: React Native foundation with auth system complete, energy tracking API integration ready
- Technology correction: React Native (not Flutter) is the actual mobile implementation

## Important Notes

- Constitution principles are NON-NEGOTIABLE and must be followed
- All phases are gated by constitutional compliance checks
- TDD is strictly enforced - no implementation before failing tests
- Templates contain executable logic and should not be manually edited
- Agent context files are auto-updated to prevent token bloat
- The system is designed for multi-language, multi-project development

**Last updated**: 2025-09-12