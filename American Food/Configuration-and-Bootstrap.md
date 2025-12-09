# Configuration and Bootstrap

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

## Purpose and Scope

This document describes the application initialization system responsible for establishing database connectivity, loading configuration parameters, and setting up session management. The bootstrap process occurs at the earliest stage of application execution, before any business logic runs.

For information about the database schema that this configuration connects to, see [Database Layer](/axchisan/AmericanFood/2-database-layer). For details on how user authentication utilizes the session system initialized here, see [Authentication Flow](/axchisan/AmericanFood/7.2-authentication-flow).

---

## Bootstrap Architecture Overview

The configuration and bootstrap system consists of two PHP files that execute in a specific sequence:

| File | Purpose | Execution Order |
| --- | --- | --- |
| `configuracion.php` | Defines database credentials and application constants as PHP constants | 1st (included by conexionDB.php) |
| `conexionDB.php` | Initializes session, establishes database connection, sets encoding | 2nd (included by application entry points) |

**Dependency Chain Diagram**

```

```

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)

---

## Configuration Constants

The `configuracion.php` file establishes the application's configuration by defining five PHP constants using the `define()` function.

### Constant Definitions

[config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 defines the following constants:

| Constant | Value | Purpose |
| --- | --- | --- |
| `host` | `'127.0.01'` | Database server address (localhost) |
| `user` | `'root'` | MySQL username for authentication |
| `password` | `''` | MySQL password (empty in development) |
| `database` | `'american_food'` | Target database name |
| `llave` | `''` | Application security key (currently unused) |

### Usage Pattern

These constants are consumed by [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

 when instantiating the `mysqli` object:

```

```

The constants are referenced without the `$` prefix or quotes, as they are defined via `define()` rather than as variables.

### Security Analysis

**Development Configuration Indicators**:

* Empty `password` constant indicates development environment
* `user` set to `'root'` grants full database privileges
* `host` set to `'127.0.01'` (note: typo for `127.0.0.1`) restricts to local-only access
* Empty `llave` constant suggests security key not implemented

**Production Hardening Required**:

* Non-empty password with strong entropy
* Dedicated database user with restricted privileges (SELECT, INSERT, UPDATE on specific tables)
* Correct IP address format (`127.0.0.1`)
* Populated `llave` for session security or API authentication
* Consider environment variables instead of hardcoded credentials

**Sources**: [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)

---

## Connection Manager Implementation

The `conexionDB.php` file serves as the application's database connection manager, implementing session initialization, connection establishment, error handling, and character encoding configuration.

### Connection Manager Flow

```

```

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

### Session Initialization

[config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6)

 implements conditional session startup:

```

```

**Behavior**:

* `session_status()` returns `PHP_SESSION_NONE` if no session is active
* Guards against `session_start()` being called multiple times (which causes warnings)
* Ensures `$_SESSION` superglobal is available for error messaging
* Session persists across requests for user state management

**Sources**: [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6)

### Database Connection Establishment

[config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

 creates the `mysqli` connection object:

```

```

**Connection Parameters**:

* Uses constants from `configuracion.php` (no quotes needed)
* Establishes TCP connection to MySQL server
* `$link` variable becomes globally accessible to application code
* Connection persists for the duration of the request

**Sources**: [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

### Error Handling: Fail-Fast Pattern

[config/conexionDB.php L10-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L15)

 implements immediate termination on connection failure:

| Step | Code Location | Action |
| --- | --- | --- |
| 1. Check error | Line 10 | `if ($link->connect_errno)` |
| 2. Set user message | Line 11 | Spanish error message stored in session |
| 3. Set message style | Line 12 | Bootstrap classes for UI styling |
| 4. Terminate | Line 14 | `exit` prevents further execution |

**Error Message**:

```
"El sistema está en mantenimiento, intente más tarde."
(The system is under maintenance, please try again later.)
```

**Design Rationale**:

* **Fail-fast**: Application cannot function without database access
* **User-friendly**: Technical connection error replaced with maintenance message
* **Security**: Prevents exposure of connection details or stack traces
* **Session storage**: Error persists across redirect if needed

**Limitations**:

* Script terminates immediately, so stored session message may not be displayed
* No logging of actual error for debugging
* No retry mechanism or fallback

**Sources**: [config/conexionDB.php L10-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L15)

### Character Encoding Configuration

[config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

 sets UTF-8 encoding for database communication:

```

```

**Purpose**:

* Ensures proper handling of international characters
* Critical for Spanish-language interface (cajera, mesera, cocinero roles)
* Prevents corruption of menu item names with special characters (e.g., "jalapeños")
* Affects both data sent to and received from MySQL

**MySQL Command Equivalent**:

```

```

**Sources**: [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

---

## Global State Management

Upon successful completion of the bootstrap sequence, the application has access to:

### Available Global Variables

| Variable | Type | Scope | Purpose |
| --- | --- | --- | --- |
| `$link` | `mysqli` object | Global | Database connection handle for queries |
| `$_SESSION` | Array | Superglobal | Session data storage for user state |
| `host` | Constant | Global | Database host address |
| `user` | Constant | Global | Database username |
| `password` | Constant | Global | Database password |
| `database` | Constant | Global | Database name |
| `llave` | Constant | Global | Application security key |

### Usage in Application Code

Application PHP files that need database access include `conexionDB.php`:

```

```

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Bootstrap Execution Timeline

**Detailed Initialization Sequence**

```

```

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)

---

## Configuration File Structure

### File Organization

```markdown
project_root/
├── config/
│   ├── configuracion.php    # Constants definition
│   └── conexionDB.php        # Connection manager
├── crud/
│   └── registro_INSERT.php   # Includes conexionDB.php
└── [other application files]
```

### Inclusion Pattern

**Single Point of Entry**:

* Application files include only `conexionDB.php`
* `conexionDB.php` internally includes `configuracion.php`
* Prevents accidental direct inclusion of `configuracion.php`

**Include Statement Used**: [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

```

```

The `include_once` ensures constants are defined exactly once, even if multiple files include the connection manager.

**Sources**: [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

---

## Error States and Recovery

### Connection Failure Handling

**Error Detection**: [config/conexionDB.php L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L10)

```

```

The `connect_errno` property contains a non-zero value when connection fails. Common error codes:

| Error Code | Meaning |
| --- | --- |
| 2002 | Cannot connect to MySQL server (server down or wrong host) |
| 1045 | Access denied (wrong username/password) |
| 1049 | Unknown database (database doesn't exist) |

**Error Response**: [config/conexionDB.php L11-L12](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L11-L12)

* User-facing message: "El sistema está en mantenimiento, intente más tarde."
* Message type: `"bg-warning text-dark"` (Bootstrap CSS classes)

**No Recovery Mechanism**:

* Application terminates immediately via `exit` [config/conexionDB.php L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L14-L14)
* No retry logic
* No fallback database
* Requires manual intervention (restart database server, fix credentials, etc.)

**Sources**: [config/conexionDB.php L10-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L15)

---

## Character Encoding Details

### UTF-8 Configuration

[config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

 sets the connection character set:

```

```

**Impact on Data Flow**:

```

```

**Why UTF-8 is Critical**:

* Spanish interface uses characters: á, é, í, ó, ú, ñ, ¿, ¡
* Menu items may include: "jalapeños", "café", "piña"
* Without UTF-8: Characters corrupt (e.g., "café" becomes "cafÃ©")

**MySQL Character Set Hierarchy**:

1. Connection level: Set by `set_charset("utf8")`
2. Database level: Defined in schema creation
3. Table level: Can override database default
4. Column level: Can override table default

**Sources**: [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

---

## Production Deployment Checklist

### Security Hardening Required

| Item | Current State | Recommended Action |
| --- | --- | --- |
| Database password | Empty string | Set strong password (16+ characters, mixed case, symbols) |
| Database user | `root` | Create dedicated user `american_food_app` with minimal privileges |
| Host address | `'127.0.01'` (typo) | Fix to `'127.0.0.1'` or use actual production host |
| Security key | Empty `llave` | Generate and set cryptographic key if needed |
| Error messages | Spanish hardcoded | Consider localization system |
| Error logging | None | Add `error_log()` for `connect_errno` details |
| Connection timeout | Default | Set `MYSQLI_OPT_CONNECT_TIMEOUT` option |
| SSL/TLS | Not configured | Enable if database on separate server |

### Environment-Specific Configuration

**Recommended Approach**:
Replace hardcoded constants with environment variables:

```

```

Set via `.env` file (not committed to version control) or server environment variables.

**Sources**: [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Integration with Application Layer

### Usage Example

The registration system demonstrates typical usage of the bootstrap system:

**File**: `crud/registro_INSERT.php` (not shown in provided files, but referenced in diagrams)

Typical pattern:

```

```

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Summary

The configuration and bootstrap system provides:

1. **Centralized Configuration**: Database credentials defined in single file (`configuracion.php`)
2. **Connection Abstraction**: Single connection manager (`conexionDB.php`) handles all setup
3. **Session Management**: Automatic session initialization with duplicate protection
4. **Error Handling**: Fail-fast pattern with user-friendly error messages
5. **Character Encoding**: UTF-8 support for internationalization
6. **Global Access**: `$link` object available throughout application

**Initialization guarantees**:

* Database connection established or application terminated
* Session system ready for state management
* UTF-8 encoding configured
* Configuration constants available

**Production considerations**:

* Current configuration is development-only (empty password, root user)
* Requires hardening before deployment
* No logging, retry, or monitoring capabilities implemented

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)