# User Registration System

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

The User Registration System handles the creation of new user accounts in the American Food restaurant management system. This document covers the input validation pipeline, security measures, password hashing, role assignment, and database persistence implemented in [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)

For information about how registered users authenticate and access role-specific interfaces, see [Authentication Flow](/axchisan/AmericanFood/7.2-authentication-flow). For details about the five user roles and their permissions, see [Role Definitions](/axchisan/AmericanFood/7.1-role-definitions). For database connection management, see [Connection Manager](/axchisan/AmericanFood/3.2-connection-manager).

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

---

## Registration Request Flow

The following diagram illustrates the complete lifecycle of a registration request from HTTP entry point through database persistence:

```

```

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## HTTP Request Requirements

The registration endpoint requires specific HTTP parameters to process a registration request:

| Parameter | Method | Required | Purpose |
| --- | --- | --- | --- |
| `opciones` | GET | Yes | Must equal `'INS'` to trigger insert operation |
| `ingresar` | POST | Yes | Submit button indicator |
| `nombre` | POST | Yes | User's first name |
| `apellido` | POST | Yes | User's last name |
| `correo` | POST | Yes | User's email address (unique) |
| `telefono` | POST | Yes | User's phone number |
| `username` | POST | Yes | User's login username |
| `rol` | POST | Yes | User role: admin, cajera, cocinero, cliente, mesera |
| `password` | POST | Yes | User's password (plain text) |
| `password2` | POST | Yes | Password confirmation |

**Request Validation Logic**:

* [crud/registro_INSERT.php L5-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L7)  checks for `$_GET['opciones']` presence
* [crud/registro_INSERT.php L30-L32](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L32)  validates switch case matches `'INS'`
* [crud/registro_INSERT.php L32](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L32-L32)  verifies `$_SERVER['REQUEST_METHOD'] === 'POST'`
* [crud/registro_INSERT.php L32](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L32-L32)  confirms `isset($_POST['ingresar'])`

**Sources**: [crud/registro_INSERT.php L5-L32](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L32)

---

## Input Sanitization Pipeline

The system implements a multi-stage sanitization process using PHP's built-in filter functions:

```

```

### Sanitization Rules

| Field | Filter | Purpose |
| --- | --- | --- |
| `nombre` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `apellido` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `correo` | `FILTER_SANITIZE_EMAIL` | Remove invalid email characters |
| `telefono` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `username` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `rol` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `password` | None | Preserved as-is for hashing |
| `password2` | None | Preserved as-is for comparison |

All sanitized fields are additionally processed with `trim()` to remove leading/trailing whitespace at [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38)

**Sources**: [crud/registro_INSERT.php L33-L40](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L40)

---

## Validation Layer

After sanitization, the system applies three validation checks that can terminate the registration process:

```

```

### Email Validation

The system validates email format using `FILTER_VALIDATE_EMAIL` at [crud/registro_INSERT.php L43-L45](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L45)

 This filter checks for valid email structure according to RFC 822.

**Error Message**: `"Error: Correo electrónico no válido."`

### Password Confirmation

Passwords are compared using strict equality (`!==`) at [crud/registro_INSERT.php L47-L49](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L47-L49)

 to ensure the user typed their password correctly twice.

**Error Message**: `"Error: Las contraseñas no coinciden."`

### Role Validation

The system maintains a whitelist of allowed roles at [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

:

```

```

The submitted `rol` value must exist in this array, validated using `in_array()` at [crud/registro_INSERT.php L52-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L52-L54)

 This prevents unauthorized role assignments.

**Error Message**: `"Error: Rol no válido."`

**Sources**: [crud/registro_INSERT.php L43-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L54)

---

## Password Security

### Hashing Algorithm

The system uses PHP's `password_hash()` function with the `PASSWORD_DEFAULT` algorithm at [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

:

```

```

As of PHP 8.2.4 (the version specified in [database/american_food L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L8-L8)

), `PASSWORD_DEFAULT` uses **bcrypt** with the following characteristics:

| Property | Value |
| --- | --- |
| Algorithm | bcrypt |
| Cost Factor | 12 (based on sample data) |
| Salt | Automatically generated (random) |
| Output Format | 60-character string |
| Prefix | `$2y$12$` |

### Hash Storage

The hashed password is stored in the `usuarios.contraseña` field, which is defined as `VARCHAR(255)` in [database/american_food L295](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L295-L295)

 This provides sufficient length for bcrypt hashes and allows for future algorithm upgrades.

### Sample Hash Analysis

Examining existing users in [database/american_food L304-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L304-L307)

:

| User | Hash Prefix | Cost |
| --- | --- | --- |
| cocina | `$2y$12$` | 12 |
| mesera | `$2y$12$` | 12 |
| cajera | `$2y$12$` | 12 |
| usuario | `$2y$12$` | 12 |

All passwords use cost factor 12, indicating consistent hashing configuration.

**Sources**: [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

 [database/american_food L295](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L295-L295)

 [database/american_food L304-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L304-L307)

---

## Database Interaction

### Prepared Statement Architecture

The system uses parameterized queries to prevent SQL injection. The `insertarUsuario()` function at [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

 encapsulates this logic:

```

```

### SQL Query Structure

The INSERT statement at [crud/registro_INSERT.php L57-L58](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L58)

 defines the database operation:

```

```

### Parameter Binding

The system uses `mysqli_stmt_bind_param()` at [crud/registro_INSERT.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L16-L16)

 with dynamic type string generation:

```

```

Since all 7 parameters are strings (including the hashed password), this generates type string `"sssssss"`.

### Parameter Order

| Position | Field | Source Variable |
| --- | --- | --- |
| 1 | `nombre` | `$nombre` |
| 2 | `apellido` | `$apellido` |
| 3 | `correo` | `$correo` |
| 4 | `telefono` | `$telefono` |
| 5 | `username` | `$username` |
| 6 | `rol` | `$rol` |
| 7 | `contraseña` | `$password_hashed` |

**Sources**: [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

 [crud/registro_INSERT.php L57-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L59)

---

## usuarios Table Schema

The registration system writes to the `usuarios` table with the following structure:

| Column | Type | Constraints | Purpose |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique user identifier |
| `nombre` | VARCHAR(100) | NOT NULL | First name |
| `apellido` | VARCHAR(100) | NOT NULL | Last name |
| `correo` | VARCHAR(100) | NOT NULL, UNIQUE | Email (login credential) |
| `telefono` | VARCHAR(20) | NULL | Contact phone number |
| `username` | VARCHAR(100) | NOT NULL | Login username |
| `rol` | ENUM | NOT NULL | One of: admin, cajera, cocinero, cliente, mesera |
| `contraseña` | VARCHAR(255) | NOT NULL | Bcrypt hash |
| `estado` | ENUM | DEFAULT 'activo' | Account status: activo, inactivo |

### Key Constraints

* **Primary Key**: `id` with `AUTO_INCREMENT` at [database/american_food L380](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L380-L380)
* **Unique Constraint**: `correo` field at [database/american_food L381](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L381-L381)  ensures one account per email
* **Index**: `idx_usuarios_rol` on `rol` field at [database/american_food L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L382-L382)  for role-based queries

### Default Values

The `estado` field defaults to `'activo'` at [database/american_food L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L296-L296)

 meaning new accounts are immediately active without administrator approval.

**Sources**: [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

 [database/american_food L377-L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L377-L382)

---

## Error Handling Strategy

The system implements fail-fast error handling with immediate termination on validation failures:

```

```

### Error Categories

| Error Type | Trigger | Message | Line |
| --- | --- | --- | --- |
| Missing Parameter | No `$_GET['opciones']` | "Advertencia: Acción no permitida." | [5-7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/5-7) |
| Invalid Action | `opciones` not in switch | "Advertencia: No se pudo identificar la acción a realizar." | [64](https://github.com/axchisan/AmericanFood/blob/67fea4d3/64) |
| Invalid Email | `FILTER_VALIDATE_EMAIL` fails | "Error: Correo electrónico no válido." | [44](https://github.com/axchisan/AmericanFood/blob/67fea4d3/44) |
| Password Mismatch | `password !== password2` | "Error: Las contraseñas no coinciden." | [48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/48) |
| Invalid Role | Role not in whitelist | "Error: Rol no válido." | [53](https://github.com/axchisan/AmericanFood/blob/67fea4d3/53) |
| Prepare Error | `mysqli_prepare()` returns false | "Error preparando la consulta: " + mysqli_error | [13](https://github.com/axchisan/AmericanFood/blob/67fea4d3/13) |
| Execute Error | `mysqli_stmt_execute()` returns false | "Error insertando el contenido: " + mysqli_error | [26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/26) |

### Exception Handling

A top-level try-catch block at [crud/registro_INSERT.php L4-L72](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L4-L72)

 catches any unhandled exceptions:

```

```

This provides a safety net for unexpected errors while logging details for debugging.

**Sources**: [crud/registro_INSERT.php L4-L72](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L4-L72)

---

## Success Response Flow

Upon successful registration, the system performs the following actions:

```

```

### Resource Cleanup

The function explicitly closes database resources at [crud/registro_INSERT.php L18-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L18-L19)

:

1. `mysqli_stmt_close($stmt)` - Closes the prepared statement
2. `mysqli_close($link)` - Closes the database connection

This ensures no connection leaks occur.

### HTTP Redirect

At [crud/registro_INSERT.php L23-L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L23-L24)

 the system redirects to the registration form with a success indicator:

```

```

The `success=1` query parameter allows the registration form to display a confirmation message to the user.

**Sources**: [crud/registro_INSERT.php L18-L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L18-L24)

---

## Integration Points

### Database Connection Dependency

The registration system requires the database connection established by [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)

:

```

```

The global `$link` variable is established by [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

 and used by registration at [crud/registro_INSERT.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L2-L2)

### Related Systems

* **Authentication System**: Registered users can log in using their `correo` and `contraseña` fields
* **Role-Based Access Control**: The `rol` field determines which interface the user accesses (see [Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system))
* **Foreign Key References**: The `usuarios.id` field is referenced by: * `pedidos.cliente_id` for online orders [database/american_food L259](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L259-L259) * Foreign key constraint at [database/american_food L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L482-L482)

**Sources**: [crud/registro_INSERT.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L2-L2)

 [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

 [database/american_food L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L482-L482)

---

## Security Summary

The User Registration System implements multiple security layers:

| Security Measure | Implementation | Location |
| --- | --- | --- |
| Input Sanitization | `FILTER_SANITIZE_SPECIAL_CHARS`, `FILTER_SANITIZE_EMAIL` | [Lines 33-38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 33-38) |
| Email Validation | `FILTER_VALIDATE_EMAIL` | [Line 43](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Line 43) |
| Role Whitelisting | `in_array()` check against allowed roles | [Lines 51-54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 51-54) |
| Password Hashing | `password_hash()` with bcrypt | [Line 56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Line 56) |
| SQL Injection Prevention | Prepared statements with parameter binding | [Lines 10-19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 10-19) |
| Unique Email Enforcement | Database UNIQUE constraint | [database/american_food L381](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L381-L381) |
| Resource Cleanup | Explicit connection/statement closing | [Lines 18-19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 18-19) |
| Exception Handling | Try-catch with error logging | [Lines 66-72](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 66-72) |

For additional security considerations including production hardening recommendations, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations).

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)