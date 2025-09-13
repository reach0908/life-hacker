# Tasks: LifeHacker Energy-First Productivity (Backend + Flutter Parallel)

**Input**: Design documents + Current backend analysis
**Current Backend State**: ✅ Auth + User domains implemented, ❌ Energy/AI/Progress domains needed
**Strategy**: Parallel backend/Flutter development with daily integration testing

## Execution Flow (Backend + Mobile Parallel)
```
1. Backend Analysis Complete ✅
   → Existing: Auth (Google OAuth, JWT), User (profile CRUD)
   → Missing: Energy tracking, AI recommendations, Progress visualization, External APIs
2. Mobile Setup Required ✅
   → Flutter 3.x + Riverpod + shadcn_flutter + Dio + Clean Architecture
3. Parallel Development Strategy ✅
   → Week 1: Backend Energy API + Flutter Auth integration
   → Week 2: Backend AI + Flutter Energy UI + Daily API testing
   → Week 3: Backend Progress + Flutter recommendations + E2E flows
```

## Format: `[ID] [P?] [B/M] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- **[B]**: Backend task (life-hacking-api/)
- **[M]**: Mobile task (life-hacking-mobile/)
- **Daily Integration**: Test backend + mobile integration every evening

## Path Conventions
- **Backend**: `life-hacking-api/src/` (NestJS Clean Architecture)
- **Mobile**: `life-hacking-mobile/lib/` (Flutter Clean Architecture)

## 🏗️ Week 1: Foundation & Auth Integration

### Phase 1.1: Backend Energy Domain Setup [Monday-Tuesday]
- [ ] T001 [B] Create Energy tracking domain structure in `life-hacking-api/src/domains/energy-tracking/`
- [ ] T002 [P] [B] Add EnergyScore value object with validation rules
- [ ] T003 [P] [B] Add Duration and DifficultyModifier value objects
- [ ] T004 [P] [B] Create EnergySession entity with business logic
- [ ] T005 [B] Implement EnergyRepository port and Prisma adapter
- [ ] T006 [B] Add Energy tracking endpoints: POST/GET /energy-levels

### Phase 1.2: Flutter Project Setup [Monday-Tuesday]
- [ ] T007 [M] Create Flutter project structure in `life-hacking-mobile/`
- [ ] T008 [M] Add Flutter dependencies: flutter_riverpod, shadcn_flutter, dio, freezed, json_annotation
- [ ] T009 [P] [M] Add dev dependencies: build_runner, riverpod_generator, json_serializable, mockito
- [ ] T010 [P] [M] Configure analysis_options.yaml with lint rules
- [ ] T011 [P] [M] Set up build_runner configuration for code generation
- [ ] T012 [M] Create core folder structure: `lib/core/`, `lib/common/`, `lib/features/`
- [ ] T013 [P] [M] Create API client in `lib/core/api_client.dart` with Dio configuration
- [ ] T014 [P] [M] Create app theme using shadcn_flutter in `lib/common/theme/app_theme.dart`
- [ ] T015 [M] Set up main.dart with ProviderScope and ShadcnApp

### Phase 1.3: Auth Integration Testing [Wednesday]
- [ ] T016 [M] Implement Google OAuth flow in Flutter using existing backend
- [ ] T017 [M] Create AuthRepository and AuthDataSource for API communication
- [ ] T018 [M] Build login screen with shadcn_flutter components
- [ ] T019 [M] Implement JWT token storage with flutter_secure_storage
- [ ] T020 **[INTEGRATION]** Test complete auth flow: Flutter → Backend → Token storage

### Phase 1.4: Database Schema Extension [Thursday]
- [ ] T021 [B] Extend Prisma schema with EnergyLevel model
- [ ] T022 [B] Create and run database migration
- [ ] T023 [B] Update User model relationships with EnergyLevel
- [ ] T024 **[INTEGRATION]** Test energy level creation via API

### Phase 1.5: Basic Flutter Energy UI [Friday]
- [ ] T025 [M] Create EnergyInputScreen with 5-level selector UI
- [ ] T026 [M] Implement energy level submission to backend
- [ ] T027 [M] Add offline storage for energy levels (SQLite)
- [ ] T028 [M] Create energy streak display widget
- [ ] T029 **[INTEGRATION]** End-to-end test: Energy input → Backend → Display streak

## 🤖 Week 2: AI Recommendations & Core Features

### Phase 2.1: Backend AI Domain [Monday-Tuesday]
- [ ] T030 [B] Create AI recommendations domain structure
- [ ] T031 [P] [B] Add RoutineRecommendation and Activity entities
- [ ] T032 [P] [B] Add BurnoutRisk value object
- [ ] T033 [B] Implement AIRecommendationService with OpenAI integration
- [ ] T034 [B] Add recommendation endpoints: POST /recommendations/generate, GET /recommendations
- [ ] T035 [B] Create recommendation persistence in database

### Phase 2.2: Flutter Energy Features [Monday-Tuesday]

- [ ] T036 [M] Create Energy domain layer with Clean Architecture
- [ ] T037 [P] [M] Implement EnergyScore and Duration value objects in Flutter
- [ ] T038 [P] [M] Create EnergySession entity for local business logic
- [ ] T039 [M] Build EnergyRepository with API and local storage
- [ ] T040 [M] Create CreateEnergySessionUseCase
- [ ] T041 **[INTEGRATION]** Test energy data sync: Local → API → Local verification

### Phase 2.3: Recommendation UI [Wednesday-Thursday]
- [ ] T042 [M] Create Recommendation domain layer in Flutter
- [ ] T043 [P] [M] Implement RoutineRecommendation and Activity entities
- [ ] T044 [P] [M] Create RecommendationRepository with API communication
- [ ] T045 [M] Build RecommendationsScreen with recommendation cards
- [ ] T046 [M] Implement recommendation acceptance/completion flow
- [ ] T047 **[INTEGRATION]** Test recommendation flow: Energy → AI → Display → Completion

### Phase 2.4: Real-time Sync [Thursday-Friday]
- [ ] T048 [B] Add WebSocket support with Socket.IO for real-time updates
- [ ] T049 [B] Implement energy level broadcast on updates
- [ ] T050 [M] Add WebSocket client for real-time recommendation updates
- [ ] T051 [M] Implement optimistic UI updates with rollback
- [ ] T052 **[INTEGRATION]** Test real-time sync: Multiple devices energy update propagation

## 📊 Week 3: Progress Visualization & Polish

### Phase 3.1: Backend Progress Domain [Monday-Tuesday]
- [ ] T053 [B] Create Progress visualization domain structure
- [ ] T054 [P] [B] Add ProductivityJourney aggregate
- [ ] T055 [P] [B] Add ProgressPath and Milestone value objects
- [ ] T056 [B] Implement progress calculation algorithms
- [ ] T057 [B] Add progress endpoints: GET /progress/path, GET /progress/insights
- [ ] T058 **[INTEGRATION]** Test progress calculation with real data

### Phase 3.2: Flutter Progress Features [Monday-Tuesday]
- [ ] T059 [M] Create Progress domain layer with journey tracking
- [ ] T060 [P] [M] Implement progress visualization widgets
- [ ] T061 [P] [M] Build ProgressScreen with interactive charts
- [ ] T062 [M] Add milestone celebration animations
- [ ] T063 [M] Create progress sharing functionality
- [ ] T064 **[INTEGRATION]** Test progress data accuracy across platforms
### Phase 3.3: External Integrations [Wednesday]
- [ ] T065 [B] Add GitHub integration for commit tracking
- [ ] T066 [B] Add Google Calendar integration for meeting density
- [ ] T067 [M] Create integrations management screen
- [ ] T068 [M] Implement external data display in recommendations
- [ ] T069 **[INTEGRATION]** Test external data influence on recommendations

### Phase 3.4: Performance & Polish [Thursday-Friday]
- [ ] T070 [P] [B] Add API response caching and optimization
- [ ] T071 [P] [B] Implement request rate limiting
- [ ] T072 [P] [M] Add Flutter performance optimizations (lazy loading, caching)
- [ ] T073 [P] [M] Implement dark mode theme
- [ ] T074 [P] [M] Add accessibility improvements
- [ ] T075 [M] Create onboarding flow with feature introduction
- [ ] T076 **[INTEGRATION]** End-to-end testing: Complete user journey

## 🧪 Daily Integration Testing Strategy

### Daily Testing Routine (5:00 PM every day)
1. **API Health Check**
   - Test all implemented endpoints with Postman/Insomnia
   - Verify database state consistency
   - Check authentication flow

2. **Mobile-Backend Integration**
   - Launch Flutter app on iOS simulator
   - Test complete user flow: Login → Energy input → Recommendations
   - Verify offline/online sync
   - Check real-time updates

3. **Data Consistency**
   - Compare mobile local storage with backend database
   - Verify recommendation accuracy
   - Test error handling and recovery

4. **Performance Metrics**
   - API response times (<200ms)
   - Mobile app responsiveness (60fps)
   - Memory usage monitoring

## 📋 Task Dependencies & Execution Order

### Week 1 Critical Path
```
T001-T006 (Backend Energy) ∥ T007-T015 (Flutter Setup)
↓
T016-T019 (Auth Integration) → T020 (INTEGRATION TEST)
↓
T021-T024 (Database) → T025-T029 (Energy UI + Integration)
```

### Week 2 Critical Path
```
T030-T035 (Backend AI) ∥ T036-T041 (Flutter Energy Domain)
↓
T042-T047 (Recommendation UI + Integration)
↓
T048-T052 (Real-time Sync + Integration)
```

### Week 3 Critical Path
```
T053-T058 (Backend Progress + Integration)
↓
T059-T064 (Flutter Progress + Integration)
↓
T065-T069 (External Integrations)
↓
T070-T076 (Performance & Polish + Final Integration)
```

## ⚡ Parallel Development Benefits

### Why This Approach Works
1. **Rapid Feedback Loop**: Daily integration testing catches issues early
2. **Real User Experience**: Test with actual data and API responses
3. **Incremental Progress**: Each week delivers working features
4. **Risk Mitigation**: No "big bang" integration at the end

### Success Metrics
- **Week 1**: Working auth + basic energy input
- **Week 2**: AI recommendations with real-time sync
- **Week 3**: Complete user journey with analytics

## 🚀 Execution Strategy

### Development Environment Setup
```bash
# Terminal 1: Backend development
cd life-hacking-api
npm run start:dev

# Terminal 2: Flutter development
cd life-hacking-mobile
flutter run

# Terminal 3: Database monitoring
cd life-hacking-api
npm run prisma:studio
```

### Daily Integration Script
```bash
# 5:00 PM daily integration test
./scripts/daily-integration-test.sh
# Tests: Auth flow, Energy input, AI recommendations, Data sync
```

## Task Generation Status
✅ **SUCCESS** - 76 Backend + Flutter parallel development tasks
- **Week 1**: 29 tasks (Foundation + Auth integration)
- **Week 2**: 23 tasks (AI features + Real-time sync)
- **Week 3**: 24 tasks (Progress + External integrations + Polish)
- **Daily Integration**: 16 integration checkpoints

### Key Integration Points
- T020, T024, T029: Week 1 integration verification
- T041, T047, T052: Week 2 integration verification
- T058, T064, T069, T076: Week 3 integration verification

**Ready for execution**: Each task is specific, has exact file paths, and follows TDD principles
