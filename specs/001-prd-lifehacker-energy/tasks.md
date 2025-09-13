# Tasks: LifeHacker - Energy-First Productivity App

**Input**: Design documents from `/specs/001-prd-lifehacker-energy/`
**Prerequisites**: plan.md (✅), research.md (✅), data-model.md (✅), contracts/ (✅), quickstart.md (✅)

## Execution Flow (main)
```
1. Load plan.md from feature directory
   → ✅ Tech stack: NestJS Backend + Flutter Mobile (basic features first)
   → ✅ Extract: Prisma ORM, PostgreSQL, basic CRUD operations
2. Load design documents:
   → ✅ data-model.md: Core entities (User, Energy, Routine, Progress)
   → ✅ contracts/: Basic API endpoints for user flows
   → ✅ quickstart.md: Essential user scenarios
3. Generate tasks by complexity layers:
   → ✅ Phase 1: Basic user management + energy tracking
   → ✅ Phase 2: Simple routine management (manual/preset)
   → ✅ Phase 3: Progress visualization
   → ✅ Phase 4: AI-powered features (separate epic)
   → ✅ Phase 5: Offline capabilities (separate epic)
4. Apply parallel rules:
   → ✅ Backend & Mobile tasks can run simultaneously [B][M]
   → ✅ Integration tests verify basic user flows
   → ✅ TDD: Contract tests → Implementation → Integration
5. Number tasks: T001-T055 (core features only)
6. Dependencies: Setup → Basic Features → Advanced Features
7. Focus: Working product with essential user flows first
   → ✅ Login → Energy Input → Routine Selection → Progress Tracking
```

## Format: `[ID] [B/M] [P?] Description`
- **[B]**: Backend task (NestJS API in `life-hacking-api/`)
- **[M]**: Mobile task (Flutter app in `life-hacking-mobile/`)
- **[P]**: Can run in parallel (different files, no dependencies)
- **Integration**: Backend + Mobile integration verification points

## Phase 1: Project Setup & Core Infrastructure

### Setup (Foundation for both platforms)
- [ ] T001 [B] Update Prisma schema with core entities (User, EnergyLevel, Routine, Progress) in `life-hacking-api/prisma/schema.prisma`
- [ ] T002 [B] Install basic dependencies (validation, auth) in `life-hacking-api/package.json`
- [ ] T003 [B] [P] Configure validation schemas in `life-hacking-api/src/config/validation-schema.ts`
- [ ] T004 [B] [P] Setup JWT authentication config in `life-hacking-api/src/config/auth.config.ts`
- [ ] T005 [M] [P] Create Flutter project with Riverpod state management in `life-hacking-mobile/`
- [ ] T006 [M] [P] Configure basic API client (no offline) in `life-hacking-mobile/lib/core/network/api_client.dart`

### Basic Feature Tests (TDD Phase 1)
**CRITICAL: These tests MUST be written and MUST FAIL before implementation**

#### Backend Contract Tests [B]
- [ ] T007 [B] [P] Contract test POST /auth/register in `life-hacking-api/test/contract/auth-register.e2e-spec.ts`
- [ ] T008 [B] [P] Contract test POST /auth/login in `life-hacking-api/test/contract/auth-login.e2e-spec.ts`
- [ ] T009 [B] [P] Contract test POST /energy-levels in `life-hacking-api/test/contract/energy-levels-post.e2e-spec.ts`
- [ ] T010 [B] [P] Contract test GET /energy-levels in `life-hacking-api/test/contract/energy-levels-get.e2e-spec.ts`
- [ ] T011 [B] [P] Contract test POST /routines in `life-hacking-api/test/contract/routines-post.e2e-spec.ts`
- [ ] T012 [B] [P] Contract test GET /routines in `life-hacking-api/test/contract/routines-get.e2e-spec.ts`

#### Mobile Widget Tests [M]
- [ ] T013 [M] [P] Widget test login form in `life-hacking-mobile/test/widgets/auth/login_form_test.dart`
- [ ] T014 [M] [P] Widget test energy input form in `life-hacking-mobile/test/widgets/energy_input_test.dart`
- [ ] T015 [M] [P] Widget test routine list in `life-hacking-mobile/test/widgets/routine_list_test.dart`

## Phase 2: User Management & Authentication

### Backend User System [B]
- [ ] T016 [B] [P] User entity in `life-hacking-api/src/domains/user-management/domain/entities/user.entity.ts`
- [ ] T017 [B] [P] RegisterUserUseCase in `life-hacking-api/src/domains/user-management/application/use-cases/register-user.use-case.ts`
- [ ] T018 [B] [P] AuthenticateUserUseCase in `life-hacking-api/src/domains/user-management/application/use-cases/authenticate-user.use-case.ts`
- [ ] T019 [B] PrismaUserRepository in `life-hacking-api/src/domains/user-management/infrastructure/persistence/prisma-user.repository.ts`
- [ ] T020 [B] Auth controller in `life-hacking-api/src/domains/auth/presentation/controllers/auth.controller.ts`
- [ ] T021 [B] JWT authentication guard in `life-hacking-api/src/domains/auth/presentation/guards/jwt-auth.guard.ts`

### Mobile Authentication [M]
- [ ] T022 [M] [P] User model in `life-hacking-mobile/lib/features/auth/models/user.dart`
- [ ] T023 [M] [P] Login screen in `life-hacking-mobile/lib/features/auth/screens/login_screen.dart`
- [ ] T024 [M] [P] Register screen in `life-hacking-mobile/lib/features/auth/screens/register_screen.dart`
- [ ] T025 [M] [P] Auth repository in `life-hacking-mobile/lib/features/auth/repositories/auth_repository.dart`
- [ ] T026 [M] [P] Auth state provider in `life-hacking-mobile/lib/features/auth/providers/auth_provider.dart`

### Integration Point 1: Authentication Testing
- [ ] T027 **Integration Test**: Registration → Login → Protected routes (Backend API + Mobile UI)

## Phase 3: Energy Tracking

### Backend Energy System [B]
- [ ] T028 [B] [P] EnergyLevel entity in `life-hacking-api/src/domains/energy-tracking/domain/entities/energy-level.entity.ts`
- [ ] T029 [B] [P] RecordEnergyLevelUseCase in `life-hacking-api/src/domains/energy-tracking/application/use-cases/record-energy-level.use-case.ts`
- [ ] T030 [B] [P] GetEnergyHistoryUseCase in `life-hacking-api/src/domains/energy-tracking/application/use-cases/get-energy-history.use-case.ts`
- [ ] T031 [B] PrismaEnergyLevelRepository in `life-hacking-api/src/domains/energy-tracking/infrastructure/persistence/prisma-energy-level.repository.ts`
- [ ] T032 [B] Energy levels controller in `life-hacking-api/src/domains/energy-tracking/presentation/controllers/energy-levels.controller.ts`

### Mobile Energy Tracking [M]
- [ ] T033 [M] [P] EnergyLevel model in `life-hacking-mobile/lib/features/energy_tracking/models/energy_level.dart`
- [ ] T034 [M] [P] Energy input screen in `life-hacking-mobile/lib/features/energy_tracking/screens/energy_input_screen.dart`
- [ ] T035 [M] [P] Energy history screen in `life-hacking-mobile/lib/features/energy_tracking/screens/energy_history_screen.dart`
- [ ] T036 [M] [P] Energy tracking repository in `life-hacking-mobile/lib/features/energy_tracking/repositories/energy_repository.dart`
- [ ] T037 [M] [P] Energy state provider in `life-hacking-mobile/lib/features/energy_tracking/providers/energy_provider.dart`

### Integration Point 2: Energy Tracking Testing
- [ ] T038 **Integration Test**: Energy input → storage → history display (Backend API + Mobile UI)

## Phase 4: Basic Routine Management

### Backend Routine System [B]
- [ ] T039 [B] [P] Routine entity in `life-hacking-api/src/domains/routine-management/domain/entities/routine.entity.ts`
- [ ] T040 [B] [P] Activity entity in `life-hacking-api/src/domains/routine-management/domain/entities/activity.entity.ts`
- [ ] T041 [B] [P] CreateRoutineUseCase in `life-hacking-api/src/domains/routine-management/application/use-cases/create-routine.use-case.ts`
- [ ] T042 [B] [P] GetRoutinesUseCase in `life-hacking-api/src/domains/routine-management/application/use-cases/get-routines.use-case.ts`
- [ ] T043 [B] PrismaRoutineRepository in `life-hacking-api/src/domains/routine-management/infrastructure/persistence/prisma-routine.repository.ts`
- [ ] T044 [B] Routines controller in `life-hacking-api/src/domains/routine-management/presentation/controllers/routines.controller.ts`

### Mobile Routine Management [M]
- [ ] T045 [M] [P] Routine model in `life-hacking-mobile/lib/features/routines/models/routine.dart`
- [ ] T046 [M] [P] Activity model in `life-hacking-mobile/lib/features/routines/models/activity.dart`
- [ ] T047 [M] [P] Routines list screen in `life-hacking-mobile/lib/features/routines/screens/routines_screen.dart`
- [ ] T048 [M] [P] Routine detail screen in `life-hacking-mobile/lib/features/routines/screens/routine_detail_screen.dart`
- [ ] T049 [M] [P] Create routine screen in `life-hacking-mobile/lib/features/routines/screens/create_routine_screen.dart`
- [ ] T050 [M] [P] Routine repository in `life-hacking-mobile/lib/features/routines/repositories/routine_repository.dart`
- [ ] T051 [M] [P] Routine state provider in `life-hacking-mobile/lib/features/routines/providers/routine_provider.dart`

### Integration Point 3: Routine Management Testing
- [ ] T052 **Integration Test**: Create routine → View routines → Complete activities (Backend API + Mobile UI)

## Phase 5: Basic Progress Tracking

### Backend Progress System [B]
- [ ] T053 [B] [P] Progress entity in `life-hacking-api/src/domains/progress-tracking/domain/entities/progress.entity.ts`
- [ ] T054 [B] [P] TrackProgressUseCase in `life-hacking-api/src/domains/progress-tracking/application/use-cases/track-progress.use-case.ts`
- [ ] T055 [B] [P] GetProgressUseCase in `life-hacking-api/src/domains/progress-tracking/application/use-cases/get-progress.use-case.ts`

### Mobile Progress Display [M]
- [ ] T056 [M] [P] Progress model in `life-hacking-mobile/lib/features/progress/models/progress.dart`
- [ ] T057 [M] [P] Progress screen in `life-hacking-mobile/lib/features/progress/screens/progress_screen.dart`
- [ ] T058 [M] [P] Basic progress charts in `life-hacking-mobile/lib/features/progress/widgets/progress_chart.dart`

### Integration Point 4: End-to-End Basic Flow Testing
- [ ] T059 **E2E Test**: Complete user journey (Login → Energy Input → Select Routine → Complete Activities → View Progress)

---

# 🚀 **EPIC: AI-Powered Features** (Separate Development Phase)
*Implement after basic features are working and tested*

## AI Epic Setup
- [ ] **AI-001** [B] Install OpenAI dependencies and configure client in `life-hacking-api/package.json`
- [ ] **AI-002** [B] Configure OpenAI client in `life-hacking-api/src/config/ai.config.ts`

## AI Recommendation System
- [ ] **AI-003** [B] [P] Contract test POST /recommendations/generate in `life-hacking-api/test/contract/recommendations-generate.e2e-spec.ts`
- [ ] **AI-004** [B] [P] AIRecommendationService in `life-hacking-api/src/domains/routine-ai/domain/services/ai-recommendation.service.ts`
- [ ] **AI-005** [B] [P] OpenAIRecommendationAdapter in `life-hacking-api/src/domains/routine-ai/infrastructure/adapters/openai-recommendation.adapter.ts`
- [ ] **AI-006** [B] GenerateRecommendationUseCase in `life-hacking-api/src/domains/routine-ai/application/use-cases/generate-recommendation.use-case.ts`
- [ ] **AI-007** [B] Recommendations controller in `life-hacking-api/src/domains/routine-ai/presentation/controllers/recommendations.controller.ts`
- [ ] **AI-008** [M] [P] AI recommendations screen in `life-hacking-mobile/lib/features/ai_recommendations/screens/ai_recommendations_screen.dart`
- [ ] **AI-009** [M] [P] AI recommendation widget in `life-hacking-mobile/lib/features/ai_recommendations/widgets/ai_recommendation_widget.dart`

## Burnout Detection & Smart Analytics
- [ ] **AI-010** [B] [P] BurnoutDetectionService in `life-hacking-api/src/domains/analytics/domain/services/burnout-detection.service.ts`
- [ ] **AI-011** [B] [P] AnalyticsController for insights in `life-hacking-api/src/domains/analytics/presentation/controllers/analytics.controller.ts`
- [ ] **AI-012** [M] [P] Burnout warning screen in `life-hacking-mobile/lib/features/analytics/screens/burnout_warning_screen.dart`

## External API Integrations (GitHub, Calendar)
- [ ] **AI-013** [B] [P] GitHubWebhookAdapter in `life-hacking-api/src/domains/external-integrations/infrastructure/adapters/github-webhook.adapter.ts`
- [ ] **AI-014** [B] [P] GoogleCalendarAdapter in `life-hacking-api/src/domains/external-integrations/infrastructure/adapters/google-calendar.adapter.ts`
- [ ] **AI-015** [M] [P] External integrations screen in `life-hacking-mobile/lib/features/integrations/screens/integrations_screen.dart`

## AI Integration Testing
- [ ] **AI-016** **Integration Test**: Energy level → AI recommendation → mobile display flow
- [ ] **AI-017** **Integration Test**: External data → burnout detection → mobile notifications

---

# 📱 **EPIC: Offline Capabilities** (Separate Development Phase)
*Implement after basic online features are stable*

## Offline Infrastructure
- [ ] **OFF-001** [M] Setup SQLite local database schema in `life-hacking-mobile/lib/core/database/offline_database.dart`
- [ ] **OFF-002** [M] [P] Offline sync queue service in `life-hacking-mobile/lib/core/sync/sync_queue_service.dart`
- [ ] **OFF-003** [M] [P] Conflict resolution service in `life-hacking-mobile/lib/core/sync/conflict_resolver.dart`

## Offline Data Management
- [ ] **OFF-004** [M] [P] Offline energy repository in `life-hacking-mobile/lib/features/energy_tracking/repositories/offline_energy_repository.dart`
- [ ] **OFF-005** [M] [P] Offline routine repository in `life-hacking-mobile/lib/features/routines/repositories/offline_routine_repository.dart`
- [ ] **OFF-006** [M] [P] Background sync service in `life-hacking-mobile/lib/core/sync/background_sync_service.dart`

## Offline UI/UX
- [ ] **OFF-007** [M] [P] Offline indicator widget in `life-hacking-mobile/lib/shared/widgets/offline_indicator.dart`
- [ ] **OFF-008** [M] [P] Sync status screen in `life-hacking-mobile/lib/features/sync/screens/sync_status_screen.dart`
- [ ] **OFF-009** [M] [P] Offline mode toggle in app settings

## Offline Integration Testing
- [ ] **OFF-010** **Integration Test**: Offline energy input → background sync → conflict resolution
- [ ] **OFF-011** **Integration Test**: Complete offline routine → sync when online → data consistency

---

# 🎨 **EPIC: Advanced UI/UX Features** (Optional Enhancement Phase)

## Real-time Features
- [ ] **UX-001** [M] [P] WebSocket service for real-time updates in `life-hacking-mobile/lib/core/network/websocket_service.dart`
- [ ] **UX-002** [M] [P] Push notification service in `life-hacking-mobile/lib/core/notifications/notification_service.dart`
- [ ] **UX-003** [M] [P] Advanced progress charts in `life-hacking-mobile/lib/features/progress/widgets/advanced_charts.dart`

---

## Dependencies & Parallel Execution Strategy

### Basic Features Phase Dependencies
```
Phase 1: Setup (T001-T006) → 
Phase 1: Tests (T007-T015) → 
Phase 2: Authentication (T016-T027) → 
Phase 3: Energy Tracking (T028-T038) → 
Phase 4: Routine Management (T039-T052) → 
Phase 5: Progress Tracking (T053-T059)
```

### Advanced Features (Separate Epics)
```
AI Epic: AI-001 → AI-017 (implement after T059)
Offline Epic: OFF-001 → OFF-011 (implement after basic features stable)
UX Epic: UX-001 → UX-003 (optional enhancements)
```

### Key Parallel Opportunities

#### Phase 1: Setup (All can run in parallel after T001-T002)
```bash
# Backend setup tasks
Task: "Configure validation schemas [B]" 
Task: "Setup JWT auth config [B]"

# Mobile setup tasks (parallel to backend)
Task: "Create Flutter project [M]"
Task: "Configure API client [M]"
```

#### Phase 2: Authentication (Backend & Mobile parallel)
```bash
# Backend user system
Task: "User entity [B]"
Task: "RegisterUserUseCase [B]"
Task: "AuthenticateUserUseCase [B]"

# Mobile authentication (simultaneously)
Task: "User model [M]"
Task: "Login screen [M]"
Task: "Register screen [M]"
```

#### Phase 3: Energy Tracking (Backend & Mobile parallel)
```bash
# Backend energy system
Task: "EnergyLevel entity [B]"
Task: "RecordEnergyLevelUseCase [B]"
Task: "Energy levels controller [B]"

# Mobile energy features (simultaneously)
Task: "EnergyLevel model [M]"
Task: "Energy input screen [M]"
Task: "Energy history screen [M]"
```

#### Phase 4: Routine Management (Backend & Mobile parallel)
```bash
# Backend routine system
Task: "Routine entity [B]"
Task: "CreateRoutineUseCase [B]"
Task: "Routines controller [B]"

# Mobile routine features (simultaneously)
Task: "Routine model [M]"
Task: "Routines list screen [M]"
Task: "Create routine screen [M]"
```

### Critical Integration Points
- **T027**: Authentication integration test (blocks Phase 3)
- **T038**: Energy tracking integration test (blocks Phase 4) 
- **T052**: Routine management integration test (blocks Phase 5)
- **T059**: End-to-end basic flow test (blocks advanced epics)

## Success Criteria

### Basic Features Implementation (Core MVP)
- ✅ **Authentication**: Users can register, login, and access protected features
- ✅ **Energy Tracking**: Users can input daily energy levels and view history
- ✅ **Routine Management**: Users can create, view, and complete basic routines
- ✅ **Progress Tracking**: Users can see their progress and completion stats
- ✅ **Full-Stack Integration**: Backend NestJS API + Flutter mobile app working together
- ✅ **Contract-First Development**: All API endpoints tested before implementation

### Essential User Flows (Must Work End-to-End)
- ✅ **Registration Flow**: New user signup → email confirmation → profile setup
- ✅ **Daily Routine**: Login → Energy input → Select routine → Complete activities → View progress
- ✅ **History & Progress**: View energy history, routine completion rates, and progress charts

### Performance Benchmarks (Basic Features)
- ✅ **Backend API**: <200ms response times for CRUD operations
- ✅ **Mobile UI**: Smooth navigation and form submissions
- ✅ **Data Persistence**: Reliable storage and retrieval of user data

### Advanced Features (Separate Epics - Optional)
- 🚀 **AI Recommendations**: Energy-based routine suggestions (AI Epic)
- 📱 **Offline Capability**: Mobile app works offline with sync (Offline Epic)
- 🎨 **Real-time Updates**: WebSocket connections and push notifications (UX Epic)
- 🔗 **External Integrations**: GitHub and Calendar data integration (AI Epic)

## Development Approach Benefits

### Basic Features First Strategy
- **Working Product Quickly**: Essential user flows operational before advanced features
- **Reduced Complexity**: Focus on core functionality without AI/offline complexity
- **Better Testing**: Stable foundation before adding complex integrations
- **User Validation**: Real user feedback on core features before investing in AI

### Parallel Development Advantages
- **Faster Feedback**: API design issues discovered immediately through mobile development
- **Real Integration Testing**: Actual mobile app validates backend functionality continuously
- **Better UX Design**: Mobile experience drives backend API design improvements
- **Risk Reduction**: Integration issues caught early in basic features

### Epic-Based Advanced Features
- **Modular Development**: AI, Offline, and UX features as separate, optional epics
- **Incremental Value**: Each epic adds significant value independently
- **Resource Flexibility**: Can prioritize epics based on user needs and resources
- **Reduced Risk**: Advanced features don't block core functionality

## Technical Architecture Validation

### Backend (NestJS) - Basic Features
- ✅ **Clean Architecture**: Domain → Application → Infrastructure → Presentation layers
- ✅ **DDD Patterns**: User, EnergyLevel, Routine, Progress entities with proper boundaries
- ✅ **CRUD Operations**: Standard create, read, update, delete for all core entities
- ✅ **TDD Compliance**: All tests written before implementation

### Mobile (Flutter) - Basic Features
- ✅ **Feature Architecture**: Clean separation by domain (auth, energy, routines, progress)
- ✅ **State Management**: Riverpod for predictable state updates
- ✅ **Online-Only**: Simple HTTP API calls without offline complexity
- ✅ **Standard UI**: Form inputs, lists, basic charts without advanced animations

## Validation Checklist
*GATE: Checked before execution*

- [x] Basic features prioritized over advanced AI/offline features
- [x] Backend and Mobile tasks properly separated with [B][M] markers
- [x] Core user flows covered: Auth → Energy → Routines → Progress
- [x] Advanced features properly separated into optional epics
- [x] TDD maintained: Tests before implementation in all phases
- [x] Each task specifies exact file paths for both platforms
- [x] Clear phase dependencies: Setup → Auth → Energy → Routines → Progress
- [x] Integration tests at each phase to verify end-to-end functionality

**Ready for basic feature development with advanced features as separate epics! 🎯**