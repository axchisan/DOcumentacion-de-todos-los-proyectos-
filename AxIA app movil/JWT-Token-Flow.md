# JWT Token Flow

> **Relevant source files**
> * [..bfg-report/2025-11-27/02-56-33/changed-files.txt](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-56-33/changed-files.txt)
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)

## Purpose

This document details the JSON Web Token (JWT) authentication lifecycle in AxIA, including token acquisition, storage, usage, and expiration handling. The JWT token serves as the primary authentication mechanism for both REST API calls and WebSocket connections between the Flutter application and the FastAPI backend.

For information about the underlying secure storage implementation, see [Secure Credential Storage](/axchisan/AxIA/4.2-secure-credential-storage). For details on how tokens are used in WebSocket connections, see [WebSocket Communication](/axchisan/AxIA/5.1-websocket-communication). For REST API authentication headers, see [REST Endpoints](/axchisan/AxIA/8.1-rest-endpoints).

## Token Acquisition Flow

The JWT token acquisition process begins when a user enters credentials in the login screen. The Flutter application exchanges username and password for an access token via the FastAPI backend's `/token` endpoint.

### Login Sequence Diagram

```mermaid
sequenceDiagram
  participant User
  participant LoginScreen
  participant AuthProvider
  participant AuthService
  participant flutter_secure_storage
  participant POST /token

  User->>LoginScreen: "Enter credentials"
  LoginScreen->>AuthProvider: "login(username, password)"
  AuthProvider->>flutter_secure_storage: "read('credentials.json')"
  flutter_secure_storage-->>AuthProvider: "Static credentials"
  AuthProvider->>AuthService: "authenticate(username, password)"
  AuthService->>POST /token: "POST /token
  POST /token-->>AuthService: {username, password}"
  AuthService-->>AuthProvider: "{access_token, token_type, expires_in}"
  AuthProvider->>flutter_secure_storage: "JWT token"
  flutter_secure_storage-->>AuthProvider: "write('token.json', token_data)"
  AuthProvider->>AuthProvider: "Success"
  AuthProvider-->>LoginScreen: "setAuthenticated(true)"
  LoginScreen->>User: "Authentication successful"
```

**Sources:** [ARCHITECTURE.md L1-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L15)

 [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

### Authentication Request and Response

The authentication endpoint accepts form-encoded credentials and returns a JWT token with metadata:

**Request Format:**

```html
POST /token
Content-Type: application/x-www-form-urlencoded

username=<username>&password=<password>
```

**Response Format:**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 86400
}
```

**Sources:** [ARCHITECTURE.md L57-L59](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L57-L59)

## Token Structure and Properties

The JWT token issued by the FastAPI backend contains encoded information about the authenticated user and session validity.

| Property | Value | Description |
| --- | --- | --- |
| **Token Type** | Bearer | Standard OAuth 2.0 token type |
| **Expiration** | 24 hours (86400 seconds) | Token lifespan from issuance |
| **Storage Location** | `token.json` in secure storage | Platform-specific Keychain/Keystore |
| **Encoding** | JWT (JSON Web Token) | Base64-encoded header, payload, signature |
| **Algorithm** | HS256 (assumed) | HMAC with SHA-256 for signing |

The token is stored in `token.json` alongside `credentials.json` for static authentication configuration. Both files are managed by `flutter_secure_storage` to ensure platform-level encryption.

**Sources:** [ARCHITECTURE.md L228-L231](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L228-L231)

 [..bfg-report/2025-11-27/02-56-33/changed-files.txt L1-L3](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-56-33/changed-files.txt#L1-L3)

## Token Storage Mechanism

JWT tokens are persisted in the device's secure storage system after successful authentication. This ensures tokens survive app restarts while maintaining security through OS-level encryption.

### Storage Architecture

```mermaid
flowchart TD

AuthProvider["AuthProvider<br>(lib/providers/auth_provider.dart)"]
SecureStorage["flutter_secure_storage<br>(Platform Abstraction)"]
Keychain["iOS Keychain<br>(System Service)"]
Keystore["Android Keystore<br>(System Service)"]
CredsJSON["credentials.json<br>(Static: username, password)"]
TokenJSON["token.json<br>(Dynamic: JWT, expires_in, token_type)"]

AuthProvider --> SecureStorage
SecureStorage --> Keychain
SecureStorage --> Keystore
Keychain --> CredsJSON
Keychain --> TokenJSON
Keystore --> CredsJSON
Keystore --> TokenJSON

subgraph subGraph2 ["Stored Files"]
    CredsJSON
    TokenJSON
end

subgraph Android ["Android"]
    Keystore
end

subgraph iOS ["iOS"]
    Keychain
end
```

**Sources:** [ARCHITECTURE.md L11-L12](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L11-L12)

 [ARCHITECTURE.md L228-L231](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L228-L231)

### Storage Operations

The `AuthProvider` manages token storage through `flutter_secure_storage` read/write operations:

**Writing Token:**

```yaml
// After successful authentication
await _storage.write(
  key: 'token.json',
  value: jsonEncode({
    'access_token': token,
    'token_type': tokenType,
    'expires_in': expiresIn,
  })
);
```

**Reading Token:**

```
// On app initialization
final tokenData = await _storage.read(key: 'token.json');
if (tokenData != null) {
  final token = jsonDecode(tokenData);
  // Use token['access_token'] for requests
}
```

**Deleting Token:**

```sql
// On logout or token expiration
await _storage.delete(key: 'token.json');
```

**Sources:** [ARCHITECTURE.md L11-L12](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L11-L12)

 [ARCHITECTURE.md L122-L126](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L122-L126)

## Token Usage Patterns

The JWT token is used in two distinct contexts within the AxIA application: REST API calls with Bearer authentication headers and WebSocket connections with query parameter authentication.

### REST API Authentication

REST endpoints use the standard OAuth 2.0 Bearer token authentication scheme in the HTTP `Authorization` header.

```mermaid
flowchart TD

ChatProvider["ChatProvider<br>(lib/providers/chat_provider.dart)"]
CalendarProvider["CalendarProvider<br>(lib/providers/calendar_provider.dart)"]
TasksProvider["TasksProvider<br>(lib/providers/tasks_provider.dart)"]
ApiService["ApiService<br>(lib/services/api_service.dart)"]
AuthProvider["AuthProvider<br>(token storage)"]
FastAPI["FastAPI Backend<br>(REST Endpoints)"]

ChatProvider --> ApiService
CalendarProvider --> ApiService
TasksProvider --> ApiService
ApiService --> AuthProvider
AuthProvider --> ApiService
ApiService --> FastAPI
```

**Header Format:**

```yaml
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Authenticated Endpoints:**

* `GET /calendar/events` - Retrieve calendar events
* `GET /tasks` - List tasks
* `POST /tasks` - Create new task
* `GET /messages/{session_id}` - Retrieve message history

**Sources:** [ARCHITECTURE.md L71-L88](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L71-L88)

 [ARCHITECTURE.md L228-L231](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L228-L231)

### WebSocket Authentication

WebSocket connections authenticate via query parameter rather than HTTP headers due to browser WebSocket API limitations. The token is appended to the connection URL.

```mermaid
sequenceDiagram
  participant ChatScreen
  participant (lib/screens/chat/)
  participant ChatProvider
  participant AuthProvider
  participant WebSocketChannel
  participant FastAPI WebSocket
  participant /ws/{username}

  ChatScreen->>ChatProvider: "initializeWebSocket()"
  ChatProvider->>AuthProvider: "getToken()"
  AuthProvider-->>ChatProvider: "JWT token"
  ChatProvider->>ChatProvider: "Build URL:
  ChatProvider->>WebSocketChannel: wss://apiaxia.axchisan.com/ws/{user}?token={JWT}"
  WebSocketChannel->>FastAPI WebSocket: "WebSocketChannel.connect(url)"
  FastAPI WebSocket->>FastAPI WebSocket: "WebSocket handshake
  FastAPI WebSocket-->>WebSocketChannel: ?token={JWT}"
  WebSocketChannel-->>ChatProvider: "Validate JWT"
  ChatProvider-->>ChatScreen: "101 Switching Protocols"
```

**Connection URL Format:**

```
wss://apiaxia.axchisan.com/ws/{username}?token={JWT_TOKEN}
```

The FastAPI backend validates the token during the WebSocket handshake before establishing the bidirectional connection.

**Sources:** [ARCHITECTURE.md L20-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L20-L33)

 [ARCHITECTURE.md L128-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L128-L129)

 [ARCHITECTURE.md L221-L225](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L221-L225)

## Token Lifecycle and Expiration Handling

JWT tokens in AxIA have a fixed 24-hour lifespan with no automatic refresh mechanism. Expired tokens trigger re-authentication through the login flow.

### Token State Diagram

```mermaid
stateDiagram-v2
    [*] --> NoToken : "App Start"
    NoToken --> Authenticating : "Authentication failed"
    Authenticating --> TokenValid : "POST /token successStore in token.json"
    Authenticating --> NoToken : "Authentication failed"
    TokenValid --> InUse : "Token loaded from storage"
    InUse --> TokenExpired : "24 hours elapsed"
    TokenExpired --> NoToken
    NoToken --> [*] : "User closes app"
    TokenExpired --> [*] : "User closes app"
```

**Sources:** [ARCHITECTURE.md L138-L141](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L138-L141)

 [ARCHITECTURE.md L228-L231](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L228-L231)

### Expiration Detection and Handling

The application detects token expiration through HTTP 401 Unauthorized responses from the FastAPI backend. The `AuthProvider` handles this scenario by clearing stored credentials and redirecting to the login screen.

**Expiration Flow:**

```mermaid
sequenceDiagram
  participant ChatProvider/CalendarProvider/etc
  participant ApiService
  participant FastAPI Backend
  participant AuthProvider
  participant LoginScreen

  ChatProvider/CalendarProvider/etc->>ApiService: "API request with token"
  ApiService->>FastAPI Backend: "Authorization: Bearer {expired_token}"
  FastAPI Backend-->>ApiService: "401 Unauthorized"
  ApiService->>AuthProvider: "Token expired detected"
  AuthProvider->>AuthProvider: "delete('token.json')"
  AuthProvider->>AuthProvider: "setAuthenticated(false)"
  AuthProvider->>LoginScreen: "Navigate to login"
  LoginScreen->>LoginScreen: "Display login form"
```

**Important:** AxIA does not implement token refresh. Users must re-authenticate with username and password to obtain a new token after expiration. This design choice simplifies the authentication flow at the cost of requiring periodic re-login.

**Sources:** [ARCHITECTURE.md L138-L141](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L138-L141)

## Code Entity Reference

This section maps the JWT token flow concepts to specific code entities in the AxIA codebase.

### Flutter Application Components

| Component | Location | Responsibility |
| --- | --- | --- |
| **AuthProvider** | `lib/providers/auth_provider.dart` | Manages authentication state, token storage, and login/logout operations |
| **AuthService** | `lib/services/auth_service.dart` | Handles HTTP communication with `/token` endpoint |
| **ApiService** | `lib/services/api_service.dart` | Attaches Bearer token to API requests |
| **LoginScreen** | `lib/screens/auth/login_screen.dart` | Collects user credentials and triggers authentication |
| **SplashScreen** | `lib/screens/auth/splash_screen.dart` | Checks for existing valid token on app startup |
| **flutter_secure_storage** | External package | Provides platform-specific secure storage abstraction |

### Backend Endpoints

| Endpoint | Method | Authentication | Purpose |
| --- | --- | --- | --- |
| `/token` | POST | None (credentials in body) | Issues JWT token |
| `/ws/{username}` | WebSocket | Token in query param | Real-time chat connection |
| `/calendar/events` | GET | Bearer token in header | Retrieve calendar data |
| `/tasks` | GET | Bearer token in header | Retrieve task list |
| `/messages/{session_id}` | GET | Bearer token in header | Retrieve chat history |

### Storage Files

| File | Type | Location | Content |
| --- | --- | --- | --- |
| **credentials.json** | Static | Secure storage | Username and password for authentication |
| **token.json** | Dynamic | Secure storage | JWT token, token_type, expires_in |

These files were previously committed to the repository but have been removed via BFG Repo-Cleaner for security reasons.

**Sources:** [ARCHITECTURE.md L148-L189](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L148-L189)

 [ARCHITECTURE.md L54-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L54-L89)

 [..bfg-report/2025-11-27/02-56-33/changed-files.txt L1-L3](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-56-33/changed-files.txt#L1-L3)

## Authentication Flow State Management

The `AuthProvider` class maintains the authentication state using the Flutter Provider pattern. It exposes reactive properties that trigger UI updates when authentication state changes.

```mermaid
flowchart TD

IsAuth["isAuthenticated: bool"]
Token["_token: String?"]
Storage["_storage: FlutterSecureStorage"]
CheckAuth["checkAuthentication()<br>→ bool"]
Login["login(username, password)<br>→ Future"]
Logout["logout()<br>→ Future"]
GetToken["getToken()<br>→ String?"]
ReadToken["_readToken()"]
WriteToken["_writeToken()"]
DeleteToken["_deleteToken()"]

CheckAuth --> ReadToken
Login --> WriteToken
Logout --> DeleteToken
GetToken --> Token
ReadToken --> Storage
WriteToken --> Storage
DeleteToken --> Storage
WriteToken --> IsAuth
DeleteToken --> IsAuth

subgraph subGraph2 ["Internal Operations"]
    ReadToken
    WriteToken
    DeleteToken
end

subgraph subGraph1 ["Public Methods"]
    CheckAuth
    Login
    Logout
    GetToken
end

subgraph subGraph0 ["AuthProvider State"]
    IsAuth
    Token
    Storage
end
```

The provider notifies all listening widgets (via `notifyListeners()`) when authentication state changes, triggering navigation between `LoginScreen` and `MainNavigation`.

**Sources:** [ARCHITECTURE.md L162-L171](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L162-L171)

 [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)