# Authentication Endpoints

> **Relevant source files**
> * [app/api/solicitudes/route.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts)
> * [app/login/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx)
> * [components/features.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/features.tsx)
> * [components/login-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx)

## Purpose and Scope

This document describes the authentication and authorization API endpoints in the SENA Gestión Complementarias system. These endpoints handle user login, token generation, JWT verification, and session management. The authentication system uses JWT tokens for stateless authentication and integrates with the role-based access control (RBAC) system.

For information about the overall authentication architecture and security model, see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization). For information about the authentication UI components, see [Authentication UI](/axchisan/gestionComplementarias/5.2-authentication-ui).

## Authentication System Overview

The authentication system consists of two primary components:

1. **Authentication Endpoints**: API routes that handle login, registration, and token generation
2. **Authorization Middleware**: The `withAuth` higher-order function that protects API routes and validates JWT tokens

The system uses JWT (JSON Web Token) for stateless authentication, where tokens contain user identity and role information. All protected API endpoints are wrapped with the `withAuth` middleware that validates tokens on each request.

**Sources:** [High-level architecture diagrams - Diagram 6]

## Authentication Flow Architecture

```

```

**Sources:** [components/login-form.tsx L37-L56](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L37-L56)

 [High-level architecture diagrams - Diagram 6]

## POST /api/auth/login

The login endpoint authenticates users and returns a JWT token for subsequent API requests.

### Request Format

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `email` | string | Yes | User's institutional email address |
| `password` | string | Yes | Plain-text password (hashed on server) |
| `rememberMe` | boolean | No | Flag for extended session duration |

**Example Request:**

```

```

### Response Format - Success

**HTTP Status:** `200 OK`

| Field | Type | Description |
| --- | --- | --- |
| `success` | boolean | Always `true` for successful authentication |
| `token` | string | JWT token containing user identity and role |
| `user` | object | User information object |
| `user.id` | string | User unique identifier (UUID) |
| `user.email` | string | User email address |
| `user.name` | string | User full name |
| `user.role` | enum | User role: `INSTRUCTOR`, `COORDINADOR`, or `ADMIN` |
| `user.centroId` | string | UUID of the centro the user belongs to |
| `user.especialidad` | string | Instructor's area of expertise (nullable) |

**Example Response:**

```

```

### Response Format - Error

**HTTP Status:** `401 Unauthorized` (invalid credentials) or `500 Internal Server Error`

```

```

### JWT Token Payload

The JWT token contains the following claims:

```

```

The token is signed using the `JWT_SECRET` environment variable and uses the HS256 algorithm.

**Sources:** [components/login-form.tsx L37-L56](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L37-L56)

 [High-level architecture diagrams - Diagram 6]

## withAuth Middleware

The `withAuth` middleware is a higher-order function that wraps API route handlers to enforce authentication and inject user context.

### Middleware Flow

```mermaid
flowchart TD

Start["API Request Received"]
ExtractToken["Extract token from<br>Authorization header"]
CheckToken["Token<br>present?"]
VerifyToken["jwt.verify(token, JWT_SECRET)"]
TokenValid["Token<br>valid?"]
InjectUser["Inject user context:<br>{userId, role, centroId}"]
ExecuteHandler["Execute wrapped<br>handler function"]
Return401["Return 401<br>Unauthorized"]
ReturnResponse["Return handler<br>response"]

Start --> ExtractToken
ExtractToken --> CheckToken
CheckToken --> Return401
CheckToken --> VerifyToken
VerifyToken --> TokenValid
TokenValid --> Return401
TokenValid --> InjectUser
InjectUser --> ExecuteHandler
ExecuteHandler --> ReturnResponse
```

### Usage Pattern

Protected API routes wrap their handlers with `withAuth`:

```

```

**Sources:** [app/api/solicitudes/route.ts L1-L71](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L1-L71)

### User Context Object

The `user` parameter injected by `withAuth` contains:

| Field | Type | Description |
| --- | --- | --- |
| `userId` | string | User UUID extracted from JWT token |
| `role` | string | User role: `INSTRUCTOR`, `COORDINADOR`, or `ADMIN` |
| `centroId` | string | Centro UUID for data scoping |

**Sources:** [app/api/solicitudes/route.ts L6-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L22)

## Role-Based Data Filtering

The authentication system enforces role-based access control at the API layer by applying different data filters based on user roles.

```mermaid
flowchart TD

Request["Authenticated Request<br>{userId, role, centroId}"]
RoleCheck["User Role?"]
InstructorFilter["INSTRUCTOR Filter<br>where: {instructorId: user.userId}"]
CoordinadorFilter["COORDINADOR Filter<br>where: {programa: {centroId: user.centroId}}"]
AdminFilter["ADMIN Filter<br>No filtering (full access)"]
Database["Prisma Query<br>prisma.solicitud.findMany()"]
Response["Filtered Results"]

Request --> RoleCheck
InstructorFilter --> Database
CoordinadorFilter --> Database
AdminFilter --> Database
Database --> Response

subgraph subGraph0 ["Role-Based Filtering"]
    RoleCheck
    InstructorFilter
    CoordinadorFilter
    AdminFilter
    RoleCheck --> InstructorFilter
    RoleCheck --> CoordinadorFilter
    RoleCheck --> AdminFilter
end
```

### Filtering Implementation Examples

**INSTRUCTOR Role:**

```

```

**COORDINADOR Role:**

```

```

**ADMIN Role:**

```

```

**Sources:** [app/api/solicitudes/route.ts L15-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L15-L22)

## Authorization Header Format

Protected API endpoints require the JWT token to be sent in the `Authorization` header using the Bearer authentication scheme.

**Format:**

```yaml
Authorization: Bearer <jwt_token>
```

**Example:**

```yaml
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjNlNDU2Ny1lODliLTEyZDMtYTQ1Ni00MjY2MTQxNzQwMDAiLCJyb2xlIjoiSU5TVFJVQ1RPUiIsImNlbnRyb0lkIjoiNTUwZTg0MDAtZTI5Yi00MWQ0LWE3MTYtNDQ2NjU1NDQwMDAwIiwiaWF0IjoxNzA2MjA4MDAwLCJleHAiOjE3MDYyOTQ0MDB9.signature
```

The client-side implementation stores the token after login and includes it in all subsequent API requests.

**Sources:** [components/login-form.tsx L43-L50](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L43-L50)

## Client-Side Authentication Flow

The client-side authentication is managed through the `useAuth()` context hook, which provides authentication state and functions to all React components.

```mermaid
flowchart TD

AuthContext["AuthContext Provider<br>(lib/auth-context)"]
Token["JWT Token<br>(memory storage)"]
User["User Object<br>{id, email, name, role, centroId}"]
IsAuth["isAuthenticated: boolean"]
Loading["isLoading: boolean"]
LoginFn["login(email, password, rememberMe)"]
LogoutFn["logout()"]
CheckAuthFn["checkAuth()"]
Components["React Components<br>(LoginForm, Navigation, etc)"]
API["API Endpoints<br>/api/auth/, /api/solicitudes/"]

Components --> AuthContext
LoginFn --> API
API --> Token

subgraph subGraph2 ["Client-Side Authentication"]
    AuthContext
    AuthContext --> LoginFn
    AuthContext --> LogoutFn

subgraph Functions ["Functions"]
    LoginFn
    LogoutFn
    CheckAuthFn
end

subgraph subGraph0 ["State Management"]
    Token
    User
    IsAuth
    Loading
    Token --> IsAuth
    User --> IsAuth
end
end
```

### useAuth Hook Interface

The `useAuth()` hook exposes the following interface:

| Property/Method | Type | Description |
| --- | --- | --- |
| `user` | object \| null | Current user object or null if not authenticated |
| `isAuthenticated` | boolean | Whether user is currently authenticated |
| `isLoading` | boolean | Whether authentication check is in progress |
| `login(email, password, rememberMe)` | function | Authenticate user and store token |
| `logout()` | function | Clear token and user state |

**Usage Example:**

```

```

**Sources:** [components/login-form.tsx L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L22-L22)

 [components/features.tsx L43-L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/features.tsx#L43-L52)

## Error Responses

All authentication endpoints follow a consistent error response format.

### Common Error Codes

| Status Code | Scenario | Response Body |
| --- | --- | --- |
| `400 Bad Request` | Missing required fields | `{"success": false, "error": "Email and password are required"}` |
| `401 Unauthorized` | Invalid credentials | `{"success": false, "error": "Credenciales incorrectas..."}` |
| `401 Unauthorized` | Token expired or invalid | `{"error": "Token expired or invalid"}` |
| `403 Forbidden` | Insufficient role permissions | `{"error": "Solo los instructores pueden crear solicitudes"}` |
| `500 Internal Server Error` | Server-side error | `{"error": "Error interno del servidor"}` |

### Token Expiration Handling

When a JWT token expires, the middleware returns a `401 Unauthorized` response. The client-side implementation should:

1. Detect the 401 status code
2. Clear the stored token and user state
3. Redirect the user to the login page
4. Display a message indicating the session has expired

**Sources:** [components/login-form.tsx L49-L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L49-L52)

 [app/api/solicitudes/route.ts L75-L76](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L75-L76)

## Security Considerations

### Password Security

* Passwords are hashed using `bcryptjs` with 10 salt rounds before storage
* Plain-text passwords are never stored in the database
* Password comparison uses constant-time algorithm to prevent timing attacks

### Token Security

* JWT tokens are signed with `JWT_SECRET` environment variable
* Tokens use HS256 (HMAC with SHA-256) signing algorithm
* Token expiration is enforced to limit session duration
* Tokens are stored in memory on the client (not localStorage by default)

### HTTPS Enforcement

* The system is configured to run behind SSL/TLS encryption
* Docker compose configuration includes SSL certificate mounting
* Security headers are configured in `next.config.ts`

### CORS Configuration

* CORS is configured to allow requests only from authorized origins
* Credentials are allowed for cookie-based authentication scenarios
* Preflight requests are handled appropriately

**Sources:** [components/login-form.tsx L164-L176](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L164-L176)

 [High-level architecture diagrams - Diagram 6]

## Integration with Protected Routes

The authentication system integrates with all protected API routes through the `withAuth` middleware pattern.

### Protected Route Example

```mermaid
flowchart TD

Client["Client Request<br>GET /api/solicitudes"]
Middleware["withAuth Middleware<br>1. Verify token<br>2. Extract user<br>3. Inject context"]
Handler["Route Handler<br>async (req, user) => {...}"]
RoleFilter["Apply Role-Based<br>Filtering"]
Database["Query Database"]
Response["Return Filtered<br>Results"]

Client --> Middleware
Middleware --> Handler
Handler --> RoleFilter
RoleFilter --> Database
Database --> Response
```

All API routes under `/api/solicitudes`, `/api/instructores`, `/api/notificaciones`, and other protected endpoints use this pattern to ensure:

1. User is authenticated (valid JWT token)
2. User context is available in the handler
3. Role-based data filtering is applied
4. Unauthorized requests are rejected with 401 status

**Sources:** [app/api/solicitudes/route.ts L6](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L6)

 [app/api/solicitudes/route.ts L73](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L73-L73)

## POST Endpoint Authorization

The `withAuth` middleware not only validates tokens but also enforces role-based authorization for write operations.

### Example: Solicitud Creation

Only users with the `INSTRUCTOR` role can create solicitudes:

```

```

This pattern ensures that:

* Authentication is verified (via `withAuth`)
* Role-based authorization is enforced (via explicit role check)
* Proper HTTP status codes are returned (403 Forbidden for authorization failures)

**Sources:** [app/api/solicitudes/route.ts L73-L77](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L73-L77)