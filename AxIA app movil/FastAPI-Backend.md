# FastAPI Backend

> **Relevant source files**
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)

## Purpose and Scope

This document covers the FastAPI backend server, which serves as the middle layer in AxIA's three-tier architecture. The backend provides REST API endpoints for authentication and data management, a WebSocket server for real-time chat communication, and acts as a proxy between the Flutter frontend and the n8n workflow engine.

For information about the Flutter frontend that consumes these APIs, see [Flutter Application](/axchisan/AxIA/3.1-flutter-application). For details on the n8n workflow engine that processes AI requests, see [n8n Workflow Engine](/axchisan/AxIA/3.3-n8n-workflow-engine). For authentication implementation details, see [JWT Token Flow](/axchisan/AxIA/4.1-jwt-token-flow).

**Sources:** [ARCHITECTURE.md L1-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L241)

---

## System Role

The FastAPI backend functions as an authentication gateway and message router in the AxIA system. It does not contain business logic for AI processing; instead, it:

1. **Authenticates users** via JWT tokens with 24-hour expiration
2. **Routes messages** from the Flutter app to the n8n workflow engine via HTTP webhooks
3. **Maintains WebSocket connections** for real-time bidirectional communication
4. **Manages database operations** for calendar events, tasks, and message history
5. **Provides health monitoring** endpoints for deployment verification

The backend is designed as a thin proxy layer, delegating AI processing to n8n while handling authentication and connection management.

**Sources:** [ARCHITECTURE.md L2-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L2-L33)

 [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

---

## Architecture Overview

### Backend Technology Stack

```mermaid
flowchart TD

MAIN["main.py<br>FastAPI Application"]
AUTH["JWT Authentication<br>OAuth2PasswordBearer"]
WS["WebSocket Endpoint<br>/ws/{username}"]
REST["REST API Endpoints<br>/token, /tasks, /calendar"]
DB["Database Layer<br>PostgreSQL Client"]
POSTGRES["PostgreSQL Database<br>axia_db"]
N8N["n8n Webhook<br>Unsupported markdown: link"]
FLUTTER["Flutter App<br>HTTPS + WSS"]

FLUTTER --> REST
FLUTTER --> WS
AUTH --> POSTGRES
REST --> POSTGRES
WS --> N8N
REST --> N8N
DB --> POSTGRES

subgraph subGraph2 ["Client Connections"]
    FLUTTER
end

subgraph subGraph1 ["External Dependencies"]
    POSTGRES
    N8N
end

subgraph subGraph0 ["FastAPI Backend"]
    MAIN
    AUTH
    WS
    REST
    DB
    MAIN --> AUTH
    MAIN --> WS
    MAIN --> REST
    MAIN --> DB
end
```

**Diagram: FastAPI Backend Component Architecture**

The backend is deployed using Docker Compose alongside PostgreSQL and n8n services. All components communicate over a Docker network.

**Sources:** [ARCHITECTURE.md L191-L226](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L191-L226)

---

## REST API Endpoints

### Endpoint Reference Table

| Method | Endpoint | Authentication | Purpose |
| --- | --- | --- | --- |
| `POST` | `/token` | None (uses form data) | Generate JWT access token from username/password |
| `POST` | `/send-message` | Bearer token | Send message to n8n (HTTP alternative to WebSocket) |
| `GET` | `/calendar/events` | Bearer token | Retrieve user's calendar events |
| `GET` | `/tasks` | Bearer token | List all tasks for authenticated user |
| `POST` | `/tasks` | Bearer token | Create new task with title, description, due_date |
| `GET` | `/messages/{session_id}` | Bearer token | Retrieve chat message history for a session |
| `GET` | `/health` | None | Server health check for monitoring |

**Sources:** [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

### Authentication Endpoint

```mermaid
sequenceDiagram
  participant Flutter App
  participant POST /token
  participant PostgreSQL

  Flutter App->>POST /token: "username + password
  POST /token->>PostgreSQL: (OAuth2PasswordRequestForm)"
  PostgreSQL-->>POST /token: "SELECT user WHERE username=?"
  POST /token->>POST /token: "User record + hashed_password"
  loop ["Valid Credentials"]
    POST /token->>POST /token: "Verify password hash"
    POST /token-->>Flutter App: "Generate JWT
    POST /token-->>Flutter App: (24h expiration)"
  end
```

**Diagram: JWT Token Generation Flow**

The `/token` endpoint accepts OAuth2 password flow credentials and returns a JWT token that expires in 24 hours (86400 seconds). This token is stored in the device's secure storage and used for all subsequent authenticated requests.

**Sources:** [ARCHITECTURE.md L5-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L5-L15)

 [ARCHITECTURE.md L57-L59](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L57-L59)

 [ARCHITECTURE.md L229-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L229-L234)

### Calendar and Tasks Endpoints

The backend provides CRUD operations for calendar events and tasks. These endpoints require Bearer token authentication in the `Authorization` header:

* **GET /calendar/events**: Returns an array of `CalendarEvent` objects with fields for title, start_time, end_time, description
* **GET /tasks**: Returns an array of `Task` objects with id, title, description, due_date, completed status
* **POST /tasks**: Creates a new task from JSON body `{title, description, due_date}`

**Sources:** [ARCHITECTURE.md L70-L81](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L70-L81)

### Message History Endpoint

The `GET /messages/{session_id}` endpoint retrieves historical chat messages for a given session. This is used when the Flutter app needs to reconstruct conversation history, complementing the local Hive cache.

**Sources:** [ARCHITECTURE.md L83-L85](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L83-L85)

### Health Check Endpoint

```
GET /health
Response: {
  "status": "ok",
  "timestamp": "2024-11-26T15:30:45Z"
}
```

This unauthenticated endpoint is used by load balancers and monitoring systems to verify the backend is responding.

**Sources:** [ARCHITECTURE.md L87-L88](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L87-L88)

---

## WebSocket Server

### Connection Protocol

```mermaid
sequenceDiagram
  participant ChatScreen
  participant (ChatProvider)
  participant WebSocket /ws/{username}
  participant n8n Webhook

  ChatScreen->>WebSocket /ws/{username}: "wss://apiaxia.axchisan.com/ws/duvan?token={JWT}"
  WebSocket /ws/{username}->>WebSocket /ws/{username}: "Validate JWT token"
  loop ["Valid Token"]
    WebSocket /ws/{username}-->>ChatScreen: "WebSocket Connected"
    note over ChatScreen,WebSocket /ws/{username}: "Connection established"
    ChatScreen->>WebSocket /ws/{username}: "{'type': 'text',
    WebSocket /ws/{username}->>n8n Webhook: 'text': 'message',
    n8n Webhook->>n8n Webhook: 'session_id': 'uuid'}"
    n8n Webhook-->>WebSocket /ws/{username}: "POST /webhook/axia
    WebSocket /ws/{username}-->>ChatScreen: {'channel': 'app', 'user': 'duvan', ...}"
    WebSocket /ws/{username}-->>ChatScreen: "Process with AI"
  end
```

**Diagram: WebSocket Connection and Message Flow**

**Sources:** [ARCHITECTURE.md L17-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L17-L33)

 [ARCHITECTURE.md L61-L65](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L61-L65)

### WebSocket Endpoint Implementation

The WebSocket endpoint is located at `/ws/{username}` and accepts JWT authentication via query parameter:

```
wss://apiaxia.axchisan.com/ws/{username}?token={JWT}
```

This design differs from REST endpoints that use Bearer token headers, as WebSocket connections established from browsers cannot set custom headers during the initial handshake.

**Sources:** [ARCHITECTURE.md L22](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L22-L22)

 [ARCHITECTURE.md L61-L64](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L61-L64)

### Message Format Specification

**Incoming Messages (Flutter → Backend):**

```json
{
  "type": "text|audio",
  "text": "user message content",
  "audio_base64": "base64_encoded_audio_data",
  "session_id": "uuid-v4"
}
```

**Outgoing Messages (Backend → Flutter):**

```json
{
  "session_id": "uuid-v4",
  "output": "AI response text",
  "type": "text|audio",
  "timestamp": "2024-11-26T15:30:45Z",
  "audio_base64": "base64_encoded_response_audio"
}
```

For text messages, only `type` and `text` are required. For audio messages, `type: 'audio'` and `audio_base64` are required. The `session_id` enables conversation tracking across multiple message exchanges.

**Sources:** [ARCHITECTURE.md L63-L64](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L63-L64)

 [ARCHITECTURE.md L99-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L99-L117)

### Bidirectional Communication

The WebSocket maintains a persistent connection that enables:

1. **Client-to-server**: User messages sent immediately without HTTP request overhead
2. **Server-to-client**: AI responses pushed in real-time as they become available
3. **Error notifications**: Connection status changes and authentication failures
4. **Reconnection handling**: Client automatically reconnects on disconnection

The ChatProvider in the Flutter app manages this connection lifecycle, including automatic reconnection attempts when network connectivity is restored.

**Sources:** [ARCHITECTURE.md L19-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L19-L33)

 [ARCHITECTURE.md L132-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L132-L145)

---

## n8n Webhook Integration

### Request Forwarding

```mermaid
flowchart TD

WS["WebSocket Handler<br>/ws/{username}"]
REST["REST Handler<br>POST /send-message"]
PAYLOAD["HTTP Request Body<br>{channel: 'app', user, session_id,<br>timestamp, type, text, audio_base64}"]
WEBHOOK["Webhook Node<br>Unsupported markdown: link"]
WORKFLOW["AxIA Workflow<br>AI Processing"]
CALLBACK["POST /app-message<br>Callback Endpoint"]

WS --> PAYLOAD
REST --> PAYLOAD
PAYLOAD --> WEBHOOK
WORKFLOW --> CALLBACK
CALLBACK --> WS

subgraph subGraph3 ["Response Path"]
    CALLBACK
end

subgraph subGraph2 ["n8n Service"]
    WEBHOOK
    WORKFLOW
    WEBHOOK --> WORKFLOW
end

subgraph subGraph1 ["Payload Construction"]
    PAYLOAD
end

subgraph subGraph0 ["FastAPI Backend"]
    WS
    REST
end
```

**Diagram: Message Routing Through n8n**

**Sources:** [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

### Webhook Payload Structure

When the backend receives a message via WebSocket or REST API, it constructs the following payload for n8n:

```json
{
  "channel": "app",
  "session_id": "uuid-v4",
  "user": "duvan",
  "timestamp": "2024-11-26T15:30:45Z",
  "type": "text|audio",
  "text": "user message",
  "audio_base64": "base64_data"
}
```

The `channel: "app"` field identifies this request as originating from the AxIA mobile app, distinguishing it from WhatsApp or Telegram messages processed by the same n8n workflow.

**Sources:** [ARCHITECTURE.md L93-L108](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L93-L108)

### Response Callback

After processing the message, n8n sends the response back to the FastAPI backend via:

```
POST /app-message
{
  "output": "AI response text",
  "type": "text|audio",
  "audio_base64": null,
  "debe_ser_audio": false
}
```

The backend then forwards this response through the WebSocket connection to the Flutter client. The `debe_ser_audio` field controls whether the client should auto-play audio responses.

**Sources:** [ARCHITECTURE.md L109-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L109-L117)

### Environment Configuration

The n8n webhook URL is configured via environment variable:

```
N8N_WEBHOOK_URL=http://n8n:5678/webhook/axia
```

In Docker Compose deployments, `n8n:5678` resolves to the n8n container on the shared network. For local development, this may be `http://localhost:5678/webhook/axia`.

**Sources:** [ARCHITECTURE.md L213-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L213-L219)

---

## Authentication & Authorization

### JWT Token Validation

```mermaid
flowchart TD

REQ["Incoming Request<br>Bearer Token / Query Param"]
EXTRACT["Extract JWT"]
VERIFY["Verify Token<br>Check signature & expiration"]
SECRET["SECRET_KEY<br>from environment"]
DECODE["jwt.decode()<br>Validate signature"]
CHECK["Check expiration<br>24 hours"]
SUCCESS["Request Processed<br>User context available"]
FAIL["401 Unauthorized<br>Token invalid/expired"]

VERIFY --> DECODE
CHECK --> SUCCESS
CHECK --> FAIL

subgraph subGraph2 ["Authorization Result"]
    SUCCESS
    FAIL
end

subgraph subGraph1 ["Token Validation Logic"]
    SECRET
    DECODE
    CHECK
    DECODE --> SECRET
    DECODE --> CHECK
end

subgraph subGraph0 ["Request Processing"]
    REQ
    EXTRACT
    VERIFY
    REQ --> EXTRACT
    EXTRACT --> VERIFY
end
```

**Diagram: JWT Token Validation Flow**

**Sources:** [ARCHITECTURE.md L229-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L229-L234)

 [ARCHITECTURE.md L138-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L138-L145)

### Token Expiration Handling

JWT tokens expire after 24 hours (86400 seconds). When a token expires:

1. Backend returns `401 Unauthorized` for REST requests
2. WebSocket connections are closed with authentication error
3. Flutter app's `AuthProvider` clears the stored token
4. User is redirected to `LoginScreen` to re-authenticate

There is no automatic token refresh mechanism; users must re-login after 24 hours.

**Sources:** [ARCHITECTURE.md L138-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L138-L145)

 [ARCHITECTURE.md L229](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L229-L229)

### OAuth2PasswordBearer Scheme

The backend uses FastAPI's `OAuth2PasswordBearer` dependency for authentication:

```python
# Pseudo-code representation
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.get("/tasks")
async def get_tasks(token: str = Depends(oauth2_scheme)):
    # token is automatically extracted from Authorization header
    user = verify_jwt_token(token)
    # ... endpoint logic
```

This scheme automatically extracts the token from the `Authorization: Bearer {token}` header and makes it available to endpoint handlers.

**Sources:** [ARCHITECTURE.md L57-L81](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L57-L81)

---

## Database Integration

### PostgreSQL Configuration

The backend connects to a PostgreSQL database with the following schema components:

* **users**: User accounts with hashed passwords
* **messages**: Chat message history with session_id, user_id, content, timestamp
* **calendar_events**: Calendar entries with title, start_time, end_time, description
* **tasks**: Task items with title, description, due_date, completed status

**Connection String:**

```
DATABASE_URL=postgresql://user:pass@postgres:5432/axia_db
```

In Docker Compose deployments, `postgres:5432` resolves to the PostgreSQL container.

**Sources:** [ARCHITECTURE.md L217](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L217-L217)

### Database Operations

The backend performs standard CRUD operations:

| Operation | Endpoint | SQL Operation |
| --- | --- | --- |
| Authenticate | `POST /token` | `SELECT * FROM users WHERE username = ?` |
| List tasks | `GET /tasks` | `SELECT * FROM tasks WHERE user_id = ?` |
| Create task | `POST /tasks` | `INSERT INTO tasks (user_id, title, ...) VALUES (?, ?, ...)` |
| Get events | `GET /calendar/events` | `SELECT * FROM calendar_events WHERE user_id = ?` |
| Store message | WebSocket | `INSERT INTO messages (session_id, content, ...) VALUES (?, ?, ...)` |
| Get history | `GET /messages/{session_id}` | `SELECT * FROM messages WHERE session_id = ?` |

All queries are scoped to the authenticated user's ID to ensure data isolation.

**Sources:** [ARCHITECTURE.md L70-L85](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L70-L85)

---

## Error Handling

### HTTP Error Responses

The backend returns standard HTTP error codes:

| Status Code | Scenario | Response Body |
| --- | --- | --- |
| `401` | Invalid/expired JWT token | `{"detail": "Could not validate credentials"}` |
| `401` | Invalid username/password | `{"detail": "Incorrect username or password"}` |
| `400` | Malformed request body | `{"detail": "Validation error"}` |
| `404` | Resource not found | `{"detail": "Not found"}` |
| `500` | Internal server error | `{"detail": "Internal server error"}` |

**Sources:** [ARCHITECTURE.md L132-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L132-L145)

### WebSocket Error Handling

WebSocket connections handle errors differently:

1. **Authentication failure**: Connection immediately closed with 401 status
2. **Message processing error**: Error sent through WebSocket as JSON message
3. **Connection loss**: Client receives `onError` callback, displays red banner, offers reconnection button
4. **n8n webhook timeout**: Backend waits for configurable timeout, then returns error

The Flutter app's `ChatProvider` manages reconnection logic, automatically attempting to re-establish the WebSocket connection when network connectivity is restored.

**Sources:** [ARCHITECTURE.md L132-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L132-L145)

---

## Configuration & Deployment

### Environment Variables

Required environment variables for the FastAPI backend:

| Variable | Purpose | Example |
| --- | --- | --- |
| `SECRET_KEY` | JWT token signing key | `your-secret-key-here` |
| `N8N_WEBHOOK_URL` | n8n webhook endpoint | `http://n8n:5678/webhook/axia` |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@postgres:5432/axia_db` |
| `ALLOWED_ORIGINS` | CORS allowed origins | `["https://app.axchisan.com"]` |

**Sources:** [ARCHITECTURE.md L213-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L213-L219)

### Docker Compose Deployment

The backend is deployed using Docker Compose alongside its dependencies:

```python
# Pseudo-structure from docker-compose.yml
services:
  fastapi:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - SECRET_KEY=${SECRET_KEY}
      - N8N_WEBHOOK_URL=http://n8n:5678/webhook/axia
      - DATABASE_URL=postgresql://user:pass@postgres:5432/axia_db
    depends_on:
      - postgres
      - n8n
      
  postgres:
    image: postgres:14
    volumes:
      - postgres_data:/var/lib/postgresql/data
      
  n8n:
    image: n8nio/n8n
    ports:
      - "5678:5678"
```

**Deployment command:**

```
docker-compose up -d
```

**Sources:** [ARCHITECTURE.md L199-L210](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L210)

### CORS Configuration

The backend enables CORS to allow the Flutter web build to communicate with the API. In production, CORS is configured with specific allowed origins rather than wildcard `*`:

```markdown
# Pseudo-code
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://app.axchisan.com"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

**Sources:** [ARCHITECTURE.md L232](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L232-L232)

---

## Performance Considerations

### WebSocket Connection Pooling

The backend maintains persistent WebSocket connections for each authenticated user. This design provides:

* **Lower latency**: No HTTP request/response overhead for each message
* **Resource efficiency**: Single connection per user rather than repeated HTTP requests
* **Real-time delivery**: Responses pushed immediately as they're available from n8n

The connection is singleton per user session, preventing connection proliferation.

**Sources:** [ARCHITECTURE.md L237-L240](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L237-L240)

### Message Caching Strategy

While the backend stores message history in PostgreSQL, the Flutter app maintains a local Hive cache to minimize database queries. The backend serves as the source of truth for:

* Message synchronization across devices
* Historical message retrieval when cache is cleared
* Session restoration after app reinstall

**Sources:** [ARCHITECTURE.md L238](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L238-L238)

### Request Timeout Configuration

The backend configures timeouts for n8n webhook calls to prevent hanging requests:

* **Connection timeout**: 5 seconds to establish connection
* **Read timeout**: 30 seconds to receive response
* **Total timeout**: 60 seconds maximum per request

If n8n doesn't respond within the timeout period, the backend returns an error to the client.

**Sources:** [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

---

## System Integration Points

```mermaid
flowchart TD

LOGIN["AuthService.login()<br>→ POST /token"]
TASKS["TasksProvider.fetchTasks()<br>→ GET /tasks"]
EVENTS["CalendarProvider.fetchEvents()<br>→ GET /calendar/events"]
CHAT["ChatProvider.initializeWebSocket()<br>→ WebSocket /ws/{user}"]
TOKEN["POST /token<br>Generate JWT"]
TASK_EP["GET /tasks<br>Query PostgreSQL"]
EVENT_EP["GET /calendar/events<br>Query PostgreSQL"]
WS_EP["WebSocket /ws/{username}<br>Forward to n8n"]
PG["PostgreSQL Database"]
N8N_WH["n8n Webhook<br>POST /webhook/axia"]

LOGIN --> TOKEN
TASKS --> TASK_EP
EVENTS --> EVENT_EP
CHAT --> WS_EP
TOKEN --> PG
TASK_EP --> PG
EVENT_EP --> PG
WS_EP --> N8N_WH

subgraph subGraph2 ["Backend Dependencies"]
    PG
    N8N_WH
end

subgraph subGraph1 ["FastAPI Backend main.py"]
    TOKEN
    TASK_EP
    EVENT_EP
    WS_EP
end

subgraph subGraph0 ["Flutter App Endpoints"]
    LOGIN
    TASKS
    EVENTS
    CHAT
end
```

**Diagram: Code-Level Integration Between Flutter and Backend**

This diagram maps Flutter service methods to specific FastAPI endpoints, showing the code-level integration points between the frontend and backend layers.

**Sources:** [ARCHITECTURE.md L149-L197](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L149-L197)

---

## API Base URL Configuration

The Flutter app configures the backend API URL in `lib/config/api_config.dart`:

```javascript
// Referenced code structure
static const String baseUrl = 'https://apiaxia.axchisan.com';
static const String wsUrl = 'wss://apiaxia.axchisan.com/ws';
```

These URLs point to the production FastAPI deployment. For local development, these values would be:

* `http://localhost:8000`
* `ws://localhost:8000/ws`

**Sources:** [ARCHITECTURE.md L221-L225](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L221-L225)