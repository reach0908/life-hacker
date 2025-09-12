# Phase 1: Data Model Design (DDD Enhanced)

**Feature**: LifeHacker - Energy-First Productivity App  
**Date**: 2025-09-12  
**Context**: Domain-driven design with proper Entity/Value Object classification and Aggregate boundaries

## Bounded Context: Productivity Domain

### Value Objects (Immutable, Identity by Value)

#### EnergyScore
**Purpose**: Represents daily energy assessment with validation
```typescript
class EnergyScore {
  constructor(
    readonly level: number, // 1-5
    readonly context?: string,
    readonly timestamp: Date = new Date()
  ) {
    if (level < 1 || level > 5) {
      throw new DomainError('Energy level must be between 1 and 5');
    }
    if (this.timestamp > new Date()) {
      throw new DomainError('Energy timestamp cannot be in the future');
    }
  }

  isLowEnergy(): boolean { return this.level <= 2; }
  isHighEnergy(): boolean { return this.level >= 4; }
  adaptIntensity(baseIntensity: number): number {
    return baseIntensity * (this.level / 5) * 0.6 + baseIntensity * 0.4;
  }
}
```

#### Duration
**Purpose**: Time duration with business rules
```typescript
class Duration {
  constructor(readonly minutes: number) {
    if (minutes < 0) throw new DomainError('Duration cannot be negative');
    if (minutes > 480) throw new DomainError('Duration cannot exceed 8 hours'); 
  }

  toHours(): number { return Math.floor(this.minutes / 60); }
  toString(): string { return `${this.minutes}min`; }
  
  static fromHours(hours: number): Duration {
    return new Duration(hours * 60);
  }
}
```

#### DifficultyModifier
**Purpose**: Routine intensity adjustment factor
```typescript
class DifficultyModifier {
  constructor(readonly value: number) {
    if (value < 0.1 || value > 2.0) {
      throw new DomainError('Difficulty modifier must be between 0.1 and 2.0');
    }
  }

  static forEnergyLevel(energyLevel: number): DifficultyModifier {
    const modifiers = { 1: 0.4, 2: 0.6, 3: 1.0, 4: 1.2, 5: 1.5 };
    return new DifficultyModifier(modifiers[energyLevel] || 1.0);
  }

  apply(baseDuration: Duration): Duration {
    return new Duration(Math.round(baseDuration.minutes * this.value));
  }
}
```

#### BurnoutRisk
**Purpose**: Risk assessment calculation
```typescript
class BurnoutRisk {
  constructor(
    readonly score: number, // 0.0 - 1.0
    readonly factors: string[],
    readonly recommendation: string
  ) {
    if (score < 0 || score > 1) {
      throw new DomainError('Risk score must be between 0 and 1');
    }
  }

  get alertLevel(): 'LOW' | 'MODERATE' | 'HIGH' | 'CRITICAL' {
    if (this.score < 0.3) return 'LOW';
    if (this.score < 0.5) return 'MODERATE'; 
    if (this.score < 0.7) return 'HIGH';
    return 'CRITICAL';
  }

  requiresIntervention(): boolean { return this.score >= 0.7; }
}
```

## Aggregate Roots and Entities

### Aggregate 1: User Identity
**Aggregate Root**: User
**Bounded Context**: User Management
```typescript
class User {
  constructor(
    private readonly userId: UserId,
    private profile: UserProfile,
    private subscription: SubscriptionTier,
    private settings: UserSettings
  ) {}

  updateProfile(newProfile: UserProfile): void {
    this.profile = newProfile;
    // Domain event: UserProfileUpdated
  }

  upgradeSubscription(newTier: SubscriptionTier): void {
    if (!this.subscription.canUpgradeTo(newTier)) {
      throw new DomainError('Invalid subscription upgrade');
    }
    this.subscription = newTier;
    // Domain event: SubscriptionUpgraded
  }

  canAccessFeature(feature: string): boolean {
    return this.subscription.allowsFeature(feature);
  }
}

class UserProfile { // Entity
  constructor(
    private readonly profileId: ProfileId,
    private email: Email,
    private timezone: Timezone,
    private notificationPreferences: NotificationPreferences
  ) {}
}

class SubscriptionTier { // Value Object
  constructor(readonly tier: 'FREE' | 'PRO' | 'TEAM') {}
  
  allowsFeature(feature: string): boolean {
    const features = {
      'FREE': ['basic-energy', 'simple-recommendations'],
      'PRO': ['basic-energy', 'simple-recommendations', 'ai-recommendations', 'external-integrations'],
      'TEAM': ['*']
    };
    return features[this.tier].includes(feature) || features[this.tier].includes('*');
  }
}
```

### Aggregate 2: Daily Energy Session
**Aggregate Root**: EnergySession
**Bounded Context**: Energy Tracking
```typescript
class EnergySession {
  private recommendations: RoutineRecommendation[] = [];
  
  constructor(
    private readonly sessionId: EnergySessionId,
    private readonly userId: UserId, // Reference only
    private readonly energyScore: EnergyScore,
    private readonly sessionDate: Date
  ) {
    this.ensureOnlyOneSessionPerDay();
  }

  generateRecommendations(
    externalContext: ExternalContext,
    aiService: AIRecommendationService // Domain Service
  ): void {
    if (this.recommendations.length > 0) {
      throw new DomainError('Recommendations already generated for this session');
    }

    this.recommendations = aiService.generateRecommendations(
      this.energyScore,
      externalContext,
      this.userId
    );

    // Domain event: RecommendationsGenerated
  }

  acceptRecommendation(recommendationId: RecommendationId): void {
    const recommendation = this.findRecommendation(recommendationId);
    recommendation.accept();
    // Domain event: RecommendationAccepted
  }

  completeActivity(activityId: ActivityId, rating?: number): void {
    const activity = this.findActivity(activityId);
    activity.complete(rating);
    
    if (this.allActivitiesCompleted()) {
      // Domain event: SessionCompleted
    }
  }

  private ensureOnlyOneSessionPerDay(): void {
    // Business invariant: One energy session per user per day
  }

  private findRecommendation(id: RecommendationId): RoutineRecommendation {
    const rec = this.recommendations.find(r => r.id.equals(id));
    if (!rec) throw new DomainError('Recommendation not found');
    return rec;
  }
}

class RoutineRecommendation { // Entity
  private status: RecommendationStatus = RecommendationStatus.SUGGESTED;
  private activities: Activity[] = [];

  constructor(
    private readonly recommendationId: RecommendationId,
    private readonly type: RecommendationType,
    private readonly title: string,
    private readonly description: string,
    private readonly estimatedDuration: Duration,
    private readonly difficultyModifier: DifficultyModifier,
    private readonly aiReasoning: string
  ) {}

  accept(): void {
    if (this.status !== RecommendationStatus.SUGGESTED) {
      throw new DomainError('Can only accept suggested recommendations');
    }
    this.status = RecommendationStatus.ACCEPTED;
  }

  skip(): void {
    this.status = RecommendationStatus.SKIPPED;
  }

  addActivity(activity: Activity): void {
    if (this.status !== RecommendationStatus.SUGGESTED) {
      throw new DomainError('Cannot modify accepted recommendation');
    }
    this.activities.push(activity);
  }
}

class Activity { // Entity
  private isCompleted: boolean = false;
  private completedAt?: Date;
  private rating?: number;

  constructor(
    private readonly activityId: ActivityId,
    private readonly title: string,
    private readonly description: string,
    private readonly category: ActivityCategory,
    private readonly estimatedDuration: Duration
  ) {}

  complete(userRating?: number): void {
    if (this.isCompleted) {
      throw new DomainError('Activity already completed');
    }
    
    this.isCompleted = true;
    this.completedAt = new Date();
    this.rating = userRating;
    
    // Domain event: ActivityCompleted
  }
}
```

### Aggregate 3: Productivity Journey
**Aggregate Root**: ProductivityJourney  
**Bounded Context**: Progress Visualization
```typescript
class ProductivityJourney {
  private pathSegments: ProgressPath[] = [];
  private milestones: Milestone[] = [];

  constructor(
    private readonly journeyId: JourneyId,
    private readonly userId: UserId, // Reference only
    private startDate: Date
  ) {}

  recordDailyProgress(
    date: Date,
    energyConsistency: number,
    completionRate: number,
    adaptationWisdom: number
  ): void {
    const existingSegment = this.pathSegments.find(s => s.date.equals(date));
    if (existingSegment) {
      throw new DomainError('Progress already recorded for this date');
    }

    const segment = new ProgressPath(
      date,
      energyConsistency,
      completionRate, 
      adaptationWisdom
    );

    this.pathSegments.push(segment);
    this.checkForMilestones(segment);
    
    // Domain event: ProgressRecorded
  }

  private checkForMilestones(newSegment: ProgressPath): void {
    // Check for streak milestones
    const streakLength = this.calculateCurrentStreak();
    if (streakLength % 7 === 0) { // Weekly milestone
      this.milestones.push(new Milestone(
        'ENERGY_STREAK',
        `${streakLength} days of consistent energy tracking`
      ));
    }

    // Check for self-compassion milestone
    if (newSegment.adaptationWisdom > 0.8 && newSegment.completionRate < 0.6) {
      this.milestones.push(new Milestone(
        'SELF_COMPASSION',
        'Choosing rest over pushing through - wise adaptation!'
      ));
    }
  }
}

class ProgressPath { // Value Object
  constructor(
    readonly date: Date,
    readonly energyConsistencyScore: number,
    readonly routineCompletionRate: number,
    readonly adaptationWisdomScore: number
  ) {
    [energyConsistencyScore, routineCompletionRate, adaptationWisdomScore]
      .forEach(score => {
        if (score < 0 || score > 1) {
          throw new DomainError('Scores must be between 0 and 1');
        }
      });
  }

  get overallWellbeingScore(): number {
    return (this.energyConsistencyScore * 0.4) +
           (this.routineCompletionRate * 0.3) +
           (this.adaptationWisdomScore * 0.3);
  }
}
```

## Domain Services

### AIRecommendationService
**Purpose**: Core business logic for generating personalized recommendations
```typescript
class AIRecommendationService {
  constructor(
    private aiPort: AIRecommendationPort,
    private userHistoryPort: UserHistoryPort
  ) {}

  async generateRecommendations(
    energyScore: EnergyScore,
    externalContext: ExternalContext,
    userId: UserId
  ): Promise<RoutineRecommendation[]> {
    // 1. Analyze user patterns
    const userHistory = await this.userHistoryPort.getUserHistory(userId);
    const patterns = this.analyzeEnergyPatterns(userHistory);
    
    // 2. Create recommendation context
    const context = new RecommendationContext(
      energyScore,
      externalContext,
      patterns
    );
    
    // 3. Generate base recommendations via AI
    const baseRecommendations = await this.aiPort.generateRecommendations(context);
    
    // 4. Apply domain rules and adaptations
    return baseRecommendations.map(rec => this.applyDomainRules(rec, energyScore));
  }

  private applyDomainRules(
    baseRecommendation: any,
    energyScore: EnergyScore
  ): RoutineRecommendation {
    const difficultyModifier = DifficultyModifier.forEnergyLevel(energyScore.level);
    const adaptedDuration = difficultyModifier.apply(
      new Duration(baseRecommendation.estimatedDurationMinutes)
    );

    return new RoutineRecommendation(
      new RecommendationId(baseRecommendation.id),
      baseRecommendation.type,
      baseRecommendation.title,
      baseRecommendation.description,
      adaptedDuration,
      difficultyModifier,
      baseRecommendation.aiReasoning
    );
  }
}
```

### BurnoutDetectionService
**Purpose**: Early burnout detection and prevention
```typescript
class BurnoutDetectionService {
  calculateRiskScore(
    recentSessions: EnergySession[],
    activityIntensity: number[]
  ): BurnoutRisk {
    const factors: string[] = [];
    let riskScore = 0;

    // Factor 1: Consecutive high-intensity days
    const consecutiveHighDays = this.countConsecutiveHighIntensityDays(activityIntensity);
    if (consecutiveHighDays >= 3) {
      factors.push(`${consecutiveHighDays} consecutive high-intensity days`);
      riskScore += 0.3;
    }

    // Factor 2: Declining energy trend
    const energyTrend = this.calculateEnergyTrend(recentSessions);
    if (energyTrend < -0.3) {
      factors.push('Declining energy trend detected');
      riskScore += 0.25;
    }

    // Factor 3: Low completion rates with high energy
    const paradox = this.detectCompletionParadox(recentSessions);
    if (paradox) {
      factors.push('High energy but low completion rates - possible overwhelm');
      riskScore += 0.2;
    }

    const recommendation = this.generateRecoveryRecommendation(riskScore);
    
    return new BurnoutRisk(Math.min(riskScore, 1.0), factors, recommendation);
  }

  private generateRecoveryRecommendation(riskScore: number): string {
    if (riskScore >= 0.7) {
      return 'Critical: Take immediate rest days and reduce workload';
    } else if (riskScore >= 0.5) {
      return 'Moderate risk: Consider lighter routines and more recovery time';
    } else {
      return 'Low risk: Maintain current balance with occasional rest days';
    }
  }
}
```

## Repository Interfaces (Ports)

### Energy Domain Repositories
```typescript
interface EnergySessionRepository {
  save(session: EnergySession): Promise<void>;
  findByUserAndDate(userId: UserId, date: Date): Promise<EnergySession | null>;
  findRecentSessions(userId: UserId, days: number): Promise<EnergySession[]>;
}

interface ProductivityJourneyRepository {
  save(journey: ProductivityJourney): Promise<void>;
  findByUserId(userId: UserId): Promise<ProductivityJourney | null>;
}
```

## External Integration Ports

### AI and External Service Ports
```typescript
interface AIRecommendationPort {
  generateRecommendations(context: RecommendationContext): Promise<AIRecommendation[]>;
  analyzeUserPatterns(history: UserHistory): Promise<UserPattern[]>;
}

interface GitHubIntegrationPort {
  fetchUserActivity(userId: UserId, dateRange: DateRange): Promise<GitHubActivity>;
  subscribeToWebhooks(userId: UserId, webhookUrl: string): Promise<void>;
}

interface CalendarIntegrationPort {
  fetchEvents(userId: UserId, date: Date): Promise<CalendarEvent[]>;
  calculateMeetingDensity(events: CalendarEvent[]): Promise<number>;
}

interface NotificationPort {
  sendEnergyReminder(userId: UserId, scheduledTime: Time): Promise<void>;
  sendRecommendationUpdate(userId: UserId, recommendation: RoutineRecommendation): Promise<void>;
  sendBurnoutAlert(userId: UserId, riskLevel: BurnoutRisk): Promise<void>;
}
```

## Domain Events

### Core Domain Events
```typescript
class EnergySessionCreated extends DomainEvent {
  constructor(
    readonly sessionId: EnergySessionId,
    readonly userId: UserId,
    readonly energyScore: EnergyScore,
    readonly timestamp: Date = new Date()
  ) { super(); }
}

class RecommendationsGenerated extends DomainEvent {
  constructor(
    readonly sessionId: EnergySessionId,
    readonly recommendations: RoutineRecommendation[],
    readonly timestamp: Date = new Date()
  ) { super(); }
}

class ActivityCompleted extends DomainEvent {
  constructor(
    readonly activityId: ActivityId,
    readonly userId: UserId,
    readonly completionRating?: number,
    readonly timestamp: Date = new Date()
  ) { super(); }
}

class BurnoutRiskDetected extends DomainEvent {
  constructor(
    readonly userId: UserId,
    readonly riskScore: BurnoutRisk,
    readonly timestamp: Date = new Date()
  ) { super(); }
}
```

## Aggregate Relationships Summary

```
User Aggregate (User Management Context)
└── User (Root)
    ├── UserProfile (Entity)
    ├── SubscriptionTier (Value Object)
    └── UserSettings (Value Object)

EnergySession Aggregate (Energy Tracking Context)  
└── EnergySession (Root)
    ├── EnergyScore (Value Object)
    ├── RoutineRecommendation[] (Entity)
    └── Activity[] (Entity per recommendation)

ProductivityJourney Aggregate (Progress Context)
└── ProductivityJourney (Root)
    ├── ProgressPath[] (Value Object)
    └── Milestone[] (Value Object)

ExternalIntegration Aggregate (Integration Context)
└── IntegrationHub (Root)
    ├── GitHubConnection (Entity) 
    ├── CalendarConnection (Entity)
    └── HealthDataConnection (Entity)
```

## Business Invariants

### Cross-Aggregate Consistency Rules
1. **One Energy Session per User per Day**: Enforced within EnergySession aggregate
2. **Subscription Feature Access**: User aggregate controls feature access across contexts
3. **Burnout Prevention**: BurnoutDetectionService prevents high-intensity recommendations for at-risk users
4. **Data Privacy**: All personal data access controlled through User aggregate permissions

### Domain Rules
1. **Energy Adaptation**: Routine intensity must adapt to energy level (40% reduction for level 1-2)
2. **Self-Compassion**: Rest and recovery count as positive progress, not failures
3. **Streak Preservation**: Missing one day doesn't break streaks if user was intentionally resting
4. **Context Awareness**: Recommendations must consider external calendar density and work patterns

---

**Data Model Status**: ✅ Enhanced with proper DDD patterns  
**Next**: Update plan.md with complete Ports & Adapters architecture