# User and Authentication Tables

> **Relevant source files**
> * [src/backend/loginValidation/validar_login.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php)
> * [src/backend/perfil/uploads/681153ef10a8b-468520576_1147758583450948_1007574650848877107_n.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/uploads/681153ef10a8b-468520576_1147758583450948_1007574650848877107_n.jpg)
> * [src/database/conexionDB.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php)
> * [src/database/configuracion.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php)
> * [src/frontend/login/css/login.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/css/login.css)
> * [src/frontend/login/login.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/login.php)

## Purpose and Scope

This document describes the database tables and schema design that support user accounts and authentication in El Rincón de ADSO. It covers the structure of the `usuarios` table, authentication-related fields, and how these tables integrate with the authentication system.

For information about the authentication workflow and login validation logic, see [Login and Session Management](/axchisan/El-rincon-de-ADSO/3.1-login-and-session-management). For details on user registration and account creation, see [User Registration](/axchisan/El-rincon-de-ADSO/3.2-user-registration). For social relationship tables (friends, messages, etc.), see [Social and Community Tables](/axchisan/El-rincon-de-ADSO/10.3-social-and-community-tables).

## Overview

The authentication system in El Rincón de ADSO relies primarily on the `usuarios` table, which stores all user account information including credentials, roles, and profile data. The system uses PostgreSQL as the database backend, hosted on Aiven, and connects via environment variables configured for Railway deployment.

```

```

**Sources:**

* [src/database/configuracion.php L1-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L9)
* [src/database/conexionDB.php L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L1-L28)
* [src/frontend/login/login.php L1-L68](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/login.php#L1-L68)
* [src/backend/loginValidation/validar_login.php L1-L65](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L1-L65)

## Database Connection Configuration

The system uses environment-based configuration for database connectivity, making it suitable for cloud deployment on Railway with Aiven PostgreSQL.

### Configuration Constants

The database connection parameters are defined in `configuracion.php` using environment variables:

| Constant | Environment Variable | Purpose |
| --- | --- | --- |
| `DB_HOST` | `PGHOST` | PostgreSQL server hostname |
| `DB_PORT` | `PGPORT` | PostgreSQL server port |
| `DB_NAME` | `PGDATABASE` | Database name |
| `DB_USER` | `PGUSER` | Database username |
| `DB_PASSWORD` | `PGPASSWORD` | Database password |
| `DB_SSLMODE` | (hardcoded) | SSL mode set to "disable" for Railway |
| `DB_SSLROOTCERT` | (hardcoded) | SSL certificate path (not needed for Railway) |

### Connection Manager

The `conexionDB` class implements a singleton pattern to manage database connections:

```

```

The singleton pattern ensures only one database connection is created per request, preventing connection overhead. The connection is established lazily on first access via `conexionDB::getConexion()`.

**Sources:**

* [src/database/configuracion.php L1-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L9)
* [src/database/conexionDB.php L4-L27](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L4-L27)

## usuarios Table Structure

The `usuarios` table is the central authentication table storing all user account information.

```

```

### Core Authentication Fields

The following fields are essential for authentication and are referenced in the login validation logic:

| Field | Type | Constraints | Purpose |
| --- | --- | --- | --- |
| `id` | INTEGER | PRIMARY KEY, AUTO INCREMENT | Unique user identifier, stored in `$_SESSION['usuario_id']` |
| `nombre_usuario` | VARCHAR | UNIQUE, NOT NULL | Username for login |
| `correo` | VARCHAR | UNIQUE, NOT NULL | Email address for login |
| `contrasena` | VARCHAR | NOT NULL | Password hash created with `password_hash()` |
| `rol` | VARCHAR | NOT NULL, DEFAULT 'user' | Role: `'user'` or `'admin'` for access control |

### Profile and Metadata Fields

Additional fields store user profile information and tracking data:

| Field | Type | Purpose |
| --- | --- | --- |
| `nombre` | VARCHAR | Display name / first name |
| `profesion` | VARCHAR | User's profession or occupation |
| `biografia` | TEXT | User biography or description |
| `foto_perfil` | VARCHAR | Filename of profile photo in uploads directory |
| `ultima_conexion` | TIMESTAMP | Last login time for online status tracking |
| `fecha_registro` | TIMESTAMP | Account creation date |

**Sources:**

* [src/backend/loginValidation/validar_login.php L26-L29](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L26-L29)
* [src/backend/loginValidation/validar_login.php L32-L36](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L32-L36)

## Authentication Flow with Database

The authentication process queries the `usuarios` table and establishes a session upon successful login.

```

```

### SQL Query Structure

The authentication query uses a prepared statement to prevent SQL injection:

```

```

This query allows users to log in with either their username or email address. The `:input` parameter is bound using PDO's prepared statement functionality.

**Sources:**

* [src/backend/loginValidation/validar_login.php L7-L64](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L7-L64)
* [src/backend/loginValidation/validar_login.php L26-L29](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L26-L29)

## Session Management Integration

Upon successful authentication, the system stores user data in PHP session variables that persist across requests.

### Session Variables

```

```

### Session Variable Mapping

| Session Variable | Source Column | Purpose |
| --- | --- | --- |
| `$_SESSION['usuario_id']` | `usuarios.id` | Primary user identifier for all queries |
| `$_SESSION['nombre_usuario']` | `usuarios.nombre_usuario` | Display username in UI |
| `$_SESSION['rol']` | `usuarios.rol` | Determine access rights (user/admin) |

### Session Lifecycle

1. **Session Start**: `session_start()` called at the beginning of login.php and validar_login.php
2. **Session Regeneration**: `session_regenerate_id(true)` called in validar_login.php for security
3. **Session Population**: User data written to `$_SESSION` after successful authentication
4. **Session Validation**: Protected pages check `$_SESSION['usuario_id']` to verify authentication
5. **Session Destruction**: Logout clears session variables

**Sources:**

* [src/backend/loginValidation/validar_login.php L2-L5](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L2-L5)
* [src/backend/loginValidation/validar_login.php L34-L47](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L34-L47)
* [src/frontend/login/login.php L2](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/login/login.php#L2-L2)

## Password Security

The system implements industry-standard password hashing for credential security.

### Password Hashing Mechanism

```

```

### Password Functions

| Function | Usage | Location |
| --- | --- | --- |
| `password_hash()` | Create hash during registration | Registration logic |
| `password_verify($input, $hash)` | Validate password during login | [validar_login.php L33](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/validar_login.php#L33-L33) |

The `password_verify()` function performs constant-time comparison to prevent timing attacks. The system uses PHP's default bcrypt algorithm, which automatically includes salt and work factor.

**Sources:**

* [src/backend/loginValidation/validar_login.php L33](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L33-L33)

## Role-Based Access Control

The `usuarios.rol` field enables role-based access control with two primary roles.

### Role Types

```

```

### Role Implementation

The role field determines:

* **Post-login Redirect**: Users go to index.php, admins to panel-admin.php
* **Feature Access**: Admin-only features check `$_SESSION['rol'] === 'admin'`
* **UI Elements**: Conditional rendering based on role

**Sources:**

* [src/backend/loginValidation/validar_login.php L42-L47](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L42-L47)

## Database Query Patterns

All authentication queries use PDO prepared statements for SQL injection prevention.

### Connection Pattern

```

```

### Error Handling

The connection manager sets error mode to exceptions:

```

```

This enables try-catch error handling in authentication logic, allowing graceful error messages to users without exposing database details.

**Sources:**

* [src/database/conexionDB.php L19-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L19-L20)
* [src/backend/loginValidation/validar_login.php L24-L29](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L24-L29)
* [src/backend/loginValidation/validar_login.php L57-L59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L57-L59)

## Related Tables and Foreign Keys

The `usuarios` table serves as a parent table for many other tables in the system through foreign key relationships.

```

```

All tables that reference users use `usuario_id` (or similar naming) as a foreign key pointing to `usuarios.id`. This enables cascading operations and maintains referential integrity. For detailed information about these related tables, see [Resource and Content Tables](/axchisan/El-rincon-de-ADSO/10.2-resource-and-content-tables) and [Social and Community Tables](/axchisan/El-rincon-de-ADSO/10.3-social-and-community-tables).

**Sources:**

* [src/backend/loginValidation/validar_login.php L26-L36](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L26-L36)

## Security Considerations

The authentication table design implements several security best practices:

| Security Feature | Implementation | Location |
| --- | --- | --- |
| **Password Hashing** | Bcrypt via `password_hash()`/`password_verify()` | [validar_login.php L33](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/validar_login.php#L33-L33) |
| **SQL Injection Prevention** | PDO prepared statements with parameter binding | [validar_login.php L27-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/validar_login.php#L27-L28) |
| **Session Security** | `session_regenerate_id(true)` after login | [validar_login.php L5](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/validar_login.php#L5-L5) |
| **Unique Constraints** | UNIQUE on `nombre_usuario` and `correo` | Database schema |
| **Connection Pooling** | Singleton pattern prevents connection exhaustion | [conexionDB.php L5-L26](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/conexionDB.php#L5-L26) |
| **Error Mode** | PDO exceptions prevent information leakage | [conexionDB.php L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/conexionDB.php#L20-L20) |
| **SSL Configuration** | Configurable SSL mode for production | [configuracion.php L7](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/configuracion.php#L7-L7) |

**Sources:**

* [src/backend/loginValidation/validar_login.php L5-L64](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/loginValidation/validar_login.php#L5-L64)
* [src/database/conexionDB.php L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L1-L28)
* [src/database/configuracion.php L1-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L9)