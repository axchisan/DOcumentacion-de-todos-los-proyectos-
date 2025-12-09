# Authentication Flow

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)
> * [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css)

## Purpose and Scope

This document details the authentication mechanisms in the American Food system, including user registration, login processes, session management, and password security. It covers how users are authenticated, how their roles are assigned during registration, and how sessions are maintained throughout the application lifecycle.

For information about the specific permissions and responsibilities of each role, see [Role Definitions](/axchisan/AmericanFood/7.1-role-definitions). For details about the role-specific user interfaces that users access after authentication, see [Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system).

---

## Session Management Architecture

The application uses PHP sessions to maintain user state across requests. Session initialization occurs at the earliest possible point in the request lifecycle to ensure session data is available to all application components.

### Session Initialization Process

Session management is handled by [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6)

 which ensures a session exists before establishing database connections:

```

```

This pattern checks whether a session is already active using `session_status()` before calling `session_start()`, preventing "session already started" warnings when the connection manager is included multiple times.

**Key Session Variables:**

| Variable | Purpose | Set By | Used By |
| --- | --- | --- | --- |
| `$_SESSION['MensajeTexto']` | User-facing error messages | conexionDB.php | UI components |
| `$_SESSION['MensajeTipo']` | Bootstrap CSS classes for alerts | conexionDB.php | UI components |
| `$_SESSION['user_id']` | Current user identifier | Login script (implied) | Authorization checks |
| `$_SESSION['rol']` | User's role (admin/cajera/cocinero/mesera/cliente) | Login script (implied) | Interface routing |

**Session Initialization Sequence Diagram:**

```

```

**Sources:** [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6)

---

## User Registration Flow

User registration is the primary entry point for new users to create accounts. The system implements a multi-layer validation and security pipeline to ensure data integrity and protect against common vulnerabilities.

### Registration Request Processing

The registration endpoint [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)

 handles POST requests with the following parameters:

| Parameter | Validation Method | Purpose |
| --- | --- | --- |
| `nombre` | FILTER_SANITIZE_SPECIAL_CHARS | User's first name |
| `apellido` | FILTER_SANITIZE_SPECIAL_CHARS | User's last name |
| `correo` | FILTER_SANITIZE_EMAIL + FILTER_VALIDATE_EMAIL | Email address (must be valid format) |
| `telefono` | FILTER_SANITIZE_SPECIAL_CHARS | Phone number |
| `username` | FILTER_SANITIZE_SPECIAL_CHARS | Login username |
| `rol` | Role whitelist validation | One of: admin, cajera, cocinero, cliente, mesera |
| `password` | Password match validation + hashing | User password (min requirements not enforced in code) |
| `password2` | Equality check with password | Password confirmation |

### Registration Security Pipeline

**Complete Registration Flow Diagram:**

```

```

**Sources:** [crud/registro_INSERT.php L30-L61](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L61)

### Input Sanitization Layer

All user inputs pass through PHP's filter functions before processing [crud/registro_INSERT.php L33-L39](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L39)

:

```

```

This sanitization prevents XSS attacks by encoding special HTML characters (`<`, `>`, `&`, `"`, `'`) and removes dangerous characters from email addresses.

**Sources:** [crud/registro_INSERT.php L33-L39](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L39)

### Email Validation

After sanitization, email addresses undergo format validation [crud/registro_INSERT.php L43-L45](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L45)

:

```

```

This ensures the email conforms to RFC 5322 standards before insertion into the database.

**Sources:** [crud/registro_INSERT.php L43-L45](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L45)

### Password Security Implementation

**Password Confirmation:**

The system requires users to enter their password twice, verifying equality before proceeding [crud/registro_INSERT.php L47-L49](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L47-L49)

:

```

```

**Password Hashing:**

Passwords are never stored in plain text. The system uses PHP's `password_hash()` function with `PASSWORD_DEFAULT` [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

:

```

```

`PASSWORD_DEFAULT` currently uses the bcrypt algorithm (as of PHP 7.0+), which automatically generates a salt and includes algorithm versioning for future-proof upgrades.

**Password Hashing Flow Diagram:**

```

```

**Sources:** [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

### Role Validation

Users must select a valid role during registration. The system enforces a whitelist of allowed roles [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54)

:

```

```

Any attempt to submit a role outside this whitelist results in immediate rejection. This prevents privilege escalation attacks where malicious users might attempt to inject unauthorized roles.

**Sources:** [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54)

### SQL Injection Prevention

The registration system uses prepared statements with parameter binding [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

:

**Prepared Statement Implementation:**

```

```

The query template uses parameterized placeholders [crud/registro_INSERT.php L57-L58](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L58)

:

```

```

All seven parameters are bound as strings using `mysqli_stmt_bind_param()` [crud/registro_INSERT.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L16-L16)

 preventing SQL injection regardless of input content.

**Sources:** [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

 [crud/registro_INSERT.php L57-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L59)

### Post-Registration Redirect

Upon successful registration, the system redirects users to the registration success page [crud/registro_INSERT.php L23-L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L23-L24)

:

```

```

The `success=1` query parameter allows the registration page to display a confirmation message. The `exit()` call ensures no further code executes after the redirect header is sent.

**Sources:** [crud/registro_INSERT.php L23-L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L23-L24)

---

## Login Flow (Interface Implementation)

While the backend login processing logic is not visible in the provided codebase, the frontend implementation in [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css)

 reveals the structure of the authentication interface.

### Login Form Components

**Component Hierarchy:**

```

```

**Sources:** [css/login.css L1-L112](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L1-L112)

### Registration Form Components

The registration interface extends the login interface with additional components [css/login.css L18-L20](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L18-L20)

:

**Registration-Specific Elements:**

| Class | Purpose | Styling |
| --- | --- | --- |
| `.register-card` | Registration form container | Max-width: 550px (wider than login) |
| `.role-options` | Grid container for role selection | CSS Grid with auto-fill columns |
| `.role-option` | Individual role selection card | Border, hover effects, selectable state |
| `.password-strength` | Password strength indicator | Progress bar with visual feedback |

### Role Selection Interface

During registration, users select their role through an interactive grid system [css/login.css L114-L154](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L114-L154)

:

**Role Selection Component Structure:**

```

```

**Role Selection Styling Rules [css/login.css L114-L154](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L114-L154)

:**

* **Grid Layout**: `grid-template-columns: repeat(auto-fill, minmax(200px, 1fr))` creates responsive columns
* **Hover State**: Border changes to `#457b9d` (blue) with subtle background tint
* **Selected State**: Border changes to `#e63946` (red) with red background tint
* **Icon Size**: `font-size: 2rem` for visual prominence
* **Mobile**: Single column layout on screens < 576px

**Sources:** [css/login.css L114-L154](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L114-L154)

 [css/login.css L178-L180](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L178-L180)

### Password Strength Indicator

The registration form includes visual feedback for password strength [css/login.css L157-L163](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L157-L163)

:

```

```

This component likely uses JavaScript to analyze password complexity and update a Bootstrap progress bar, providing real-time feedback as users type.

**Sources:** [css/login.css L157-L163](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L157-L163)

---

## Authentication State Management

### Session Persistence

Once authenticated, user information is stored in the PHP session and persists across page requests. The session remains active until:

1. The user explicitly logs out
2. The session expires due to inactivity (PHP default: 24 minutes)
3. The user closes their browser (if cookies are session-based)
4. The server restarts (sessions are typically file-based)

### Error Message System

Authentication failures and validation errors are communicated through the session-based message system [config/conexionDB.php L11-L12](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L11-L12)

:

```

```

This pattern separates error handling from the response output:

* **MensajeTexto**: Human-readable error message (Spanish)
* **MensajeTipo**: Bootstrap CSS classes for alert styling (bg-warning, bg-danger, etc.)

The UI layer reads these session variables and displays formatted alerts to users.

**Sources:** [config/conexionDB.php L11-L12](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L11-L12)

---

## Security Considerations

### Implemented Protections

| Attack Vector | Protection Mechanism | Implementation |
| --- | --- | --- |
| SQL Injection | Prepared statements with parameter binding | [crud/registro_INSERT.php L11-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L18) |
| XSS | Input sanitization with FILTER_SANITIZE_* | [crud/registro_INSERT.php L33-L39](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L39) |
| Password theft | bcrypt hashing with automatic salting | [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56) |
| Privilege escalation | Role whitelist validation | [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54) |
| CSRF | (Not visible in code) | Likely implemented in form generation |
| Brute force | (Not visible in code) | No rate limiting observed |

### Known Limitations

**Password Policy:**

The current implementation does not enforce minimum password requirements. While password confirmation is required [crud/registro_INSERT.php L47-L49](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L47-L49)

 there are no checks for:

* Minimum length
* Character complexity (uppercase, lowercase, numbers, symbols)
* Common password blacklist
* Password reuse prevention

The frontend includes a password strength indicator [css/login.css L157-L163](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L157-L163)

 but this appears to be advisory rather than enforced.

**Session Security:**

The codebase does not show:

* Session regeneration after login (prevents session fixation)
* HttpOnly flag on session cookies
* Secure flag for HTTPS-only cookies
* Session timeout configuration
* IP address binding to prevent session hijacking

**Login Attempt Tracking:**

No mechanism is visible for:

* Failed login attempt counting
* Account lockout after repeated failures
* CAPTCHA after multiple failed attempts
* Suspicious activity logging

**Sources:** [crud/registro_INSERT.php L32-L60](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L32-L60)

 [css/login.css L157-L163](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L157-L163)

---

## Integration with Role-Based Access Control

After successful authentication, the system routes users to role-specific interfaces based on their `rol` field in the `usuarios` table. The authentication layer sets `$_SESSION['rol']`, which determines:

1. **Interface Selection**: Which CSS file loads (cliente.css, cajera.css, cocina.css, mesera.css)
2. **Feature Access**: What operations the user can perform
3. **Data Visibility**: Which database records the user can query

The mapping between roles and interfaces is:

| Role | Interface File | Primary Functions |
| --- | --- | --- |
| `admin` | style.css | System-wide management |
| `cajera` | cajera.css | Payment processing, invoices |
| `cocinero` | cocina.css | Order preparation tracking |
| `mesera` | mesera.css | Table management, order taking |
| `cliente` | cliente.css | Menu browsing, online ordering |

For detailed information on role-specific permissions and workflows, see [Role Definitions](/axchisan/AmericanFood/7.1-role-definitions) and [Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system).

**Sources:** [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 Inferred from system architecture

---

## Request Flow Summary

**Complete Authentication Request Flow:**

```

```

**Sources:** [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)