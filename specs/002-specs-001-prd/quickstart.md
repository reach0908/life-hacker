# Quickstart: LifeHacker Documentation Update

**Date**: 2025-09-15
**Purpose**: Current development environment setup and validation scenarios

## Prerequisites

### System Requirements
- Node.js 18+ 
- PostgreSQL 14+
- iOS 15+ (for mobile testing)
- Android 8+ (for mobile testing)
- Xcode 15+ (iOS development)
- Android Studio (Android development)

### Repository Structure Verification
```bash
# Verify current repository structure
ls -la /Users/jinhokim/dev/life-hacker/
# Expected: life-hacking-api/, life-hacking-mobile/, specs/, templates/, scripts/

# Check submodule status
git submodule status
# Expected: Both life-hacking-api and life-hacking-mobile should be tracked
```

## Backend Setup (life-hacking-api/)

### Environment Configuration
```bash
cd life-hacking-api/

# Check required environment files
ls src/config/env/
# Expected: .env.development, .env.test, .env.production

# Verify database connection
npm run prisma:generate
# Should generate client to generated/prisma/

# Run database migrations
npm run prisma:migrate
# Should apply all 3 existing migrations
```

### Backend Validation Tests
```bash
# Run all tests to verify implementation status
npm run test
# Expected: All tests should pass (240+ tests)

# Run specific domain tests
npm run test -- src/domains/auth
npm run test -- src/domains/user  
npm run test -- src/domains/energy-tracking
npm run test -- src/domains/routine-recommendations

# Start development server
npm run start:dev
# Expected: Server starts on configured port
```

### API Endpoint Verification
```bash
# Test health endpoint
curl http://localhost:3000/

# Test auth endpoints (should redirect to Google)
curl -I http://localhost:3000/auth/google

# Test energy tracking (requires JWT)
curl -H "Authorization: Bearer <jwt-token>" \
     http://localhost:3000/energy-levels
```

## Mobile Setup (life-hacking-mobile/)

### React Native Environment
```bash
cd life-hacking-mobile/

# Check React Native version
npx react-native --version
# Expected: 0.81.4

# Install dependencies
npm install

# iOS setup
cd ios && pod install && cd ..

# Check Metro configuration
cat metro.config.js
# Should include proper resolver and transformer setup
```

### Mobile Build Verification
```bash
# iOS build
npx react-native run-ios
# Expected: App builds and launches in simulator

# Android build  
npx react-native run-android
# Expected: App builds and launches in emulator

# Check TypeScript compilation
npx tsc --noEmit
# Expected: No TypeScript errors
```

### Mobile Feature Testing
```bash
# Check implemented features structure
ls src/features/
# Expected: auth/, onboarding/

# Verify API client setup
cat src/api/client.ts
# Should have proper base URL and interceptors

# Check state management
ls src/state/
# Expected: appStore.ts, authStore.ts, queryClient.ts
```

## Documentation Update Validation

### Current Documentation Status Check
```bash
# Check existing documentation
ls specs/001-prd-lifehacker-energy/
# Expected: spec.md, plan.md, tasks.md, research.md, data-model.md, contracts/, quickstart.md

# Verify documentation consistency
grep -r "React Native Reusables" specs/001-prd-lifehacker-energy/
# Should find inconsistencies to be updated

# Check implementation claims vs reality
grep -r "IMPLEMENTED" specs/001-prd-lifehacker-energy/tasks.md
# Should show many items still marked as pending despite being complete
```

### Validation Scenarios

#### Scenario 1: Backend Implementation Verification
```bash
# Verify all 4 domains exist
ls life-hacking-api/src/domains/
# Expected: auth/, user/, energy-tracking/, routine-recommendations/

# Check CQRS implementation
find life-hacking-api/src/domains -name "*command*" -o -name "*query*"
# Should find command/query repositories in each domain

# Verify test coverage
npm run test:cov
# Should show high test coverage across all domains
```

#### Scenario 2: Mobile Architecture Verification
```bash
# Check Container/Presentational pattern
find life-hacking-mobile/src/features -name "*Container*" -o -name "*Screen*"
# Should find proper component separation

# Verify state management setup
grep -r "Zustand\|TanStack" life-hacking-mobile/src/
# Should find proper state management implementation

# Check API integration types
cat life-hacking-mobile/src/types/api.ts
# Should match backend DTOs
```

#### Scenario 3: Technology Stack Validation
```bash
# Backend dependencies check
cat life-hacking-api/package.json | jq '.dependencies | keys[]'
# Should include: @nestjs/*, prisma, @prisma/client, passport, etc.

# Mobile dependencies check  
cat life-hacking-mobile/package.json | jq '.dependencies | keys[]'
# Should include: react-native, @tanstack/react-query, zustand, etc.

# Check for outdated technology references
grep -r "React Native Reusables" life-hacking-mobile/
# Should determine if this is actually used or documentation error
```

## Common Issues and Solutions

### Backend Issues

#### Database Connection Problems
```bash
# Reset database
npm run prisma:db:reset

# Regenerate client
npm run prisma:generate

# Check connection
npm run prisma:studio
```

#### Test Failures
```bash
# Clear test cache
npm run test -- --clearCache

# Run tests with coverage
npm run test:cov

# Debug specific test
npm run test -- --detectOpenHandles src/domains/auth
```

### Mobile Issues

#### Metro Bundle Problems
```bash
# Reset Metro cache
npx react-native start --reset-cache

# Clear node modules
rm -rf node_modules && npm install

# Reinstall pods (iOS)
cd ios && rm -rf Pods && pod install && cd ..
```

#### Build Failures
```bash
# Clean builds
npx react-native clean

# iOS clean
cd ios && xcodebuild clean && cd ..

# Android clean
cd android && ./gradlew clean && cd ..
```

## Success Criteria

### Backend Validation ✅
- [ ] All 4 domains (auth, user, energy-tracking, routine-recommendations) are implemented
- [ ] CQRS pattern is properly implemented with command/query separation
- [ ] All tests pass (240+ tests)
- [ ] API endpoints match the documented contracts
- [ ] Clean Architecture + DDD + Hexagonal patterns are verified

### Mobile Validation 🔄 
- [ ] React Native 0.81.4 with New Architecture is confirmed
- [ ] Container/Presentational pattern is properly implemented
- [ ] TanStack Query + Zustand state management is working
- [ ] Google OAuth integration is functional
- [ ] Energy tracking API integration types are defined
- [ ] Missing UI components are identified and documented

### Documentation Validation ❌
- [ ] Current spec.md accurately reflects implementation status
- [ ] plan.md technology choices match actual implementation
- [ ] tasks.md completion status is accurate
- [ ] data-model.md matches current Prisma schema
- [ ] contracts/ reflect actual API endpoints
- [ ] Agent context files are updated with current tech stack

## Next Steps After Validation

1. **Update Documentation**: Use findings to update all specification documents
2. **Technology Alignment**: Correct any technology choice discrepancies
3. **Implementation Status**: Mark completed features as IMPLEMENTED
4. **Missing Features**: Clearly identify what remains to be built
5. **Architecture Documentation**: Ensure patterns match actual implementation

## Development Workflow

### Adding New Features
```bash
# Backend: Follow Clean Architecture + DDD
cd life-hacking-api/
npm run create:domain <domain-name>

# Mobile: Follow Container/Presentational pattern  
cd life-hacking-mobile/
mkdir -p src/features/<feature-name>/{containers,components,hooks,types}
```

### Testing Strategy
```bash
# Backend: Contract → Integration → Unit
npm run test:e2e  # Contract tests
npm run test      # Unit + Integration

# Mobile: Component → Integration → E2E
npm run test              # Component tests
npm run test:integration  # Integration tests
```

### Documentation Updates
```bash
# Update specification documents after implementation
cd specs/001-prd-lifehacker-energy/
# Edit spec.md, plan.md, tasks.md as needed

# Update agent context files
scripts/update-agent-context.sh claude
```

This quickstart guide serves as both a validation checklist and setup guide for maintaining accurate documentation that reflects the current implementation status.