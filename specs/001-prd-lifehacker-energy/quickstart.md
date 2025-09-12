# Phase 1: Integration Test Scenarios

**Feature**: LifeHacker - Energy-First Productivity App  
**Date**: 2025-09-12  
**Context**: User story validation scenarios for TDD implementation

## Test Scenario 1: Morning Energy Input → AI Recommendation Flow

**User Story**: A user feels tired (energy level 2/5) in the morning and receives lighter activity recommendations instead of intense workout.

### Test Steps
1. **Setup**:
   - Create test user with PRO subscription
   - Set user timezone to "America/New_York"
   - Mock current time as 8:00 AM on test date

2. **Execute Energy Input**:
   ```http
   POST /v1/energy-levels
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "level": 2,
     "mood_context": "Feeling tired after late night coding"
   }
   ```

3. **Verify Energy Level Response**:
   - Status: 201 Created
   - Response contains energy level ID
   - Level value is 2
   - Timestamp is current time

4. **Generate Recommendation**:
   ```http
   POST /v1/recommendations/generate
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "energy_level_id": "{energy_level_id_from_step_3}",
     "preferred_categories": ["EXERCISE", "WORK_BLOCK"]
   }
   ```

5. **Verify Recommendation Response**:
   - Status: 200 OK
   - Difficulty modifier ≤ 0.6 (adapted for low energy)
   - Activities include gentle options (e.g., "15-minute stretching")
   - No high-intensity activities (e.g., "HIIT workout")
   - AI reasoning explains energy-based adaptation

### Expected Results
- ✅ Energy level recorded successfully
- ✅ Recommendation generated with reduced intensity
- ✅ Activities match user's low energy state
- ✅ System explains adaptation reasoning

## Test Scenario 2: Burnout Detection → Recovery Routine Suggestion

**User Story**: A user has had 3 consecutive high-intensity work days and receives automatic recovery-focused routine suggestion.

### Test Setup
1. **Create Historical Data**:
   - Day 1: Energy level 4, completed high-intensity work + exercise routine
   - Day 2: Energy level 4, completed high-intensity work + exercise routine  
   - Day 3: Energy level 3, completed moderate work routine
   - Day 4: Current day, energy level 2

2. **Mock External Data**:
   - GitHub commits: 15+ per day for last 3 days
   - Calendar density: 6+ hours meetings per day
   - Sleep data: Declining quality trend

### Test Steps
1. **Input Today's Energy Level**:
   ```http
   POST /v1/energy-levels
   Content-Type: application/json
   
   {
     "level": 2,
     "mood_context": "Exhausted, need rest"
   }
   ```

2. **Check Burnout Risk Assessment**:
   ```http
   GET /v1/progress/insights?period=weekly
   Authorization: Bearer {test_jwt}
   ```

3. **Generate Recommendation**:
   ```http
   POST /v1/recommendations/generate
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "energy_level_id": "{current_energy_id}"
   }
   ```

### Expected Results
- ✅ Burnout risk score > 0.7 detected
- ✅ Recommendation type is "REST" 
- ✅ Activities focus on recovery (meditation, light walk, early sleep)
- ✅ AI reasoning mentions burnout prevention
- ✅ System suggests calendar blocks for rest

## Test Scenario 3: GitHub Activity → Eye Rest Recommendation

**User Story**: A user with many GitHub commits this week receives eye rest activity suggestions.

### Test Setup
1. **Mock External Data Sync**:
   ```http
   POST /v1/integrations/sync
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "providers": ["GITHUB"]
   }
   ```

2. **Mock GitHub Data** (simulated by test):
   - Commits this week: 47
   - Lines changed: 2,847
   - Hours coding (estimated): 32

### Test Steps
1. **Input Energy Level**:
   ```http
   POST /v1/energy-levels
   Content-Type: application/json
   
   {
     "level": 4,
     "mood_context": "Focused and productive"
   }
   ```

2. **Generate Recommendation**:
   ```http
   POST /v1/recommendations/generate
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "energy_level_id": "{energy_level_id}"
   }
   ```

### Expected Results
- ✅ Recommendation includes eye care activities
- ✅ Activities: "20-20-20 eye break", "Outdoor 10-minute walk"
- ✅ AI reasoning mentions high screen time correlation
- ✅ Estimated duration accounts for coding intensity

## Test Scenario 4: Completion Tracking → Progress Path Update

**User Story**: A user completes adapted routine and sees progress reflected as intentional growth rather than reduced performance.

### Test Steps
1. **Setup Initial State**:
   - User has active recommendation with status "ACCEPTED"
   - Recommendation has 3 activities: meditation, light exercise, journaling

2. **Complete First Activity**:
   ```http
   PATCH /v1/recommendations/{id}/status
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "status": "COMPLETED",
     "completion_rating": 4
   }
   ```

3. **Check Progress Path Update**:
   ```http
   GET /v1/progress/path?days=7
   Authorization: Bearer {test_jwt}
   ```

### Expected Results
- ✅ Progress path shows completion as positive growth
- ✅ "Adaptation wisdom score" increases (not decreases)
- ✅ Milestone achieved: "Self-Compassion Achievement"
- ✅ Visual path data shows forward movement
- ✅ No negative indicators for choosing rest over intensity

## Test Scenario 5: Calendar Density → Routine Intensity Adjustment

**User Story**: A user's calendar shows back-to-back meetings, so system reduces intensity of other recommended activities.

### Test Setup
1. **Mock Calendar Integration**:
   ```http
   POST /v1/integrations/calendar
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "oauth_code": "mock_google_oauth_code"
   }
   ```

2. **Mock Calendar Data** (simulated):
   - Today: 9 AM - 5 PM solid meetings
   - Available slots: 7-9 AM, 6-8 PM only
   - Meeting density: 89%

### Test Steps
1. **Input Energy Level**:
   ```http
   POST /v1/energy-levels
   Content-Type: application/json
   
   {
     "level": 4,
     "mood_context": "High energy but busy day ahead"
   }
   ```

2. **Generate Recommendation**:
   ```http
   POST /v1/recommendations/generate
   Authorization: Bearer {test_jwt}
   Content-Type: application/json
   
   {
     "energy_level_id": "{energy_level_id}"
   }
   ```

### Expected Results
- ✅ Despite high energy (4/5), activities have reduced intensity
- ✅ Difficulty modifier ≈ 0.8 (considering calendar constraints)
- ✅ Activities fit available time slots (7-9 AM, 6-8 PM)
- ✅ AI reasoning mentions calendar density impact
- ✅ Recommendations include "micro-breaks" during meetings

## Integration Test Infrastructure

### Test Data Management
```sql
-- Test user setup
INSERT INTO user_profiles (id, email, subscription_tier, timezone) 
VALUES ('test-user-123', 'test@lifehacker.app', 'PRO', 'America/New_York');

-- Historical energy levels for burnout testing
INSERT INTO energy_levels (user_id, level, timestamp) VALUES
  ('test-user-123', 4, NOW() - INTERVAL '3 days'),
  ('test-user-123', 4, NOW() - INTERVAL '2 days'),
  ('test-user-123', 3, NOW() - INTERVAL '1 day');
```

### Mock External API Responses
```javascript
// GitHub API mock
mockGitHubResponse = {
  commits: 47,
  lines_changed: 2847,
  repositories: ['lifehacker-api', 'lifehacker-mobile'],
  most_active_hours: ['09:00', '14:00', '19:00']
};

// Google Calendar API mock  
mockCalendarResponse = {
  events: [
    { start: '09:00', end: '10:00', title: 'Team Standup' },
    { start: '10:00', end: '12:00', title: 'Architecture Review' },
    // ... 7 more back-to-back meetings
  ],
  free_busy_slots: [
    { start: '07:00', end: '09:00' },
    { start: '18:00', end: '20:00' }
  ]
};
```

### WebSocket Real-time Testing
```javascript
// Test real-time recommendation updates
test('Energy level change triggers recommendation update', async () => {
  const socket = io('ws://localhost:3001', { 
    auth: { token: testJwt } 
  });
  
  socket.on('recommendation-update', (data) => {
    expect(data.difficulty_modifier).toBeLessThan(0.6);
    expect(data.ai_reasoning).toContain('energy level change');
  });
  
  // Trigger energy level update
  await postEnergyLevel({ level: 2 });
  
  // Wait for WebSocket event
  await waitForEvent(socket, 'recommendation-update', 5000);
});
```

## Automated Test Execution

### Contract Test Suite
```bash
# Run API contract tests
npm run test:contracts

# Run integration scenarios
npm run test:integration

# Run end-to-end user flows
npm run test:e2e
```

### Performance Benchmarks
- Energy input → Recommendation generation: < 3 seconds
- External API sync: < 10 seconds for all providers
- Progress path calculation: < 1 second
- WebSocket message delivery: < 500ms

### Success Criteria
- ✅ All 5 user story scenarios pass
- ✅ API responses match OpenAPI schema
- ✅ Real-time updates work via WebSocket
- ✅ External integrations handle errors gracefully
- ✅ Performance benchmarks met
- ✅ Database transactions maintain consistency

---

**Quickstart Status**: ✅ Complete - 5 integration scenarios defined with test infrastructure  
**Next Phase**: Agent context update (CLAUDE.md)