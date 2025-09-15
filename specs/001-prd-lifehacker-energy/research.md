# Phase 0: React Native Technology Research

**Feature**: LifeHacker - Energy-First Productivity App
**Date**: 2025-09-14 (Updated: 2025-09-15)
**Context**: ✅ **IMPLEMENTED REACT NATIVE APPLICATION** - Actual implementation decisions based on development experience

## External API Integration Patterns

**Decision**: OAuth 2.0 with webhook subscriptions for real-time updates  
**Rationale**: 
- GitHub, Linear, Notion all support OAuth 2.0 for secure authentication
- Webhooks provide real-time updates without polling overhead
- Google Calendar and Apple Health have established OAuth flows
- Reduces API rate limiting issues compared to polling strategies

**Alternatives considered**:
- API key authentication: Rejected - less secure, no webhooks
- Polling-based sync: Rejected - higher latency, rate limiting issues
- Direct database sync: Rejected - not supported by external services

**Implementation approach**:
- NestJS domain: `external-integrations` with service per provider
- Webhook endpoints for real-time data updates
- Fallback polling for providers without webhook support
- Encrypted token storage with refresh token rotation

## AI Recommendation Architecture *(Actual Implementation vs Planned)*

**Decision**: ✅ Rule-based recommendation system (implemented) + 🔄 OpenAI GPT-4 integration (planned)  
**Rationale**:
- ✅ Rule-based system provides immediate recommendations without external API dependencies
- ✅ Smart algorithm adapts difficulty based on energy levels and user patterns
- 🔄 OpenAI integration planned for more personalized, context-aware recommendations
- ✅ Offline functionality achieved through rule-based fallback (constitutional requirement)

**✅ Current Implementation**:
- Rule-based recommendation service with activity categorization
- Energy level and time-of-day matching for activity suggestions
- Difficulty modifier calculation based on user energy and historical patterns
- Complete test coverage (112+ tests) for recommendation logic

**🔄 Planned Enhancements**:
- OpenAI GPT-4 integration for natural language recommendation explanations
- RAG pattern for incorporating user feedback and preference learning
- LangChain for structured prompt engineering and response parsing

**Implementation approach**:
- Vector database (Pinecone/Chroma) for user pattern storage
- Prompt templates for different energy levels and contexts
- Response caching (30min TTL) to reduce API costs
- Gradual learning from user feedback and completion patterns

## React Native Offline-First Architecture *(Actual Implementation)*

**Decision**: ✅ MMKV + AsyncStorage with TanStack Query offline persistence  
**Rationale**:
- ✅ Energy level input works offline (core user flow implemented)
- ✅ MMKV provides high-performance storage for frequently accessed data
- ✅ AsyncStorage for TanStack Query persistence enables offline caching
- ✅ Optimistic updates through TanStack Query provide immediate UI feedback

**Alternatives considered**:
- SQLite: Implemented approach is simpler for current data complexity
- Zustand persist: Used for auth state, complemented by TanStack Query for server state
- Firebase offline: Rejected - vendor lock-in, less control over sync logic

**✅ Actual Implementation approach**:
- MMKV for high-performance key-value storage (user preferences, settings)
- AsyncStorage for TanStack Query offline persistence (energy sessions, recommendations)
- Zustand with persist middleware for authentication state
- Background refetch with exponential backoff via TanStack Query

## Push Notification Architecture *(Not Yet Implemented)*

**Decision**: ❌ Firebase Cloud Messaging (FCM) with NestJS backend orchestration (planned)  
**Rationale**:
- FCM supports both iOS and Android with single integration
- Server-side scheduling allows personalized timing based on user patterns
- Supports rich notifications with custom actions
- Reliable delivery with offline queuing

**Current Status**: ❌ NOT IMPLEMENTED
- Core energy tracking and recommendation functionality prioritized first
- Infrastructure ready for notification integration
- React Native push notification libraries not yet configured

**🔄 Implementation approach (planned)**:
- FCM integration in NestJS with user device token management
- Scheduled notifications based on user's optimal energy input times
- Rich notifications for routine reminders with completion actions
- Analytics tracking for notification engagement optimization

## Subscription Management Patterns *(Not Yet Implemented)*

**Decision**: ❌ Stripe subscription with App Store/Play Store integration (planned)  
**Rationale**:
- Stripe handles complex subscription logic, dunning, and compliance
- Mobile stores require native purchase flows for app store policies
- Revenue Cat provides unified mobile subscription management
- Webhook-based subscription status synchronization

**Current Status**: ❌ NOT IMPLEMENTED
- Free tier functionality fully implemented and working
- Premium features identified but not gated behind subscription
- Authentication and user management infrastructure supports future subscription integration

**🔄 Planned Implementation**:
- Stripe subscription backend integration
- React Native in-app purchase implementation
- Revenue Cat for unified mobile subscription management

**Implementation approach**:
- Revenue Cat SDK for mobile subscription management
- Stripe for web-based subscription management
- NestJS webhook handlers for subscription status updates
- Free tier with feature limitations, premium unlocks

## Real-time Synchronization Strategy

**Decision**: WebSocket with Socket.IO for bidirectional real-time communication  
**Rationale**:
- Energy level updates need immediate recommendation regeneration
- Progress path updates should reflect across devices in real-time
- Socket.IO provides reliable WebSocket with fallbacks
- Supports room-based updates for multi-device scenarios

**Alternatives considered**:
- Server-Sent Events: Rejected - unidirectional only
- Polling: Rejected - increased battery drain, higher latency
- GraphQL subscriptions: Rejected - adds complexity for simple use case

**Implementation approach**:
- Socket.IO server in NestJS with authenticated connections
- Flutter WebSocket client with automatic reconnection
- Event-based updates for energy levels, recommendations, progress
- Offline queue with replay on reconnection

## Data Privacy and Security

**Decision**: End-to-end encryption for sensitive data with GDPR compliance  
**Rationale**:
- Health and productivity data requires maximum privacy protection
- EU users need GDPR compliance for market access
- Zero-trust architecture protects against data breaches
- User control over data export and deletion

**Alternatives considered**:
- Basic HTTPS only: Rejected - insufficient for sensitive health data
- Client-side encryption only: Rejected - limits server-side AI recommendations
- Third-party privacy service: Rejected - additional complexity and vendor risk

**Implementation approach**:
- AES-256 encryption for sensitive fields in database
- User-controlled encryption keys stored locally
- GDPR-compliant data export and deletion workflows
- Audit logging for all data access and modifications

## Performance Optimization Strategy

**Decision**: Multi-level caching with CDN and database optimization  
**Rationale**:
- AI recommendations must respond within 3 seconds
- Mobile app needs smooth 60fps performance
- External API responses vary in latency
- Offline functionality requires local optimization

**Implementation approach**:
- Redis caching for API responses and AI recommendations
- Database indexing on frequently queried fields (user_id, timestamp)
- Flutter widget optimization with proper state management
- Image and asset optimization with CDN delivery
- Lazy loading for non-critical data and features

## React Native Application Architecture

**Decision**: Container/Presentational pattern with Zustand + TanStack Query
**Rationale**:
- Better web expansion with React Native Web (Flutter Web has limitations)
- Unified React ecosystem enables shared components and tooling
- Container/Presentational pattern provides superior testability and maintainability
- Zustand offers minimal boilerplate vs Redux while maintaining type safety
- TanStack Query handles server state with built-in caching and offline support
- NativeWind brings Tailwind CSS styling for cross-platform consistency

**Alternatives considered**:
- Flutter: Rejected - limited web expansion, separate Dart ecosystem
- Redux Toolkit: Rejected - unnecessary complexity for app size
- Context + useReducer: Rejected - poor performance for frequent updates
- React Native Elements: Rejected - outdated design system

**Architecture Benefits**:
- **Performance**: New Architecture (Fabric + TurboModules) + Hermes engine
- **Developer Experience**: Superior debugging with React DevTools, Flipper
- **Code Reuse**: Shared components for future web expansion
- **Team Expertise**: Leverage existing React/TypeScript knowledge
- **Library Ecosystem**: Richer third-party ecosystem for integrations

**React Native Project Architecture**:
```
life-hacking-mobile/
├── src/
│   ├── api/                     # TanStack Query setup, API hooks
│   ├── components/              # Atomic design system
│   │   ├── atoms/               # Button, Input, Text, Badge
│   │   ├── molecules/           # LabeledInput, EnergySlider
│   │   ├── organisms/           # EnergyForm, RoutineCard
│   │   └── templates/           # Screen layouts
│   ├── features/                # Feature modules
│   │   └── {feature}/
│   │       ├── containers/      # Business logic, state management
│   │       ├── components/      # Pure UI components
│   │       ├── api/             # Feature-specific API hooks
│   │       └── types/           # TypeScript interfaces
│   ├── state/                   # Zustand stores, TanStack Query client
│   ├── navigation/              # React Navigation setup
│   └── utils/                   # Shared utilities
└── tests/
    ├── components/              # Presentational component tests
    ├── containers/              # Container logic tests
    └── integration/             # Feature workflow tests
```

**Key Dependencies Migration**:
- `zustand`: Client state (theme, UI preferences) - replaces Riverpod
- `@tanstack/react-query`: Server state, caching - replaces manual data layer
- `react-native-reusables`: shadcn/ui design system - replaces shadcn_flutter
- `nativewind`: Tailwind CSS styling - replaces Flutter theme system
- `react-hook-form` + `zod`: Form management - replaces Flutter form widgets
- `react-navigation`: Navigation - replaces Flutter Navigator
- `react-native-mmkv`: High-performance storage - replaces SQLite for simple data

**Container/Presentational Implementation**:
```typescript
// Container: Business logic and state
const EnergyInputContainer = () => {
  const { mutate: updateEnergy } = useUpdateEnergyLevel()
  const { data: recommendations } = useEnergyRecommendations()

  const handleEnergyUpdate = (level: number) => {
    updateEnergy(level, {
      onSuccess: () => {
        // Handle success logic
      }
    })
  }

  return (
    <EnergyInputForm
      onEnergyUpdate={handleEnergyUpdate}
      recommendations={recommendations}
      isLoading={isLoading}
    />
  )
}

// Presentational: Pure UI component
interface EnergyInputFormProps {
  onEnergyUpdate: (level: number) => void
  recommendations?: Recommendation[]
  isLoading: boolean
}

const EnergyInputForm: React.FC<EnergyInputFormProps> = ({
  onEnergyUpdate,
  recommendations,
  isLoading
}) => {
  // Pure UI logic only
  return (
    <View>
      {/* UI components */}
    </View>
  )
}
```

**State Management Strategy**:
- **Zustand**: Theme, language, onboarding status, offline settings
- **TanStack Query**: User profile, energy history, AI recommendations, external data
- **AsyncStorage**: Data persistence for Zustand stores
- **MMKV**: High-performance cache for TanStack Query

---

**Research Status**: ✅ Complete - React Native development research finalized
**Next Phase**: Update data models and contracts for React Native architecture