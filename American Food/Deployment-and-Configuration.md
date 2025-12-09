# Deployment and Configuration

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)

## Purpose and Scope

This document provides guidance for deploying the American Food restaurant management system from its current development configuration to production environments. It covers the configuration architecture, security hardening requirements, environment-specific settings, and operational considerations necessary for a production deployment.

For detailed information about specific subsystems, see:

* Database schema and relationships: [Database Layer](/axchisan/AmericanFood/2-database-layer)
* Application security practices: [Security Practices](/axchisan/AmericanFood/4.2-security-practices)
* Role-based access control implementation: [User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control)

**Warning**: The current codebase contains a development configuration with significant security vulnerabilities that must be addressed before production deployment.

---

## Configuration System Architecture

The application uses a two-file configuration system that separates credential definition from connection management:

| Component | File | Purpose | Line References |
| --- | --- | --- | --- |
| **Credentials Store** | `config/configuracion.php` | Defines database connection constants as PHP `define()` statements | [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7) |
| **Connection Manager** | `config/conexionDB.php` | Establishes `mysqli` connection, manages sessions, handles errors | [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18) |

### Configuration Constants

The following constants are defined in [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

:

| Constant | Current Value | Purpose | Production Requirement |
| --- | --- | --- | --- |
| `host` | `'127.0.01'` | Database server address | **Must change** to production database host |
| `user` | `'root'` | Database username | **Must change** to limited-privilege user |
| `password` | `''` (empty) | Database password | **Must set** strong password |
| `database` | `'american_food'` | Database name | May remain same or change per environment |
| `llave` | `''` (empty) | Application encryption key | **Must set** for session encryption |

**Critical Issue**: Note the typo in `host` constant: `'127.0.01'` should be `'127.0.0.1'`. This typo may cause connection failures depending on DNS resolution.

### Configuration Architecture Diagram

```

```

**Sources**: [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Application Bootstrap Sequence

The application initialization follows a strict sequence that establishes database connectivity and session management before any application code executes.

```

```

**Sources**: [config/conexionDB.php L2-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L17)

### Bootstrap Process Steps

1. **Configuration Loading** [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2) * `include_once 'configuracion.php'` loads database credentials * Constants become globally accessible via `define()`
2. **Session Management** [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6) * Checks if session is active using `session_status() === PHP_SESSION_NONE` * Calls `session_start()` only if no session exists * Prevents "headers already sent" errors from multiple session starts
3. **Database Connection** [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8) * Creates `mysqli` object: `$link = new mysqli(host, user, password, database)` * Object becomes global variable accessible throughout application
4. **Error Handling** [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14) * Checks `$link->connect_errno` for connection failures * Stores user-friendly Spanish error message in session * Terminates execution with `exit` (fail-fast pattern) * Does not expose technical error details to user
5. **Character Encoding** [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17) * Sets UTF-8 encoding: `$link->set_charset("utf8")` * Critical for Spanish-language interface (mesera, cajera, cocinero terms) * Prevents data corruption for special characters in names and menu items

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Development vs Production Configuration

The current configuration is suitable only for local development. The following table details required changes for production deployment:

| Setting | Development (Current) | Production (Required) | Security Impact |
| --- | --- | --- | --- |
| **Database Host** | `'127.0.01'` (typo: should be `127.0.0.1`) | Production database server IP or hostname | **High** - Exposes database if not firewalled |
| **Database User** | `'root'` | Dedicated application user with minimal privileges | **Critical** - Root has full system access |
| **Database Password** | `''` (empty string) | Strong password (20+ characters, mixed case, symbols) | **Critical** - Anyone can connect |
| **Application Key** | `''` (empty string) | 256-bit random key for encryption/CSRF tokens | **High** - Sessions vulnerable to hijacking |
| **Error Display** | Implicit (PHP defaults) | `error_reporting(0)` and `display_errors = Off` in php.ini | **Medium** - Errors leak system information |
| **HTTPS** | Not configured | Required with valid SSL certificate | **Critical** - Passwords transmitted in plaintext |
| **File Permissions** | Likely 644/755 | 640/750 with restricted ownership | **Medium** - Config files readable by web server only |

### Security Vulnerabilities in Current Configuration

#### 1. Empty Database Password config/configuracion.php5

```

```

**Risk**: Any user or process can connect to the database without credentials.

**Mitigation**: Generate strong password:

```

```

#### 2. Root User Access config/configuracion.php4

```

```

**Risk**: Compromised application can drop databases, create users, or access all data across all databases.

**Mitigation**: Create dedicated MySQL user with minimal privileges:

```

```

#### 3. Empty Application Key config/configuracion.php7

```

```

**Risk**: Cannot encrypt sessions, CSRF tokens, or sensitive data.

**Mitigation**: Generate cryptographically secure key:

```

```

#### 4. Localhost-Only Database config/configuracion.php3

```

```

**Risk**: Typo may cause connection failures. Localhost limitation prevents distributed architecture.

**Mitigation**:

* Fix typo to `127.0.0.1` for development
* Use production database hostname for deployment
* Configure MySQL to bind to appropriate network interface

**Sources**: [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Deployment Checklist

### Pre-Deployment Requirements

#### 1. Database Configuration

* Create production database instance
* Execute database schema: `database/american_food (6).sql`
* Create dedicated application MySQL user with limited privileges: * `SELECT, INSERT, UPDATE, DELETE` on `american_food.*` * No `DROP`, `CREATE`, `ALTER`, `GRANT` privileges
* Generate strong password (20+ characters)
* Configure MySQL to listen only on internal network interface
* Enable MySQL slow query log for performance monitoring

#### 2. Configuration Hardening

Update [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

:

```

```

* Fix `host` typo: `127.0.01` → `127.0.0.1` (development) or production hostname
* Change `user` from `root` to dedicated application user
* Set strong `password` via environment variable (not hardcoded)
* Generate and set `llave` (application encryption key)
* Remove `configuracion.php` from version control, use environment-specific files

#### 3. PHP Configuration

Update `php.ini` or `.htaccess`:

```

```

* Disable error display to users
* Enable error logging to secure location
* Configure secure session cookies (HTTPS required)
* Set appropriate file upload limits
* Set `memory_limit` and `max_execution_time` appropriately

#### 4. Web Server Configuration

For Apache with `.htaccess`:

```

```

* Configure HTTPS with valid SSL certificate
* Restrict direct access to configuration files
* Disable directory listing
* Enable security headers
* Configure appropriate file permissions (640 for config files, 750 for directories)

#### 5. Security Enhancements in Application Code

Review and enhance [crud/registro_INSERT.php L33-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L59)

:

* Implement rate limiting for registration endpoint (prevent brute force)
* Add CSRF token validation for all POST requests
* Implement email verification before account activation
* Add logging for security events (failed logins, privilege escalations)
* Implement account lockout after repeated failed authentication attempts
* Add honeypot fields to registration forms (bot detection)

Error handling improvements for [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

:

* Log connection errors to secure file (not session)
* Implement database connection pooling for performance
* Add retry logic with exponential backoff for transient failures
* Monitor database connection health

#### 6. Image Licensing Compliance

Review Getty Images licensing (see [Image Licensing Compliance](/axchisan/AmericanFood/9.3-image-licensing-compliance)):

* Verify valid license for all 26 images in `img/` directory
* Confirm AssetIDs match licensed images
* Ensure license covers commercial use and geographic regions
* Display required attribution if stipulated by license terms
* Implement hotlink protection to prevent unauthorized image use
* Set appropriate Cache-Control headers for image assets

**Sources**: [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

 [crud/registro_INSERT.php L33-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L59)

---

## Environment-Specific Configuration Strategy

### Recommended Approach: Environment Variables

Instead of hardcoding credentials in [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

 load from environment variables:

**Modified configuracion.php**:

```

```

### Setting Environment Variables

**Development (.env file)**:

```

```

**Production (Apache VirtualHost)**:

```

```

**Production (Nginx with PHP-FPM)**:

```

```

### Configuration File Structure

```python
american-food/
├── config/
│   ├── configuracion.php          # Modified to load from env vars
│   ├── conexionDB.php              # Unchanged
│   ├── .env.example                # Template for local development
│   └── .env                        # Local config (gitignored)
├── .htaccess                       # Web server configuration
└── .gitignore                      # Include: .env, configuracion.local.php
```

**Sources**: [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

---

## Connection Error Handling

The current error handling in [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

 terminates the application on database failure:

```

```

### Current Behavior Analysis

| Aspect | Implementation | Assessment |
| --- | --- | --- |
| **User Message** | Generic maintenance message in Spanish | ✓ Good - No technical details leaked |
| **Error Storage** | Session variables with Bootstrap classes | ✓ Good - UI-friendly format |
| **Execution Flow** | Immediate `exit` without cleanup | ⚠️ Acceptable - Prevents execution without DB |
| **Error Logging** | None | ✗ Bad - No debugging information captured |
| **Recovery** | None | ✗ Bad - No retry or fallback mechanism |

### Production Recommendations

**Enhanced Error Handling**:

```

```

**Monitoring Integration**:

* Implement health check endpoint that verifies database connectivity
* Set up monitoring system (e.g., Nagios, Zabbix) to poll health endpoint
* Configure alerts for sustained connection failures
* Track database connection metrics (latency, pool exhaustion)

**Sources**: [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

---

## Character Encoding Configuration

The application sets UTF-8 encoding in [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

:

```

```

### Character Encoding Requirements

| Layer | Configuration | Purpose |
| --- | --- | --- |
| **Database Connection** | `$link->set_charset("utf8")` | Ensures queries and results use UTF-8 |
| **MySQL Server** | `character_set_server=utf8mb4` | Default charset for new databases |
| **Database** | `DEFAULT CHARSET=utf8mb4` | Database-level encoding |
| **Tables** | `DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci` | Table-level encoding and collation |
| **PHP Files** | Save as UTF-8 without BOM | Source file encoding |
| **HTTP Headers** | `Content-Type: text/html; charset=UTF-8` | Browser interpretation |

### UTF-8 vs UTF8MB4

The current code uses `utf8` charset, which is a MySQL-specific 3-byte UTF-8 implementation that cannot store 4-byte characters (emojis, some Asian characters). For production, upgrade to `utf8mb4`:

**Updated conexionDB.php**:

```

```

**Database Schema Update**:

```

```

This ensures proper handling of:

* Spanish language characters (ñ, á, é, í, ó, ú, ü)
* Customer names with international characters
* Menu item descriptions with special symbols
* Emoji characters in customer messages or reviews

**Sources**: [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

---

## Deployment Architecture

```

```

**Sources**: [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## File Permissions and Ownership

### Recommended Permissions

```

```

### Security Rationale

| Permission | Meaning | Applied To | Reason |
| --- | --- | --- | --- |
| **640** | Owner: rw, Group: r, World: none | Configuration files | Web server can read, others cannot |
| **750** | Owner: rwx, Group: rx, World: none | Application directories | Web server can traverse, others cannot list |
| **644** | Owner: rw, Group: r, World: r | CSS, images, JavaScript | Public static assets |
| **755** | Owner: rwx, Group: rx, World: rx | Public directories | Allow web server and users to access |

**Critical**: Ensure [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

 is **never** world-readable (no 644 or 666 permissions), as it contains database credentials.

**Sources**: [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Production Deployment Steps

### Step-by-Step Deployment Process

1. **Provision Infrastructure** * Set up web server (Apache 2.4+ or Nginx 1.18+) * Install PHP 8.0+ with extensions: `mysqli`, `mbstring`, `curl`, `json` * Set up MySQL 8.0+ database server * Configure firewall rules (allow HTTPS:443, restrict MySQL:3306 to app servers only)
2. **Database Initialization** ``` ```
3. **Application Deployment** ``` ```
4. **Configuration** ``` ```
5. **SSL Certificate** ``` ```
6. **Verification** * Test database connectivity * Verify HTTPS certificate validity * Test user registration flow * Verify role-based access control * Check error logging * Perform load testing

**Sources**: [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

---

## Operational Monitoring

### Health Check Endpoint

Create `health.php` in application root:

```

```

### Monitoring Metrics

| Metric | Source | Alert Threshold |
| --- | --- | --- |
| **Database Connections** | `SHOW STATUS LIKE 'Threads_connected'` | > 80% of `max_connections` |
| **Slow Queries** | MySQL slow query log | > 100ms per query |
| **Failed Logins** | Application logs | > 10 per minute per IP |
| **Disk Space** | `df -h` | > 85% usage |
| **Memory Usage** | `free -m` | > 90% usage |
| **HTTP Errors** | Web server access logs | > 1% 5xx errors |
| **SSL Certificate Expiry** | `openssl x509 -enddate` | < 30 days |

**Sources**: [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

---

## Summary of Critical Configuration Changes

| Current Setting | Security Issue | Required Change | Priority |
| --- | --- | --- | --- |
| `password = ''` | No authentication | Strong password (20+ chars) | **CRITICAL** |
| `user = 'root'` | Excessive privileges | Dedicated app user | **CRITICAL** |
| `llave = ''` | No encryption key | 256-bit random key | **HIGH** |
| `host = '127.0.01'` | Typo in IP address | Fix to `127.0.0.1` or prod hostname | **HIGH** |
| No HTTPS | Credentials in plaintext | SSL certificate + forced HTTPS | **CRITICAL** |
| `display_errors` default | Information disclosure | Disable in production | **MEDIUM** |
| Direct DB credentials | Version control exposure | Environment variables | **HIGH** |
| No error logging | No audit trail | Configure secure logging | **MEDIUM** |

For detailed security recommendations, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations). For environment-specific configuration strategies, see [Environment Configuration](/axchisan/AmericanFood/9.2-environment-configuration). For image asset licensing requirements, see [Image Licensing Compliance](/axchisan/AmericanFood/9.3-image-licensing-compliance).

**Sources**: [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [crud/registro_INSERT.php L33-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L59)