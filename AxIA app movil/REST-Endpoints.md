# REST Endpoints

> **Relevant source files**
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)

## Purpose and Scope

This document provides complete reference documentation for all REST API endpoints exposed by the FastAPI backend. These endpoints handle authentication, message transmission, task management, calendar integration, and system health monitoring.

For real-time bidirectional communication via WebSocket, see [WebSocket Protocol](/axchisan/AxIA/8.2-websocket-protocol). For detailed message payload schemas, see [Message Data Structures](/axchisan/AxIA/8.3-message-data-structures).

**Sources:** [ARCHITECTURE.md L1-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L241)

## Base Configuration

The FastAPI backend serves REST endpoints at the base URL defined in the Flutter application configuration:

```
Base URL: https://apiaxia.axchisan.com
```

All authenticated endpoints require a JWT Bearer token in the `Authorization` header. The token is obtained via the `/token` endpoint and has a 24-hour lifespan.

**API Configuration Constants:**

* `ApiConfig.baseUrl` - Base URL for all HTTP requests
* `ApiConfig.wsUrl` - WebSocket URL (separate from REST endpoints)

**Sources:** [ARCHITECTURE.md L221-L225](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L221-L225)

## Endpoint Overview

The following diagram shows the complete REST API surface organized by functional domain:

**Diagram: REST API Endpoint Categories**

```

```

**Sources:** [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

## Authentication Endpoints

### POST /token

Authenticates a user and returns a JWT access token for subsequent API requests.

**Authentication:** None (public endpoint)

**Request Format:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `username` | string | Yes | User's login username |
| `password` | string | Yes | User's password (plain text, transmitted over HTTPS) |

**Content-Type:** `application/x-www-form-urlencoded`

**Response Format:**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 86400
}
```

| Field | Type | Description |
| --- | --- | --- |
| `access_token` | string | JWT token for authentication |
| `token_type` | string | Always "bearer" |
| `expires_in` | integer | Token validity period in seconds (24 hours = 86400) |

**Status Codes:**

* `200 OK` - Authentication successful
* `401 Unauthorized` - Invalid credentials
* `422 Unprocessable Entity` - Missing or malformed request data

**Usage in Flutter:**

* Service: `AuthService.login(username, password)`
* Provider: `AuthProvider.login(username, password)`
* Storage: Token saved to `flutter_secure_storage` in `token.json` file

**Sources:** [ARCHITECTURE.md L57-L59](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L57-L59)

 [ARCHITECTURE.md L5-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L5-L15)

 [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

**Diagram: Token Authentication Flow**

```mermaid
sequenceDiagram
  participant Flutter App
  participant AuthService
  participant flutter_secure_storage
  participant POST /token

  Flutter App->>AuthService: "login(username, password)"
  AuthService->>POST /token: "POST username, password"
  POST /token->>AuthService: "{ access_token, token_type, expires_in }"
  AuthService->>flutter_secure_storage: "write('token.json', token_data)"
  flutter_secure_storage->>flutter_secure_storage: "Store in Keychain/Keystore"
  AuthService->>Flutter App: "Success"
  Flutter App->>Flutter App: "Navigate to MainNavigation"
```

**Sources:** [ARCHITECTURE.md L5-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L5-L15)

 [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

## Message Endpoints

### POST /send-message

Sends a message to the AxIA assistant via HTTP POST. This is an alternative to the WebSocket connection for one-off message transmission.

**Authentication:** Bearer token required

**Request Format:**

```json
{
  "text": "What's the weather today?",
  "audio_base64": null,
  "type": "text"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `text` | string | Conditional | Message text (required if `type` is "text") |
| `audio_base64` | string | Conditional | Base64-encoded audio data (required if `type` is "audio") |
| `type` | string | Yes | Message type: "text" or "audio" |

**Response Format:**

```json
{
  "session_id": "550e8400-e29b-41d4-a716-446655440000",
  "output": "The weather today is sunny with a high of 75°F.",
  "type": "text",
  "timestamp": "2024-11-26T10:30:00Z"
}
```

| Field | Type | Description |
| --- | --- | --- |
| `session_id` | string | UUID for this conversation session |
| `output` | string | AxIA's response text |
| `type` | string | Response type: "text" or "audio" |
| `timestamp` | string | ISO 8601 timestamp of response |

**Status Codes:**

* `200 OK` - Message processed successfully
* `401 Unauthorized` - Missing or invalid token
* `422 Unprocessable Entity` - Invalid message format
* `500 Internal Server Error` - Backend or n8n processing failure

**Sources:** [ARCHITECTURE.md L66-L68](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L66-L68)

### GET /messages/{session_id}

Retrieves message history for a specific conversation session.

**Authentication:** Bearer token required

**Path Parameters:**

| Parameter | Type | Description |
| --- | --- | --- |
| `session_id` | string | UUID of the conversation session |

**Response Format:**

```json
[
  {
    "id": "msg_001",
    "session_id": "550e8400-e29b-41d4-a716-446655440000",
    "text": "What's the weather?",
    "type": "text",
    "is_user": true,
    "timestamp": "2024-11-26T10:30:00Z"
  },
  {
    "id": "msg_002",
    "session_id": "550e8400-e29b-41d4-a716-446655440000",
    "text": "The weather is sunny.",
    "type": "text",
    "is_user": false,
    "timestamp": "2024-11-26T10:30:05Z"
  }
]
```

**Status Codes:**

* `200 OK` - Messages retrieved successfully
* `401 Unauthorized` - Missing or invalid token
* `404 Not Found` - Session ID does not exist

**Sources:** [ARCHITECTURE.md L83-L85](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L83-L85)

### POST /app-message

Internal endpoint used by n8n workflows to send processed responses back to the application. This endpoint is called by the n8n workflow after AI processing completes.

**Authentication:** Internal endpoint (typically no auth, or API key)

**Request Format:**

```json
{
  "session_id": "550e8400-e29b-41d4-a716-446655440000",
  "output": "The weather today is sunny.",
  "type": "text",
  "debe_ser_audio": false,
  "audio_base64": null
}
```

| Field | Type | Description |
| --- | --- | --- |
| `session_id` | string | UUID matching the original request |
| `output` | string | AI-generated response text |
| `type` | string | Response type: "text" or "audio" |
| `debe_ser_audio` | boolean | Flag indicating if client should play audio |
| `audio_base64` | string | Base64-encoded audio (if `type` is "audio") |

**Response Format:**

```json
{
  "status": "delivered",
  "timestamp": "2024-11-26T10:30:05Z"
}
```

**Purpose:** This endpoint bridges the n8n workflow back to active WebSocket connections, allowing the FastAPI backend to forward responses to connected clients.

**Sources:** [ARCHITECTURE.md L30-L32](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L30-L32)

**Diagram: Message Flow via HTTP and n8n**

```mermaid
sequenceDiagram
  participant Flutter ChatProvider
  participant POST /send-message
  participant n8n Webhook
  participant /webhook/axia
  participant AI Processing
  participant POST /app-message
  participant WebSocket Connection

  Flutter ChatProvider->>POST /send-message: "POST { text, type, audio_base64 }"
  POST /send-message->>n8n Webhook: "Forward message payload"
  n8n Webhook->>AI Processing: "Process with OpenAI/Anthropic"
  AI Processing->>n8n Webhook: "Return response"
  n8n Webhook->>POST /app-message: "POST { output, type, audio_base64 }"
  POST /app-message->>WebSocket Connection: "Broadcast to session_id"
  WebSocket Connection->>Flutter ChatProvider: "Deliver response"
  Flutter ChatProvider->>Flutter ChatProvider: "Update ChatProvider state"
```

**Sources:** [ARCHITECTURE.md L17-L45](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L17-L45)

 [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

## Calendar Endpoints

### GET /calendar/events

Retrieves upcoming calendar events for the authenticated user.

**Authentication:** Bearer token required

**Query Parameters:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `start_date` | string | No | ISO 8601 date for range start (default: today) |
| `end_date` | string | No | ISO 8601 date for range end (default: today + 30 days) |

**Response Format:**

```json
[
  {
    "id": "evt_001",
    "title": "Team Meeting",
    "description": "Weekly sync with the team",
    "start_time": "2024-11-26T14:00:00Z",
    "end_time": "2024-11-26T15:00:00Z",
    "location": "Conference Room A",
    "attendees": ["john@example.com", "jane@example.com"]
  }
]
```

**Status Codes:**

* `200 OK` - Events retrieved successfully
* `401 Unauthorized` - Missing or invalid token

**Usage in Flutter:**

* Provider: `CalendarProvider.fetchEvents()`
* Service: `ApiService.get('/calendar/events')`

**Sources:** [ARCHITECTURE.md L70-L72](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L70-L72)

## Task Management Endpoints

### GET /tasks

Retrieves all tasks for the authenticated user.

**Authentication:** Bearer token required

**Query Parameters:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `status` | string | No | Filter by status: "pending", "completed", "all" (default: "all") |
| `priority` | string | No | Filter by priority: "high", "medium", "low" |

**Response Format:**

```json
[
  {
    "id": "task_001",
    "title": "Review pull request",
    "description": "Review and merge feature branch",
    "due_date": "2024-11-27T17:00:00Z",
    "priority": "high",
    "status": "pending",
    "created_at": "2024-11-26T09:00:00Z",
    "updated_at": "2024-11-26T09:00:00Z"
  }
]
```

**Status Codes:**

* `200 OK` - Tasks retrieved successfully
* `401 Unauthorized` - Missing or invalid token

**Usage in Flutter:**

* Provider: `TasksProvider.fetchTasks()`
* Service: `ApiService.get('/tasks')`

**Sources:** [ARCHITECTURE.md L74-L76](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L74-L76)

### POST /tasks

Creates a new task for the authenticated user.

**Authentication:** Bearer token required

**Request Format:**

```json
{
  "title": "Complete documentation",
  "description": "Write API documentation for all endpoints",
  "due_date": "2024-11-30T17:00:00Z",
  "priority": "medium"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Task title |
| `description` | string | No | Detailed task description |
| `due_date` | string | No | ISO 8601 timestamp for deadline |
| `priority` | string | No | Priority level: "high", "medium", "low" (default: "medium") |

**Response Format:**

```json
{
  "id": "task_002",
  "title": "Complete documentation",
  "description": "Write API documentation for all endpoints",
  "due_date": "2024-11-30T17:00:00Z",
  "priority": "medium",
  "status": "pending",
  "created_at": "2024-11-26T10:45:00Z",
  "updated_at": "2024-11-26T10:45:00Z"
}
```

**Status Codes:**

* `201 Created` - Task created successfully
* `401 Unauthorized` - Missing or invalid token
* `422 Unprocessable Entity` - Missing required fields or invalid data

**Usage in Flutter:**

* Provider: `TasksProvider.createTask(title, description, dueDate)`
* Service: `ApiService.post('/tasks', body)`

**Sources:** [ARCHITECTURE.md L77-L81](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L77-L81)

## System Health Endpoints

### GET /health

Health check endpoint for monitoring backend availability.

**Authentication:** None (public endpoint)

**Response Format:**

```json
{
  "status": "ok",
  "timestamp": "2024-11-26T10:45:30Z",
  "version": "1.0.0"
}
```

| Field | Type | Description |
| --- | --- | --- |
| `status` | string | Service status: "ok", "degraded", "down" |
| `timestamp` | string | Current server timestamp (ISO 8601) |
| `version` | string | API version identifier |

**Status Codes:**

* `200 OK` - Backend is healthy
* `503 Service Unavailable` - Backend is experiencing issues

**Purpose:** Used for uptime monitoring, load balancer health checks, and deployment verification.

**Sources:** [ARCHITECTURE.md L87-L88](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L87-L88)

## Authentication Header Format

All authenticated endpoints require the JWT token in the `Authorization` header:

```yaml
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Implementation in Flutter:**

The `ApiService` class automatically injects the Bearer token from `flutter_secure_storage` into all requests:

* Token retrieval: `SecureStorage.read('token.json')`
* Header injection: `headers['Authorization'] = 'Bearer $token'`
* Expiration handling: `401` response triggers `AuthProvider.logout()` and redirect to `LoginScreen`

**Sources:** [ARCHITECTURE.md L228-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L228-L234)

**Diagram: Request Authentication Flow**

```mermaid
flowchart TD

APP["ApiService.request()"]
STORAGE["flutter_secure_storage"]
TOKEN_FILE["token.json"]
READ["Read Token"]
INJECT["Inject Bearer Header"]
HTTP["HTTP Request"]
VALIDATE["JWT Validation"]
ENDPOINT["Protected Endpoint"]
RESPONSE["Response / 401"]

APP --> READ
READ --> STORAGE
TOKEN_FILE --> INJECT
HTTP --> VALIDATE
RESPONSE --> APP

subgraph subGraph2 ["FastAPI Backend"]
    VALIDATE
    ENDPOINT
    RESPONSE
    VALIDATE --> ENDPOINT
    VALIDATE --> RESPONSE
    ENDPOINT --> RESPONSE
end

subgraph subGraph1 ["Request Processing"]
    READ
    INJECT
    HTTP
    INJECT --> HTTP
end

subgraph subGraph0 ["Flutter Client"]
    APP
    STORAGE
    TOKEN_FILE
    STORAGE --> TOKEN_FILE
end
```

**Sources:** [ARCHITECTURE.md L119-L142](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L142)

## Error Handling

All endpoints follow a consistent error response format:

```json
{
  "detail": "Error description",
  "error_code": "AUTH_TOKEN_EXPIRED",
  "timestamp": "2024-11-26T10:45:30Z"
}
```

**Common HTTP Status Codes:**

| Code | Meaning | Common Causes |
| --- | --- | --- |
| `200` | OK | Request successful |
| `201` | Created | Resource created successfully |
| `400` | Bad Request | Malformed request data |
| `401` | Unauthorized | Missing, invalid, or expired token |
| `403` | Forbidden | Valid token but insufficient permissions |
| `404` | Not Found | Resource does not exist |
| `422` | Unprocessable Entity | Validation errors in request data |
| `500` | Internal Server Error | Backend processing failure |
| `503` | Service Unavailable | Backend or dependency unavailable |

**Token Expiration Handling:**

When a `401 Unauthorized` response is received:

1. `ApiService` throws an exception
2. `AuthProvider` catches the exception
3. Token is cleared from `flutter_secure_storage`
4. User is redirected to `LoginScreen`
5. User must re-authenticate to obtain a fresh token

**Sources:** [ARCHITECTURE.md L131-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L131-L145)

## CORS Configuration

The FastAPI backend configures CORS headers to allow requests from the Flutter web application:

* **Allowed Origins:** Configurable via environment variables (restrictive in production)
* **Allowed Methods:** `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`
* **Allowed Headers:** `Authorization`, `Content-Type`, `Accept`
* **Credentials:** Allowed for cross-origin authenticated requests

**Sources:** [ARCHITECTURE.md L232](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L232-L232)

## Rate Limiting

While not explicitly documented in the current architecture, production deployments should implement rate limiting at the API gateway or FastAPI middleware level to prevent abuse:

* Authentication endpoints: 5 requests per minute per IP
* Message endpoints: 20 requests per minute per user
* Other endpoints: 60 requests per minute per user

## Related Documentation

* For real-time WebSocket communication, see [WebSocket Protocol](/axchisan/AxIA/8.2-websocket-protocol)
* For detailed message payload schemas, see [Message Data Structures](/axchisan/AxIA/8.3-message-data-structures)
* For JWT token management, see [JWT Token Flow](/axchisan/AxIA/4.1-jwt-token-flow)
* For secure credential storage, see [Secure Credential Storage](/axchisan/AxIA/4.2-secure-credential-storage)
* For n8n integration details, see [n8n Integration](/axchisan/AxIA/7-n8n-integration)

**Sources:** [ARCHITECTURE.md L1-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L241)