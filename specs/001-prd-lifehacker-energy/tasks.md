# Tasks: LifeHacker Energy-First Productivity App

**Input**: Design documents from `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/`
**Prerequisites**: plan.md (✅), research.md (✅), data-model.md (✅), contracts/ (✅), quickstart.md (✅)
**Current Status**: ✅ **BACKEND FULLY IMPLEMENTED** (532+ tests) | ✅ **MOBILE INFRASTRUCTURE READY** | 🔄 **MOBILE UI COMPLETION**

## Execution Flow (main)
```
1. Load plan.md from feature directory ✅
   → Tech stack: React Native + NestJS, PostgreSQL + Prisma
   → Structure: Mobile + API architecture confirmed
2. Load design documents: ✅
   → data-model.md: 4 entities (User, EnergySession, Activity, RoutineRecommendation)
   → contracts/openapi.yaml: 20+ API endpoints (✅ backend implemented)
   → quickstart.md: 4 integration test scenarios (✅ backend working)
3. Generate tasks by category: Mobile UI completion (backend fully implemented)
4. Apply task rules:
   → Different components/screens = mark [P] for parallel
   → TDD approach: Tests before implementation
5. Number tasks sequentially (T001, T002...)
6. Generate dependency graph
7. Create parallel execution examples
8. Return: SUCCESS (tasks ready for execution)
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different files/components, no dependencies)
- Include exact file paths in life-hacking-mobile/src/ directory

## Path Conventions
- **Mobile**: life-hacking-mobile/src/ (React Native app)
- **API**: life-hacking-api/src/ (✅ fully implemented - 532+ tests passing)
- Focus on mobile UI completion as backend is production-ready

## Phase 3.1: Mobile App Setup & Infrastructure
- [ ] T001 Update navigation structure to connect authenticated users to main app screens
- [ ] T002 [P] Configure theme system with NativeWind for consistent styling across energy tracking screens
- [ ] T003 [P] Setup error boundary components for production-ready error handling
- [ ] T004 [P] Configure push notification infrastructure for recommendation alerts

## Phase 3.2: Tests First (TDD) ⚠️ MUST COMPLETE BEFORE 3.3
**CRITICAL: These tests MUST be written and MUST FAIL before ANY implementation**
- [ ] T005 [P] Component test for EnergySessionForm in life-hacking-mobile/src/features/energy-tracking/components/__tests__/EnergySessionForm.test.tsx
- [ ] T006 [P] Component test for EnergyHistoryList in life-hacking-mobile/src/features/energy-tracking/components/__tests__/EnergyHistoryList.test.tsx
- [ ] T007 [P] Component test for RecommendationCard in life-hacking-mobile/src/features/routine-recommendations/components/__tests__/RecommendationCard.test.tsx
- [ ] T008 [P] Component test for ProgressVisualization in life-hacking-mobile/src/features/progress-visualization/components/__tests__/ProgressVisualization.test.tsx
- [ ] T009 [P] Integration test for complete energy tracking workflow in life-hacking-mobile/src/__tests__/integration/energy-tracking-flow.test.tsx
- [ ] T010 [P] Integration test for recommendation acceptance flow in life-hacking-mobile/src/__tests__/integration/recommendation-flow.test.tsx
- [ ] T011 [P] E2E test for authenticated user energy input flow in life-hacking-mobile/e2e/energy-input.e2e.ts
- [ ] T012 [P] API integration test for energy session CRUD operations in life-hacking-mobile/src/api/__tests__/energy-api.test.ts

## Phase 3.3: Core Mobile UI Implementation (ONLY after tests are failing)

### Energy Tracking UI (Backend ✅ Implemented - 7 endpoints working)
- [ ] T013 [P] EnergyInputScreen with energy level picker (1-5 scale) in life-hacking-mobile/src/features/energy-tracking/screens/EnergyInputScreen.tsx
- [ ] T014 [P] EnergySessionForm component with duration, activity, tags input in life-hacking-mobile/src/features/energy-tracking/components/EnergySessionForm.tsx
- [ ] T015 [P] EnergyHistoryScreen with filtering and pagination in life-hacking-mobile/src/features/energy-tracking/screens/EnergyHistoryScreen.tsx
- [ ] T016 [P] EnergyStatsScreen displaying productivity analytics in life-hacking-mobile/src/features/energy-tracking/screens/EnergyStatsScreen.tsx
- [ ] T017 [P] ActiveSessionCard component for ongoing energy sessions in life-hacking-mobile/src/features/energy-tracking/components/ActiveSessionCard.tsx

### Routine Recommendations UI (Backend ✅ Implemented - Rule-based engine working)
- [ ] T018 [P] RecommendationsScreen displaying personalized suggestions in life-hacking-mobile/src/features/routine-recommendations/screens/RecommendationsScreen.tsx
- [ ] T019 [P] RecommendationCard component with accept/skip/complete actions in life-hacking-mobile/src/features/routine-recommendations/components/RecommendationCard.tsx
- [ ] T020 [P] RecommendationGenerationScreen for custom recommendation requests in life-hacking-mobile/src/features/routine-recommendations/screens/RecommendationGenerationScreen.tsx
- [ ] T021 [P] ActivityListScreen showing available activities by category in life-hacking-mobile/src/features/routine-recommendations/screens/ActivityListScreen.tsx

### Progress Visualization UI (Backend ✅ Analytics endpoints ready)
- [ ] T022 [P] ProgressDashboardScreen with charts and trends in life-hacking-mobile/src/features/progress-visualization/screens/ProgressDashboardScreen.tsx
- [ ] T023 [P] EnergyTrendChart component using react-native-chart-kit in life-hacking-mobile/src/features/progress-visualization/components/EnergyTrendChart.tsx
- [ ] T024 [P] ProductivityInsightsCard showing weekly/monthly summaries in life-hacking-mobile/src/features/progress-visualization/components/ProductivityInsightsCard.tsx

### Navigation Integration
- [ ] T025 MainTabNavigator connecting all feature screens in life-hacking-mobile/src/navigation/MainTabNavigator.tsx
- [ ] T026 Update AppNavigator to handle authenticated vs unauthenticated states in life-hacking-mobile/src/navigation/AppNavigator.tsx

## Phase 3.4: API Integration & State Management
- [ ] T027 [P] Energy tracking API hooks using TanStack Query in life-hacking-mobile/src/features/energy-tracking/hooks/useEnergyApi.ts
- [ ] T028 [P] Routine recommendations API hooks in life-hacking-mobile/src/features/routine-recommendations/hooks/useRecommendationsApi.ts
- [ ] T029 [P] Progress visualization API hooks in life-hacking-mobile/src/features/progress-visualization/hooks/useProgressApi.ts
- [ ] T030 [P] Offline data sync with MMKV for energy sessions in life-hacking-mobile/src/services/offline-sync/EnergySessionSync.ts
- [ ] T031 Update Zustand stores to handle new feature states in life-hacking-mobile/src/state/appStore.ts
- [ ] T032 Error handling and retry logic for API calls in life-hacking-mobile/src/api/errorHandling.ts

## Phase 3.5: User Experience & Polish
- [ ] T033 [P] Loading states and skeleton screens for all major components in life-hacking-mobile/src/components/common/LoadingStates.tsx
- [ ] T034 [P] Empty states with helpful onboarding messages in life-hacking-mobile/src/components/common/EmptyStates.tsx
- [ ] T035 [P] Success animations and feedback for completed actions in life-hacking-mobile/src/components/common/SuccessFeedback.tsx
- [ ] T036 [P] Accessibility improvements (screen reader support, focus management) across all screens
- [ ] T037 [P] Performance optimization for list rendering and navigation in high-usage components
- [ ] T038 [P] Dark mode support consistent with system settings in life-hacking-mobile/src/styles/themes/
- [ ] T039 Update user onboarding flow to introduce energy tracking concepts in life-hacking-mobile/src/features/onboarding/
- [ ] T040 [P] App icon and splash screen updates for production release in life-hacking-mobile/ios/ and life-hacking-mobile/android/

## Dependencies
- Tests (T005-T012) before implementation (T013-T026)
- T001 (navigation) blocks T025, T026
- T013-T017 (energy UI) blocks T027
- T018-T021 (recommendations UI) blocks T028  
- T022-T024 (progress UI) blocks T029
- T025-T026 (navigation) before T031
- Implementation before polish (T033-T040)

## Parallel Example
```
# Launch T013-T024 together (different screen components):
Task: "EnergyInputScreen with energy level picker (1-5 scale) in life-hacking-mobile/src/features/energy-tracking/screens/EnergyInputScreen.tsx"
Task: "EnergySessionForm component in life-hacking-mobile/src/features/energy-tracking/components/EnergySessionForm.tsx"
Task: "RecommendationsScreen in life-hacking-mobile/src/features/routine-recommendations/screens/RecommendationsScreen.tsx"
Task: "ProgressDashboardScreen in life-hacking-mobile/src/features/progress-visualization/screens/ProgressDashboardScreen.tsx"
```

## Notes
- [P] tasks = different files/components, no shared state dependencies
- Backend is ✅ production-ready with 532+ tests - focus on mobile UI completion
- Follow Container/Presentational pattern established in existing auth features
- Use TanStack Query for server state, Zustand for client state
- Implement offline-first approach with MMKV storage

## Task Generation Rules
*Applied during main() execution*

1. **From Contracts** (✅ Backend implemented):
   - 20+ API endpoints ready → Mobile integration tasks
   - Energy tracking: 7 endpoints → 5 UI components
   - Recommendations: 6 endpoints → 4 UI components
   
2. **From Data Model** (✅ Backend implemented):
   - EnergySession entity → Energy tracking screens T013-T017
   - RoutineRecommendation entity → Recommendations screens T018-T021
   - Activity entity → Activity management T021
   - User entity → ✅ Already implemented in auth
   
3. **From User Stories** (Quickstart scenarios):
   - Complete authentication flow → ✅ Implemented
   - Energy tracking workflow → T013-T017, T027
   - Routine recommendations flow → T018-T021, T028
   - Progress visualization → T022-T024, T029

4. **Ordering**:
   - Navigation → Tests → UI Components → API Integration → Polish
   - TDD approach: Tests before implementation
   - Different screens/components can be parallel

## Validation Checklist
*GATE: Checked by main() before returning*

- [x] All major backend endpoints have corresponding mobile UI tasks
- [x] All entities have screen/component tasks
- [x] All tests come before implementation (T005-T012 before T013+)
- [x] Parallel tasks truly independent (different files/components)
- [x] Each task specifies exact file path in life-hacking-mobile/src/
- [x] No task modifies same file as another [P] task
- [x] Backend implementation status documented (✅ production-ready)
- [x] Focus on completing mobile UI to match implemented backend functionality

## Implementation Status Summary
- **Backend API**: ✅ Fully implemented (4 domains, 532+ tests, 20+ endpoints)
- **Mobile Infrastructure**: ✅ React Native 0.81.4, Zustand + TanStack Query, Google OAuth
- **Mobile UI**: 🔄 Auth + Onboarding complete, Energy tracking + Recommendations needed
- **Ready for**: Mobile UI implementation to complete full-stack application

This task list focuses on completing the mobile UI to match the fully implemented and tested backend API, following the established React Native architecture and constitutional principles.

---

## Previous Implementation Status

### ✅ **COMPLETED - Phase 1-2: Full Backend + Mobile Infrastructure (2024-2025)**
- **Backend**: ✅ 4 domains implemented (Auth, User, Energy-Tracking, Routine-Recommendations)
- **Architecture**: ✅ Clean Architecture + DDD + CQRS patterns
- **API Endpoints**: ✅ 20+ endpoints with comprehensive testing
- **Test Coverage**: ✅ 532+ passing tests
- **Mobile Infrastructure**: ✅ React Native 0.81.4, Zustand + TanStack Query, Google OAuth
- **Ready for**: Mobile UI completion to match backend functionality

**Focus**: Complete the mobile UI implementation to provide users with full access to the implemented backend functionality.

---

**Total Tasks Generated**: 40 numbered tasks (T001-T040)
**Task Categories**: Infrastructure (4) + Tests (8) + UI Components (14) + API Integration (6) + Polish (8)  
**Estimated Completion**: 2-3 weeks of focused mobile development
**Ready for Execution**: All tasks include specific file paths and clear acceptance criteria
