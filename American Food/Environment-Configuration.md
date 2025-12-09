# Environment Configuration

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

## Purpose and Scope

This document explains how to configure the American Food restaurant management system for different deployment environments (development, staging, production). It covers the configuration file structure, database connection settings, security parameters, and environment-specific considerations.

For security hardening recommendations, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations). For database schema and credentials, see [Database Configuration](/axchisan/AmericanFood/3.1-database-configuration). For the connection initialization process, see [Connection Manager](/axchisan/AmericanFood/3.2-connection-manager).

---

## Configuration System Architecture

The application uses a two-file configuration system that separates credential definition from connection management:

**Configuration System Flow**

```

```

**Sources:** [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Configuration File Structure

### configuracion.php

The `configuracion.php` file defines five constants using PHP's `define()` function:

| Constant | Current Value | Purpose | Environment Sensitivity |
| --- | --- | --- | --- |
| `host` | `'127.0.01'` | Database server address | High - changes per environment |
| `user` | `'root'` | Database username | High - should differ per environment |
| `password` | `''` (empty) | Database password | Critical - must be set for non-dev |
| `database` | `'american_food'` | Database name | Medium - may include environment suffix |
| `llave` | `''` (empty) | Application security key | Critical - required for encryption/signing |

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

### conexionDB.php

The `conexionDB.php` file performs three critical operations:

1. **Configuration Import** [Line 2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Line 2) : Includes `configuracion.php` to access constants
2. **Session Initialization** [Lines 4-6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 4-6) : Starts PHP session if not already active
3. **Database Connection** [Lines 8-17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/Lines 8-17) : Creates mysqli connection with error handling

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Environment-Specific Configuration

### Development Environment

The current configuration represents a typical development setup:

```

```

**Characteristics:**

* **Host:** Localhost (note: typo in code shows `'127.0.01'` instead of `'127.0.0.1'`)
* **User:** Root account with full privileges
* **Password:** Empty (no authentication required)
* **Database:** Direct database name without environment prefix
* **Key:** Empty (encryption/signing features unavailable)

**Suitable for:** Local development machines, not accessible from network

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

### Staging Environment

Recommended configuration for staging/testing environments:

| Constant | Recommended Value | Rationale |
| --- | --- | --- |
| `host` | `'staging-db.internal.domain'` or specific IP | Separate database server |
| `user` | `'american_food_staging'` | Limited privilege user account |
| `password` | Strong password (20+ characters) | Secure authentication |
| `database` | `'american_food_staging'` | Environment-specific database |
| `llave` | 32+ character random string | Enable encryption features |

**Configuration Example:**

```
define('host', '10.0.1.50');
define('user', 'american_food_staging');
define('password', 'xK9$mPq2...'); // Long secure password
define('database', 'american_food_staging');
define('llave', 'a3f8c9d1e4b7...'); // 32-byte random key
```

**Characteristics:**

* Mimics production architecture
* Uses dedicated database user with restricted privileges
* Isolated database to prevent test data from affecting production
* Enables all security features

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

### Production Environment

Critical configuration requirements for production deployment:

| Constant | Requirements | Security Level |
| --- | --- | --- |
| `host` | Production database server (never localhost) | Critical |
| `user` | Dedicated app user with minimal privileges | Critical |
| `password` | Stored in environment variable, not in code | Critical |
| `database` | `'american_food'` or versioned name | High |
| `llave` | 256-bit key stored securely, never committed | Critical |

**Recommended Approach:**

Instead of hardcoding values, read from environment variables:

```

```

**Environment Variable Mapping:**

```

```

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Connection Initialization Sequence

The `conexionDB.php` file executes the following sequence on every request:

```

```

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Error Handling Configuration

### Connection Failure Behavior

The connection manager implements a fail-fast pattern when database connection fails:

**Error Flow:**

```

```

**Error Message Configuration:**

The current implementation sets a Spanish-language maintenance message:

* **Text:** `"El sistema está en mantenimiento, intente más tarde."`
* **Type:** `"bg-warning text-dark"` (Bootstrap CSS classes)

**Environment-Specific Error Messages:**

| Environment | Recommended Message Detail Level | Session Variables |
| --- | --- | --- |
| Development | Detailed error with `connect_error` | Include actual error message |
| Staging | Generic message with error ID | Log full error, show reference number |
| Production | User-friendly message only | Never expose technical details |

**Sources:** [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

---

## Character Encoding Configuration

The connection manager explicitly sets UTF-8 encoding:

**Encoding Setup** [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

:

```
$link->set_charset("utf8");
```

**Purpose:**

* Ensures proper handling of Spanish characters (mesera, cajera, cocinero)
* Prevents data corruption for menu items with special characters
* Required for international character support in names and addresses

**Environment Considerations:**

| Environment | Encoding Setting | Verification |
| --- | --- | --- |
| Development | `utf8` | Sufficient for most cases |
| Staging | `utf8mb4` | Recommended for emoji support |
| Production | `utf8mb4` | Full Unicode including emoji |

**Migration to utf8mb4:**

To support full Unicode (including emoji in customer reviews, comments):

```

```

**Sources:** [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

---

## Security Key (llave) Usage

The `llave` constant is currently empty but intended for security operations:

**Potential Uses:**

```

```

**Key Generation:**

For production environments, generate a secure random key:

```

```

**Storage:**

* **Never commit to version control**
* Store in environment variables or secure key management system
* Rotate periodically (requires re-encryption of existing data)

**Sources:** [config/configuracion.php L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L7-L7)

---

## Configuration Validation

### Pre-Deployment Checklist

Before deploying to each environment, verify:

| Check | Development | Staging | Production |
| --- | --- | --- | --- |
| `host` not localhost | ❌ | ✅ | ✅ |
| `user` not 'root' | ❌ | ✅ | ✅ |
| `password` not empty | ❌ | ✅ | ✅ |
| `llave` is 32+ bytes | ❌ | ✅ | ✅ |
| Connection uses SSL | ❌ | Optional | ✅ |
| Credentials in environment vars | ❌ | ✅ | ✅ |
| Error messages generic | ❌ | ✅ | ✅ |

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Database User Privileges

### Recommended Privilege Sets

**Development User (root):**

```

```

**Staging User:**

```

```

**Production User:**

```

```

**Privilege Mapping:**

```

```

**Sources:** [config/configuracion.php L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L4-L4)

---

## Known Configuration Issues

### Issue 1: Host IP Typo

**Current Code** [config/configuracion.php L3](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L3)

:

```
define('host', '127.0.01');
```

**Issue:** IP address has typo (`127.0.01` instead of `127.0.0.1`)

**Status:** Works on some systems due to lenient parsing, but non-standard

**Fix:**

```
define('host', '127.0.0.1');
```

---

### Issue 2: Empty Password

**Current Code** [config/configuracion.php L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L5-L5)

:

```
define('password', '');
```

**Issue:** Root MySQL user has no password protection

**Security Impact:**

* Anyone with network access can connect as root
* Local processes can access database without authentication
* Data breach risk if system is compromised

**Fix:** Set strong password for root user or create dedicated application user

---

### Issue 3: Empty Security Key

**Current Code** [config/configuracion.php L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L7-L7)

:

```
define('llave', '');
```

**Issue:** Security features that depend on `llave` constant are non-functional

**Impact:**

* Cannot generate secure CSRF tokens
* Cannot encrypt sensitive session data
* Cannot sign cookies or API requests

**Fix:** Generate and set 256-bit random key

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Migration Strategy

### Moving from Development to Production

**Step-by-Step Configuration Migration:**

```

```

**Migration Checklist:**

1. **Pre-Migration:** * Create production database and user * Generate secure password (20+ characters) * Generate `llave` key (32+ bytes) * Document new credentials in secure location * Set up environment variables on server
2. **Configuration Update:** * Update `host` to production database server * Update `user` to dedicated application user * Update `password` from environment variable * Update `database` name (add environment suffix if needed) * Update `llave` from environment variable
3. **Post-Migration:** * Test database connectivity * Verify character encoding (utf8/utf8mb4) * Test error handling (disconnect database temporarily) * Verify session initialization * Remove any debug output

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Connection Object Global Availability

After successful initialization, the `$link` mysqli object becomes globally available:

**Global Variable** [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

:

```
$link = new mysqli(host, user, password, database);
```

**Usage Pattern:**

Any PHP script that includes `conexionDB.php` can access `$link` directly:

```sql
require_once 'config/conexionDB.php';
// $link is now available
$result = $link->query("SELECT * FROM menu");
```

**Scope Considerations:**

| Context | $link Availability | Notes |
| --- | --- | --- |
| Global scope | ✅ Available | Created at global scope |
| Function scope | ❌ Not available | Must use `global $link;` |
| Class methods | ❌ Not available | Pass as parameter or use `global` |

**Sources:** [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

---

## Summary

The American Food system uses a two-file configuration approach:

1. **configuracion.php**: Defines five constants for database credentials and security key
2. **conexionDB.php**: Initializes session, creates mysqli connection, handles errors

**Current State:** Development configuration (localhost, root user, empty passwords)

**Production Requirements:**

* Update all five constants with secure values
* Store credentials in environment variables
* Use dedicated database user with minimal privileges
* Generate and protect `llave` security key
* Implement proper error logging instead of generic messages

**Critical Action Items:**

* Fix host IP typo (`127.0.01` → `127.0.0.1`)
* Set strong password for database user
* Generate 256-bit `llave` key
* Migrate to environment variable-based configuration
* Restrict database user privileges

**Sources:** [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)