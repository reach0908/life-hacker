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
- **Frontend**: Flutter 3.x (Dart) with Riverpod state management + shadcn_flutter design system
- **Database**: PostgreSQL + Prisma ORM (backend), SQLite (mobile offline storage)
- **AI Integration**: OpenAI GPT-4 + RAG with LangChain
- **Real-time**: WebSocket (Socket.IO)
- **HTTP Client**: Dio with interceptors for API communication
- **Code Generation**: freezed, json_serializable, riverpod_generator
- **External APIs**: GitHub, Linear, Notion, Google Calendar, Apple Health
- **Testing**: Jest (backend), Flutter Test + Widget Test + Integration Test (mobile)

### Domain Architecture (NestJS)
```
life-hacking-api/src/domains/
├── energy-tracking/        # Energy level input and streak tracking
├── routine-ai/            # AI recommendation engine with external data
├── progress-visualization/ # Productivity path and insights  
├── external-integrations/ # GitHub, Calendar, Health sync
└── user-management/       # Authentication and subscription tiers
```

### Mobile Architecture (Flutter)
```
life-hacking-mobile/lib/
├── core/                  # API client, utilities, constants
├── common/                # App theme, shared widgets
└── features/              # Feature modules (Clean Architecture)
    └── {feature}/
        ├── data/          # DTOs, DataSources, Repository implementations
        ├── domain/        # Entities, UseCases, Repository interfaces
        └── presentation/  # Screens, Widgets, Riverpod Providers

Features:
├── energy_tracking/       # Daily energy input UI with streak tracking
├── routine_recommendations/ # AI-generated routine display and completion
├── progress_visualization/ # Productivity path and milestone insights
├── external_integrations/ # GitHub, Calendar, Health API connections
└── user_management/       # Authentication, profile, subscription UI
```

### Recent Changes
- 001-prd-lifehacker-energy: Enhanced Flutter mobile architecture with Riverpod + shadcn_flutter
- Research updated: Added Flutter best practices, Clean Architecture, and offline-first design
- Planning complete: Flutter project structure, dependencies, and implementation approach defined
- Phase 0-1 complete: Backend data model exists, mobile architecture planned

## Important Notes

- Constitution principles are NON-NEGOTIABLE and must be followed
- All phases are gated by constitutional compliance checks
- TDD is strictly enforced - no implementation before failing tests
- Templates contain executable logic and should not be manually edited
- Agent context files are auto-updated to prevent token bloat
- The system is designed for multi-language, multi-project development

**Last updated**: 2025-09-12