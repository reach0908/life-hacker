# Data Model: LifeHacker Documentation Update

**Date**: 2025-09-15
**Context**: Current implementation status documentation

## Entity Definitions

### Documentation Entities

#### DocumentationStatus
- **Purpose**: Track implementation status of documented features
- **Fields**:
  - `id`: Unique identifier
  - `documentType`: spec | plan | tasks | research | contracts | quickstart
  - `featureName`: Name of the documented feature
  - `currentStatus`: DRAFT | IN_PROGRESS | IMPLEMENTED | OUTDATED | DEPRECATED
  - `lastUpdated`: Timestamp of last documentation update
  - `implementationDate`: When the feature was actually implemented
  - `discrepancies`: List of known inconsistencies between docs and implementation

#### ImplementationEvidence
- **Purpose**: Link documentation to actual codebase evidence
- **Fields**:
  - `id`: Unique identifier
  - `documentationId`: Reference to DocumentationStatus
  - `codebasePath`: File or directory path in actual implementation
  - `evidenceType`: FILE | DIRECTORY | TEST | API_ENDPOINT | UI_COMPONENT
  - `verificationStatus`: VERIFIED | MISSING | PARTIAL | OUTDATED
  - `lastChecked`: Timestamp of last verification

## Actual Implemented Entities (from life-hacking-api)

### User Domain

#### User Entity
- **Purpose**: Core user management and authentication
- **Implementation Status**: ✅ FULLY IMPLEMENTED
- **Fields**:
  - `id`: UUID primary key
  - `email`: Unique email address
  - `name`: Optional display name
  - `provider`: OAuth provider (e.g., "google")
  - `providerId`: External provider user ID
  - `createdAt`: Account creation timestamp
  - `updatedAt`: Last update timestamp
- **Relationships**:
  - One-to-many with RefreshToken
  - One-to-many with EnergySession
  - One-to-many with RoutineRecommendation
- **Architecture Pattern**: DDD Entity with Clean Architecture
- **Repository**: Prisma-based with CQRS separation

#### RefreshToken Entity
- **Purpose**: JWT refresh token management
- **Implementation Status**: ✅ FULLY IMPLEMENTED
- **Fields**:
  - `token`: Unique token string (primary key)
  - `userId`: Reference to User
  - `isValid`: Token validity flag
  - `expiresAt`: Expiration timestamp
  - `createdAt`: Creation timestamp
  - `updatedAt`: Last update timestamp
- **Architecture Pattern**: Domain Entity with proper lifecycle management

### Energy Tracking Domain

#### EnergySession Entity
- **Purpose**: Core energy level tracking and productivity measurement
- **Implementation Status**: ✅ FULLY IMPLEMENTED
- **Fields**:
  - `id`: UUID primary key
  - `userId`: Reference to User
  - `energyScore`: 1-5 energy level scale
  - `duration`: Session duration in minutes
  - `difficultyModifier`: 0.1-3.0 difficulty adjustment factor
  - `activity`: Optional activity description
  - `notes`: Optional session notes
  - `location`: Optional location information
  - `tags`: Array of tag strings
  - `calculatedProductivity`: Computed productivity score
  - `sessionStartTime`: Session start timestamp
  - `sessionEndTime`: Optional session end timestamp
  - `createdAt`: Record creation timestamp
  - `updatedAt`: Last update timestamp
- **Value Objects**: EnergyScore, Duration, DifficultyModifier
- **Architecture Pattern**: CQRS with separate Command/Query repositories
- **API Endpoints**: 7 implemented REST endpoints
- **Test Coverage**: 129+ passing tests

### Routine Recommendations Domain

#### Activity Entity
- **Purpose**: Predefined activities for routine recommendations
- **Implementation Status**: ✅ FULLY IMPLEMENTED
- **Fields**:
  - `id`: UUID primary key
  - `name`: Activity name
  - `description`: Optional description
  - `category`: EXERCISE | MEDITATION | WORK | LEARNING | SOCIAL | REST
  - `defaultDurationMinutes`: Default activity duration
  - `defaultDifficultyModifier`: Default difficulty level
  - `energyLevel`: Recommended energy level (1-5)
  - `timeOfDay`: MORNING | AFTERNOON | EVENING | ANY
  - `tags`: Array of tag strings
  - `instructions`: Optional activity instructions
  - `isActive`: Activity availability flag
  - `createdAt`: Record creation timestamp
  - `updatedAt`: Last update timestamp
- **Architecture Pattern**: DDD Entity with proper validation

#### RoutineRecommendation Entity
- **Purpose**: AI-generated daily routine recommendations
- **Implementation Status**: ✅ FULLY IMPLEMENTED (Rule-based, not AI-dependent)
- **Fields**:
  - `id`: UUID primary key
  - `userId`: Reference to User
  - `activityId`: Reference to Activity
  - `recommendationType`: ENERGY_BOOST | PRODUCTIVITY | RECOVERY | MAINTENANCE
  - `status`: SUGGESTED | ACCEPTED | COMPLETED | SKIPPED | EXPIRED
  - `priority`: 1-10 priority scale
  - `estimatedDurationMinutes`: Estimated completion time
  - `difficultyModifier`: Difficulty adjustment factor
  - `reason`: Explanation for recommendation
  - `expiresAt`: Recommendation expiration timestamp
  - `metadata`: Optional JSON metadata
  - `acceptedAt`: Optional acceptance timestamp
  - `completedAt`: Optional completion timestamp
  - `skippedAt`: Optional skip timestamp
  - `createdAt`: Record creation timestamp
  - `updatedAt`: Last update timestamp
- **Architecture Pattern**: CQRS with full lifecycle management
- **Test Coverage**: 112+ passing tests

## Mobile Implementation Status (life-hacking-mobile)

### Implemented Data Types

#### API Integration Types
- **Implementation Status**: ✅ PARTIALLY IMPLEMENTED
- **Files**: `src/types/api.ts`
- **Coverage**: Energy tracking types, authentication types
- **Missing**: Routine recommendation types, progress visualization types

#### State Management
- **Implementation Status**: ✅ INFRASTRUCTURE READY
- **Zustand Stores**: App state, authentication state
- **TanStack Query**: Query key structure for energy tracking
- **Missing**: Actual API integration for energy tracking UI

### Missing Mobile Entities

#### EnergySessionLocal
- **Purpose**: Offline energy session storage
- **Implementation Status**: ❌ NOT IMPLEMENTED
- **Needed For**: Offline-first mobile experience

#### ProgressMilestone
- **Purpose**: User progress tracking and gamification
- **Implementation Status**: ❌ NOT IMPLEMENTED
- **Needed For**: Progress visualization features

#### UserPreferences
- **Purpose**: Mobile app settings and preferences
- **Implementation Status**: ❌ NOT IMPLEMENTED
- **Needed For**: Personalization features

## Database Indexing Strategy

### Implemented Indexes (PostgreSQL)
- **User lookups**: Provider-based unique constraint
- **Energy sessions**: User-based, time-based, score-based queries
- **Activities**: Category, energy level, time of day filtering
- **Routine recommendations**: User status, priority, expiration queries

### Performance Considerations
- **Current**: Optimized for CQRS read/write separation
- **Future**: May need materialized views for complex analytics

## Data Validation Rules

### Backend Validation (Implemented)
- **Energy Score**: 1-5 integer range validation
- **Duration**: Positive integer validation
- **Difficulty Modifier**: 0.1-3.0 float range validation
- **Email**: Standard email format validation
- **UUID**: Proper UUID format validation

### Mobile Validation (Needed)
- **Form validation**: Client-side validation for user inputs
- **Offline validation**: Data integrity without backend connectivity
- **Sync validation**: Conflict resolution for offline/online data

## Migration Status

### Completed Migrations
1. **20250911113906_first_migration**: Initial schema setup
2. **20250913180358_add_energy_session**: Energy tracking domain
3. **20250914160220_add_routine_recommendations_and_activities**: Recommendation system

### Pending Migrations
- **Progress tracking domain**: ProductivityJourney aggregate
- **External integrations**: API connection tracking
- **Mobile-specific tables**: Offline sync support

## Documentation Synchronization Requirements

### High Priority Updates Needed
1. **specs/001-prd-lifehacker-energy/data-model.md**: Replace with current Prisma schema
2. **specs/001-prd-lifehacker-energy/contracts/**: Update with actual API endpoints
3. **specs/001-prd-lifehacker-energy/tasks.md**: Mark completed backend domains
4. **specs/001-prd-lifehacker-energy/plan.md**: Update implementation status

### Consistency Validation Rules
- All entity definitions must match Prisma schema
- API contracts must reflect actual NestJS controllers
- Mobile types must align with backend DTOs
- Test coverage claims must match actual test results