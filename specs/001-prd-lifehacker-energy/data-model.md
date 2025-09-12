# Phase 1: Data Model Design

**Feature**: LifeHacker - Energy-First Productivity App  
**Date**: 2025-09-12  
**Context**: Domain entities and relationships for energy-first productivity system

## Core Entities

### User Profile
**Purpose**: Central user entity containing account information and preferences
**Key Attributes**:
- `id`: UUID primary identifier
- `email`: Unique user email address
- `subscription_tier`: Enum (FREE, PRO, TEAM)
- `onboarding_progress`: JSON tracking completed steps
- `timezone`: User's timezone for scheduling
- `notification_preferences`: JSON configuration for push notifications
- `privacy_settings`: JSON data sharing and export preferences
- `created_at`, `updated_at`: Timestamp tracking

**Relationships**:
- One-to-many: EnergyLevel, RoutineRecommendation, UserSettings
- One-to-many: ExternalAppData, InsightReport, BurnoutRiskAssessment

**Validation Rules**:
- Email must be valid format and unique
- Subscription tier must be valid enum value
- Timezone must be valid IANA timezone identifier

### Energy Level
**Purpose**: Daily energy assessments with context for AI recommendations
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `level`: Integer (1-5 scale) representing energy state
- `mood_context`: Optional text description of current mood
- `timestamp`: When energy level was recorded
- `location_context`: Optional location data if enabled
- `weather_context`: Optional weather data correlation

**Relationships**:
- Many-to-one: User Profile
- One-to-many: RoutineRecommendation (recommendations generated from this energy)

**Validation Rules**:
- Level must be between 1 and 5 inclusive
- Each user can only have one energy level per day (unique constraint)
- Timestamp must not be in the future

**State Transitions**:
- Created → Updated (same day modification allowed)
- Cannot be deleted once used for recommendations

### Routine Recommendation
**Purpose**: AI-generated daily activity suggestions adapted to energy and context
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `energy_level_id`: Foreign key to triggering Energy Level
- `recommendation_type`: Enum (WORK, EXERCISE, REST, PERSONAL)
- `title`: Short descriptive title
- `description`: Detailed activity description
- `estimated_duration_minutes`: Expected time to complete
- `difficulty_modifier`: Float (0.4 to 1.5 based on energy adaptation)
- `ai_reasoning`: Text explaining why this was recommended
- `status`: Enum (SUGGESTED, ACCEPTED, COMPLETED, SKIPPED)
- `completion_rating`: Optional 1-5 user feedback after completion
- `created_at`: When recommendation was generated

**Relationships**:
- Many-to-one: User Profile, Energy Level
- One-to-many: Activity (activities within this recommendation)

**Validation Rules**:
- Difficulty modifier must be between 0.1 and 2.0
- Status transitions: SUGGESTED → (ACCEPTED|SKIPPED), ACCEPTED → (COMPLETED|SKIPPED)
- Completion rating only valid when status is COMPLETED

### Activity
**Purpose**: Individual tasks within routine recommendations with tracking
**Key Attributes**:
- `id`: UUID primary identifier
- `routine_recommendation_id`: Foreign key to Routine Recommendation
- `title`: Activity name
- `description`: Detailed instructions
- `category`: Enum (EXERCISE, MEDITATION, WORK_BLOCK, BREAK, LEARNING)
- `estimated_duration_minutes`: Expected completion time
- `is_completed`: Boolean completion status
- `completed_at`: Timestamp when marked complete
- `notes`: Optional user notes after completion

**Relationships**:
- Many-to-one: Routine Recommendation

**Validation Rules**:
- Cannot be marked completed without parent recommendation being accepted
- Completed timestamp must not be before recommendation creation

### External App Data
**Purpose**: Synchronized information from productivity and health tools
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `provider`: Enum (GITHUB, LINEAR, NOTION, GOOGLE_CALENDAR, APPLE_HEALTH)
- `data_type`: Enum (COMMITS, ISSUES, MEETINGS, SLEEP, STEPS, HEART_RATE)
- `value`: JSON containing provider-specific data
- `sync_timestamp`: When data was last synchronized
- `date_range_start`, `date_range_end`: Time period this data covers

**Relationships**:
- Many-to-one: User Profile

**Validation Rules**:
- Provider and data_type combinations must be valid
- Sync timestamp must not be in the future
- Date range end must not be before start

**Data Retention**:
- Raw data retained for 90 days
- Aggregated insights retained indefinitely

### Progress Path
**Purpose**: Visual representation of user's productivity journey with milestones
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `path_date`: Date this path segment represents
- `energy_consistency_score`: Float 0-1 based on regular energy tracking
- `routine_completion_rate`: Float 0-1 based on completed recommendations
- `adaptation_wisdom_score`: Float 0-1 based on taking rest when needed
- `milestone_type`: Enum (ENERGY_STREAK, SELF_COMPASSION, BALANCE_ACHIEVEMENT)
- `milestone_description`: Human-readable achievement description
- `visual_path_data`: JSON containing UI visualization data

**Relationships**:
- Many-to-one: User Profile

**Validation Rules**:
- All score values must be between 0.0 and 1.0
- Path date must be unique per user
- Cannot have future path dates

### Insight Report
**Purpose**: Weekly and monthly analytics showing patterns and recommendations
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `report_type`: Enum (WEEKLY, MONTHLY)
- `period_start`, `period_end`: Date range covered by report
- `energy_pattern_analysis`: JSON with energy trends and patterns
- `routine_effectiveness`: JSON with completion rates and satisfaction
- `burnout_risk_factors`: JSON with identified risk patterns
- `growth_recommendations`: JSON with personalized suggestions
- `generated_at`: When report was created

**Relationships**:
- Many-to-one: User Profile

**Validation Rules**:
- Period end must be after period start
- Cannot generate multiple reports of same type for overlapping periods
- Reports can only be generated for past periods

### Burnout Risk Assessment
**Purpose**: Calculated risk score based on activity intensity and energy patterns
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `assessment_date`: Date of risk calculation
- `risk_score`: Float 0-1 indicating burnout probability
- `consecutive_high_intensity_days`: Count of recent high-activity periods
- `energy_decline_rate`: Float indicating energy level trend
- `recovery_recommendation`: Text suggestion for risk mitigation
- `alert_level`: Enum (LOW, MODERATE, HIGH, CRITICAL)

**Relationships**:
- Many-to-one: User Profile

**Validation Rules**:
- Risk score must be between 0.0 and 1.0
- Alert level must correspond to risk score ranges
- Assessment date must not be in the future

**Business Rules**:
- Daily calculation for users with >7 days of data
- Automatic recovery routine recommendations when risk > 0.7

### User Settings
**Purpose**: Personalization preferences and app configuration
**Key Attributes**:
- `id`: UUID primary identifier
- `user_id`: Foreign key to User Profile
- `notification_morning_time`: Time for daily energy input reminder
- `notification_evening_time`: Time for daily reflection reminder
- `routine_categories_enabled`: JSON array of preferred routine types
- `external_integrations_enabled`: JSON configuration for connected apps
- `privacy_data_sharing`: Boolean for anonymized analytics participation
- `ui_theme`: Enum (LIGHT, DARK, AUTO)
- `language_preference`: ISO 639-1 language code

**Relationships**:
- Many-to-one: User Profile

**Validation Rules**:
- Notification times must be valid time format (HH:MM)
- Language preference must be supported language code
- External integrations must reference valid provider types

## Entity Relationships Summary

```
User Profile (1) ←→ (∞) Energy Level
User Profile (1) ←→ (∞) Routine Recommendation  
User Profile (1) ←→ (∞) External App Data
User Profile (1) ←→ (∞) Progress Path
User Profile (1) ←→ (∞) Insight Report
User Profile (1) ←→ (∞) Burnout Risk Assessment
User Profile (1) ←→ (1) User Settings

Energy Level (1) ←→ (∞) Routine Recommendation
Routine Recommendation (1) ←→ (∞) Activity
```

## Database Indexes

**Performance Critical Indexes**:
- `energy_levels(user_id, timestamp)` - Daily energy lookup
- `routine_recommendations(user_id, created_at)` - Recent recommendations
- `external_app_data(user_id, provider, sync_timestamp)` - Latest sync data
- `progress_path(user_id, path_date)` - Progress visualization
- `burnout_risk_assessments(user_id, assessment_date)` - Risk monitoring

## Data Archival Strategy

**Hot Data** (Active queries):
- Energy levels: Last 90 days
- Recommendations: Last 30 days
- External app data: Last 30 days

**Warm Data** (Analytics):
- Progress path: Last 365 days
- Insight reports: Last 730 days  
- Burnout assessments: Last 365 days

**Cold Data** (Long-term storage):
- Historical data older than retention periods
- Anonymized for research with user consent

---

**Data Model Status**: ✅ Complete - 9 entities defined with relationships and validation  
**Next Phase**: API Contracts (contracts/ directory)