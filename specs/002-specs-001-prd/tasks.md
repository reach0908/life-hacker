# Tasks: LifeHacker Documentation Update

**Input**: Design documents from `/Users/jinhokim/dev/life-hacker/specs/002-specs-001-prd/`
**Prerequisites**: plan.md ✅, research.md ✅, data-model.md ✅, contracts/ ✅, quickstart.md ✅

## Execution Flow (main)
```
1. Load plan.md from feature directory
   → ✅ Documentation update strategy and 4-phase approach defined
2. Load research findings:
   → ✅ Implementation status analyzed, discrepancies identified
   → ✅ Technology stack validated, outdated references found
3. Generate tasks by category:
   → Validation: Codebase verification and status analysis
   → Core Updates: Specification document corrections
   → Technical Updates: Data models, contracts, setup docs
   → Status Sync: Implementation status alignment
   → Consistency: Cross-document validation
4. Apply task rules:
   → Different documents = mark [P] for parallel
   → Cross-references = sequential for consistency
   → Validation before corrections (Documentation TDD)
5. Number tasks sequentially (T001, T002...)
6. Target: All specs/001-prd-lifehacker-energy/ documents updated
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different documents, no dependencies)
- **[V]**: Validation task (verify against actual codebase)
- Include exact file paths in descriptions

## Path Conventions
- **Target Documentation**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/`
- **Reference Implementation**: `/Users/jinhokim/dev/life-hacker/life-hacking-api/` and `/Users/jinhokim/dev/life-hacker/life-hacking-mobile/`
- **Agent Context**: `/Users/jinhokim/dev/life-hacker/CLAUDE.md`

## Phase 1: Implementation Status Validation ⚠️ CRITICAL FIRST STEP

**CRITICAL: These validations MUST be completed to ensure documentation accuracy**

### T001 [P] [V] Validate Backend Domain Implementation Status
**File**: Verify `/Users/jinhokim/dev/life-hacker/life-hacking-api/src/domains/`
**Task**: 
- Confirm all 4 domains exist: auth/, user/, energy-tracking/, routine-recommendations/
- Run `npm test` to verify 240+ tests pass
- Check CQRS implementation with command/query repository separation
- Document actual completion status vs. current documentation claims
**Expected Result**: Implementation status report for backend domains

### T002 [P] [V] Validate Mobile Architecture Implementation Status  
**File**: Verify `/Users/jinhokim/dev/life-hacker/life-hacking-mobile/src/`
**Task**:
- Confirm React Native 0.81.4 with New Architecture implementation
- Verify Container/Presentational pattern in features/auth/ and features/onboarding/
- Check TanStack Query + Zustand state management setup
- Validate Google OAuth integration completeness
- Document missing energy-tracking UI components
**Expected Result**: Mobile implementation status report

### T003 [P] [V] Validate Technology Stack Accuracy
**File**: Compare documented vs. actual technology choices
**Task**:
- Verify backend: NestJS + TypeScript + PostgreSQL + Prisma + Clean Architecture
- Verify mobile: React Native (NOT Flutter) + TypeScript + Zustand + TanStack Query
- Check for "React Native Reusables" references vs actual UI implementation
- Identify any outdated technology references in documentation
**Expected Result**: Technology stack discrepancy report

### T004 [P] [V] Validate API Endpoints Implementation
**File**: Compare `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/contracts/` vs actual controllers
**Task**:
- Check auth endpoints against `/Users/jinhokim/dev/life-hacker/life-hacking-api/src/domains/auth/presentation/controllers/auth.controller.ts`
- Check energy-tracking endpoints against `/Users/jinhokim/dev/life-hacker/life-hacking-api/src/domains/energy-tracking/presentation/controllers/energy-tracking.controller.ts`
- Verify routine-recommendations and user endpoints
- Document endpoint discrepancies and missing documentation
**Expected Result**: API endpoint accuracy report

## Phase 2: Core Specification Document Updates (ONLY after validation complete)

### T005 [P] Update Feature Specification with Implementation Status
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/spec.md`
**Task**:
- Add "Current Implementation Status Assessment" section from `/Users/jinhokim/dev/life-hacker/specs/002-specs-001-prd/spec.md`
- Mark completed features as ✅ IMPLEMENTED with actual dates
- Update functional requirements to reflect actual vs. planned features
- Correct any business requirements that changed during implementation
**Dependencies**: Requires T001, T002 completion

### T006 [P] Update Implementation Plan with Correct Technology Stack
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/plan.md`
**Task**:
- Replace Flutter references with React Native 0.81.4 + New Architecture
- Update "Technical Context" section with validated technology choices
- Correct mobile state management: Zustand + TanStack Query (not Riverpod)
- Update storage strategy: MMKV + AsyncStorage (not SQLite)
- Fix architecture status to reflect actual CQRS + Clean Architecture implementation
**Dependencies**: Requires T003 completion

### T007 Update Research Document with Actual Technical Decisions
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/research.md`
**Task**:
- Replace research decisions with actual implementation choices made
- Document why React Native was chosen over Flutter (if context available)
- Update dependency analysis with actual backend and mobile libraries used
- Add performance implications of actual architecture patterns
- Include lessons learned from actual implementation experience
**Dependencies**: Requires T001, T002, T003 completion

## Phase 3: Technical Documentation Updates

### T008 [P] Replace Data Model with Current Prisma Schema
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/data-model.md`
**Task**:
- Copy current data model from `/Users/jinhokim/dev/life-hacker/specs/002-specs-001-prd/data-model.md`
- Ensure entity definitions match actual Prisma schema at `/Users/jinhokim/dev/life-hacker/life-hacking-api/prisma/schema.prisma`
- Update relationship mappings to reflect CQRS implementation
- Add mobile data types from `/Users/jinhokim/dev/life-hacker/life-hacking-mobile/src/types/api.ts`
- Document missing mobile entities (EnergySessionLocal, ProgressMilestone, UserPreferences)
**Dependencies**: Requires T001 completion

### T009 [P] Update API Contracts with Actual Endpoints
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/contracts/`
**Task**:
- Replace existing OpenAPI spec with actual API endpoints from `/Users/jinhokim/dev/life-hacker/specs/002-specs-001-prd/contracts/api-endpoints.md`
- Ensure all 7 energy-tracking endpoints are documented
- Add authentication endpoints (web + mobile OAuth flows)
- Include routine-recommendations and user management endpoints
- Document missing endpoints for progress visualization domain
**Dependencies**: Requires T004 completion

### T010 [P] Update Development Setup Guide
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/quickstart.md`
**Task**:
- Replace setup instructions with current development environment from `/Users/jinhokim/dev/life-hacker/specs/002-specs-001-prd/quickstart.md`
- Update React Native setup instructions (remove Flutter references)
- Add actual validation scenarios for implemented features
- Include current test commands: `npm test` (backend), mobile testing setup
- Update database migration commands with actual Prisma workflow
**Dependencies**: Requires T002, T003 completion

## Phase 4: Implementation Status Synchronization

### T011 Update Task Completion Status
**File**: `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/tasks.md`
**Task**:
- Mark all completed backend domain tasks as ✅ IMPLEMENTED with completion dates
- Update mobile tasks to reflect actual progress (auth complete, energy tracking partial)
- Add [COMPLETED] tags to finished tasks with reference to actual implementation files
- Update technology stack references throughout task descriptions
- Correct development workflow to match actual patterns used
**Dependencies**: Requires T001, T002 completion

### T012 Correct Technology References Throughout All Documents
**File**: All files in `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/`
**Task**:
- Find and replace all "Flutter" references with "React Native"
- Update "Riverpod" references to "Zustand + TanStack Query"
- Replace "SQLite" with "MMKV + AsyncStorage" for mobile storage
- Correct "shadcn_flutter" references with actual UI component approach
- Update any "Dart" references to "TypeScript"
- Ensure consistent terminology across all documents
**Dependencies**: Requires T005, T006, T007, T008, T009, T010 completion

### T013 Update Agent Context Files
**File**: `/Users/jinhokim/dev/life-hacker/CLAUDE.md`
**Task**:
- Verify current technology stack section reflects actual implementation
- Update domain architecture status with completion markers
- Add current implementation progress to Recent Changes
- Ensure mobile architecture section shows React Native (not Flutter)
- Update testing strategy to reflect actual test coverage achieved
**Dependencies**: Requires all previous tasks completion

## Phase 5: Validation and Consistency Checks

### T014 Cross-Reference Document Consistency Validation
**File**: All files in `/Users/jinhokim/dev/life-hacker/specs/001-prd-lifehacker-energy/`
**Task**:
- Verify all documents reference same technology stack
- Check implementation status consistency across spec.md, plan.md, tasks.md
- Validate API contracts match data model entities
- Ensure quickstart.md setup aligns with technical context in plan.md
- Confirm no conflicting information between documents
**Dependencies**: Requires T005-T013 completion

### T015 Implementation Claims Verification
**File**: Cross-reference with actual codebase
**Task**:
- Verify every "✅ IMPLEMENTED" claim has corresponding code evidence
- Check test coverage claims match actual `npm run test:cov` results
- Validate mobile feature claims against actual `/Users/jinhokim/dev/life-hacker/life-hacking-mobile/src/features/` structure
- Ensure API endpoint documentation matches actual controller implementations
- Document any remaining discrepancies for future correction
**Dependencies**: Requires T014 completion

### T016 Final Documentation Quality Assurance
**File**: All updated documentation files
**Task**:
- Review all updated documents for consistency in tone and style
- Check that implementation dates and status markers are accurate
- Verify external links and file path references are correct
- Ensure technical accuracy of all architecture pattern descriptions
- Validate that missing features are clearly identified vs. completed features
**Dependencies**: Requires T015 completion

## Dependencies
- **Phase 1 (T001-T004)**: Validation tasks run in parallel, must complete before Phase 2
- **T005-T006**: Can run in parallel (different documents)
- **T007**: Sequential after T001-T003 (needs validation results)
- **T008-T010**: Can run in parallel (different file types)
- **T011**: Sequential after T001-T002 (needs implementation status)
- **T012**: Sequential after T005-T010 (needs content to correct)
- **T013**: Sequential after all document updates
- **T014-T016**: Sequential validation cascade

## Parallel Execution Examples

### Phase 1 Validation (Run Together):
```bash
# Launch all validation tasks simultaneously
Task: "Validate Backend Domain Implementation Status - check all 4 domains and test suite"
Task: "Validate Mobile Architecture Implementation Status - check React Native setup and features"  
Task: "Validate Technology Stack Accuracy - compare documented vs actual tech choices"
Task: "Validate API Endpoints Implementation - compare contracts vs actual controllers"
```

### Phase 2 Core Updates (Run Together):
```bash
# Launch core specification updates simultaneously
Task: "Update spec.md with implementation status and completed feature markers"
Task: "Update plan.md with correct React Native technology stack and architecture"
```

### Phase 3 Technical Updates (Run Together):
```bash
# Launch technical documentation updates simultaneously
Task: "Replace data-model.md with current Prisma schema and entities"
Task: "Update contracts/ with actual API endpoints and authentication flows"
Task: "Update quickstart.md with current React Native development setup"
```

## Success Criteria

### Documentation Accuracy ✅
- [ ] All backend domains marked as ✅ IMPLEMENTED with evidence
- [ ] Mobile implementation status accurately reflects actual progress  
- [ ] Technology stack consistent across all documents (React Native, not Flutter)
- [ ] API contracts match actual implemented endpoints
- [ ] Data models reflect current Prisma schema

### Implementation Status Alignment ✅
- [ ] Completed features properly marked with implementation dates
- [ ] Missing features clearly identified and documented
- [ ] Test coverage claims match actual results
- [ ] Architecture patterns match actual implementation

### Cross-Document Consistency ✅
- [ ] No conflicting technology references
- [ ] Implementation status consistent across all files
- [ ] Agent context files updated with current information
- [ ] All outdated references corrected

## Notes

- **[P] tasks** = different documents, can run in parallel
- **[V] tasks** = validation against actual codebase required
- **Validation First**: Must verify actual implementation before updating documentation
- **Evidence Required**: Every "✅ IMPLEMENTED" claim must have codebase evidence
- **Commit Strategy**: Commit after each phase for atomic updates
- **Avoid**: Updating documentation without validating against actual implementation

## Task Generation Rules

1. **Validation Before Update**: Always verify current implementation before changing documentation
2. **Parallel by Document**: Different files can be updated simultaneously  
3. **Sequential by Dependency**: Status updates depend on validation results
4. **Evidence-Based**: All claims must be verifiable against actual codebase
5. **Consistency Last**: Cross-document validation after individual updates complete