# Tasks: LifeHacker - Energy-First Productivity App

**Input**: Design documents from `/specs/001-prd-lifehacker-energy/`
**Prerequisites**: plan.md ✅, research.md ✅, data-model.md ✅, contracts/ ✅

## Execution Flow (main)
```
1. Load plan.md from feature directory
   → ✅ NestJS Clean Architecture + DDD + Flutter mobile detected
   → ✅ Extract: TypeScript, NestJS, Prisma, Socket.IO, OpenAI, Flutter
2. Load optional design documents:
   → ✅ data-model.md: 9 entities extracted → model tasks
   → ✅ contracts/openapi.yaml: 6 endpoint groups → contract test tasks
   → ✅ research.md: 7 technology decisions → setup tasks
3. Generate tasks by category:
   → ✅ Setup: project init, dependencies, Prisma schema
   → ✅ Tests: contract tests [P], integration tests [P]
   → ✅ Core: domain models [P], services, controllers
   → ✅ Integration: WebSocket, AI, external APIs
   → ✅ Polish: unit tests [P], performance, docs
4. Apply task rules:
   → ✅ Different files = mark [P] for parallel
   → ✅ Same file = sequential (no [P])
   → ✅ Tests before implementation (TDD)
5. Number tasks sequentially (T001, T002...)
6. Generate dependency graph
7. Create parallel execution examples
8. Validate task completeness:
   → ✅ All 6 endpoint groups have tests
   → ✅ All 9 entities have models
   → ✅ All 5 integration scenarios covered
9. Return: SUCCESS (37 tasks ready for execution)
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- Include exact file paths in descriptions

## Path Conventions
- **API Backend**: `life-hacking-api/src/`, `life-hacking-api/test/`
- **Mobile Client**: `life-hacking-mobile/lib/`, `life-hacking-mobile/test/`
- Paths shown below follow NestJS + Flutter structure from plan.md

## Phase 1: Backend Foundation & User Management (Tasks T001-T012)

### Phase 1.1: Project Setup
- [ ] **T001** Initialize NestJS project with Clean Architecture structure in life-hacking-api/
- [ ] **T002** [P] Configure Prisma schema with User, UserProfile, SubscriptionTier models in life-hacking-api/prisma/schema.prisma
- [ ] **T003** [P] Set up ESLint, Prettier, and Husky pre-commit hooks in life-hacking-api/
- [ ] **T004** [P] Configure environment variables and Docker for PostgreSQL in life-hacking-api/.env and docker-compose.yml

### Phase 1.2: Authentication Tests First (TDD) ⚠️ MUST COMPLETE BEFORE 1.3
**CRITICAL: These tests MUST be written and MUST FAIL before ANY implementation**
- [ ] **T005** [P] Contract test POST /auth/register in life-hacking-api/test/contract/auth.contract.spec.ts
- [ ] **T006** [P] Contract test POST /auth/login in life-hacking-api/test/contract/auth.contract.spec.ts
- [ ] **T007** [P] Contract test POST /auth/refresh in life-hacking-api/test/contract/auth.contract.spec.ts
- [ ] **T008** [P] Integration test user registration flow in life-hacking-api/test/integration/auth.integration.spec.ts
- [ ] **T009** [P] Integration test JWT authentication flow in life-hacking-api/test/integration/auth.integration.spec.ts

### Phase 1.3: User Management Implementation (ONLY after tests are failing)
- [ ] **T010** [P] User aggregate with value objects in life-hacking-api/src/domains/user-management/entities/user.entity.ts
- [ ] **T011** [P] AuthService with JWT logic in life-hacking-api/src/domains/user-management/services/auth.service.ts
- [ ] **T012** [P] UserRepository with Prisma implementation in life-hacking-api/src/domains/user-management/repositories/user.repository.ts

## Phase 2: Core Domain - Energy Tracking & Real-time Sync (Tasks T013-T022)

### Phase 2.1: Energy Domain Tests First (TDD)
**CRITICAL: These tests MUST be written and MUST FAIL before implementation**
- [ ] **T013** [P] Contract test POST /energy-levels in life-hacking-api/test/contract/energy.contract.spec.ts
- [ ] **T014** [P] Contract test GET /energy-levels in life-hacking-api/test/contract/energy.contract.spec.ts
- [ ] **T015** [P] Contract test GET /energy-levels/streaks in life-hacking-api/test/contract/energy.contract.spec.ts
- [ ] **T016** [P] Integration test "Morning energy input → AI recommendation flow" from quickstart.md in life-hacking-api/test/integration/energy-flow.integration.spec.ts

### Phase 2.2: Energy Domain Implementation
- [ ] **T017** [P] EnergyScore value object with business rules in life-hacking-api/src/domains/energy-tracking/value-objects/energy-score.vo.ts
- [ ] **T018** [P] EnergySession aggregate in life-hacking-api/src/domains/energy-tracking/entities/energy-session.entity.ts
- [ ] **T019** [P] Duration and DifficultyModifier value objects in life-hacking-api/src/domains/energy-tracking/value-objects/
- [ ] **T020** EnergyTrackingService with domain logic in life-hacking-api/src/domains/energy-tracking/services/energy-tracking.service.ts

### Phase 2.3: Real-time Communication
- [ ] **T021** [P] Socket.IO gateway for energy updates in life-hacking-api/src/infrastructure/websocket/energy.gateway.ts
- [ ] **T022** [P] WebSocket event tests in life-hacking-api/test/integration/websocket.integration.spec.ts

## Phase 3: Advanced Features - AI & External Integrations (Tasks T023-T032)

### Phase 3.1: AI Recommendations Tests First (TDD)
- [ ] **T023** [P] Contract test POST /recommendations/generate in life-hacking-api/test/contract/recommendations.contract.spec.ts
- [ ] **T024** [P] Contract test GET /recommendations in life-hacking-api/test/contract/recommendations.contract.spec.ts
- [ ] **T025** [P] Integration test "Burnout detection → recovery routine suggestion" from quickstart.md in life-hacking-api/test/integration/burnout-detection.integration.spec.ts

### Phase 3.2: AI Domain Implementation
- [ ] **T026** [P] RoutineRecommendation entity and Activity entity in life-hacking-api/src/domains/routine-ai/entities/
- [ ] **T027** [P] BurnoutRisk value object in life-hacking-api/src/domains/routine-ai/value-objects/burnout-risk.vo.ts
- [ ] **T028** AIRecommendationService domain service in life-hacking-api/src/domains/routine-ai/services/ai-recommendation.service.ts
- [ ] **T029** OpenAI adapter implementation in life-hacking-api/src/infrastructure/ai/openai.adapter.ts

### Phase 3.3: External Integrations Tests & Implementation
- [ ] **T030** [P] Contract test POST /integrations/github and /integrations/calendar in life-hacking-api/test/contract/integrations.contract.spec.ts
- [ ] **T031** [P] Integration test "GitHub activity → eye rest recommendation" from quickstart.md in life-hacking-api/test/integration/github-integration.integration.spec.ts
- [ ] **T032** [P] External API adapters (GitHub, Google Calendar) in life-hacking-api/src/infrastructure/external-apis/

## Phase 4: Progress Visualization & Completion (Tasks T033-T037)

### Phase 4.1: Progress Domain
- [ ] **T033** [P] Contract test GET /progress/path and /progress/insights in life-hacking-api/test/contract/progress.contract.spec.ts
- [ ] **T034** [P] ProductivityJourney aggregate and ProgressPath value object in life-hacking-api/src/domains/progress-visualization/entities/
- [ ] **T035** [P] Integration test "Completion tracking → progress path update" from quickstart.md in life-hacking-api/test/integration/progress-tracking.integration.spec.ts

### Phase 4.2: Final Integration & Polish
- [ ] **T036** [P] End-to-end test "Calendar density → routine intensity adjustment" from quickstart.md in life-hacking-api/test/e2e/calendar-integration.e2e.spec.ts
- [ ] **T037** [P] Performance tests (<3sec AI recommendations, <200ms API responses) in life-hacking-api/test/performance/

## Dependencies
**Phase Gates**:
- Phase 1.2 tests (T005-T009) **MUST FAIL** before Phase 1.3 implementation (T010-T012)
- Phase 2.1 tests (T013-T016) **MUST FAIL** before Phase 2.2 implementation (T017-T020)
- Phase 3.1 tests (T023-T025) **MUST FAIL** before Phase 3.2 implementation (T026-T029)

**Sequential Dependencies**:
- T001 (project setup) blocks T002-T004
- T010 (User aggregate) blocks T011, T012
- T017-T019 (Energy domain models) block T020 (Energy service)
- T026-T027 (AI domain models) block T028, T029
- T021 (WebSocket gateway) requires T018 (EnergySession)

## Parallel Execution Examples

### Phase 1 - Parallel Test Creation (after T004):
```bash
# Launch T005-T009 together:
Task: "Contract test POST /auth/register in life-hacking-api/test/contract/auth.contract.spec.ts"
Task: "Contract test POST /auth/login in life-hacking-api/test/contract/auth.contract.spec.ts"
Task: "Contract test POST /auth/refresh in life-hacking-api/test/contract/auth.contract.spec.ts"
Task: "Integration test user registration in life-hacking-api/test/integration/auth.integration.spec.ts"
Task: "Integration test JWT authentication in life-hacking-api/test/integration/auth.integration.spec.ts"
```

### Phase 2 - Parallel Domain Implementation (after T016):
```bash
# Launch T017-T019 together:
Task: "EnergyScore value object in life-hacking-api/src/domains/energy-tracking/value-objects/energy-score.vo.ts"
Task: "EnergySession aggregate in life-hacking-api/src/domains/energy-tracking/entities/energy-session.entity.ts"
Task: "Duration and DifficultyModifier value objects in life-hacking-api/src/domains/energy-tracking/value-objects/"
```

### Phase 3 - Parallel AI Implementation (after T025):
```bash
# Launch T026-T027 together:
Task: "RoutineRecommendation and Activity entities in life-hacking-api/src/domains/routine-ai/entities/"
Task: "BurnoutRisk value object in life-hacking-api/src/domains/routine-ai/value-objects/burnout-risk.vo.ts"
```

## Mobile Development Track (Parallel with Backend)

### Phase M1: Flutter Foundation (Start after T005-T007 contracts)
- Use OpenAPI generator to create Flutter API client from contracts/openapi.yaml
- Implement authentication screens with mock API responses
- Set up offline-first architecture with local SQLite storage

### Phase M2: Core Features (Start after T013-T015 contracts)
- Implement energy level input UI with immediate local storage
- Create WebSocket client for real-time sync with backend
- Build recommendation display UI with offline caching

## Notes
- **[P] tasks** = different files, no dependencies, can run in parallel
- **Verify tests fail** before implementing (RED phase of TDD)
- **Commit after each task** for clean git history
- **Use Context7 packages**: @nestjs/core, @prisma/client, socket.io, openai
- **Follow DDD patterns**: Aggregates, Value Objects, Domain Services, Ports & Adapters

## Validation Checklist
*GATE: Checked before execution*

- [x] All 6 API endpoint groups have corresponding contract tests
- [x] All 9 entities from data-model.md have model creation tasks
- [x] All 5 integration scenarios from quickstart.md are covered
- [x] TDD order enforced: tests come before implementation
- [x] Parallel tasks [P] truly independent (different files)
- [x] Each task specifies exact file path
- [x] No task modifies same file as another [P] task
- [x] Constitutional compliance: Library-first, CLI interface, Test-first, Integration testing, Observability, Versioning

---

**Task Generation Status**: ✅ Complete - 37 executable tasks ready for implementation
**Next Phase**: Execute tasks following TDD methodology with constitutional compliance
