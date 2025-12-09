# Connection Manager

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

## Purpose and Scope

This document covers the `conexionDB.php` file, which serves as the central database connection manager for the American Food restaurant management system. This component is responsible for establishing and configuring the MySQL database connection, managing PHP sessions, and implementing fail-fast error handling.

For information about the database credentials and constants used by this connection manager, see [Database Configuration](/axchisan/AmericanFood/3.1-database-configuration).

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Overview

The Connection Manager implements a simple but critical bootstrap pattern where it:

1. Loads database configuration constants
2. Initializes PHP session if needed
3. Establishes a mysqli database connection
4. Handles connection failures with user-friendly error messages
5. Configures UTF-8 character encoding
6. Exposes the connection as a global `$link` variable

This file is typically included at the beginning of PHP scripts that require database access.

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Bootstrap Sequence Diagram

```

```

**Sources:** [config/conexionDB.php L2-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L17)

 [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Code Structure Mapping

```

```

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Session Management

### Session Status Check

The connection manager performs a defensive session status check before attempting to start a session:

```

```

**Behavior:**

* `session_status()` returns `PHP_SESSION_NONE` if no session has been started
* Only calls `session_start()` if the session is not already active
* Prevents "session already started" errors when multiple files include `conexionDB.php`

This pattern is necessary because multiple application scripts may include this file, and PHP throws an error if `session_start()` is called when a session is already active.

**Sources:** [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6)

---

## Connection Establishment

### mysqli Object Creation

The connection is established using the procedural `mysqli` constructor:

```

```

**Parameters:**

* `host`: Database server address (defined in `configuracion.php`)
* `user`: Database username (defined in `configuracion.php`)
* `password`: Database password (defined in `configuracion.php`)
* `database`: Database name (defined in `configuracion.php`)

**Global Variable:**
The connection object is assigned to `$link`, which becomes available globally after this file is included. All application code that includes `conexionDB.php` can access the database connection through this variable.

**Sources:** [config/conexionDB.php L2-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L8)

 [config/configuracion.php L3-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L6)

---

## Error Handling

### Fail-Fast Pattern

The connection manager implements a fail-fast error handling pattern:

```

```

**Error Detection:**

* Checks `$link->connect_errno` property
* Non-zero value indicates connection failure

**Error Response:**

| Session Variable | Value | Purpose |
| --- | --- | --- |
| `$_SESSION['MensajeTexto']` | "El sistema está en mantenimiento, intente más tarde." | User-facing error message in Spanish |
| `$_SESSION['MensajeTipo']` | "bg-warning text-dark" | Bootstrap CSS classes for styling the error message |

**Script Termination:**

* `exit` statement terminates script execution immediately
* Prevents application code from running without database access
* No database operations can proceed without a valid connection

### User Experience

The error message is intentionally generic:

* **Spanish text:** "The system is under maintenance, please try again later"
* **Purpose:** Prevents exposure of technical details to end users
* **Styling:** Uses Bootstrap's warning classes (`bg-warning text-dark`) for yellow background with dark text

**Sources:** [config/conexionDB.php L10-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L15)

---

## Character Encoding Configuration

### UTF-8 Setup

After successful connection, the manager configures UTF-8 character encoding:

```

```

**Purpose:**

* Ensures proper handling of international and special characters
* Critical for Spanish-language interface elements
* Prevents data corruption for characters outside ASCII range

**Affected Data:**

* Menu item names and descriptions
* User names and addresses
* Role names (mesera, cajera, cocinero)
* Error messages and UI text
* Any text stored in database columns

Without UTF-8 configuration, characters like "ñ" (in "niño"), accented vowels (á, é, í, ó, ú), and other Spanish language characters would be corrupted during database operations.

**Sources:** [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

---

## Global State and Variable Scope

### The $link Variable

After including `conexionDB.php`, the `$link` variable is available in the global scope:

```

```

**Usage Pattern:**

1. Application script includes `conexionDB.php`
2. The `$link` variable becomes available
3. Script uses `$link` for all database operations (queries, prepared statements, etc.)

**Example from codebase:**
The registration system uses this pattern: `registro_INSERT.php` would include `conexionDB.php` and then use the `$link` variable for prepared statements.

**Sources:** [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

---

## Integration Points

### Files That Depend on This Component

The Connection Manager is a foundational component that must be included by any file requiring database access:

| File Type | Usage Pattern | Purpose |
| --- | --- | --- |
| CRUD operations | `require_once 'config/conexionDB.php'` | User registration, data manipulation |
| Authentication | `include 'config/conexionDB.php'` | Login validation, session management |
| Order processing | `require 'config/conexionDB.php'` | Order creation, status updates |
| Payment processing | `include_once 'config/conexionDB.php'` | Payment recording, invoice generation |
| Reporting | `require_once 'config/conexionDB.php'` | Sales reports, inventory queries |

### Inclusion Methods

The codebase uses `include_once` when including this file, which:

* Includes the file only once even if requested multiple times
* Prevents redefinition of the `$link` variable
* Avoids multiple connection attempts

**Sources:** [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

---

## Configuration Dependencies

### Required Constants from configuracion.php

The Connection Manager depends on four constants defined in `configuracion.php`:

| Constant | Current Value | Purpose |
| --- | --- | --- |
| `host` | `'127.0.01'` (note: typo, should be `127.0.0.1`) | MySQL server hostname |
| `user` | `'root'` | MySQL username |
| `password` | `''` (empty string) | MySQL password |
| `database` | `'american_food'` | Target database name |

**Note:** The `llave` constant is defined but not used by the Connection Manager.

**Configuration Note:** The typo in the `host` constant (`127.0.01` instead of `127.0.0.1`) may cause connection failures depending on the system's hosts file configuration.

**Sources:** [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

 [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Error Flow Diagram

```

```

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Security Considerations

### Current Security Posture

The current configuration has several security concerns appropriate for development but requiring changes for production:

| Aspect | Current State | Production Recommendation |
| --- | --- | --- |
| Database Password | Empty string | Strong password required |
| Database User | `root` | Create dedicated application user with limited privileges |
| Host Address | Localhost (`127.0.0.1`) | Configure for production database server |
| Error Messages | Generic Spanish message stored in session | Implement proper error logging system |
| Connection Encryption | Not configured | Enable SSL/TLS for database connections |

### Security Features

**Implemented:**

* Generic error messages prevent exposure of technical details
* Session-based error storage (not displayed directly to output)
* Fail-fast pattern prevents execution without database

**Missing:**

* Connection pooling or reuse mechanism
* Connection timeout configuration
* Retry logic for transient failures
* Proper error logging to files

For comprehensive security considerations, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations).

**Sources:** [config/conexionDB.php L10-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L15)

 [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Common Usage Patterns

### Standard Include Pattern

```

```

### Error Checking After Include

While the Connection Manager handles fatal errors, application code can perform additional checks:

```

```

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Summary

The Connection Manager (`conexionDB.php`) provides:

1. **Centralized Connection Management**: Single point for database connectivity
2. **Session Initialization**: Ensures sessions are available for error handling
3. **Fail-Fast Error Handling**: Terminates execution on connection failure
4. **UTF-8 Support**: Properly handles international characters
5. **Global Access**: Makes `$link` available to all application code

This simple but essential component follows the principle of doing one thing well: establishing and configuring database connectivity with appropriate error handling.

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)