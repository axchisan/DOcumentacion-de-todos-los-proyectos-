# Authentication & Security

> **Relevant source files**
> * [..bfg-report/2025-11-27/02-50-28/deleted-files.txt](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-50-28/deleted-files.txt)
> * [..bfg-report/2025-11-27/02-56-33/changed-files.txt](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-56-33/changed-files.txt)
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)

## Purpose and Scope

This document covers the authentication and security mechanisms in the AxIA system. It details the JWT-based authentication flow, secure credential storage using platform-specific keychains, and the security remediation performed on the repository. For details on the JWT token lifecycle and refresh mechanisms, see [JWT Token Flow](/axchisan/AxIA/4.1-jwt-token-flow). For implementation details of secure storage, see [Secure Credential Storage](/axchisan/AxIA/4.2-secure-credential-storage).

Related topics:

* For WebSocket connection security, see [WebSocket Communication](/axchisan/AxIA/5.1-websocket-communication)
* For backend API authentication, see [REST Endpoints](/axchisan/AxIA/8.1-rest-endpoints)
* For environment configuration, see [Environment Setup](/axchisan/AxIA/2.2-environment-setup)

---

## Authentication Architecture

The AxIA system implements a JWT (JSON Web Token) based authentication system with three primary components: the Flutter client that manages user sessions, the FastAPI backend that validates credentials and issues tokens, and the secure storage layer that protects sensitive data using platform-specific keychains.

### High-Level Authentication Flow

```mermaid
flowchart TD

SplashScreen["SplashScreen"]
AuthProvider["AuthProvider"]
SecureStorage["flutter_secure_storage"]
LoginScreen["LoginScreen"]
MainNav["MainNavigation"]
Keychain["iOS Keychain / Android Keystore"]
CredFile["credentials.json"]
TokenFile["token.json"]
TokenEndpoint["/token endpoint"]
JWTValidator["JWT Validation"]
ProtectedRoutes["Protected Routes"]

SecureStorage --> Keychain
LoginScreen --> TokenEndpoint
TokenEndpoint --> TokenFile
MainNav --> ProtectedRoutes
JWTValidator --> LoginScreen

subgraph subGraph2 ["FastAPI Backend"]
    TokenEndpoint
    JWTValidator
    ProtectedRoutes
    ProtectedRoutes --> JWTValidator
end

subgraph subGraph1 ["Secure Storage Layer"]
    Keychain
    CredFile
    TokenFile
    Keychain --> CredFile
    Keychain --> TokenFile
end

subgraph subGraph0 ["Flutter Client"]
    SplashScreen
    AuthProvider
    SecureStorage
    LoginScreen
    MainNav
    SplashScreen --> AuthProvider
    AuthProvider --> SecureStorage
    AuthProvider --> LoginScreen
    AuthProvider --> MainNav
end
```

**Sources:** [ARCHITECTURE.md L1-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L1-L15)

 [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

---

## JWT Token System

The authentication system uses JWT tokens with a 24-hour expiration period. Tokens are issued by the FastAPI backend and stored securely on the device. The system does not implement automatic token refresh; instead, expired tokens require re-authentication.

### Token Request and Storage

| Component | Responsibility | Storage Location |
| --- | --- | --- |
| `AuthService` | Sends `POST /token` request with username/password | N/A |
| `AuthProvider` | Manages authentication state and token lifecycle | In-memory |
| `flutter_secure_storage` | Persists tokens across app restarts | Platform keychain |
| `credentials.json` | Stores static authentication credentials | iOS Keychain / Android Keystore |
| `token.json` | Stores dynamic JWT access tokens | iOS Keychain / Android Keystore |

### JWT Token Lifecycle

```mermaid
sequenceDiagram
  participant User
  participant LoginScreen
  participant AuthProvider
  participant AuthService
  participant SecureStorage
  participant POST /token

  User->>LoginScreen: "Enter credentials"
  LoginScreen->>AuthProvider: "login(username, password)"
  AuthProvider->>AuthService: "login(username, password)"
  AuthService->>POST /token: "POST /token
  POST /token->>AuthService: {username, password}"
  AuthService->>AuthProvider: "{access_token, token_type, expires_in}"
  AuthProvider->>SecureStorage: "Return token"
  SecureStorage->>AuthProvider: "write(key: token.json, value: JWT)"
  AuthProvider->>LoginScreen: "Success"
  LoginScreen->>User: "Authentication success"
```

**Sources:** [ARCHITECTURE.md L3-L15](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L3-L15)

 [ARCHITECTURE.md L56-L60](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L56-L60)

### Token Usage Patterns

The JWT token is used in two distinct patterns depending on the communication channel:

**REST API Requests:**

* Token sent in `Authorization` header
* Format: `Bearer {jwt_token}`
* Used for all protected endpoints (calendar, tasks, messages)

**WebSocket Connections:**

* Token sent as URL query parameter
* Format: `wss://host/ws/{username}?token={jwt_token}`
* Validated once during connection establishment

```mermaid
flowchart TD

Token["JWT Token<br>from token.json"]
REST["REST API Calls"]
WS["WebSocket Connection"]
RestAuth["verify_token()<br>from Authorization header"]
WsAuth["verify_websocket_token()<br>from query param"]
Protected["Protected Endpoints<br>/calendar/events<br>/tasks<br>/messages"]
WsEndpoint["/ws/{username}"]

REST --> RestAuth
WS --> WsAuth

subgraph subGraph1 ["Backend Validation"]
    RestAuth
    WsAuth
    Protected
    WsEndpoint
    RestAuth --> Protected
    WsAuth --> WsEndpoint
end

subgraph subGraph0 ["Token Usage"]
    Token
    REST
    WS
    Token --> REST
    Token --> WS
end
```

**Sources:** [ARCHITECTURE.md L56-L89](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L56-L89)

 [ARCHITECTURE.md L20-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L20-L33)

---

## Secure Storage Implementation

The AxIA system uses the `flutter_secure_storage` package to protect sensitive authentication data. This package provides a unified API that delegates to platform-specific secure storage mechanisms.

### Platform-Specific Storage

| Platform | Storage Mechanism | Key Features |
| --- | --- | --- |
| iOS | Keychain Services | Hardware-backed encryption on devices with Secure Enclave |
| Android | EncryptedSharedPreferences / Keystore | AES-256 encryption with hardware-backed keys on supported devices |

### Secure Storage Architecture

```mermaid
flowchart TD

AuthProvider2["AuthProvider"]
ChatProvider["ChatProvider"]
ApiService["ApiService"]
FSS["FlutterSecureStorage<br>Package"]
iOS["iOS Keychain<br>SecItemAdd/SecItemCopyMatching"]
Android["Android Keystore<br>EncryptedSharedPreferences"]
Cred["credentials.json<br>{username, password}"]
Tok["token.json<br>{access_token, expires_in}"]

AuthProvider2 --> FSS
ChatProvider --> FSS
ApiService --> FSS
FSS --> iOS
FSS --> Android
iOS --> Cred
iOS --> Tok
Android --> Cred
Android --> Tok

subgraph subGraph3 ["Stored Data"]
    Cred
    Tok
end

subgraph subGraph2 ["Platform Layer"]
    iOS
    Android
end

subgraph subGraph1 ["Storage Abstraction"]
    FSS
end

subgraph subGraph0 ["Flutter Application Layer"]
    AuthProvider2
    ChatProvider
    ApiService
end
```

**Sources:** [ARCHITECTURE.md L227-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L234)

### Stored Credential Files

Two JSON files are stored in the secure storage layer:

**credentials.json** (Static):

* Contains username and password
* Used for initial authentication and token refresh
* Read-only during app runtime
* Size: 287-395 bytes (as shown in cleanup report)

**token.json** (Dynamic):

* Contains JWT access token
* Updated on each successful login
* Read on app startup to check authentication status
* Cleared on logout or token expiration

**Sources:** [.bfg-report/2025-11-27/02-56-33/changed-files.txt L1-L3](https://github.com/axchisan/AxIA/blob/1fe26c44/.bfg-report/2025-11-27/02-56-33/changed-files.txt#L1-L3)

---

## WebSocket Authentication

WebSocket connections require authentication but cannot use standard HTTP headers. The AxIA system embeds the JWT token in the WebSocket URL as a query parameter.

### WebSocket Connection Flow

```mermaid
sequenceDiagram
  participant ChatScreen
  participant ChatProvider
  participant SecureStorage
  participant WebSocket /ws/{user}
  participant FastAPI WS Handler
  participant verify_websocket_token()

  ChatScreen->>ChatProvider: "initializeWebSocket()"
  ChatProvider->>SecureStorage: "read(key: token.json)"
  SecureStorage->>ChatProvider: "Return JWT"
  ChatProvider->>WebSocket /ws/{user}: "Connect to wss://host/ws/{user}?token={jwt}"
  WebSocket /ws/{user}->>FastAPI WS Handler: "Connection request"
  FastAPI WS Handler->>verify_websocket_token(): "Validate token from query param"
  loop [Token Valid]
    verify_websocket_token()->>FastAPI WS Handler: "Token valid"
    FastAPI WS Handler->>WebSocket /ws/{user}: "Connection established"
    WebSocket /ws/{user}->>ChatProvider: "onDone callback"
    ChatProvider->>ChatScreen: "WebSocket ready"
    verify_websocket_token()->>FastAPI WS Handler: "401 Unauthorized"
    FastAPI WS Handler->>WebSocket /ws/{user}: "Connection refused"
    WebSocket /ws/{user}->>ChatProvider: "onError callback"
    ChatProvider->>ChatScreen: "Show reconnect banner"
  end
```

**Sources:** [ARCHITECTURE.md L20-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L20-L33)

 [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

### Error Handling and Reconnection

When a WebSocket connection fails due to authentication issues:

1. `ChatProvider` receives an error callback
2. Connection status changes to disconnected
3. UI displays a red banner with "Reconectar" button
4. User clicks reconnect button
5. `ChatProvider.reconnect()` method: * Closes existing connection * Reads token from secure storage * Attempts new connection * If token expired, redirects to login

**Sources:** [ARCHITECTURE.md L133-L146](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L133-L146)

---

## Token Expiration and Session Management

JWT tokens in the AxIA system have a 24-hour lifespan. The system does not automatically refresh tokens; instead, it requires user re-authentication.

### Token Expiration Flow

```mermaid
flowchart TD

Request["API Request with Token"]
Validate["Backend JWT Validation"]
Expired["Token Expired?"]
Success["200 OK Response"]
Unauthorized["401 Unauthorized"]
ClearToken["AuthProvider.logout()"]
ClearStorage["SecureStorage.delete(token.json)"]
Redirect["Navigate to LoginScreen"]

Request --> Validate
Validate --> Expired
Expired --> Success
Expired --> Unauthorized
Unauthorized --> ClearToken
ClearToken --> ClearStorage
ClearStorage --> Redirect
```

### Session Management Characteristics

| Behavior | Implementation |
| --- | --- |
| Token Lifetime | 24 hours from issuance |
| Refresh Strategy | Manual re-authentication required |
| Expiration Handling | 401 response triggers logout and redirect |
| Token Storage Duration | Persistent across app restarts until expiration |
| Multi-device Support | Each device maintains independent token |

**Sources:** [ARCHITECTURE.md L138-L145](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L138-L145)

 [ARCHITECTURE.md L227-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L234)

---

## Security Best Practices

The AxIA codebase implements several security measures to protect user credentials and authentication tokens.

### Security Measures

1. **Platform-Specific Encryption** * iOS: Keychain Services with hardware-backed encryption * Android: Keystore system with AES-256 encryption * No plaintext credentials stored in app preferences
2. **JWT Token Security** * 24-hour expiration enforces session limits * Tokens contain minimal user information * Backend validates token signature on each request
3. **Transport Security** * HTTPS for all REST API calls * WSS (WebSocket Secure) for real-time communication * TLS 1.2+ enforced at backend level
4. **Credential Management** * Passwords never stored in app memory after login * Credentials cleared from memory on logout * No credential logging in production builds

**Sources:** [ARCHITECTURE.md L227-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L234)

---

## Repository Security Remediation

The AxIA repository underwent a comprehensive security cleanup to remove accidentally committed sensitive files from Git history.

### BFG Repo-Cleaner Execution

On 2025-11-27, the BFG Repo-Cleaner tool was used to permanently remove sensitive files from the repository history:

**Deleted Files:**

* `.env` files (3 versions: 395, 287, 667 bytes)
* `credentials.json` (rewritten in commits)
* `token.json` (rewritten in commits)

**Impact:**

* 60 commits were rewritten
* Complete object ID remapping performed
* Git history sanitized while preserving commit structure

```mermaid
flowchart TD

OldRepo["Git Repository<br>60 commits"]
EnvFiles[".env files<br>395, 287, 667 bytes"]
CredFiles["credentials.json<br>token.json"]
BFG["BFG Repo-Cleaner<br>2025-11-27"]
DeletedReport["deleted-files.txt"]
ChangedReport["changed-files.txt"]
ObjectMap["Object ID Mapping<br>old → new"]
CleanRepo["Clean Repository"]
Gitignore[".gitignore updates"]
EnvConfig["Environment Variable Config"]

OldRepo --> BFG
EnvFiles --> BFG
CredFiles --> BFG
BFG --> CleanRepo

subgraph subGraph2 ["After Cleanup"]
    CleanRepo
    Gitignore
    EnvConfig
    CleanRepo --> Gitignore
    CleanRepo --> EnvConfig
end

subgraph subGraph1 ["BFG Tool Execution"]
    BFG
    DeletedReport
    ChangedReport
    ObjectMap
    BFG --> DeletedReport
    BFG --> ChangedReport
    BFG --> ObjectMap
end

subgraph subGraph0 ["Before Cleanup"]
    OldRepo
    EnvFiles
    CredFiles
end
```

**Sources:** [.bfg-report/2025-11-27/02-50-28/deleted-files.txt L1-L4](https://github.com/axchisan/AxIA/blob/1fe26c44/.bfg-report/2025-11-27/02-50-28/deleted-files.txt#L1-L4)

 [.bfg-report/2025-11-27/02-56-33/changed-files.txt L1-L3](https://github.com/axchisan/AxIA/blob/1fe26c44/.bfg-report/2025-11-27/02-56-33/changed-files.txt#L1-L3)

### Post-Cleanup Security Configuration

After the BFG cleanup, the following security measures were implemented:

1. **Updated .gitignore** * Added `.env` pattern * Added `credentials.json` pattern * Added `token.json` pattern * Prevents future accidental commits
2. **Environment Variable Migration** * Backend now uses environment variables exclusively * No `.env` files committed to repository * `.env.example` file provides template for local setup
3. **Credential Management** * Static credentials removed from version control * Deployment uses secrets management systems * Local development requires manual credential setup

**Sources:** [ARCHITECTURE.md L212-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L212-L219)

---

## Security Configuration Reference

### Backend Environment Variables

The FastAPI backend requires the following security-related environment variables:

| Variable | Purpose | Example |
| --- | --- | --- |
| `SECRET_KEY` | JWT token signing key | Random 32+ character string |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@host:5432/db` |
| `N8N_WEBHOOK_URL` | n8n webhook endpoint | `http://n8n:5678/webhook/axia` |

### Flutter API Configuration

The Flutter client maintains API configuration in code:

* Base URL: `https://apiaxia.axchisan.com`
* WebSocket URL: `wss://apiaxia.axchisan.com/ws`
* Timeout settings: 30 seconds for REST, no timeout for WebSocket
* Retry logic: Manual reconnection only

**Sources:** [ARCHITECTURE.md L212-L226](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L212-L226)

---

## Authentication Flow Summary

The complete authentication flow integrates all components described in this document:

1. **App Launch:** `SplashScreen` checks for existing token in secure storage
2. **Token Check:** `AuthProvider.checkAuthentication()` validates stored token
3. **Login:** If no valid token, user enters credentials in `LoginScreen`
4. **Token Issuance:** FastAPI validates credentials and returns JWT
5. **Secure Storage:** Token saved to `token.json` via `flutter_secure_storage`
6. **Session Start:** User navigated to `MainNavigation` with valid token
7. **API Access:** All requests include token (Bearer header or query param)
8. **Expiration:** 401 responses trigger logout and return to login screen

This design prioritizes security through platform-specific encryption, short-lived tokens, and manual session management, ensuring that compromised credentials have limited temporal impact.

**Sources:** [ARCHITECTURE.md L119-L129](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L119-L129)

 [ARCHITECTURE.md L227-L234](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L234)