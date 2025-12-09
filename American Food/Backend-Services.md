# Backend Services

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document covers the PHP application layer that implements business logic, data validation, and database operations for the American Food restaurant management system. The backend services handle all server-side processing including user registration, data validation, security enforcement, and database interactions.

For database connection establishment and configuration loading, see [Configuration and Bootstrap](/axchisan/AmericanFood/3-configuration-and-bootstrap). For the database schema and table relationships, see [Database Layer](/axchisan/AmericanFood/2-database-layer). For role-based access control and authentication flow, see [User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control).

**Sources:** [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Backend Architecture Overview

The backend is organized as a collection of PHP scripts in the `crud/` directory that process HTTP requests, validate input, and perform database operations. All scripts follow a common architecture pattern:

| Layer | Responsibility | Implementation |
| --- | --- | --- |
| **Request Layer** | Receive HTTP requests | `$_GET`, `$_POST`, `$_SERVER` superglobals |
| **Validation Layer** | Sanitize and validate input | PHP filter functions, custom validation |
| **Business Logic Layer** | Process data and enforce rules | PHP functions and switch statements |
| **Data Access Layer** | Execute database operations | MySQLi prepared statements |
| **Response Layer** | Return results or redirect | `header()` redirects, `die()` with messages |

Currently, the only implemented backend service is user registration ([crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)

), but the architecture establishes patterns for future CRUD operations.

**Sources:** [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

---

## Request Processing Flow

```

```

**Sources:** [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

---

## User Registration Service

The user registration service is implemented in [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)

 and provides the primary example of backend service architecture.

### Endpoint Structure

**URL:** `crud/registro_INSERT.php?opciones=INS`
**Method:** `POST`
**Content-Type:** `application/x-www-form-urlencoded`

### Request Parameters

| Parameter | Type | Sanitization | Validation | Description |
| --- | --- | --- | --- | --- |
| `nombre` | string | `FILTER_SANITIZE_SPECIAL_CHARS` | Required, trimmed | User's first name |
| `apellido` | string | `FILTER_SANITIZE_SPECIAL_CHARS` | Required, trimmed | User's last name |
| `correo` | string | `FILTER_SANITIZE_EMAIL` | `FILTER_VALIDATE_EMAIL` | Email address (unique) |
| `telefono` | string | `FILTER_SANITIZE_SPECIAL_CHARS` | Required, trimmed | Phone number |
| `username` | string | `FILTER_SANITIZE_SPECIAL_CHARS` | Required, trimmed | Login username |
| `rol` | enum | `FILTER_SANITIZE_SPECIAL_CHARS` | Must be in `allowed_roles[]` | User role type |
| `password` | string | None | Must match `password2` | Plain password |
| `password2` | string | None | Must match `password` | Password confirmation |

### Operation Routing

The service uses a switch statement to route operations based on the `opciones` query parameter:

```
crud/registro_INSERT.php?opciones=INS  → User registration
crud/registro_INSERT.php?opciones=UPD  → Not implemented
crud/registro_INSERT.php?opciones=DEL  → Not implemented
```

Currently, only the `INS` (insert) operation is implemented at [crud/registro_INSERT.php L31-L61](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L31-L61)

 Other operations would follow the same pattern.

**Sources:** [crud/registro_INSERT.php L5-L9](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L9)

 [crud/registro_INSERT.php L30-L65](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L65)

---

## Input Validation Pipeline

```

```

### Sanitization Filters

The system applies PHP's built-in filter functions to prevent XSS attacks:

| Filter | Purpose | Applied To |
| --- | --- | --- |
| `FILTER_SANITIZE_SPECIAL_CHARS` | Escape HTML special characters (`<`, `>`, `&`, etc.) | `nombre`, `apellido`, `telefono`, `username`, `rol` |
| `FILTER_SANITIZE_EMAIL` | Remove invalid email characters | `correo` |
| `trim()` | Remove leading/trailing whitespace | All string fields |

Implementation at [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38)

### Validation Rules

After sanitization, the system enforces business rules:

1. **Email Format Validation:** Uses `FILTER_VALIDATE_EMAIL` to verify RFC 822 compliance ([crud/registro_INSERT.php L43-L45](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L45) )
2. **Password Matching:** Compares `password` and `password2` before hashing ([crud/registro_INSERT.php L47-L49](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L47-L49) )
3. **Role Whitelist:** Checks `$rol` against `$allowed_roles = ['admin', 'cajera', 'cocinero', 'cliente', 'mesera']` ([crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54) )

**Sources:** [crud/registro_INSERT.php L33-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L54)

---

## Database Operations Pattern

All database operations follow a secure prepared statement pattern to prevent SQL injection attacks.

### The insertarUsuario() Function

```

```

### Function Implementation Details

**Function Signature:**

```

```

**Parameters:**

* `$link` - MySQLi connection object from [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)
* `$query` - SQL query string with placeholders (`?`)
* `$params` - Array of parameter values to bind

**Process:**

1. **Prepare Statement** ([crud/registro_INSERT.php L11-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L14) ) * Creates a prepared statement from the SQL query * Separates query structure from data * Returns `$stmt` object or `false` on failure
2. **Bind Parameters** ([crud/registro_INSERT.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L16-L16) ) * Uses `str_repeat("s", count($params))` to generate type string (all strings) * Spreads `$params` array with `...` operator * Binds all parameters as strings (`"sssssss"` for 7 parameters)
3. **Execute Statement** ([crud/registro_INSERT.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L17-L17) ) * Executes the prepared statement * Returns boolean success status
4. **Clean Up Resources** ([crud/registro_INSERT.php L18-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L18-L19) ) * Closes statement handle with `mysqli_stmt_close($stmt)` * Closes database connection with `mysqli_close($link)` * Note: This closes the global `$link`, preventing further operations in the same request
5. **Handle Result** ([crud/registro_INSERT.php L21-L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L21-L27) ) * Success: Redirects to `../views/register.php?success=1` * Failure: Terminates with error message

**SQL Query:**

```

```

The query uses placeholders (`?`) that are replaced with sanitized parameters during execution.

**Sources:** [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

 [crud/registro_INSERT.php L57-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L59)

---

## Security Implementation

The backend implements multiple layers of security to protect against common web vulnerabilities.

### Security Measures Summary

| Vulnerability | Protection Mechanism | Implementation |
| --- | --- | --- |
| XSS (Cross-Site Scripting) | Input sanitization | `FILTER_SANITIZE_SPECIAL_CHARS`, `FILTER_SANITIZE_EMAIL` |
| SQL Injection | Prepared statements with parameter binding | `mysqli_prepare()`, `mysqli_stmt_bind_param()` |
| Password Exposure | Cryptographic hashing | `password_hash()` with `PASSWORD_DEFAULT` |
| Unauthorized Access | Role whitelist validation | `in_array($rol, $allowed_roles)` |
| CSRF (Cross-Site Request Forgery) | Not implemented | ⚠️ Future enhancement needed |

### Password Security

Passwords are never stored in plain text. The system uses PHP's `password_hash()` function:

```

```

**Hashing Details:**

* **Algorithm:** `PASSWORD_DEFAULT` (currently bcrypt)
* **Cost Factor:** PHP default (10 rounds)
* **Salt:** Automatically generated and embedded in hash
* **Output Format:** 60-character string (algorithm + cost + salt + hash)

Example hashed password from database:

```
$2y$12$Ws512WEdhL431bFJbbt3suGfH4fC2iOhyHAge7qaLNFOWuIrO56d6
```

Breakdown:

* `$2y$` - Bcrypt algorithm identifier
* `12$` - Cost factor (2^12 iterations)
* Next 22 chars - Base64-encoded salt
* Remaining chars - Base64-encoded hash

Implementation at [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

**Sources:** [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

 [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

### SQL Injection Prevention

The system prevents SQL injection through prepared statements with parameter binding:

**Vulnerable Pattern (NOT USED):**

```

```

**Secure Pattern (IMPLEMENTED):**

```

```

This approach ensures that user input is treated as data, never as SQL code, eliminating the risk of SQL injection attacks.

**Sources:** [crud/registro_INSERT.php L10-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L19)

 [crud/registro_INSERT.php L57-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L59)

### Role-Based Access Control

The registration service enforces role validation to prevent privilege escalation:

```

```

This whitelist approach ensures only valid roles can be assigned during registration. The roles correspond to the enum values defined in the database schema at [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

**Sources:** [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

---

## Error Handling Strategy

The backend implements a dual-layer error handling system combining exception handling with explicit error checks.

### Exception Handling Wrapper

All request processing is wrapped in try-catch blocks:

```

```

### Error Handling Layers

**Layer 1: Pre-validation Checks** ([crud/registro_INSERT.php L5-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L7)

)

* Verifies `$_GET['opciones']` is set
* Terminates with warning message if missing
* Prevents execution without proper action parameter

**Layer 2: Validation Errors** ([crud/registro_INSERT.php L43-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L54)

)

* Email format validation failure
* Password mismatch
* Invalid role selection
* Each terminates with `die()` and user-friendly Spanish message

**Layer 3: Database Operation Errors** ([crud/registro_INSERT.php L12-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L12-L14)

 [crud/registro_INSERT.php L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L26-L26)

)

* Statement preparation failure
* Query execution failure
* Terminates with technical error message including `mysqli_error()`

**Layer 4: Exception/Error Catching** ([crud/registro_INSERT.php L66-L72](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L66-L72)

)

* Catches `Exception` (runtime exceptions)
* Catches `Error` (PHP 7+ errors like type errors, parse errors)
* Logs detailed error to server logs with `error_log()`
* Returns generic user-friendly message to client

### Error Messages

| Type | Audience | Language | Example |
| --- | --- | --- | --- |
| Validation | End User | Spanish | "Error: Correo electrónico no válido." |
| Database | End User | Spanish | "Error insertando el contenido: [technical details]" |
| System | End User | Spanish | "Ha ocurrido un error. Estamos trabajando en corregir esta situación." |
| Technical | Server Logs | Spanish | "Excepción no controlada: [stack trace]" |

**Sources:** [crud/registro_INSERT.php L5-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L7)

 [crud/registro_INSERT.php L43-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L54)

 [crud/registro_INSERT.php L66-L72](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L66-L72)

---

## Connection Management Integration

Backend services depend on the database connection established by [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)

 (documented in [Configuration and Bootstrap](/axchisan/AmericanFood/3-configuration-and-bootstrap)).

### Connection Usage Pattern

```

```

### Key Integration Points

1. **Dependency Loading** ([crud/registro_INSERT.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L2-L2) ) * Uses `require_once` to include connection manager * Ensures `$link` is available before any operations * Inherits session management from connection script
2. **Global $link Variable** * Connection object created at [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8) * Passed as parameter to database functions * Available throughout script execution
3. **Character Encoding** * UTF-8 encoding set at [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17) * Ensures proper handling of Spanish characters * Prevents encoding issues with names like "José", "María"
4. **Connection Closure** * Backend services close connection after operations ([crud/registro_INSERT.php L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L19-L19) ) * Prevents connection leaks * Note: This makes `$link` unavailable for subsequent operations in the same request

**Sources:** [crud/registro_INSERT.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L2-L2)

 [config/conexionDB.php L2-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L17)

---

## Future Backend Services

The current implementation establishes patterns for future CRUD operations. Based on the database schema and role-specific interfaces, the following backend services are anticipated:

### Planned Service Categories

| Category | Planned Services | Target Tables | User Roles |
| --- | --- | --- | --- |
| **Authentication** | Login, logout, session management | `usuarios` | All roles |
| **Menu Management** | Add/edit/delete menu items, availability toggle | `menu` | `admin` |
| **Order Processing** | Create order, add items, update status | `pedidos`, `detalles_pedidos` | `cliente`, `mesera`, `cocinero` |
| **Table Management** | Assign/release tables, update status | `mesas` | `mesera` |
| **Payment Processing** | Record payment, calculate change, generate invoice | `pagos`, `facturas` | `cajera` |
| **Inventory Management** | Update stock, set minimums, record suppliers | `inventario` | `admin` |
| **Delivery Management** | Record delivery information | `informacion_entrega` | `cliente` |
| **Reporting** | Sales reports, order history, inventory alerts | All tables | `admin`, `cajera` |

### Anticipated CRUD Operations

Based on the switch statement pattern at [crud/registro_INSERT.php L30](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L30)

 future operations would follow:

```sql
?opciones=INS  → Insert new record
?opciones=UPD  → Update existing record
?opciones=DEL  → Delete record (soft or hard delete)
?opciones=SEL  → Select/query records
```

### Architectural Recommendations

1. **Separate Files by Entity:** Create dedicated files for each entity (e.g., `crud/menu_CRUD.php`, `crud/pedidos_CRUD.php`)
2. **RESTful Patterns:** Consider mapping operations to HTTP verbs (POST=create, PUT=update, DELETE=delete, GET=select)
3. **Response Format:** Standardize JSON responses for AJAX operations
4. **Authentication Middleware:** Add session validation before processing requests
5. **CSRF Protection:** Implement token-based CSRF protection for state-changing operations
6. **API Documentation:** Document each endpoint's parameters, responses, and error codes

**Sources:** [crud/registro_INSERT.php L30-L65](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L65)

 [database/american_food L30-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L483)

---

## Request/Response Flow Summary

```

```

**Sources:** [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L2-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L17)