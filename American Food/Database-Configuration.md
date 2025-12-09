# Database Configuration

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

## Purpose and Scope

This document describes the database configuration system defined in [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

 This file establishes the core database connection parameters and application security keys as PHP constants that are consumed throughout the application.

For documentation on how these constants are used to establish database connections, see [Connection Manager](/axchisan/AmericanFood/3.2-connection-manager).

**Sources:** [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)

---

## Configuration Constants

The [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)

 file defines five PHP constants using the `define()` function. These constants provide centralized configuration for database access and application security:

| Constant | Current Value | Purpose | Type |
| --- | --- | --- | --- |
| `host` | `'127.0.01'` | MySQL server address | Database Connection |
| `user` | `'root'` | MySQL username | Database Connection |
| `password` | `''` | MySQL password | Database Connection |
| `database` | `'american_food'` | Target database name | Database Connection |
| `llave` | `''` | Application security key | Security |

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Constant Definition Pattern

The configuration file uses PHP's `define()` function to create global constants. Constants defined with `define()` are:

* **Globally accessible** throughout the application after inclusion
* **Case-sensitive** (the constants use lowercase names)
* **Immutable** after definition (cannot be redefined or modified)
* **Available in all scopes** without requiring `global` keyword

```
define(name, value)
```

Each constant is defined with a string name and string value. The lack of a third parameter means these constants are case-sensitive by default.

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Configuration Usage Flow

The following diagram illustrates how the configuration constants flow from definition to consumption:

**Diagram: Configuration Constant Usage Pattern**

```

```

**Sources:** [config/configuracion.php L2-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L2-L7)

 [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

 [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

---

## Database Connection Parameters

### host Constant

```

```

Specifies the MySQL server address. The current value `'127.0.01'` **contains a typo** - it should be `'127.0.0.1'` (localhost loopback address). This typo may cause connection failures depending on MySQL's hostname resolution behavior.

**Implications:**

* Restricts database connections to the local machine only
* No remote database access is possible
* Suitable for development environments
* May fail to connect due to incorrect IP format

**Sources:** [config/configuracion.php L3](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L3)

### user Constant

```

```

Specifies the MySQL username for authentication. The `root` user is the MySQL superuser account with full administrative privileges.

**Implications:**

* Application has unrestricted database access
* Can perform DDL operations (CREATE, DROP, ALTER)
* Security risk if application is compromised
* Not following principle of least privilege

**Sources:** [config/configuracion.php L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L4-L4)

### password Constant

```

```

Specifies the MySQL password. Currently set to an empty string, indicating no password authentication is required.

**Implications:**

* **Critical security vulnerability** for production environments
* Anyone with network access can connect as root
* Indicates development/local testing configuration
* Must be changed before deployment

**Sources:** [config/configuracion.php L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L5-L5)

### database Constant

```

```

Specifies the target database name. All queries executed through the connection established in [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)

 will default to this database schema.

**Implications:**

* Application operates on single database
* Database must exist before connection attempt
* Database name references restaurant management system

**Sources:** [config/configuracion.php L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L6-L6)

### llave Constant

```

```

Defines an application-level security key. Currently empty, suggesting it is unused or not yet configured.

**Potential Uses:**

* Session encryption key
* CSRF token generation
* API authentication token
* Data encryption key

The empty value indicates this security feature is not implemented.

**Sources:** [config/configuracion.php L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L7-L7)

---

## Configuration Consumption

The following diagram shows which code entities consume each configuration constant:

**Diagram: Constant Consumption Map**

```

```

The four database constants (`host`, `user`, `password`, `database`) are passed directly to the `mysqli` constructor in [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

 The `llave` constant is not consumed in the provided codebase.

**Sources:** [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

---

## Configuration Pattern Benefits

The centralized configuration pattern implemented in this file provides several architectural benefits:

### Separation of Concerns

Configuration is isolated from business logic and connection management code. Changes to database credentials require modifying only one file.

### Single Source of Truth

All database connection parameters are defined in one location, preventing inconsistencies across multiple connection points.

### Environment Adaptability

By modifying this single file, the application can be pointed to different database environments:

* Development: localhost with no password
* Staging: staging server with test credentials
* Production: production server with secure credentials

### Include-Once Safety

The configuration file is designed to be included via `include_once` in [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

 preventing duplicate constant definition errors if multiple files include the connection manager.

**Sources:** [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)

 [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2)

---

## Security Analysis

The current configuration reveals this is a **development environment** with significant security vulnerabilities:

### Critical Issues

| Issue | Current State | Risk Level | Production Requirement |
| --- | --- | --- | --- |
| Empty password | `password = ''` | **CRITICAL** | Must set strong password |
| Root user | `user = 'root'` | **HIGH** | Create limited-privilege user |
| Localhost only | `host = '127.0.01'` | Low (intentional) | May need remote host |
| Host IP typo | `127.0.01` vs `127.0.0.1` | **MEDIUM** | Fix to valid IP |
| Empty security key | `llave = ''` | **HIGH** | Generate strong random key |

### Recommended Production Configuration

```

```

For deployment guidance, see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations) and [Environment Configuration](/axchisan/AmericanFood/9.2-environment-configuration).

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Configuration File Structure

**Diagram: Configuration File Composition**

```

```

The file follows a simple structure:

* Opening PHP tag without closing tag (best practice for config files)
* Blank line for readability
* Four database connection constants grouped together
* One security constant
* Trailing blank line

The absence of a closing `?>` tag prevents accidental whitespace output that could cause "headers already sent" errors.

**Sources:** [config/configuracion.php L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L8)