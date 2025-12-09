# Security Practices

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)

## Purpose and Scope

This document covers the security mechanisms implemented in the American Food application backend, including input validation, SQL injection prevention, password hashing, role-based validation, and session management. These practices are primarily implemented in the user registration flow and database connection layer.

For database configuration security concerns, see [Database Configuration](/axchisan/AmericanFood/3.1-database-configuration). For role-based access control at the application level, see [User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control). For production deployment security hardening, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations).

---

## Security Architecture Overview

The application implements a defense-in-depth security model with multiple validation layers before data reaches the database. The following diagram illustrates how user input flows through successive security barriers:

### Security Layer Flow Diagram

```

```

**Sources**: [crud/registro_INSERT.php L33-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L59)

---

## Input Sanitization and Validation

### Input Sanitization Pipeline

The registration system sanitizes all user input using PHP's filter functions to prevent Cross-Site Scripting (XSS) attacks. Each field passes through `trim()` to remove leading/trailing whitespace, followed by appropriate sanitization filters:

| Field | Sanitization Filter | Purpose |
| --- | --- | --- |
| `nombre` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `apellido` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `correo` | `FILTER_SANITIZE_EMAIL` | Remove invalid email characters |
| `telefono` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `username` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `rol` | `FILTER_SANITIZE_SPECIAL_CHARS` | Encode HTML special characters |
| `password` | None | Raw input preserved for hashing |

**Implementation:**

```

```

**Sources**: [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38)

### Email Validation

After sanitization, the email field undergoes format validation using `FILTER_VALIDATE_EMAIL`. Invalid emails cause immediate script termination with an error message:

```

```

**Sources**: [crud/registro_INSERT.php L43-L45](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L45)

### Input Sanitization Flow Diagram

```

```

**Sources**: [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38)

---

## SQL Injection Prevention

### Prepared Statements Architecture

The application prevents SQL injection attacks exclusively through prepared statements using MySQLi's parameterized query interface. Direct string concatenation of user input into SQL queries is never used.

### Prepared Statement Implementation

The `insertarUsuario()` function demonstrates the secure query execution pattern:

```

```

**Sources**: [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

### Parameter Binding Details

| Component | Implementation | Line Reference |
| --- | --- | --- |
| Query preparation | `mysqli_prepare($link, $query)` | [crud/registro_INSERT.php L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L11) |
| Type specification | `str_repeat("s", count($params))` | [crud/registro_INSERT.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L16-L16) |
| Parameter binding | `mysqli_stmt_bind_param($stmt, ...$params)` | [crud/registro_INSERT.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L16-L16) |
| Execution | `mysqli_stmt_execute($stmt)` | [crud/registro_INSERT.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L17-L17) |
| Cleanup | `mysqli_stmt_close($stmt)` | [crud/registro_INSERT.php L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L18-L18) |

The query uses placeholders that are bound to sanitized parameters:

```

```

All seven parameters are bound as strings (`"sssssss"`), ensuring no direct SQL injection is possible through user input.

**Sources**: [crud/registro_INSERT.php L57-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L57-L59)

 [crud/registro_INSERT.php L10-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L18)

---

## Password Security

### Password Hashing Implementation

Passwords are hashed using PHP's `password_hash()` function with the `PASSWORD_DEFAULT` algorithm (currently bcrypt with cost factor 10):

```

```

### Password Security Flow

```

```

**Sources**: [crud/registro_INSERT.php L39-L40](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L39-L40)

 [crud/registro_INSERT.php L47-L49](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L47-L49)

 [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

### Password Security Properties

| Property | Implementation | Status |
| --- | --- | --- |
| **Algorithm** | `PASSWORD_DEFAULT` (bcrypt) | ✓ Secure |
| **Salt** | Automatically generated by `password_hash()` | ✓ Secure |
| **Cost factor** | Default (currently 10) | ✓ Adequate |
| **Confirmation** | Password match check before hashing | ✓ Implemented |
| **Strength requirements** | None enforced server-side | ⚠ Missing |
| **Raw storage** | Never stored; only hash persisted | ✓ Secure |

**Note**: The system does not enforce password complexity requirements (minimum length, character types, etc.) at the backend level. Password strength validation may exist in frontend code but is not verified server-side.

**Sources**: [crud/registro_INSERT.php L47-L49](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L47-L49)

 [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

---

## Role-Based Validation

### Allowed Roles Whitelist

The system implements a whitelist-based role validation to prevent privilege escalation attacks. Only five predefined roles are accepted:

```

```

### Role Validation Diagram

```

```

**Sources**: [crud/registro_INSERT.php L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L38-L38)

 [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54)

### Role Definitions

| Role | Spanish Name | Access Level |
| --- | --- | --- |
| `admin` | Admin | Full system access |
| `cajera` | Cajera | Cashier operations (payments, invoices) |
| `cocinero` | Cocinero | Kitchen operations (order preparation) |
| `mesera` | Mesera | Server operations (tables, orders) |
| `cliente` | Cliente | Customer operations (menu browsing, online orders) |

This whitelist prevents attackers from:

* Creating accounts with arbitrary role values
* Injecting SQL through the role field
* Escalating privileges by specifying unauthorized roles

**Sources**: [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54)

---

## Session Management

### Session Initialization

The connection manager [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)

 initializes PHP sessions before establishing database connections, enabling secure error message storage:

```

```

### Session-Based Error Messaging

Database connection failures store user-friendly messages in session variables rather than exposing technical details:

```

```

### Session Security Flow

```

```

**Sources**: [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6)

 [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

### Session Security Properties

| Property | Implementation | Security Impact |
| --- | --- | --- |
| **Initialization check** | `session_status()` check prevents duplicate starts | Prevents session errors |
| **Error messaging** | Generic message prevents information disclosure | ✓ Prevents reconnaissance |
| **Session fixation** | No session regeneration implemented | ⚠ Potential vulnerability |
| **Session timeout** | Not configured | ⚠ Sessions persist indefinitely |
| **Secure cookie flags** | Not configured | ⚠ Sessions vulnerable to interception |

**Sources**: [config/conexionDB.php L4-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L14)

---

## Error Handling and Information Disclosure

### Fail-Fast Error Pattern

The application implements a fail-fast approach that terminates execution on critical errors:

```

```

### Error Handling Locations

| File | Error Type | Handler | User Message |
| --- | --- | --- | --- |
| [crud/registro_INSERT.php L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L6-L6) | Missing action parameter | `die()` | "Advertencia: Acción no permitida." |
| [crud/registro_INSERT.php L13](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L13-L13) | Prepared statement failure | `die()` | "Error preparando la consulta: " + mysqli_error |
| [crud/registro_INSERT.php L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L26-L26) | Insert execution failure | `die()` | "Error insertando el contenido: " + mysqli_error |
| [crud/registro_INSERT.php L44](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L44-L44) | Invalid email format | `die()` | "Error: Correo electrónico no válido." |
| [crud/registro_INSERT.php L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L48-L48) | Password mismatch | `die()` | "Error: Las contraseñas no coinciden." |
| [crud/registro_INSERT.php L53](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L53-L53) | Invalid role | `die()` | "Error: Rol no válido." |
| [crud/registro_INSERT.php L67-L71](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L67-L71) | Uncaught exceptions | `error_log()` + generic message | "Ha ocurrido un error. Estamos trabajando en corregir esta situación." |
| [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14) | Database connection failure | Session storage + `exit()` | "El sistema está en mantenamiento, intente más tarde." |

### Information Disclosure Concerns

**Vulnerability**: Several error handlers expose technical details through `mysqli_error($link)`:

```

```

These messages can reveal:

* Database structure details
* Column names and constraints
* SQL syntax used by the application
* Database version information

**Recommendation**: Replace with generic messages and log technical details server-side.

**Sources**: [crud/registro_INSERT.php L13](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L13-L13)

 [crud/registro_INSERT.php L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L26-L26)

 [crud/registro_INSERT.php L67-L71](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L67-L71)

 [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

---

## Character Encoding Security

### UTF-8 Encoding Configuration

The database connection explicitly sets UTF-8 character encoding to prevent encoding-based injection attacks:

```

```

### Encoding Security Benefits

| Attack Vector | Protection Mechanism |
| --- | --- |
| **Multi-byte character injection** | UTF-8 ensures consistent character interpretation |
| **Encoding mismatch exploits** | Database and PHP use same encoding |
| **Character set confusion** | Explicit configuration prevents defaults |
| **Special character handling** | Proper encoding of Spanish characters (ñ, á, é, í, ó, ú) |

### Character Encoding Flow

```

```

**Sources**: [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

---

## Database Connection Security

### Connection Configuration

Database credentials are centralized in [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

 and accessed via global constants:

| Constant | Value (Development) | Security Status |
| --- | --- | --- |
| `host` | `'127.0.0.1'` | ⚠ Localhost only |
| `user` | `'root'` | ⚠ Root access |
| `password` | `''` (empty) | ⚠ No password |
| `database` | `'american_food'` | ✓ Named database |
| `llave` | `''` (empty) | ⚠ Unused security key |

### Connection Security Diagram

```

```

**Sources**: [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

 [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

 [config/conexionDB.php L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L10)

 [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

### Security Concerns

**Critical Issues**:

1. **Empty root password**: Production databases must use strong passwords
2. **Root user access**: Application should use limited-privilege database user
3. **Localhost binding**: Prevents remote database servers
4. **Unused security key**: `llave` constant empty and never referenced
5. **Exposed credentials**: Configuration file must be secured

For detailed security hardening recommendations, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations).

**Sources**: [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

---

## Exception and Error Catching

### Try-Catch Block Structure

The registration script wraps all operations in a try-catch block that handles both `Exception` and `Error` classes:

```

```

### Exception Handling Flow

```

```

**Sources**: [crud/registro_INSERT.php L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L4-L4)

 [crud/registro_INSERT.php L66-L72](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L66-L72)

### Error Logging Strategy

| Component | Purpose | Security Benefit |
| --- | --- | --- |
| `error_log()` | Records technical details server-side | Internal debugging without disclosure |
| Generic message | Displays to user | Prevents information leakage |
| Exception/Error separation | Handles both exception types | Comprehensive error coverage |

**Note**: The system logs errors but does not specify a custom log file location, using PHP's default error logging configuration.

**Sources**: [crud/registro_INSERT.php L67](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L67-L67)

 [crud/registro_INSERT.php L70](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L70-L70)

---

## Security Best Practices Summary

### Implemented Protections

| Security Measure | Implementation | Effectiveness |
| --- | --- | --- |
| **XSS Prevention** | `FILTER_SANITIZE_SPECIAL_CHARS` | ✓ Adequate |
| **SQL Injection Prevention** | Prepared statements with parameter binding | ✓ Strong |
| **Password Security** | `password_hash()` with bcrypt | ✓ Strong |
| **Email Validation** | `FILTER_VALIDATE_EMAIL` | ✓ Adequate |
| **Role Validation** | Whitelist-based checking | ✓ Strong |
| **Character Encoding** | UTF-8 explicitly set | ✓ Adequate |
| **Error Handling** | Try-catch with generic messages | ⚠ Partial |
| **Session Management** | Basic session initialization | ⚠ Minimal |

### Security Gaps

| Vulnerability | Current State | Recommendation |
| --- | --- | --- |
| **Database credentials** | Empty password, root user | Use strong passwords, limited-privilege users |
| **Password complexity** | No server-side enforcement | Implement minimum requirements |
| **Session security** | No regeneration, timeout, or secure flags | Configure session hardening |
| **Information disclosure** | mysqli_error() exposed to users | Log errors server-side only |
| **HTTPS enforcement** | Not visible in backend code | Enforce HTTPS at web server level |
| **CSRF protection** | Not implemented | Add token validation |
| **Rate limiting** | Not implemented | Implement on authentication endpoints |

**Sources**: [crud/registro_INSERT.php L33-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L59)

 [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

---

## Code Entity Reference

### Functions

| Function | File | Purpose |
| --- | --- | --- |
| `insertarUsuario()` | [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28) | Executes prepared statement for user insertion |
| `password_hash()` | [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56) | Hashes passwords using bcrypt |
| `filter_var()` | [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38) | Sanitizes and validates input |
| `mysqli_prepare()` | [crud/registro_INSERT.php L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L11) | Creates prepared statement |
| `mysqli_stmt_bind_param()` | [crud/registro_INSERT.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L16-L16) | Binds parameters to prepared statement |
| `mysqli_stmt_execute()` | [crud/registro_INSERT.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L17-L17) | Executes prepared statement |

### Variables and Constants

| Symbol | Type | File | Purpose |
| --- | --- | --- | --- |
| `$allowed_roles` | Array | [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51) | Role whitelist |
| `$link` | mysqli | [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8) | Global database connection |
| `host`, `user`, `password`, `database` | Constants | [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php) | Database credentials |
| `$password_hashed` | String | [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56) | Bcrypt hash output |

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)