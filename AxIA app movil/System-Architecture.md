# System Architecture

> **Relevant source files**
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)
> * [README.md](https://github.com/axchisan/AxIA/blob/1fe26c44/README.md)

## Purpose and Scope

This document provides a comprehensive overview of AxIA's three-tier system architecture, detailing how the Flutter mobile client, FastAPI backend server, and n8n workflow engine interact to deliver a real-time AI assistant application. This page focuses on the high-level structural relationships and communication patterns between these components.

For implementation details of individual tiers, see:

* Flutter application structure and state management: [Flutter Application](/axchisan/AxIA/3.1-flutter-application)
* Backend API endpoints and database integration: [FastAPI Backend](/axchisan/AxIA/3.2-fastapi-backend)
* AI workflow automation and channel routing: [n8n Workflow Engine](/axchisan/AxIA/3.3-n8n-workflow-engine)

For authentication mechanisms, see [Authentication & Security](/axchisan/AxIA/4-authentication-and-security). For real-time communication protocols, see [Real-time Chat System](/axchisan/AxIA/5-real-time-chat-system).

---

## Architectural Overview

AxIA implements a three-tier client-server architecture where each tier has distinct responsibilities:

**Three-Tier Architecture Diagram**

```mermaid
flowchart TD

Flutter["Flutter Mobile App<br>lib/main.dart"]
UI["UI Screens<br>lib/screens/*"]
Providers["State Management<br>lib/providers/*"]
Services["Services<br>lib/services/*"]
FastAPI["FastAPI Server<br>backend/main.py"]
RestAPI["REST API Endpoints<br>/token, /tasks, /calendar"]
WebSocket["WebSocket Server<br>/ws/{username}"]
Database["PostgreSQL<br>Database"]
n8n["n8n Workflow Engine"]
Webhook["Webhook Endpoint<br>/webhook/axia"]
AILogic["AI Processing Logic"]
External["External APIs<br>OpenAI, ElevenLabs, Whisper"]

Services --> FastAPI
WebSocket --> Webhook
AILogic --> FastAPI

subgraph Tier3 ["Tier 3: Integration Layer"]
    n8n
    Webhook
    AILogic
    External
    Webhook --> AILogic
    AILogic --> External
end

subgraph Tier2 ["Tier 2: Application Layer"]
    FastAPI
    RestAPI
    WebSocket
    Database
    FastAPI --> RestAPI
    FastAPI --> WebSocket
    FastAPI --> Database
end

subgraph Tier1 ["Tier 1: Presentation Layer"]
    Flutter
    UI
    Providers
    Services
    Flutter --> Providers
    Providers --> Services
end
```

**Sources:** [ARCHITECTURE.md L1-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L241)

 [README.md L52-L78](https://github.com/axchisan/AxIA/blob/1fe26c44/README.md#L52-L78)

| Tier | Responsibility | Primary Technology | Key Components |
| --- | --- | --- | --- |
| **Presentation** | User interface, state management, local data persistence | Flutter 3.x, Provider 6.x | Screens, Providers, Services, Hive cache, Secure Storage |
| **Application** | Authentication, API routing, WebSocket management, data persistence | FastAPI, PostgreSQL | REST endpoints, WebSocket server, JWT validation |
| **Integration** | AI processing, workflow orchestration, external service integration | n8n, Python | Workflow engine, webhook handlers, AI model connectors |

---

## Technology Stack

The system utilizes modern, production-ready technologies across all tiers:

### Presentation Layer (Flutter)

```yaml
Framework: Flutter 3.4.0+
Language: Dart 3.2.0+
State Management: provider 6.2.1
Local Storage: hive 2.2.3
Secure Storage: flutter_secure_storage 9.0.0
HTTP Client: dio 5.4.2
Audio Recording: record 5.0.4
Audio Playback: just_audio 0.9.36
WebSocket: web_socket_channel 2.4.0
```

**Sources:** [README.md L68-L78](https://github.com/axchisan/AxIA/blob/1fe26c44/README.md#L68-L78)

### Application Layer (Backend)

```yaml
Framework: FastAPI (Python)
Database: PostgreSQL
Authentication: JWT (JSON Web Tokens)
WebSocket: Native FastAPI WebSocket support
Deployment: Docker Compose
```

**Sources:** [ARCHITECTURE.md L199-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L219)

 [README.md L63-L67](https://github.com/axchisan/AxIA/blob/1fe26c44/README.md#L63-L67)

### Integration Layer (n8n)

```yaml
Platform: n8n Workflow Automation
AI Services: OpenAI API, Anthropic Claude
TTS: ElevenLabs
STT: Whisper
Message Format: JSON
```

**Sources:** [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

---

## Component Architecture with Code Entities

This diagram maps high-level system components to their concrete implementations in the codebase:

**Component-to-Code Mapping Diagram**

```mermaid
flowchart TD

Main["main.dart<br>App Entry Point"]
SplashScreen["auth/splash_screen.dart<br>Initial Route"]
LoginScreen["auth/login_screen.dart<br>Authentication UI"]
MainNav["main_navigation.dart<br>BottomNavigationBar"]
ChatScreen["chat/chat_screen.dart<br>WebSocket UI"]
Dashboard["dashboard/*<br>Dashboard UI"]
AuthProvider["auth_provider.dart<br>checkAuthentication()<br>login()"]
ChatProvider["chat_provider.dart<br>initializeWebSocket()<br>sendMessage()<br>sendAudioMessage()"]
CalendarProvider["calendar_provider.dart"]
TasksProvider["tasks_provider.dart"]
AuthService["auth_service.dart<br>login()<br>POST /token"]
ApiService["api_service.dart<br>HTTP Client<br>Bearer Token Header"]
AudioService["audio_service.dart<br>startRecording()<br>stopRecording()<br>playAudio()"]
SecureStorage["flutter_secure_storage<br>credentials.json<br>token.json"]
HiveDB["Hive Database<br>Message Cache"]
ApiConfig["api_config.dart<br>baseUrl<br>wsUrl"]
MainPy["main.py<br>FastAPI()"]
TokenEP["POST /token<br>create_access_token()"]
WSEP["WebSocket /ws/{username}<br>websocket_endpoint()"]
SendMsg["POST /send-message"]
CalendarEP["GET /calendar/events"]
TasksEP["GET /tasks<br>POST /tasks"]
AppMsg["POST /app-message<br>Receive from n8n"]
DB["PostgreSQL<br>Users, Messages<br>Tasks, Events"]
WebhookNode["Webhook Node<br>POST /webhook/axia"]
SwitchNode["Switch Node<br>channel detection"]
HTTPNode["HTTP Request Node<br>POST /app-message"]

AuthService --> TokenEP
ChatProvider --> WSEP
WSEP --> WebhookNode
HTTPNode --> AppMsg

subgraph N8N ["n8n Workflows"]
    WebhookNode
    SwitchNode
    HTTPNode
    WebhookNode --> SwitchNode
    SwitchNode --> HTTPNode
end

subgraph Backend ["Backend - backend/"]
    MainPy
    DB
    TokenEP --> DB
    CalendarEP --> DB
    TasksEP --> DB

subgraph Endpoints ["API Endpoints"]
    TokenEP
    WSEP
    SendMsg
    CalendarEP
    TasksEP
    AppMsg
    AppMsg --> WSEP
end
end

subgraph Flutter ["Flutter App - lib/"]
    Main
    Main --> SplashScreen
    SplashScreen --> AuthProvider
    AuthProvider --> AuthService
    AuthService --> ApiConfig
    AuthProvider --> SecureStorage
    LoginScreen --> AuthProvider
    ChatScreen --> ChatProvider
    ChatProvider --> AudioService
    ChatProvider --> ApiConfig
    ChatProvider --> HiveDB

subgraph Config ["config/"]
    ApiConfig
end

subgraph Storage ["Storage"]
    SecureStorage
    HiveDB
end

subgraph Services ["services/"]
    AuthService
    ApiService
    AudioService
end

subgraph Providers ["providers/"]
    AuthProvider
    ChatProvider
    CalendarProvider
    TasksProvider
end

subgraph Screens ["screens/"]
    SplashScreen
    LoginScreen
    MainNav
    ChatScreen
    Dashboard
    MainNav --> ChatScreen
end
end
```

**Sources:** [ARCHITECTURE.md L148-L196](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L148-L196)

 [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

---

## Data Flow Patterns

### Authentication Flow with Code References

```mermaid
sequenceDiagram
  participant User
  participant SplashScreen
  participant splash_screen.dart
  participant AuthProvider
  participant auth_provider.dart
  participant flutter_secure_storage
  participant token.json
  participant AuthService
  participant auth_service.dart
  participant FastAPI
  participant POST /token
  participant MainNavigation
  participant main_navigation.dart
  participant LoginScreen

  User->>SplashScreen: "App Launch"
  SplashScreen->>AuthProvider: "checkAuthentication()"
  AuthProvider->>flutter_secure_storage: "read('token')"
  loop [Token exists and valid]
    flutter_secure_storage-->>AuthProvider: "JWT Token"
    AuthProvider-->>SplashScreen: "Authenticated"
    SplashScreen->>MainNavigation: "Navigate"
    AuthProvider-->>SplashScreen: "Not authenticated"
    SplashScreen->>LoginScreen: "Navigate"
    User->>LoginScreen: "Enter credentials"
    LoginScreen->>AuthProvider: "login(username, password)"
    AuthProvider->>AuthService: "login()"
    AuthService->>FastAPI: "POST /token
    FastAPI-->>AuthService: {username, password}"
    AuthService-->>AuthProvider: "{access_token, expires_in}"
    AuthProvider->>flutter_secure_storage: "JWT Token"
    AuthProvider-->>LoginScreen: "write('token', jwt)"
    LoginScreen->>MainNavigation: "Success"
  end
```

**Sources:** [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

 [ARCHITECTURE.md L3-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L3-L15)

### Real-time Message Flow with Code References

```mermaid
sequenceDiagram
  participant User
  participant ChatScreen
  participant chat_screen.dart
  participant ChatProvider
  participant sendMessage()
  participant sendAudioMessage()
  participant AudioService
  participant stopRecording()
  participant WebSocketChannel
  participant /ws/{username}?token=JWT
  participant FastAPI
  participant websocket_endpoint()
  participant n8n
  participant POST /webhook/axia
  participant POST /app-message

  User->>ChatScreen: "Type message / Record audio"
  loop [Text Message]
    ChatScreen->>ChatProvider: "sendMessage(text)"
    ChatProvider->>WebSocketChannel: "sink.add(json)
    ChatScreen->>AudioService: {type: 'text', text: '...'}"
    User->>ChatScreen: "startRecording()"
    ChatScreen->>AudioService: "Release button"
    AudioService-->>ChatProvider: "stopRecording()"
    ChatProvider->>WebSocketChannel: "base64AudioData"
  end
  WebSocketChannel->>FastAPI: "sink.add(json)
  FastAPI->>n8n: {type: 'audio', audio_base64: '...'}"
  n8n->>n8n: "Receive WebSocket message"
  n8n->>POST /app-message: "POST webhook
  POST /app-message->>FastAPI: {session_id, user, type, text/audio}"
  FastAPI->>WebSocketChannel: "AI Processing
  WebSocketChannel->>ChatProvider: OpenAI, ElevenLabs, Whisper"
  ChatProvider->>ChatScreen: "POST /app-message
  ChatScreen->>User: {output, type, audio_base64}"
```

**Sources:** [ARCHITECTURE.md L35-L53](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L35-L53)

 [ARCHITECTURE.md L17-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L17-L33)

---

## Communication Protocols

### HTTP REST API

The FastAPI backend exposes the following REST endpoints for non-realtime operations:

| Endpoint | Method | Purpose | Request Body | Response |
| --- | --- | --- | --- | --- |
| `/token` | POST | Authenticate user, obtain JWT | `{username, password}` | `{access_token, token_type, expires_in}` |
| `/calendar/events` | GET | Retrieve calendar events | N/A | `[CalendarEvent]` |
| `/tasks` | GET | Retrieve task list | N/A | `[Task]` |
| `/tasks` | POST | Create new task | `{title, description, due_date}` | `Task` |
| `/messages/{session_id}` | GET | Retrieve message history | N/A | `[Message]` |
| `/health` | GET | Health check | N/A | `{status, timestamp}` |
| `/app-message` | POST | Receive n8n response | `{output, type, audio_base64}` | `{status}` |

**Implementation:** [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

All endpoints except `/token` and `/health` require JWT authentication via `Authorization: Bearer <token>` header, handled by `ApiService` in [lib/services/api_service.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/services/api_service.dart)

**Sources:** [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

### WebSocket Protocol

Real-time chat communication uses WebSocket connections:

**Connection:**

```
wss://apiaxia.axchisan.com/ws/{username}?token={JWT}
```

**Client → Server Message Format:**

```json
{
  "type": "text|audio",
  "text": "message content",
  "audio_base64": "base64_encoded_audio",
  "session_id": "uuid-v4"
}
```

**Server → Client Message Format:**

```
{
  "session_id": "uuid-v4",
  "output": "response text",
  "type": "text|audio",
  "timestamp": "ISO8601",
  "audio_base64": "base64_encoded_audio",
  "debe_ser_audio": true|false
}
```

**Implementation:** WebSocket connection established in `ChatProvider.initializeWebSocket()` [lib/providers/chat_provider.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/providers/chat_provider.dart)

 and server endpoint in `websocket_endpoint()` [backend/main.py](https://github.com/axchisan/AxIA/blob/1fe26c44/backend/main.py)

**Sources:** [ARCHITECTURE.md L17-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L17-L33)

 [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

### n8n Webhook Integration

The n8n workflow receives messages via HTTP POST webhook and returns responses through the FastAPI `/app-message` endpoint:

**Webhook Request (FastAPI → n8n):**

```
POST http://n8n:5678/webhook/axia
Content-Type: application/json

{
  "session_id": "uuid",
  "user": "username",
  "timestamp": "ISO8601",
  "type": "text|audio",
  "text": "message content",
  "audio_base64": "base64_audio_data",
  "channel": "app"
}
```

**Webhook Response (n8n → FastAPI):**

```
POST /app-message
Content-Type: application/json

{
  "output": "AI response text",
  "type": "text|audio",
  "audio_base64": "base64_audio_data",
  "debe_ser_audio": true|false
}
```

The `channel: "app"` field distinguishes AxIA mobile app messages from other sources (WhatsApp, Telegram) processed by the same n8n workflow.

**Sources:** [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

---

## Storage Architecture

### Client-Side Storage

**Secure Storage (flutter_secure_storage)**

* **Location:** Platform-specific (iOS Keychain, Android Keystore)
* **Purpose:** Store sensitive authentication data
* **Files:** * `credentials.json`: Static username/password configuration * `token.json`: Dynamic JWT access tokens
* **Access:** `AuthProvider` [lib/providers/auth_provider.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/providers/auth_provider.dart)

**Local Cache (Hive)**

* **Location:** Application documents directory
* **Purpose:** Offline message persistence
* **Data:** `ChatMessage` objects with text, audio, timestamps
* **Access:** `ChatProvider` [lib/providers/chat_provider.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/providers/chat_provider.dart)

**Sources:** [ARCHITECTURE.md L227-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L234)

### Server-Side Storage

**PostgreSQL Database**

* **Connection:** Configured via `DATABASE_URL` environment variable
* **Tables:** * `users`: User accounts, hashed passwords * `messages`: Message history with session tracking * `tasks`: Task data with due dates and status * `calendar_events`: Calendar events with start/end times
* **Access:** FastAPI SQLAlchemy ORM [backend/main.py](https://github.com/axchisan/AxIA/blob/1fe26c44/backend/main.py)

**Sources:** [ARCHITECTURE.md L213-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L213-L219)

---

## Configuration Management

### Flutter Configuration

The `ApiConfig` class [lib/config/api_config.dart](https://github.com/axchisan/AxIA/blob/1fe26c44/lib/config/api_config.dart)

 defines environment-specific endpoints:

```javascript
static const String baseUrl = 'https://apiaxia.axchisan.com';
static const String wsUrl = 'wss://apiaxia.axchisan.com/ws';
```

**Sources:** [ARCHITECTURE.md L221-L225](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L221-L225)

### Backend Configuration

Environment variables in `.env` file [backend/.env](https://github.com/axchisan/AxIA/blob/1fe26c44/backend/.env)

:

```
SECRET_KEY=jwt_signing_key
N8N_WEBHOOK_URL=http://n8n:5678/webhook/axia
DATABASE_URL=postgresql://user:pass@postgres:5432/axia_db
```

**Sources:** [ARCHITECTURE.md L213-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L213-L219)

---

## Performance Characteristics

The architecture is optimized for low-latency real-time communication:

| Aspect | Implementation | Benefit |
| --- | --- | --- |
| **Real-time Communication** | WebSocket persistent connection | ~50ms latency vs. ~500ms HTTP polling |
| **Message Caching** | Hive local database | Instant message history access, offline support |
| **State Management** | Provider with `ChangeNotifier` | Efficient UI updates, minimal rebuilds |
| **Lazy Loading** | `ListView.builder()` in chat UI | Memory-efficient for large message lists |
| **Connection Reuse** | Singleton `WebSocketChannel` | Single persistent connection per session |
| **Audio Streaming** | Base64 encoding over WebSocket | No separate HTTP requests for audio files |

**Sources:** [ARCHITECTURE.md L235-L241](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L235-L241)

---

## Security Architecture

The system implements defense-in-depth security:

**Security Layers Diagram**

```mermaid
flowchart TD

App["Flutter App"]
Keychain["iOS Keychain /<br>Android Keystore"]
CredFile["credentials.json<br>Encrypted at rest"]
TokenFile["token.json<br>Encrypted at rest"]
HTTPS["HTTPS/TLS 1.3<br>REST API"]
WSS["WSS/TLS 1.3<br>WebSocket"]
JWT["JWT Validation<br>24h expiration"]
Bearer["Bearer Token<br>Header/Query Param"]
Hash["Password Hashing<br>bcrypt"]
CORS["CORS Policy<br>Restrictive in prod"]
RateLimit["Rate Limiting<br>Per-user quotas"]

App --> HTTPS
App --> WSS
HTTPS --> Bearer
WSS --> Bearer
HTTPS --> CORS
WSS --> CORS

subgraph Network ["Network Security Layer"]
    CORS
    RateLimit
    CORS --> RateLimit
end

subgraph Application ["Application Security Layer"]
    JWT
    Bearer
    Hash
    Bearer --> JWT
    JWT --> Hash
end

subgraph Transport ["Transport Security Layer"]
    HTTPS
    WSS
end

subgraph Device ["Device Security Layer"]
    App
    Keychain
    CredFile
    TokenFile
    App --> Keychain
    Keychain --> CredFile
    Keychain --> TokenFile
end
```

**Security Controls:**

1. **Credential Storage:** Static credentials in `credentials.json` and dynamic tokens in `token.json` are both encrypted by `flutter_secure_storage`, which uses platform-specific secure enclaves (iOS Keychain, Android Keystore).
2. **Transport Encryption:** All communication uses TLS 1.3 (HTTPS for REST, WSS for WebSocket).
3. **Authentication:** JWT tokens with 24-hour expiration, signed with `SECRET_KEY` [backend/.env](https://github.com/axchisan/AxIA/blob/1fe26c44/backend/.env)
4. **Authorization:** Bearer token validation on every protected endpoint via FastAPI dependency injection.
5. **Password Security:** Passwords hashed with bcrypt before database storage.

**Sources:** [ARCHITECTURE.md L227-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L234)

---

## Error Handling and Resilience

The architecture includes multiple resilience mechanisms:

**WebSocket Reconnection:**

* `ChatProvider` listens to WebSocket `onError` stream
* UI displays red disconnection banner
* User-triggered reconnection via `reconnect()` method
* Automatic cleanup and re-initialization of WebSocket channel

**Token Expiration:**

* 401 responses trigger `AuthProvider.logout()`
* Token cleared from secure storage
* User redirected to `LoginScreen`
* Re-authentication required (no auto-refresh)

**Request Failures:**

* HTTP errors caught by `ApiService`
* Error messages displayed to user
* Retry capability via UI action buttons

**Sources:** [ARCHITECTURE.md L131-L146](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L131-L146)

---

## Deployment Architecture

**Production Deployment Topology:**

```mermaid
flowchart TD

MobileClient["AxIA Mobile App<br>Android/iOS"]
LB["Load Balancer<br>HTTPS/WSS"]
FastAPI["FastAPI Container<br>backend/main.py"]
Postgres["PostgreSQL Container<br>Port 5432"]
n8nContainer["n8n Container<br>Port 5678"]
OpenAI["OpenAI API"]
ElevenLabs["ElevenLabs API"]

MobileClient --> LB
n8nContainer --> OpenAI
n8nContainer --> ElevenLabs

subgraph External ["External Services"]
    OpenAI
    ElevenLabs
end

subgraph CloudInfra ["Cloud Infrastructure"]
    LB
    LB --> FastAPI

subgraph DockerCompose ["Docker Compose"]
    FastAPI
    Postgres
    n8nContainer
    FastAPI --> Postgres
    FastAPI --> n8nContainer
end
end

subgraph Internet ["Public Internet"]
    MobileClient
end
```

**Deployment Commands:**

Backend (Docker Compose):

```
docker-compose up -d
```

Flutter (Development):

```
flutter pub get
flutter run
```

Flutter (Production Release):

```markdown
flutter build apk --release  # Android
flutter build ios --release  # iOS
```

**Sources:** [ARCHITECTURE.md L199-L210](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L210)

 [README.md L177-L187](https://github.com/axchisan/AxIA/blob/1fe26c44/README.md#L177-L187)