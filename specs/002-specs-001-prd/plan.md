# Implementation Plan: LifeHacker Documentation Update

**Branch**: `002-specs-001-prd` | **Date**: 2025-09-15 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/Users/jinhokim/dev/life-hacker/specs/002-specs-001-prd/spec.md`

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
Comprehensive documentation update to synchronize all specification documents with current implementation status. The LifeHacker energy-first productivity app has significant backend and mobile implementation completed, requiring documentation to accurately reflect what's been built versus what remains pending.

## Technical Context
**Language/Version**: TypeScript 5.0+ (Backend: Node.js 18+, Mobile: React Native 0.81.4)
**Primary Dependencies**: NestJS + Prisma + PostgreSQL (Backend), React Native + Zustand + TanStack Query (Mobile)
**Storage**: PostgreSQL with Prisma ORM (Backend), MMKV + AsyncStorage (Mobile offline storage)
**Testing**: Jest + Supertest (Backend), Jest + React Native Testing Library (Mobile)
**Target Platform**: Backend API (Node.js), iOS 15+ and Android 8+ (React Native)
**Project Type**: mobile - React Native app with NestJS API backend
**Performance Goals**: Documentation accuracy and maintainability, not performance-critical
**Constraints**: Must reflect actual implementation status, maintain consistency across all documents
**Scale/Scope**: 4 backend domains, 5+ mobile features, 15+ specification documents to update

## Constitution Check
*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity**:
- Projects: 2 (life-hacking-api + life-hacking-mobile) - within constitutional limit
- Using framework directly? YES (documentation update, no new abstractions)
- Single data model? YES (documentation entities, no complex DTOs needed)
- Avoiding patterns? YES (direct documentation update, no over-engineering)

**Architecture**:
- EVERY feature as library? N/A (documentation update, not code implementation)
- Libraries listed: Documentation management, status tracking, consistency validation
- CLI per library: N/A (documentation task, not library development)
- Library docs: llms.txt format planned? YES (agent context files will be updated)

**Testing (NON-NEGOTIABLE)**:
- RED-GREEN-Refactor cycle enforced? N/A (documentation task, not code)
- Git commits show tests before implementation? N/A (documentation update)
- Order: Contract→Integration→E2E→Unit strictly followed? N/A
- Real dependencies used? YES (actual codebase analysis for accuracy)
- Integration tests for: Documentation accuracy validation
- FORBIDDEN: N/A for documentation tasks

**Observability**:
- Structured logging included? N/A (documentation task)
- Frontend logs → backend? N/A
- Error context sufficient? YES (clear tracking of what needs updating)

**Versioning**:
- Version number assigned? YES (documentation version tracking)
- BUILD increments on every change? YES (documentation change tracking)
- Breaking changes handled? YES (migration of outdated documentation)

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

**Structure Decision**: Option 3 (Mobile + API) - life-hacking-mobile/ and life-hacking-api/ already exist

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
- Generate documentation update tasks based on Phase 0 research findings
- Each discrepancy found → documentation update task [P]
- Each implementation status change → documentation sync task [P]
- Each outdated technology reference → correction task [P]
- Validation tasks to ensure consistency

**Documentation Update Ordering Strategy**:
1. **Phase 1**: Core specification updates
   - Update specs/001-prd-lifehacker-energy/spec.md with implementation status
   - Update specs/001-prd-lifehacker-energy/plan.md with correct technology stack
   - Mark [P] for parallel execution (independent documents)

2. **Phase 2**: Technical documentation updates
   - Replace specs/001-prd-lifehacker-energy/data-model.md with current Prisma schema
   - Update specs/001-prd-lifehacker-energy/contracts/ with actual API endpoints
   - Update specs/001-prd-lifehacker-energy/quickstart.md with current setup
   - Mark [P] for parallel execution (different file types)

3. **Phase 3**: Implementation status synchronization
   - Update specs/001-prd-lifehacker-energy/tasks.md completion status
   - Mark all completed backend domains as ✅ IMPLEMENTED
   - Update mobile implementation status with actual progress
   - Correct technology references throughout

4. **Phase 4**: Validation and consistency checks
   - Cross-reference all documents for consistency
   - Validate technology stack alignment
   - Ensure implementation claims match actual codebase
   - Update agent context files with corrected information

**Estimated Output**: 15-20 numbered, ordered documentation update tasks in tasks.md

**Key Differences from Implementation Tasks**:
- Focus on documentation accuracy rather than code implementation
- Validation tasks check against actual codebase rather than creating new code
- Technology correction tasks update outdated references
- Status synchronization tasks mark completed work appropriately

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
| N/A | Documentation update task | No constitutional violations for documentation work |


## Progress Tracking
*This checklist is updated during execution flow*

**Phase Status**:
- [x] Phase 0: Research complete (/plan command)
- [x] Phase 1: Design complete (/plan command)
- [x] Phase 2: Task planning complete (/plan command - describe approach only)
- [ ] Phase 3: Tasks generated (/tasks command)
- [ ] Phase 4: Documentation updates complete
- [ ] Phase 5: Validation passed

**Gate Status**:
- [x] Initial Constitution Check: PASS
- [x] Post-Design Constitution Check: PASS
- [x] All NEEDS CLARIFICATION resolved
- [x] Complexity deviations documented (N/A for documentation task)

---
*Based on Constitution v2.1.1 - See `/memory/constitution.md`*