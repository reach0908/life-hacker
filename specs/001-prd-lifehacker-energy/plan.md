# Implementation Plan: LifeHacker Energy-First Productivity App

**Branch**: `001-prd-lifehacker-energy` | **Date**: 2025-09-13 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-prd-lifehacker-energy/spec.md`

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
Energy-first productivity app for tech workers that combines daily energy level input, AI-powered routine recommendations, and adaptive difficulty scaling to prevent burnout while maintaining high productivity. Backend uses NestJS with Clean Architecture (already partially implemented), frontend will be Flutter iOS app with shadcn_flutter design system and Riverpod state management.

## Technical Context
**Language/Version**: Backend: TypeScript/NestJS (existing), Frontend: Dart/Flutter 3.x (new setup)
**Primary Dependencies**: Backend: NestJS, Prisma ORM, PostgreSQL (existing), Frontend: flutter_riverpod, shadcn_flutter, dio (HTTP client)
**Storage**: PostgreSQL with Prisma ORM (backend), local storage/secure storage (mobile)
**Testing**: Backend: Jest (existing), Frontend: Flutter Test (unit), Widget Test, Integration Test
**Target Platform**: iOS 15+ (primary), NestJS API server (existing)
**Project Type**: mobile - Mobile + API (Option 3 from structure)
**Performance Goals**: <100ms app response time, 60fps UI, <50MB memory usage on mobile
**Constraints**: Offline-capable basic recommendations, real-time sync when online, battery-efficient
**Scale/Scope**: 10k initial users, ~15 core screens, 5 main feature domains (energy, routines, progress, integrations, user)

## Constitution Check
*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity**:
- Projects: 2 (life-hacking-api NestJS backend existing, life-hacking-mobile Flutter app new)
- Using framework directly? Yes - Riverpod providers, shadcn_flutter components, Dio HTTP client
- Single data model? No - DTOs needed for API serialization, Entities for business logic
- Avoiding patterns? Repository pattern justified for API abstraction and offline capability

**Architecture**:
- EVERY feature as library? Mobile: Features as self-contained modules with clear boundaries
- Libraries listed: energy-tracking (input/streaks), routine-ai (recommendations), progress-visualization (journey), external-integrations (API sync), user-management (auth/profile)
- CLI per library: Not applicable for mobile app, backend has existing CLI structure
- Library docs: Dart documentation with example usage per feature module

**Testing (NON-NEGOTIABLE)**:
- RED-GREEN-Refactor cycle enforced? Yes - Flutter tests written first for UseCase logic
- Git commits show tests before implementation? Yes - test files committed before implementation
- Order: Contract→Integration→E2E→Unit strictly followed? Yes - API contracts, user workflows, component widgets, business logic
- Real dependencies used? Yes - actual HTTP calls, real SQLite database for integration tests
- Integration tests for: Flutter-backend API integration, WebSocket real-time updates, offline sync
- FORBIDDEN: Implementation before test, skipping RED phase

**Observability**:
- Structured logging included? Yes - Flutter logging to backend via API
- Frontend logs → backend? Yes - unified error stream for debugging
- Error context sufficient? Yes - user context, device info, error stack traces

**Versioning**:
- Version number assigned? Mobile: 1.0.1 (MAJOR.MINOR.BUILD), API: existing versioning
- BUILD increments on every change? Yes - automated via CI/CD
- Breaking changes handled? Yes - API versioning, mobile app migration handling

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

# Option 3: Mobile + API (when "iOS/Android" detected)
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure]
```

**Structure Decision**: Option 3 (Mobile + API) - life-hacking-api/ (NestJS backend, existing) + life-hacking-mobile/ (Flutter iOS app, new)

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

**Task Generation Strategy - Flutter Focus**:
- Load `/templates/tasks-template.md` as base
- Flutter project setup and dependencies (flutter_riverpod, shadcn_flutter, dio)
- Generate Flutter DTO classes from existing backend contracts
- Create Repository interfaces and implementations for API communication
- Build UseCase classes for business logic (Clean Architecture)
- Create Riverpod providers with code generation
- Implement UI screens with shadcn_flutter components
- Add offline capability with SQLite and sync logic

**Ordering Strategy - Mobile Development**:
- TDD order: Tests before implementation (Flutter Test framework)
- Setup: Project creation → Dependencies → Code generation setup
- Architecture: Core utilities → Feature modules → Integration
- Features: Domain layer → Data layer → Presentation layer
- Integration: API communication → WebSocket → Offline sync
- Mark [P] for parallel execution (independent feature modules)

**Estimated Output**: 35-40 numbered, ordered tasks focusing on Flutter mobile app setup and feature implementation

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
- [x] Phase 0: Research complete (/plan command) - Enhanced with Flutter mobile architecture
- [x] Phase 1: Design complete (/plan command) - Existing data model and contracts remain valid
- [x] Phase 2: Task planning complete (/plan command - describe Flutter-focused approach)
- [ ] Phase 3: Tasks generated (/tasks command)
- [ ] Phase 4: Implementation complete
- [ ] Phase 5: Validation passed

**Gate Status**:
- [x] Initial Constitution Check: PASS - 2 projects justified, repository pattern needed
- [x] Post-Design Constitution Check: PASS - Flutter architecture follows clean principles
- [x] All NEEDS CLARIFICATION resolved - Flutter tech stack decided
- [x] Complexity deviations documented - Repository pattern and DTOs justified

---
*Based on Constitution v2.1.1 - See `/memory/constitution.md`*