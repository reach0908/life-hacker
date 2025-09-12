# Implementation Plan: LifeHacker - Energy-First Productivity App

**Branch**: `001-prd-lifehacker-energy` | **Date**: 2025-09-12 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-prd-lifehacker-energy/spec.md`

## Execution Flow (/plan command scope)
```
1. Load feature spec from Input path
   → ✅ Feature spec loaded successfully
2. Fill Technical Context (scan for NEEDS CLARIFICATION)
   → ✅ Mobile + API architecture detected
   → ✅ NestJS backend + Flutter mobile determined from user context
3. Evaluate Constitution Check section below
   → ✅ 2 projects (within max 3), following established patterns
   → ✅ Update Progress Tracking: Initial Constitution Check
4. Execute Phase 0 → research.md
   → ✅ Technology research completed
5. Execute Phase 1 → contracts, data-model.md, quickstart.md, CLAUDE.md
   → ✅ Design artifacts generated
6. Re-evaluate Constitution Check section
   → ✅ No new violations, design follows constitutional principles
   → ✅ Update Progress Tracking: Post-Design Constitution Check
7. Plan Phase 2 → Describe task generation approach (DO NOT create tasks.md)
   → ✅ Task planning approach documented
8. STOP - Ready for /tasks command
```

**IMPORTANT**: The /plan command STOPS at step 7. Phases 2-4 are executed by other commands:
- Phase 2: /tasks command creates tasks.md
- Phase 3-4: Implementation execution (manual or via tools)

## Summary
Energy-first productivity app for tech workers featuring AI-powered daily routine recommendations that adapt to user energy levels and external data from productivity tools. Backend uses NestJS with Clean Architecture/DDD, mobile client built with Flutter. Core innovation: treating rest and recovery as intentional growth rather than reduced performance.

## Technical Context
**Language/Version**: TypeScript (NestJS backend), Dart (Flutter 3.x mobile)  
**Primary Dependencies**: NestJS, Prisma, PostgreSQL, OpenAI API, Riverpod, WebSocket  
**Storage**: PostgreSQL (backend), SQLite (mobile local cache)  
**Testing**: Jest (backend), Flutter Test (mobile)  
**Target Platform**: iOS 15+, Android API 21+ with NestJS backend  
**Project Type**: mobile - Flutter app with NestJS API backend  
**Performance Goals**: <3sec AI recommendations, <200ms API responses, 60fps UI  
**Constraints**: Offline-capable, real-time sync, privacy-compliant data handling  
**Scale/Scope**: 100K MAU target, 15 functional requirements, 9 core entities

## Constitution Check
*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity**:
- Projects: 2 (mobile app + API) - within max of 3 ✓
- Using framework directly? Yes (NestJS, Flutter directly) ✓
- Single data model? Yes (shared Prisma schema + Flutter models) ✓
- Avoiding patterns? Following established NestJS Clean Architecture ✓

**Architecture**:
- EVERY feature as library? Yes (NestJS domain modules + Flutter packages) ✓
- Libraries listed (DDD Bounded Contexts): 
  - **energy-tracking** (domain): EnergySession aggregate, energy scoring, daily tracking
  - **progress-visualization** (domain): ProductivityJourney aggregate, milestone tracking  
  - **external-integrations** (domain): IntegrationHub aggregate, GitHub/Calendar/Health APIs
  - **user-management** (domain): User aggregate, authentication, subscription management
  - **shared-kernel** (domain): Common value objects (Duration, BurnoutRisk, EnergyScore)
- CLI per library: API exposes CLI commands per domain ✓
- Library docs: llms.txt format planned for each domain ✓

**Ports & Adapters (Hexagonal Architecture)**:
- **Inbound Ports (Use Cases)**:
  - RecordEnergyLevelUseCase, GenerateRecommendationUseCase, CompleteActivityUseCase
  - TrackProgressUseCase, DetectBurnoutUseCase, ManageSubscriptionUseCase
- **Outbound Ports (Infrastructure Interfaces)**:
  - EnergySessionRepository, ProductivityJourneyRepository, UserRepository
  - AIRecommendationPort, GitHubIntegrationPort, CalendarIntegrationPort
  - NotificationPort, PaymentPort, EmailServicePort
- **Adapters (Infrastructure Implementation)**:
  - PrismaEnergySessionRepositoryAdapter, OpenAIRecommendationAdapter
  - GitHubWebhookAdapter, GoogleCalendarAdapter, FirebaseNotificationAdapter
  - StripePaymentAdapter, SendGridEmailAdapter

**Domain Services (Pure Business Logic)**:
- AIRecommendationService: Energy-based routine adaptation
- BurnoutDetectionService: Risk calculation and prevention
- StreakCalculationService: Continuous tracking with self-compassion rules
- IntensityAdaptationService: Calendar density and energy-based adjustments

**Application Services (Use Case Orchestration)**:
- EnergyTrackingApplicationService: Coordinates energy recording and recommendations
- ProgressVisualizationApplicationService: Orchestrates journey tracking and insights
- IntegrationManagementApplicationService: Manages external API connections
- UserLifecycleApplicationService: Handles registration, subscription, settings

**Testing (NON-NEGOTIABLE)**:
- RED-GREEN-Refactor cycle enforced? Yes - tests written first ✓
- Git commits show tests before implementation? Yes - strict TDD ✓
- Order: Contract→Integration→E2E→Unit strictly followed? Yes ✓
- Real dependencies used? Yes (actual PostgreSQL, external APIs) ✓
- Integration tests for: new libraries, contract changes, shared schemas? Yes ✓
- FORBIDDEN: Implementation before test, skipping RED phase ✓

**Observability**:
- Structured logging included? Yes (NestJS logger + Flutter logging) ✓
- Frontend logs → backend? Yes (unified error stream) ✓
- Error context sufficient? Yes (user context + technical details) ✓

**Versioning**:
- Version number assigned? v1.0.0 (MAJOR.MINOR.BUILD) ✓
- BUILD increments on every change? Yes ✓
- Breaking changes handled? Yes (parallel tests, migration plan) ✓

## Project Structure

### Documentation (this feature)
```
specs/001-prd-lifehacker-energy/
├── plan.md              # This file (/plan command output)
├── research.md          # Phase 0 output (/plan command)
├── data-model.md        # Phase 1 output (/plan command)
├── quickstart.md        # Phase 1 output (/plan command)
├── contracts/           # Phase 1 output (/plan command)
└── tasks.md             # Phase 2 output (/tasks command - NOT created by /plan)
```

### Source Code (repository root)
```
# Option 3: Mobile + API (detected from Flutter + NestJS context)
life-hacking-api/
├── src/
│   ├── domains/
│   │   ├── energy-tracking/
│   │   ├── routine-ai/
│   │   ├── progress-visualization/
│   │   ├── external-integrations/
│   │   └── user-management/
│   ├── config/
│   └── database/
└── tests/
    ├── contract/
    ├── integration/
    └── unit/

life-hacking-mobile/
├── lib/
│   ├── features/
│   │   ├── energy_tracking/
│   │   ├── routine_recommendations/
│   │   ├── progress_visualization/
│   │   ├── external_integrations/
│   │   └── user_management/
│   ├── shared/
│   └── core/
└── test/
    ├── widget/
    ├── integration/
    └── unit/
```

**Structure Decision**: Option 3 (Mobile + API) - Flutter mobile app with separate NestJS API backend

## Phase 0: Outline & Research
1. **Extract unknowns from Technical Context** above:
   - External API integration patterns (GitHub, Linear, Notion, Google Calendar, Apple Health)
   - AI recommendation architecture (OpenAI/Gemini + RAG implementation)
   - Flutter offline-first architecture with WebSocket synchronization
   - Real-time push notification system design
   - Subscription/payment integration patterns

2. **Generate and dispatch research agents**:
   ```
   Task: "Research external API integration patterns for GitHub, Linear, Notion APIs"
   Task: "Find best practices for OpenAI/Gemini RAG implementation in NestJS"
   Task: "Research Flutter offline-first architecture with WebSocket sync"
   Task: "Find push notification patterns for iOS/Android with NestJS backend"
   Task: "Research subscription management patterns for mobile apps"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]

**Output**: research.md with all technology decisions documented

## Phase 1: Design & Contracts
*Prerequisites: research.md complete*

1. **Extract entities from feature spec** → `data-model.md`:
   - User Profile: preferences, subscription, onboarding progress
   - Energy Level: daily assessment with mood context and timestamp
   - Routine Recommendation: AI-generated suggestions with difficulty/duration
   - Activity: individual tasks within routines with completion tracking
   - External App Data: synchronized productivity and health metrics
   - Progress Path: visual journey representation with milestones
   - Insight Report: weekly/monthly analytics and growth recommendations
   - Burnout Risk Assessment: calculated score based on patterns
   - User Settings: personalization preferences and privacy choices

2. **Generate API contracts** from functional requirements:
   - Authentication endpoints (login, register, refresh)
   - Energy level tracking (POST /energy-levels, GET /energy-levels/streaks)
   - Routine recommendation (POST /recommendations/generate, GET /recommendations/history)
   - External app sync (POST /integrations/github, POST /integrations/calendar)
   - Progress visualization (GET /progress/path, GET /progress/insights)
   - User management (GET/PUT /users/profile, POST /users/subscribe)
   - Output OpenAPI 3.0 schema to `/contracts/`

3. **Generate contract tests** from contracts:
   - One test file per endpoint group
   - Assert request/response schemas match OpenAPI spec
   - Tests must fail initially (no implementation yet)

4. **Extract test scenarios** from user stories:
   - Morning energy input → AI recommendation flow
   - Burnout detection → recovery routine suggestion
   - GitHub activity → eye rest recommendation
   - Completion tracking → progress path update
   - Calendar density → routine intensity adjustment

5. **Update agent file incrementally** (O(1) operation):
   - Run `/scripts/update-agent-context.sh claude` for Claude Code
   - Add: NestJS domains, Flutter features, AI integration, external APIs
   - Preserve manual additions between markers
   - Update recent changes (current plan)
   - Keep under 150 lines for token efficiency

**Output**: data-model.md, /contracts/*, failing contract tests, quickstart.md, CLAUDE.md

## Phase 2: Task Planning Approach
*This section describes what the /tasks command will do - DO NOT execute during /plan*

**Task Generation Strategy**:
- Load `/templates/tasks-template.md` as base
- Generate tasks from Phase 1 design docs (contracts, data model, quickstart)
- Each API endpoint → contract test task [P]
- Each entity → Prisma model + Flutter model creation task [P] 
- Each user story → integration test task
- External API integration tasks (GitHub, Calendar, etc.)
- AI recommendation engine implementation tasks
- Flutter UI component tasks for each feature
- WebSocket real-time sync implementation tasks
- Offline functionality and local storage tasks
- Push notification setup and handling tasks

**Ordering Strategy**:
- TDD order: Contract tests → Integration tests → E2E tests → Unit tests → Implementation
- Dependency order: Models → Services → Controllers → UI components
- External integrations parallel with core features [P]
- AI engine can be developed parallel to basic CRUD [P]

**Estimated Output**: 35-40 numbered, ordered tasks in tasks.md covering backend domains, mobile features, AI integration, external APIs, and testing

**IMPORTANT**: This phase is executed by the /tasks command, NOT by /plan

## Phase 3+: Future Implementation
*These phases are beyond the scope of the /plan command*

**Phase 3**: Task execution (/tasks command creates tasks.md)  
**Phase 4**: Implementation (execute tasks.md following constitutional principles)  
**Phase 5**: Validation (run tests, execute quickstart.md, performance validation)

## Complexity Tracking
*Fill ONLY if Constitution Check has violations that must be justified*

No constitutional violations identified. Design follows established patterns:
- 2 projects (within max 3)
- NestJS Clean Architecture + Flutter standard practices
- TDD methodology strictly enforced
- Real dependencies for integration testing

## Progress Tracking
*This checklist is updated during execution flow*

**Phase Status**:
- [x] Phase 0: Research complete (/plan command)
- [x] Phase 1: Design complete (/plan command)
- [x] Phase 2: Task planning complete (/plan command - describe approach only)
- [ ] Phase 3: Tasks generated (/tasks command)
- [ ] Phase 4: Implementation complete
- [ ] Phase 5: Validation passed

**Gate Status**:
- [x] Initial Constitution Check: PASS
- [x] Post-Design Constitution Check: PASS
- [x] All NEEDS CLARIFICATION resolved
- [x] Complexity deviations documented

---
*Based on Constitution v2.1.1 - See `/memory/constitution.md`*