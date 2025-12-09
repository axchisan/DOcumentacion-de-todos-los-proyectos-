# Client-Side Authentication

> **Relevant source files**
> * [client/lib/core/services/session_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart)
> * [client/lib/data/models/user_model.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.dart)
> * [client/lib/data/models/user_model.g.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.g.dart)
> * [client/lib/main.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart)
> * [client/lib/presentation/providers/auth_provider.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart)
> * [client/lib/presentation/screens/auth/register_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart)
> * [client/lib/presentation/screens/qr/qr_code_generator_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart)
> * [server/.env](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.env)
> * [server/.gitignore](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.gitignore)
> * [server/app/config.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/config.py)
> * [server/app/database.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/database.py)
> * [server/docker-compose.yml](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/docker-compose.yml)

## Purpose and Scope

This document describes the client-side authentication system in the Flutter application, including state management with `AuthProvider`, session persistence, token handling, and user authentication flows. For backend authentication endpoints and JWT generation, see [Backend Authentication API](/axchisan/GestionInventarioSENA/3.2-backend-authentication-api). For role-based navigation and access control logic, see [Role-Based Access Control](/axchisan/GestionInventarioSENA/3.3-role-based-access-control).

## Architecture Overview

The client authentication system implements a Provider-based state management pattern with persistent session storage. The architecture consists of three primary layers: UI screens for user interaction, the `AuthProvider` for state management and API communication, and the `SessionService` for persistent storage.

### Client Authentication Architecture

```

```

**Sources:** [client/lib/presentation/providers/auth_provider.dart L1-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L1-L159)

 [client/lib/core/services/session_service.dart L1-L65](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart#L1-L65)

 [client/lib/data/models/user_model.dart L1-L55](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.dart#L1-L55)

## AuthProvider State Management

The `AuthProvider` class extends `ChangeNotifier` to provide reactive authentication state management throughout the application. It manages user authentication state, token storage, and coordinates between the UI, session persistence, and backend API.

### AuthProvider State Properties

| Property | Type | Purpose |
| --- | --- | --- |
| `_currentUser` | `UserModel?` | Currently authenticated user object |
| `_token` | `String?` | JWT access token |
| `_isAuthenticated` | `bool` | Authentication status flag |
| `_isLoading` | `bool` | Loading state for async operations |
| `_errorMessage` | `String?` | Error messages from auth operations |

### AuthProvider Initialization Flow

```

```

**Sources:** [client/lib/presentation/providers/auth_provider.dart L22-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L22-L24)

 [client/lib/presentation/providers/auth_provider.dart L115-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L115-L158)

The constructor [client/lib/presentation/providers/auth_provider.dart L22-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L22-L24)

 immediately calls `checkSession()` to validate any existing stored session on app initialization. This enables automatic re-authentication without requiring the user to log in again.

## Session Persistence Service

The `SessionService` provides static methods for persistent storage of authentication data using the `shared_preferences` package. It stores four key pieces of data in device storage.

### Session Storage Keys

```

```

**Sources:** [client/lib/core/services/session_service.dart L5-L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart#L5-L10)

### SessionService Methods

| Method | Parameters | Returns | Purpose |
| --- | --- | --- | --- |
| `saveSession()` | token, role, user, expiresAt | `Future<void>` | Persist complete session data |
| `clear()` | none | `Future<void>` | Remove all session data |
| `hasValidSession()` | none | `Future<bool>` | Check if stored token is valid and not expired |
| `getRole()` | none | `Future<String?>` | Retrieve stored user role |
| `getAccessToken()` | none | `Future<String?>` | Retrieve stored JWT token |
| `getUser()` | none | `Future<Map<String, dynamic>?>` | Retrieve stored user data |
| `getExpiresAt()` | none | `Future<int?>` | Retrieve token expiration timestamp |

**Sources:** [client/lib/core/services/session_service.dart L11-L65](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart#L11-L65)

The `hasValidSession()` method [client/lib/core/services/session_service.dart L32-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart#L32-L43)

 uses the `jwt_decoder` package to check token expiration. If the token is expired, it automatically clears the session to prevent stale authentication state.

## User Data Model

The `UserModel` class represents the authenticated user's data structure. It uses `json_annotation` for automatic JSON serialization/deserialization.

### UserModel Fields

```

```

**Sources:** [client/lib/data/models/user_model.dart L6-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.dart#L6-L51)

The model includes the `@JsonKey` annotations to map between Dart camelCase property names and Python snake_case field names from the backend API. The generated file [client/lib/data/models/user_model.g.dart L1-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.g.dart#L1-L48)

 provides the `fromJson()` and `toJson()` methods.

## Login Flow

The login flow begins when a user submits credentials through the login screen, which invokes the `login()` method on `AuthProvider`.

### Login Sequence Diagram

```

```

**Sources:** [client/lib/presentation/providers/auth_provider.dart L26-L64](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L26-L64)

### Login Implementation Details

The `login()` method [client/lib/presentation/providers/auth_provider.dart L26-L64](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L26-L64)

 performs the following steps:

1. **Set Loading State**: Sets `_isLoading = true` and `_errorMessage = null`, then calls `notifyListeners()` to update the UI
2. **HTTP Request**: Makes a POST request to `$baseUrl$loginEndpoint` with JSON body containing email and password
3. **Response Validation**: Checks for HTTP 200 status code and validates that the response contains both `access_token` and `user` fields
4. **Token Processing**: Extracts the JWT token and decodes it using `JwtDecoder.decode()` to retrieve the expiration timestamp
5. **State Update**: Sets `_token`, `_currentUser`, and `_isAuthenticated` state properties
6. **Session Persistence**: Calls `SessionService.saveSession()` with token, role, user data, and expiration timestamp
7. **Notification**: Calls `notifyListeners()` to trigger UI rebuild with authenticated state

The token expiration is calculated at [client/lib/presentation/providers/auth_provider.dart L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L45-L45)

 by multiplying the JWT `exp` claim (in seconds) by 1000 to convert to milliseconds.

## Registration Flow

The registration flow allows new users to create accounts. The system automatically assigns the `student` role to self-registered users.

### Registration Sequence Diagram

```

```

**Sources:** [client/lib/presentation/screens/auth/register_screen.dart L64-L124](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart#L64-L124)

 [client/lib/presentation/providers/auth_provider.dart L66-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L66-L105)

### Registration Screen Implementation

The `RegisterScreen` [client/lib/presentation/screens/auth/register_screen.dart L9-L438](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart#L9-L438)

 provides a comprehensive registration form with validation:

**Form Fields:**

* First Name and Last Name (minimum 3 characters)
* Email (email format validation with regex)
* Phone (optional)
* Program dropdown with predefined options or custom entry
* Ficha (course group number)
* Password (minimum 8 characters, must include uppercase, lowercase, and numbers)
* Confirm Password (must match password)
* Terms acceptance checkbox

**Validation:** The form uses Flutter's built-in form validation with custom validators [client/lib/presentation/screens/auth/register_screen.dart L195-L356](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart#L195-L356)

 Password validation enforces the pattern: `^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$`.

**Auto-login:** After successful registration [client/lib/presentation/screens/auth/register_screen.dart L100-L101](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart#L100-L101)

 the screen automatically calls `authProvider.login()` to authenticate the user without requiring a separate login step.

**Role Assignment:** The role is hardcoded to `'student'` [client/lib/presentation/screens/auth/register_screen.dart L96](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart#L96-L96)

 for self-registration. Other roles must be assigned by administrators through the user management system.

## Session Validation and Auto-Login

The `checkSession()` method provides automatic session restoration when the app starts or resumes from background.

### Session Validation Flow

```

```

**Sources:** [client/lib/presentation/providers/auth_provider.dart L115-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L115-L158)

The validation process consists of:

1. **Token Check**: Calls `SessionService.hasValidSession()` [client/lib/presentation/providers/auth_provider.dart L117](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L117-L117)  which retrieves the stored token and validates it's not expired using `JwtDecoder.isExpired()`
2. **API Validation**: If token exists and is not expired, makes a GET request to `/api/auth/me` [client/lib/presentation/providers/auth_provider.dart L121-L127](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L121-L127)  with the token in the Authorization header
3. **User Data Refresh**: On successful API response, updates `_currentUser` with fresh data from the backend [client/lib/presentation/providers/auth_provider.dart L129-L130](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L129-L130)
4. **Session Refresh**: Saves the updated session data [client/lib/presentation/providers/auth_provider.dart L135-L140](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L135-L140)
5. **State Notification**: Calls `notifyListeners()` to update all listening widgets [client/lib/presentation/providers/auth_provider.dart L141](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L141-L141)

This approach ensures that the client always has the latest user data and that backend-side changes (role modifications, account deactivation) are reflected immediately upon session validation.

## Logout Flow

The logout process clears all authentication state from both memory and persistent storage.

### Logout Implementation

```

```

**Sources:** [client/lib/presentation/providers/auth_provider.dart L107-L113](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L107-L113)

The `logout()` method [client/lib/presentation/providers/auth_provider.dart L107-L113](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L107-L113)

 is a straightforward two-step process:

1. **Clear Persistent Storage**: Calls `SessionService.clear()` to remove all stored authentication data
2. **Clear Memory State**: Sets all authentication-related properties to null/false
3. **Notify Listeners**: Calls `notifyListeners()` to trigger UI rebuild

After logout, the navigation guards detect the unauthenticated state and redirect the user to the login screen.

## Integration with Application

The `AuthProvider` is registered as a global provider in the app's root widget hierarchy, making authentication state accessible throughout the application.

### Provider Setup in main.dart

```

```

**Sources:** [client/lib/main.dart L13-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart#L13-L43)

The setup at [client/lib/main.dart L26-L42](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart#L26-L42)

 creates a provider hierarchy where:

1. **ThemeService and LanguageService**: Independent providers for UI preferences
2. **AuthProvider**: Core authentication state provider, created without dependencies
3. **LoanProvider**: Depends on `AuthProvider` via `ChangeNotifierProxyProvider` to access authentication state and tokens for API calls

The `AuthProvider` constructor is called during app initialization [client/lib/main.dart L31](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart#L31-L31)

 which triggers the automatic `checkSession()` call to validate any existing stored session.

### Authentication-Aware Navigation

The navigation system integrates with `AuthProvider` through route guards that check authentication state before allowing access to protected routes. The `NavigationService.router` configuration (referenced in Diagram 6 of the high-level overview) uses `AuthProvider.isAuthenticated` to determine route access and redirect logic.

**Sources:** [client/lib/main.dart L69](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart#L69-L69)