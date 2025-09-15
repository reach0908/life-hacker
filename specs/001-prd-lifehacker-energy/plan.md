# Implementation Plan: LifeHacker Energy-First Productivity App

**Branch**: `001-prd-lifehacker-energy` | **Date**: 2025-09-14 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/spec.md`

## Execution Flow (/plan command scope)
```
1. Load feature spec from Input path
   → If not found: ERROR "No feature spec at {path}"
2. Fill Technical Context (scan for NEEDS CLARIFICATION)
   → Detect Project Type from context (web=frontend+backend, mobile=app+api)
   → Set Structure Decision based on project type
3. Evaluate Constitution Check section below
   → If violations exist: Document in Complexity Tracking
   → If no justification possible: ERROR "Simplify approach first"
   → Update Progress Tracking: Initial Constitution Check
4. Execute Phase 0 → research.md
   → If NEEDS CLARIFICATION remain: ERROR "Resolve unknowns"
5. Execute Phase 1 → contracts, data-model.md, quickstart.md, agent-specific template file (e.g., `CLAUDE.md` for Claude Code, `.github/copilot-instructions.md` for GitHub Copilot, or `GEMINI.md` for Gemini CLI).
6. Re-evaluate Constitution Check section
   → If new violations: Refactor design, return to Phase 1
   → Update Progress Tracking: Post-Design Constitution Check
7. Plan Phase 2 → Describe task generation approach (DO NOT create tasks.md)
8. STOP - Ready for /tasks command
```

**IMPORTANT**: The /plan command STOPS at step 7. Phases 2-4 are executed by other commands:
- Phase 2: /tasks command creates tasks.md
- Phase 3-4: Implementation execution (manual or via tools)

## Summary
Energy-first productivity app that adapts daily routines based on user energy levels and external data integrations. ✅ **IMPLEMENTED REACT NATIVE APPLICATION** built with Container/Presentational architecture, Zustand + TanStack Query state management, and NativeWind styling for iOS-first deployment with future web expansion capability.

## Technical Context *(Updated 2025-09-15 - Actual Implementation)*
**Language/Version**: ✅ TypeScript 5.0+ with React Native 0.81.4 (New Architecture enabled)
**Primary Dependencies**: ✅ React Native, Zustand, TanStack Query, NativeWind (not React Native Reusables), React Navigation
**Storage**: ✅ AsyncStorage + MMKV (high-performance storage), PostgreSQL + Prisma (backend API)
**Testing**: ✅ Jest + React Native Testing Library (implemented), Contract tests (532+ passing backend tests)
**Target Platform**: ✅ iOS 15+ (primary), Android 8+ (secondary), React Native Web (future expansion)
**Project Type**: ✅ mobile - React Native app with NestJS API backend (both implemented)
**Performance Goals**: ✅ 60fps UI, <100ms API response, <3s app launch time, New Architecture (Fabric + TurboModules)
**Constraints**: ✅ <150MB memory usage, offline-capable with rule-based fallbacks (implemented), privacy-first data handling
**Scale/Scope**: 🔄 10k+ users (capacity ready), 20-30 screens (7 implemented), 5 external integrations (0 implemented yet)

## Constitution Check
*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity**:
- Projects: 2 (life-hacking-mobile + life-hacking-api) - within constitutional limit
- Using framework directly? YES (React Native + NestJS, no unnecessary abstractions)
- Single data model? YES (shared TypeScript types between mobile and API)
- Avoiding patterns? YES (direct React Native patterns, no over-engineering)

**Architecture**:
- EVERY feature as library? YES (feature modules in /src/features/, each self-contained)
- Libraries listed:
  - energy-tracking: energy input and streak tracking
  - routine-ai: AI recommendations engine
  - progress-visualization: productivity insights
  - external-integrations: GitHub/Calendar/Health sync
  - user-management: auth and subscriptions
- CLI per library: NO (mobile app, but each feature module exports testable functions)
- Library docs: llms.txt format planned? YES (for AI context)

**Testing (NON-NEGOTIABLE)**:
- RED-GREEN-Refactor cycle enforced? YES (test-first development mandatory)
- Git commits show tests before implementation? YES (enforced by workflow)
- Order: Contract→Integration→E2E→Unit strictly followed? YES
- Real dependencies used? YES (actual API calls, real storage, no mocks for integration tests)
- Integration tests for: feature modules, API contracts, external integrations
- FORBIDDEN: Implementation before test, skipping RED phase

**Observability**:
- Structured logging included? YES (unified logging to backend API)
- Frontend logs → backend? YES (structured error reporting and analytics)
- Error context sufficient? YES (error boundaries, crash reporting)

**Versioning**:
- Version number assigned? YES (MAJOR.MINOR.BUILD format)
- BUILD increments on every change? YES (automated versioning)
- Breaking changes handled? YES (migration strategies, backward compatibility)

## Project Structure

### Documentation (this feature)
```
specs/[###-feature]/
├── plan.md              # This file (/plan command output)
├── research.md          # Phase 0 output (/plan command)
├── data-model.md        # Phase 1 output (/plan command)
├── quickstart.md        # Phase 1 output (/plan command)
├── contracts/           # Phase 1 output (/plan command)
└── tasks.md             # Phase 2 output (/tasks command - NOT created by /plan)
```

### Source Code (repository root)
```
# Option 1: Single project (DEFAULT)
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# Option 2: Web application (when "frontend" + "backend" detected)
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# Option 3: Mobile + API (SELECTED - React Native architecture)
life-hacking-api/                # NestJS backend (existing submodule)
└── [existing NestJS structure with Clean Architecture + DDD]

life-hacking-mobile/             # React Native app
├── android/                     # Android-specific files
├── ios/                         # iOS-specific files
├── src/
│   ├── api/                     # TanStack Query setup and hooks
│   ├── components/              # Atomic design system (atoms/molecules/organisms)
│   ├── features/                # Feature modules (Container/Presentational)
│   │   ├── energy-tracking/
│   │   ├── routine-ai/
│   │   ├── progress-visualization/
│   │   ├── external-integrations/
│   │   └── user-management/
│   ├── hooks/                   # Global custom hooks
│   ├── navigation/              # React Navigation setup
│   ├── state/                   # Zustand stores and TanStack Query client
│   ├── styles/                  # NativeWind global styles
│   └── utils/
└── tests/                       # Test files mirroring src structure
```

**Structure Decision**: Option 3 selected - Mobile + API architecture for React Native app with existing NestJS backend

## Phase 0: Outline & Research
1. **Extract unknowns from Technical Context** above:
   - For each NEEDS CLARIFICATION → research task
   - For each dependency → best practices task
   - For each integration → patterns task

2. **Generate and dispatch research agents**:
   ```
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]

**Output**: research.md with all NEEDS CLARIFICATION resolved

## Phase 1: Design & Contracts
*Prerequisites: research.md complete*

1. **Extract entities from feature spec** → `data-model.md`:
   - Entity name, fields, relationships
   - Validation rules from requirements
   - State transitions if applicable

2. **Generate API contracts** from functional requirements:
   - For each user action → endpoint
   - Use standard REST/GraphQL patterns
   - Output OpenAPI/GraphQL schema to `/contracts/`

3. **Generate contract tests** from contracts:
   - One test file per endpoint
   - Assert request/response schemas
   - Tests must fail (no implementation yet)

4. **Extract test scenarios** from user stories:
   - Each story → integration test scenario
   - Quickstart test = story validation steps

5. **Update agent file incrementally** (O(1) operation):
   - Run `/scripts/update-agent-context.sh [claude|gemini|copilot]` for your AI assistant
   - If exists: Add only NEW tech from current plan
   - Preserve manual additions between markers
   - Update recent changes (keep last 3)
   - Keep under 150 lines for token efficiency
   - Output to repository root

**Output**: data-model.md, /contracts/*, failing tests, quickstart.md, agent-specific file

## Phase 2: Task Planning Approach
*This section describes what the /tasks command will do - DO NOT execute during /plan*

**Task Generation Strategy**:
- Load `/templates/tasks-template.md` as base
- Generate tasks from Phase 1 design docs (contracts, data model, quickstart)
- Each contract → contract test task [P]
- Each entity → model creation task [P] 
- Each user story → integration test task
- Implementation tasks to make tests pass

**Ordering Strategy**:
- TDD order: Tests before implementation 
- Dependency order: Models before services before UI
- Mark [P] for parallel execution (independent files)

**Estimated Output**: 25-30 numbered, ordered tasks in tasks.md

**IMPORTANT**: This phase is executed by the /tasks command, NOT by /plan

## Phase 3+: Future Implementation
*These phases are beyond the scope of the /plan command*

**Phase 3**: Task execution (/tasks command creates tasks.md)  
**Phase 4**: Implementation (execute tasks.md following constitutional principles)  
**Phase 5**: Validation (run tests, execute quickstart.md, performance validation)

## Complexity Tracking
*Fill ONLY if Constitution Check has violations that must be justified*

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |


## Progress Tracking
*This checklist is updated during execution flow*

**Phase Status**:
- [x] Phase 0: Research complete (/plan command) - React Native development research finalized
- [x] Phase 1: Design complete (/plan command) - Data models updated for React Native TypeScript
- [x] Phase 2: Task planning complete (/plan command - describe approach only)
- [ ] Phase 3: Tasks generated (/tasks command)
- [ ] Phase 4: Implementation complete
- [ ] Phase 5: Validation passed

**Gate Status**:
- [x] Initial Constitution Check: PASS - 2 projects within limit, library architecture confirmed
- [x] Post-Design Constitution Check: PASS - React Native patterns align with constitutional principles
- [x] All NEEDS CLARIFICATION resolved - React Native technology decisions made
- [x] Complexity deviations documented - None required, architecture fits constitutional constraints

---
*Based on Constitution v2.1.1 - See `/memory/constitution.md`*