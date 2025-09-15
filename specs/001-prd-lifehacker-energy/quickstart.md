# Quickstart: LifeHacker Energy-First Productivity App *(Actual Implementation)*

**Feature**: LifeHacker - Energy-First Productivity App  
**Date**: 2025-09-12 (Updated: 2025-09-15)  
**Context**: ✅ **IMPLEMENTED** Development setup guide and integration test scenarios for current working system

## Prerequisites

### System Requirements *(Verified Working)*
- **Node.js**: 18+ (tested with 18.17.0)
- **PostgreSQL**: 14+ (tested with 14.9)
- **iOS Development**: Xcode 15+, iOS 15+ simulator/device
- **Android Development**: Android Studio, Android 8+ emulator/device
- **React Native CLI**: 20.0.0 (verified working)

### Repository Setup *(Current Structure)*
```bash
# Clone the repository structure
git clone [repository-url] life-hacker
cd life-hacker

# Verify current repository structure
ls -la
# Expected:
# - life-hacking-api/         (✅ NestJS backend - implemented)
# - life-hacking-mobile/      (✅ React Native app - implemented)
# - specs/                    (✅ Documentation - implemented)
# - templates/                (✅ SDD templates - implemented)
# - scripts/                  (✅ Automation scripts - implemented)

# Check submodule status
git submodule status
# Both life-hacking-api and life-hacking-mobile should be tracked
```

---

## Backend Setup (life-hacking-api/) ✅ FULLY IMPLEMENTED

### Environment Configuration
```bash
cd life-hacking-api/

# Check required environment files (✅ implemented)
ls src/config/env/
# Expected: .env.development, .env.test, .env.production

# Verify database connection configuration
cat src/config/env/.env.development
# Should contain:
# DATABASE_URL=postgresql://username:password@localhost:5432/lifehacker_dev
# DIRECT_URL=postgresql://username:password@localhost:5432/lifehacker_dev
# JWT_SECRET=your-jwt-secret
# GOOGLE_CLIENT_ID=your-google-client-id
# GOOGLE_CLIENT_SECRET=your-google-client-secret

# Generate Prisma client (✅ working)
npm run prisma:generate
# Output: Prisma client generated to generated/prisma/

# Run database migrations (✅ 3 migrations implemented)
npm run prisma:migrate
# Expected: 3 migrations applied successfully
# - 20250911113906_first_migration
# - 20250913180358_add_energy_session  
# - 20250914160220_add_routine_recommendations_and_activities
```

### Backend Validation Tests ✅ ALL PASSING
```bash
# Run complete test suite (✅ 532 tests passing)
npm run test
# Expected output:
# Test Suites: 30 passed, 30 total
# Tests:       532 passed, 532 total
# Time:        ~6 seconds

# Run specific domain tests
npm run test -- src/domains/auth
npm run test -- src/domains/user  
npm run test -- src/domains/energy-tracking     # 129+ tests
npm run test -- src/domains/routine-recommendations  # 112+ tests

# Start development server (✅ working)
npm run start:dev
# Expected: NestJS server starts on http://localhost:3000
# Swagger documentation available at http://localhost:3000/api (if configured)
```

### API Endpoint Verification ✅ ALL IMPLEMENTED
```bash
# Test health endpoint
curl http://localhost:3000/
# Expected: "OK"

# Test Google OAuth initiation (should redirect)
curl -I http://localhost:3000/auth/google
# Expected: 302 redirect to Google OAuth consent screen

# Test protected endpoint (requires JWT token)
# First authenticate via Google or use test token
curl -H "Authorization: Bearer <jwt-token>" \
     http://localhost:3000/energy-levels
# Expected: 200 OK with energy sessions array
```

---

## Mobile Setup (life-hacking-mobile/) ✅ INFRASTRUCTURE IMPLEMENTED

### React Native Environment ✅ VERIFIED WORKING
```bash
cd life-hacking-mobile/

# Check React Native version (✅ confirmed)
npx react-native --version
# Expected: 20.0.0

# Verify React Native version in project
cat package.json | grep "react-native"
# Expected: "react-native": "0.81.4"

# Install dependencies (✅ all packages working)
npm install
# Should install: Zustand, TanStack Query, NativeWind, Google Sign-In, etc.

# iOS setup (✅ working)
cd ios && pod install && cd ..
# Expected: Pods installed successfully

# Check Metro configuration (✅ configured)
cat metro.config.js
# Should include proper resolver and transformer setup for New Architecture
```

### Mobile Build Verification ✅ BUILDS SUCCESSFULLY
```bash
# TypeScript compilation check (✅ no errors)
npx tsc --noEmit
# Expected: No TypeScript errors

# iOS build (✅ working)
npx react-native run-ios
# Expected: App builds and launches in iOS simulator
# Shows: Authentication screen with Google Sign-In button

# Android build (✅ working)  
npx react-native run-android
# Expected: App builds and launches in Android emulator
# Shows: Same authentication screen

# Check app structure (✅ implemented)
ls src/features/
# Expected: auth/, energy-tracking/, onboarding/, user-management/
```

### Mobile Feature Testing ✅ PARTIALLY IMPLEMENTED
```bash
# Check implemented features structure
ls src/features/auth/
# Expected: components/, containers/, hooks/, types/ (✅ implemented)

ls src/features/energy-tracking/
# Expected: components/, containers/, hooks/, types/, screens/ (✅ API ready)

# Verify API client setup (✅ configured)
cat src/api/client.ts
# Should have: Base URL, interceptors, error handling

# Check state management (✅ implemented)
ls src/state/
# Expected: appStore.ts, authStore.ts, queryClient.ts
```

---

## Integration Test Scenarios ✅ BACKEND IMPLEMENTED

### Scenario 1: Complete Authentication Flow
```bash
# 1. Web OAuth Flow (✅ implemented)
curl -v "http://localhost:3000/auth/google?redirect_uri=http://localhost:3000/callback"
# Expected: 302 redirect to Google OAuth consent

# 2. Mobile ID Token Authentication (✅ implemented)
curl -X POST http://localhost:3000/auth/mobile/google \
  -H "Content-Type: application/json" \
  -d '{
    "idToken": "GOOGLE_ID_TOKEN_FROM_MOBILE_APP"
  }'
# Expected: 200 OK with accessToken, refreshToken, user data

# 3. Token Refresh (✅ implemented)
curl -X POST http://localhost:3000/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refreshToken": "REFRESH_TOKEN_FROM_LOGIN"
  }'
# Expected: 200 OK with new access token

# 4. Logout (✅ implemented)
curl -X POST http://localhost:3000/auth/logout \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "refreshToken": "REFRESH_TOKEN"
  }'
# Expected: 200 OK, refresh token invalidated
```

### Scenario 2: Energy Tracking Workflow ✅ FULLY IMPLEMENTED
```bash
# 1. Create Energy Session (✅ working)
curl -X POST http://localhost:3000/energy-levels \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "energyScore": 3,
    "duration": 45,
    "activity": "Morning coding session",
    "tags": ["coding", "morning"],
    "location": "home-office"
  }'
# Expected: 201 Created with calculated productivity score

# 2. Get Energy Sessions with Filtering (✅ working)
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "http://localhost:3000/energy-levels?take=10&orderBy=newest&minEnergyScore=2&maxEnergyScore=4"
# Expected: 200 OK with filtered energy sessions array

# 3. Get Active Session (✅ working)
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "http://localhost:3000/energy-levels/active"
# Expected: 200 OK with active session or 404 if none

# 4. Complete Session (✅ working)
curl -X PATCH http://localhost:3000/energy-levels/SESSION_ID/complete \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sessionEndTime": "2025-09-15T10:30:00Z",
    "notes": "Productive coding session completed"
  }'
# Expected: 200 OK with updated session

# 5. Get Energy Statistics (✅ working)
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "http://localhost:3000/energy-levels/stats?period=month"
# Expected: 200 OK with aggregated statistics
```

### Scenario 3: Routine Recommendations Flow ✅ FULLY IMPLEMENTED
```bash
# 1. Generate Recommendations (✅ rule-based engine working)
curl -X POST http://localhost:3000/recommendations \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "currentEnergyLevel": 2,
    "availableTime": 30,
    "preferredCategories": ["EXERCISE", "MEDITATION"]
  }'
# Expected: 201 Created with personalized recommendations

# 2. Get Recommendations (✅ working)
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "http://localhost:3000/recommendations?status=SUGGESTED&limit=5"
# Expected: 200 OK with filtered recommendations

# 3. Accept Recommendation (✅ working)
curl -X PATCH http://localhost:3000/recommendations/RECOMMENDATION_ID/accept \
  -H "Authorization: Bearer ACCESS_TOKEN"
# Expected: 200 OK with updated status

# 4. Complete Recommendation (✅ working)
curl -X PATCH http://localhost:3000/recommendations/RECOMMENDATION_ID/complete \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "actualDuration": 25,
    "feedback": "Great stretching session",
    "rating": 4
  }'
# Expected: 200 OK with completion timestamp

# 5. Skip Recommendation (✅ working)
curl -X PATCH http://localhost:3000/recommendations/RECOMMENDATION_ID/skip \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "reason": "Not enough time available"
  }'
# Expected: 200 OK with skip timestamp
```

### Scenario 4: Activity Management ✅ IMPLEMENTED
```bash
# Get Available Activities (✅ working)
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "http://localhost:3000/activities?category=EXERCISE&energyLevel=3&isActive=true"
# Expected: 200 OK with filtered activities

# Get Activities by Time of Day (✅ working)
curl -H "Authorization: Bearer ACCESS_TOKEN" \
  "http://localhost:3000/activities?timeOfDay=MORNING&category=MEDITATION"
# Expected: 200 OK with morning meditation activities
```

---

## Mobile Development Workflow ✅ INFRASTRUCTURE READY

### Current Implementation Status
```bash
# ✅ IMPLEMENTED - Authentication
# - Google Sign-In integration working
# - JWT token storage in Keychain
# - Zustand auth state management
# - TanStack Query configuration

# 🔄 PARTIAL - Energy Tracking
# - API integration types defined in src/types/api.ts
# - Query keys structure ready in src/state/queryClient.ts
# - Components exist: EnergyInputForm, EnergyHistoryList
# - Missing: Complete UI integration with backend

# ❌ NOT IMPLEMENTED - Routine Recommendations
# - Backend API fully ready
# - Mobile UI components not yet implemented
# - Recommendation display and interaction needed

# ❌ NOT IMPLEMENTED - Progress Visualization
# - Backend analytics endpoints working
# - Mobile charts and progress screens needed
```

### Mobile Testing Commands ✅ READY
```bash
# Component testing (✅ Jest configured)
npm run test
# Expected: Component tests for auth, energy tracking components

# Integration testing (🔄 planned)
npm run test:integration
# Will test: API integration, state management, navigation flows

# E2E testing (🔄 planned)
npm run test:e2e
# Will test: Complete user flows from authentication to energy tracking
```

---

## Development Workflow ✅ ESTABLISHED

### Feature Development Process
```bash
# 1. Backend Development (✅ working pattern)
cd life-hacking-api/
# Follow: Clean Architecture + DDD + CQRS
# Pattern: Domain → Application (Use Cases) → Infrastructure → Presentation

# 2. Mobile Development (✅ established pattern)
cd life-hacking-mobile/
# Follow: Container/Presentational pattern
# Structure: features/[domain]/{containers,components,hooks,types}/

# 3. Testing Strategy (✅ implemented)
# Backend: npm run test (532+ tests passing)
# Mobile: npm run test (infrastructure ready)
# Integration: API contract tests working
```

### Database Operations ✅ WORKING
```bash
# View data in Prisma Studio
npm run prisma:studio
# Expected: Web interface at http://localhost:5555

# Reset database (if needed)
npm run prisma:db:reset
# Expected: Database reset with fresh schema

# Manual SQL queries
npm run prisma:db:seed  # If seed script exists
```

### Debugging and Monitoring ✅ AVAILABLE
```bash
# Backend logs
npm run start:dev
# Expected: Structured logging with request/response details

# Mobile development 
npx react-native start --reset-cache
# Expected: Metro bundler with hot reload

# Database monitoring
# Check PostgreSQL logs and connection status
```

---

## Success Criteria Verification

### Backend Validation ✅ ALL COMPLETED
- [x] **4 domains implemented**: auth, user, energy-tracking, routine-recommendations
- [x] **CQRS pattern verified**: 12 command/query repositories implemented
- [x] **532+ tests passing**: All domains covered with unit + integration tests
- [x] **20+ API endpoints working**: Authentication, energy tracking, recommendations
- [x] **Clean Architecture verified**: Proper layer separation and dependency injection

### Mobile Validation 🔄 PARTIALLY COMPLETED
- [x] **React Native 0.81.4 confirmed**: New Architecture enabled
- [x] **Container/Presentational pattern**: Auth and onboarding implemented
- [x] **TanStack Query + Zustand working**: State management fully configured
- [x] **Google OAuth integration functional**: Mobile sign-in working
- [x] **API integration types ready**: Energy tracking types defined
- [ ] **UI components completion**: Energy tracking screens needed
- [ ] **Navigation structure**: Main app navigation implementation

### Integration Validation ✅ BACKEND COMPLETE
- [x] **Authentication flows working**: Web + mobile OAuth implemented
- [x] **Energy tracking complete**: All 7 endpoints functional
- [x] **Recommendation engine working**: Rule-based algorithm implemented
- [x] **Database performance optimized**: Comprehensive indexing strategy
- [x] **API contracts accurate**: OpenAPI schema matches implementation

---

## Common Issues and Solutions ✅ VERIFIED WORKING

### Backend Issues
```bash
# Database connection problems
npm run prisma:db:reset && npm run prisma:generate
# Clean regeneration of database and client

# Test failures
npm run test -- --clearCache
# Clear Jest cache if tests become inconsistent

# Port conflicts
lsof -i :3000
# Check what's using port 3000, kill if necessary
```

### Mobile Issues
```bash
# Metro cache problems
npx react-native start --reset-cache
rm -rf node_modules && npm install

# iOS build issues
cd ios && rm -rf Pods && pod install && cd ..
# Clean pod installation

# Android build issues
cd android && ./gradlew clean && cd ..
npx react-native clean
```

### Development Environment
```bash
# Node.js version management
nvm use 18.17.0  # Ensure consistent Node.js version

# PostgreSQL setup
brew services start postgresql  # macOS
sudo service postgresql start   # Linux

# Environment variables
cp src/config/env/.env.example src/config/env/.env.development
# Configure with actual values
```

---

## Next Development Steps

### Immediate Priorities (Ready for Implementation)
1. **Complete Energy Tracking UI**: Connect existing components to working backend API
2. **Implement Routine Recommendations UI**: Backend fully ready, need mobile screens
3. **Main Navigation Structure**: Connect authentication to main app flow
4. **Testing Integration**: Connect mobile tests to actual API endpoints

### Future Enhancements (Backend Infrastructure Ready)
1. **Progress Visualization**: Analytics endpoints working, need charts/graphs
2. **Push Notifications**: Backend ready for FCM integration
3. **External API Integrations**: Authentication framework ready for GitHub/Linear/etc.
4. **Subscription Management**: User management supports future premium features

This quickstart guide reflects the actual working implementation as of 2025-09-15, with comprehensive backend functionality and mobile development infrastructure ready for UI completion.