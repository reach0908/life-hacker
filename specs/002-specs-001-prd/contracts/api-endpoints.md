# API Endpoints Documentation

**Date**: 2025-09-15
**Status**: Current Implementation (Verified from Codebase)

## Authentication Endpoints

### Web OAuth Flow

#### `GET /auth/google`
**Description**: Initiate Google OAuth authentication (Web)
**Implementation Status**: ✅ IMPLEMENTED
**Response**: 302 Redirect to Google OAuth consent screen

#### `GET /auth/google/callback`
**Description**: Google OAuth callback handler (Web)
**Implementation Status**: ✅ IMPLEMENTED
**Query Parameters**:
- `code` (required): OAuth authorization code from Google
- `state` (optional): State parameter for CSRF protection
**Response**: LoginResponseDto with JWT tokens

### Mobile OAuth Flow

#### `POST /auth/google/mobile`
**Description**: Google ID Token authentication (React Native)
**Implementation Status**: ✅ IMPLEMENTED
**Request Body**: LoginWithGoogleIdTokenRequestDto
```json
{
  "idToken": "string (required)"
}
```
**Response**: LoginResponseDto with JWT tokens

### Token Management

#### `POST /auth/refresh`
**Description**: Refresh access token
**Implementation Status**: ✅ IMPLEMENTED
**Request Body**: RefreshTokenRequestDto
```json
{
  "refreshToken": "string (required)"
}
```
**Response**: New access token

#### `POST /auth/logout`
**Description**: Logout from specific device
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Request Body**:
```json
{
  "refreshToken": "string (required)"
}
```
**Response**: 204 No Content

#### `POST /auth/logout/all`
**Description**: Logout from all devices
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Response**: 204 No Content

#### `GET /auth/me`
**Description**: Get current user information
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Response**:
```json
{
  "id": "string",
  "email": "string",
  "name": "string"
}
```

## Energy Tracking Endpoints

### Energy Session Management

#### `POST /energy-levels`
**Description**: Create new energy session
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Request Body**: CreateEnergySessionDto
```json
{
  "energyScore": "number (1-5, required)",
  "duration": "number (minutes, required)",
  "difficultyModifier": "number (0.1-3.0, required)",
  "activity": "string (optional)",
  "notes": "string (optional)",
  "location": "string (optional)",
  "tags": "string[] (optional)",
  "sessionStartTime": "Date (required)",
  "sessionEndTime": "Date (optional)"
}
```
**Response**: EnergySessionResponseDto

#### `GET /energy-levels`
**Description**: Get energy sessions with filtering
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Query Parameters**:
- `skip` (optional): Number of records to skip
- `take` (optional): Number of records to take (default: 20)
- `orderBy` (optional): newest | oldest | energyScore | productivity
- `startDate` (optional): Filter by start date
- `endDate` (optional): Filter by end date
- `minEnergyScore` (optional): Minimum energy score filter
- `maxEnergyScore` (optional): Maximum energy score filter
- `tags` (optional): Comma-separated tags filter
- `activity` (optional): Activity filter
- `location` (optional): Location filter
- `isActive` (optional): Active session filter
**Response**: Array of EnergySessionResponseDto

#### `GET /energy-levels/active`
**Description**: Get currently active energy session
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Response**: EnergySessionResponseDto | null

#### `GET /energy-levels/recent`
**Description**: Get recent energy sessions
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Query Parameters**:
- `limit` (optional): Number of sessions to return (default: 10)
**Response**: Array of EnergySessionResponseDto

#### `GET /energy-levels/high-productivity`
**Description**: Get high productivity energy sessions
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Query Parameters**:
- `minProductivity` (optional): Minimum productivity score (default: 5.0)
- `skip` (optional): Number of records to skip
- `take` (optional): Number of records to take (default: 20)
**Response**: Array of EnergySessionResponseDto

#### `GET /energy-levels/date/:date`
**Description**: Get energy sessions for specific date
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Path Parameters**:
- `date`: Date in YYYY-MM-DD format
**Response**: Array of EnergySessionResponseDto

#### `GET /energy-levels/count`
**Description**: Get count of energy sessions with filters
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Query Parameters**: Same as GET /energy-levels
**Response**:
```json
{
  "count": "number"
}
```

## User Management Endpoints

### User Profile

#### `GET /users/profile`
**Description**: Get user profile
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Response**: UserResponseDto

#### `PUT /users/profile`
**Description**: Update user profile
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Request Body**: UpdateUserDto
**Response**: UserResponseDto

## Routine Recommendations Endpoints

### Recommendation Management

#### `POST /routine-recommendations/generate`
**Description**: Generate new routine recommendations
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Request Body**: GenerateRecommendationsRequestDto
**Response**: RecommendationsGenerationResponseDto

#### `GET /routine-recommendations`
**Description**: Get user's routine recommendations
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Query Parameters**:
- `status` (optional): Filter by status
- `type` (optional): Filter by recommendation type
- `page` (optional): Page number
- `limit` (optional): Records per page
**Response**: GetRecommendationsResponseDto

#### `PUT /routine-recommendations/:id/accept`
**Description**: Accept a routine recommendation
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Path Parameters**:
- `id`: Recommendation ID
**Response**: RecommendationResponseDto

#### `PUT /routine-recommendations/:id/complete`
**Description**: Mark recommendation as completed
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Path Parameters**:
- `id`: Recommendation ID
**Response**: RecommendationResponseDto

#### `PUT /routine-recommendations/:id/skip`
**Description**: Skip a recommendation
**Implementation Status**: ✅ IMPLEMENTED
**Authentication**: JWT required
**Path Parameters**:
- `id`: Recommendation ID
**Response**: RecommendationResponseDto

## Missing Endpoints (Not Yet Implemented)

### Progress Visualization Domain
- `GET /progress/journey` - Get user's productivity journey
- `GET /progress/insights` - Get progress insights and analytics
- `GET /progress/milestones` - Get achieved milestones

### External Integrations Domain
- `POST /integrations/github/connect` - Connect GitHub account
- `GET /integrations/github/activity` - Get GitHub activity data
- `POST /integrations/calendar/connect` - Connect Google Calendar
- `GET /integrations/calendar/events` - Get calendar events
- `POST /integrations/health/connect` - Connect Apple Health
- `GET /integrations/health/data` - Get health metrics

## API Response Formats

### Standard Response DTOs

#### LoginResponseDto
```json
{
  "accessToken": "string",
  "refreshToken": "string",
  "user": "UserResponseDto",
  "expiresIn": "string"
}
```

#### UserResponseDto
```json
{
  "id": "string",
  "email": "string",
  "name": "string | null"
}
```

#### EnergySessionResponseDto
```json
{
  "id": "string",
  "energyScore": "number",
  "duration": "number",
  "difficultyModifier": "number",
  "activity": "string | null",
  "notes": "string | null",
  "location": "string | null",
  "tags": "string[]",
  "calculatedProductivity": "number",
  "sessionStartTime": "Date",
  "sessionEndTime": "Date | null",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

## Error Responses

### Standard Error Format
```json
{
  "message": "string",
  "error": "string",
  "statusCode": "number"
}
```

### Common HTTP Status Codes
- `200 OK`: Successful request
- `201 Created`: Resource created successfully
- `204 No Content`: Successful request with no response body
- `400 Bad Request`: Invalid request data
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `422 Unprocessable Entity`: Business logic validation failed
- `500 Internal Server Error`: Server error

## Implementation Notes

### Authentication
- All protected endpoints require JWT Bearer token in Authorization header
- JWT tokens are generated with configurable expiration times
- Refresh tokens are stored securely with expiration dates

### Data Validation
- Request DTOs use class-validator decorators for validation
- Validation errors return 400 Bad Request with detailed error messages
- Business logic validation returns 422 Unprocessable Entity

### Performance Optimizations
- Database queries use proper indexing for energy sessions
- CQRS pattern separates read and write operations
- Response DTOs exclude sensitive data using @Expose decorators

### Architecture Compliance
- All endpoints follow Clean Architecture principles
- Controllers delegate to Use Cases for business logic
- Domain entities are never exposed directly to clients
- Proper layer separation is maintained throughout