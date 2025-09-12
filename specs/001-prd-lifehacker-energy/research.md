# Phase 0: Technology Research & Decisions

**Feature**: LifeHacker - Energy-First Productivity App  
**Date**: 2025-09-12  
**Context**: Mobile app with NestJS API backend requiring external integrations and AI recommendations

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

## AI Recommendation Architecture

**Decision**: OpenAI GPT-4 + RAG with LangChain for structured recommendations  
**Rationale**:
- OpenAI provides structured output capabilities for routine recommendations
- RAG pattern allows incorporation of user history and best practices
- LangChain simplifies prompt engineering and response parsing
- Fallback to rule-based recommendations for offline/API failures

**Alternatives considered**:
- Custom ML model: Rejected - requires extensive training data and infrastructure
- Gemini API only: Rejected - less structured output support
- Pure rule-based system: Rejected - lacks adaptability and personalization

**Implementation approach**:
- Vector database (Pinecone/Chroma) for user pattern storage
- Prompt templates for different energy levels and contexts
- Response caching (30min TTL) to reduce API costs
- Gradual learning from user feedback and completion patterns

## Flutter Offline-First Architecture

**Decision**: SQLite local storage with optimistic updates and WebSocket sync  
**Rationale**:
- Energy level input must work offline (core user flow)
- SQLite provides reliable local storage with ACID transactions
- Optimistic updates provide immediate UI feedback
- WebSocket enables real-time sync when online

**Alternatives considered**:
- Online-only with caching: Rejected - breaks core energy input flow
- Firebase offline: Rejected - vendor lock-in, less control over sync logic
- Custom file-based storage: Rejected - complexity, no ACID guarantees

**Implementation approach**:
- Local SQLite database mirroring server schema
- Sync queue for pending changes during offline periods
- Conflict resolution favoring most recent timestamps
- Background sync service with exponential backoff

## Push Notification Architecture

**Decision**: Firebase Cloud Messaging (FCM) with NestJS backend orchestration  
**Rationale**:
- FCM supports both iOS and Android with single integration
- Server-side scheduling allows personalized timing based on user patterns
- Supports rich notifications with custom actions
- Reliable delivery with offline queuing

**Alternatives considered**:
- Apple Push Notification Service only: Rejected - Android not supported
- Third-party service (Pusher): Rejected - additional dependency and cost
- Local notifications only: Rejected - no server-side intelligence

**Implementation approach**:
- FCM integration in NestJS with user device token management
- Scheduled notifications based on user's optimal energy input times
- Rich notifications for routine reminders with completion actions
- Analytics tracking for notification engagement optimization

## Subscription Management Patterns

**Decision**: Stripe subscription with App Store/Play Store integration  
**Rationale**:
- Stripe handles complex subscription logic, dunning, and compliance
- Mobile stores require native purchase flows for app store policies
- Revenue Cat provides unified mobile subscription management
- Webhook-based subscription status synchronization

**Alternatives considered**:
- Direct mobile store integration only: Rejected - limited web management
- PayPal subscriptions: Rejected - less mobile-friendly
- Custom billing: Rejected - complexity, compliance requirements

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

---

**Research Status**: ✅ Complete - All technical unknowns resolved  
**Next Phase**: Design & Contracts (data-model.md, contracts/, quickstart.md)