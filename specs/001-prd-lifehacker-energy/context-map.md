# Context Map: LifeHacker Bounded Contexts

**Feature**: LifeHacker - Energy-First Productivity App  
**Date**: 2025-09-12  
**Context**: DDD Context Map showing relationships between bounded contexts

## Bounded Context Overview

```
┌─────────────────────┐    ┌─────────────────────┐    ┌─────────────────────┐
│   User Management   │    │   Energy Tracking   │    │Progress Visualization│
│     Context         │    │      Context        │    │      Context        │
│                     │    │                     │    │                     │
│ • User (AR)         │    │ • EnergySession(AR) │    │• ProductivityJourney │
│ • UserProfile       │    │ • RoutineRecommend  │    │  (AR)               │
│ • SubscriptionTier  │    │ • Activity          │    │ • ProgressPath      │
│ • UserSettings      │    │ • EnergyScore (VO)  │    │ • Milestone (VO)    │
│                     │    │ • Duration (VO)     │    │                     │
└─────────────────────┘    └─────────────────────┘    └─────────────────────┘
         │                            │                            │
         │                            │                            │
         └──────────── Shared Kernel ──────────────────────────────┘
                            │
              ┌─────────────────────┐
              │   Shared Kernel     │
              │                     │
              │ • UserId (VO)       │
              │ • BurnoutRisk (VO)  │
              │ • Duration (VO)     │
              │ • DifficultyMod(VO) │
              │ • Domain Events     │
              │ • DomainError       │
              └─────────────────────┘
                         │
         ┌─────────────────────────────────┐
         │                                 │
┌─────────────────────┐          ┌─────────────────────┐
│External Integrations│          │    Supporting       │
│      Context        │          │     Contexts        │
│                     │          │                     │
│• IntegrationHub(AR) │          │ • Notification      │
│• GitHubConnection   │          │ • Payment           │
│• CalendarConnection │          │ • Email             │
│• HealthConnection   │          │ • Analytics         │
└─────────────────────┘          └─────────────────────┘
```

## Context Relationships

### 1. User Management ↔ Energy Tracking
**Relationship Type**: Customer-Supplier (User Management upstream)
**Integration Pattern**: Published Language (Domain Events)

```typescript
// User Management publishes events
class UserSubscriptionUpgraded extends DomainEvent {
  constructor(readonly userId: UserId, readonly newTier: SubscriptionTier) {}
}

// Energy Tracking subscribes and adapts behavior
class EnergyTrackingEventHandler {
  @EventHandler(UserSubscriptionUpgraded)
  async handleSubscriptionUpgrade(event: UserSubscriptionUpgraded) {
    // Enable/disable premium AI features based on subscription
    await this.featureToggleService.updateUserFeatures(
      event.userId, 
      event.newTier.allowedFeatures
    );
  }
}
```

**Shared Concepts**: 
- `UserId` (identity)
- User feature access permissions
- Subscription-based feature toggles

### 2. Energy Tracking ↔ Progress Visualization  
**Relationship Type**: Customer-Supplier (Energy Tracking upstream)
**Integration Pattern**: Published Language (Domain Events)

```typescript
// Energy Tracking publishes completion events
class SessionCompleted extends DomainEvent {
  constructor(
    readonly userId: UserId,
    readonly sessionId: EnergySessionId,
    readonly completionRate: number,
    readonly energyAdaptation: number
  ) {}
}

// Progress Visualization updates journey
class ProgressVisualizationEventHandler {
  @EventHandler(SessionCompleted)
  async handleSessionCompletion(event: SessionCompleted) {
    const journey = await this.journeyRepo.findByUserId(event.userId);
    journey.recordDailyProgress(
      new Date(),
      event.completionRate,
      event.energyAdaptation
    );
  }
}
```

**Shared Concepts**:
- `UserId` (identity reference)
- Progress metrics calculation
- Milestone achievement logic

### 3. External Integrations → Energy Tracking
**Relationship Type**: Supplier-Customer (External Integrations upstream)  
**Integration Pattern**: Anti-Corruption Layer

```typescript
// Anti-Corruption Layer protects Energy domain from external API changes
class GitHubActivityAdapter {
  async fetchUserActivity(userId: UserId): Promise<ExternalContext> {
    try {
      const rawGitHubData = await this.gitHubApi.getUserActivity(userId.value);
      
      // Transform external model to domain model
      return new ExternalContext({
        workIntensity: this.calculateWorkIntensity(rawGitHubData.commits),
        screenTime: this.estimateScreenTime(rawGitHubData.additions),
        codeComplexity: this.analyzeComplexity(rawGitHubData.languages)
      });
    } catch (error) {
      // Fallback when external service fails
      return ExternalContext.createDefault();
    }
  }
}
```

**Anti-Corruption Boundaries**:
- GitHub API → Domain ExternalContext
- Google Calendar → Domain CalendarDensity  
- Apple Health → Domain BiometricData

### 4. Shared Kernel Usage
**Shared Value Objects**:
```typescript
// Used across all contexts
class UserId {
  constructor(readonly value: string) {
    if (!this.isValidUUID(value)) {
      throw new DomainError('Invalid UserId format');
    }
  }
}

class Duration {
  constructor(readonly minutes: number) {
    if (minutes < 0) throw new DomainError('Duration cannot be negative');
  }
  
  // Shared business logic
  isShortActivity(): boolean { return this.minutes <= 15; }
  isMediumActivity(): boolean { return this.minutes > 15 && this.minutes <= 60; }
  isLongActivity(): boolean { return this.minutes > 60; }
}

class BurnoutRisk {
  constructor(readonly score: number, readonly factors: string[]) {
    // Shared risk assessment logic used by multiple contexts
  }
  
  requiresIntervention(): boolean { return this.score >= 0.7; }
}
```

## Context Integration Patterns

### 1. Domain Events (Asynchronous)
**Use Case**: Loose coupling between contexts
```typescript
// Energy Tracking → Progress Visualization
EnergySessionCreated → UpdateProgressPath
RecommendationAccepted → RecordUserChoice  
ActivityCompleted → CalculateDailyScore

// User Management → All Contexts
UserSubscriptionChanged → UpdateFeatureAccess
UserPreferencesUpdated → AdaptRecommendations
```

### 2. Repository References (Read-Only)
**Use Case**: Cross-context data queries without coupling
```typescript
class GenerateRecommendationUseCase {
  constructor(
    private energyRepo: EnergySessionRepository,
    private userRepo: UserRepository, // Read-only reference
    private aiService: AIRecommendationService
  ) {}
  
  async execute(userId: UserId): Promise<Recommendation[]> {
    // Can read user subscription for feature access
    const user = await this.userRepo.findById(userId);
    const canUseAI = user.subscription.allowsFeature('ai-recommendations');
    
    if (canUseAI) {
      return this.aiService.generateAdvancedRecommendations(userId);
    } else {
      return this.generateBasicRecommendations(userId);
    }
  }
}
```

### 3. API Gateway Pattern (Synchronous)
**Use Case**: Mobile client needs unified API
```typescript
// Application Service coordinates multiple contexts
class DailyRoutineApplicationService {
  async generateDailyRoutine(userId: UserId): Promise<DailyRoutineResponse> {
    // Coordinate across contexts
    const [energySession, userPreferences, externalContext, progressHistory] = 
      await Promise.all([
        this.energyTrackingService.getCurrentSession(userId),
        this.userService.getPreferences(userId),
        this.integrationService.fetchTodaysContext(userId),
        this.progressService.getRecentHistory(userId)
      ]);
    
    return this.routineAssembler.createDailyRoutine({
      energySession,
      userPreferences,
      externalContext,
      progressHistory
    });
  }
}
```

## Context Boundaries and Responsibilities

### Energy Tracking Context
**Core Responsibility**: Energy-based routine generation and tracking
**Owns**: 
- Energy level recording and validation
- AI-powered recommendation generation  
- Activity completion tracking
- Energy streak calculation

**Dependencies**:
- User permissions (read-only)
- External activity data (via Anti-Corruption Layer)

### Progress Visualization Context  
**Core Responsibility**: Journey tracking and milestone achievement
**Owns**:
- Progress path calculation
- Milestone detection and celebration
- Insight report generation
- Wellbeing score computation

**Dependencies**: 
- Energy session completion events
- User preferences (read-only)

### User Management Context
**Core Responsibility**: User identity and subscription management
**Owns**:
- User authentication and authorization
- Subscription tier management
- User preferences and settings
- Privacy and data control

**Dependencies**: None (upstream context)

### External Integrations Context
**Core Responsibility**: Third-party API management and data sync
**Owns**:
- OAuth connection management
- Webhook subscription handling
- Data synchronization and caching
- API rate limiting and error handling

**Dependencies**:
- User permissions for integration access
- Real-time notification requirements

## Evolution and Migration Strategy

### Context Splitting Strategy
If contexts grow too large, split by:
1. **Energy Tracking** → Split into "Energy Recording" + "Recommendation Generation"
2. **External Integrations** → Split by provider type (Code Tools, Calendar Tools, Health Tools)
3. **User Management** → Split into "Identity" + "Subscription" + "Preferences"

### Context Merging Strategy  
If contexts are too granular:
1. Merge "Progress Visualization" back into "Energy Tracking" if tight coupling emerges
2. Combine small Supporting Contexts into "Platform Services"

### Migration Guidelines
1. **Never** change Shared Kernel without all context agreement
2. Use **Strangler Fig** pattern for external API migrations
3. Maintain **backward compatibility** for Domain Events during transitions
4. Apply **Database per Context** gradually (start with schema separation)

---

**Context Map Status**: ✅ Complete with clear boundaries and integration patterns  
**Next**: Update OpenAPI contracts with domain-aligned schemas