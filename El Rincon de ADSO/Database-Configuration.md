# Database Configuration

> **Relevant source files**
> * [src/database/conexionDB.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php)
> * [src/database/configuracion.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php)

## Purpose and Scope

This document describes the database configuration and connection management system for El Rincón de ADSO. It covers the environment variable-based configuration system, the singleton connection pattern implementation, and SSL handling for PostgreSQL connections hosted on Aiven.

For information about the actual database schema and table structures, see [Database Schema](/axchisan/El-rincon-de-ADSO/10-database-schema). For deployment configuration including environment variable setup in Railway, see [Deployment with Railway and Docker](/axchisan/El-rincon-de-ADSO/2.2-deployment-with-railway-and-docker).

---

## Configuration Architecture Overview

The database configuration system consists of two primary components that work together to establish and maintain database connections throughout the application lifecycle:

```

```

**Connection Flow Diagram**

The system uses a two-tier architecture where configuration is separated from connection management. Environment variables are read once at configuration time and stored as PHP constants, which are then consumed by the singleton connection manager.

Sources: [src/database/configuracion.php L1-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L9)

 [src/database/conexionDB.php L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L1-L28)

---

## Environment Variables

The database configuration reads five environment variables that must be set in the deployment environment (Railway platform):

| Environment Variable | PHP Constant | Purpose | Required |
| --- | --- | --- | --- |
| `PGHOST` | `DB_HOST` | PostgreSQL server hostname | Yes |
| `PGPORT` | `DB_PORT` | PostgreSQL server port (typically 5432) | Yes |
| `PGDATABASE` | `DB_NAME` | Database name | Yes |
| `PGUSER` | `DB_USER` | Database username | Yes |
| `PGPASSWORD` | `DB_PASSWORD` | Database password | Yes |

The `configuracion.php` file reads these environment variables using PHP's `getenv()` function and defines them as global constants:

```
DB_HOST = getenv('PGHOST')
DB_PORT = getenv('PGPORT')
DB_NAME = getenv('PGDATABASE')
DB_USER = getenv('PGUSER')
DB_PASSWORD = getenv('PGPASSWORD')
```

Additionally, two SSL-related constants are hardcoded:

* `DB_SSLMODE`: Set to `"disable"` for Railway deployment
* `DB_SSLROOTCERT`: Set to `""` (empty string, not needed)

The comment on [src/database/configuracion.php L7](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L7-L7)

 indicates that Railway does not require explicit SSL configuration.

Sources: [src/database/configuracion.php L2-L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L2-L8)

---

## Connection Manager Implementation

The `conexionDB` class implements the singleton pattern to ensure a single database connection is reused throughout the application lifecycle.

```

```

**conexionDB Class Structure**

### Singleton Pattern

The class maintains a single static instance:

```

```

The `getConexion()` static method [src/database/conexionDB.php L7-L26](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L7-L26)

 implements lazy initialization:

1. **Check if connection exists**: If `self::$conexion === null`, proceed to create connection
2. **Build DSN string**: Constructs PostgreSQL connection string
3. **Create PDO instance**: Instantiates new PDO with credentials
4. **Configure error handling**: Sets `PDO::ATTR_ERRMODE` to `PDO::ERRMODE_EXCEPTION`
5. **Return connection**: Returns the PDO instance

### DSN Construction

The Data Source Name (DSN) is constructed on [src/database/conexionDB.php L10-L17](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L10-L17)

:

```
"pgsql:host={DB_HOST};port={DB_PORT};dbname={DB_NAME}"
```

The SSL mode is conditionally appended only if `DB_SSLMODE` is set and not equal to `"disable"`:

```

```

For the Railway deployment, this condition evaluates to false, so SSL mode is not included in the DSN.

### PDO Configuration

After instantiation, the connection is configured with exception-based error handling on [src/database/conexionDB.php L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L20-L20)

:

```

```

This ensures that database errors throw `PDOException` objects rather than triggering PHP warnings or returning false values.

Sources: [src/database/conexionDB.php L4-L26](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L4-L26)

---

## SSL Configuration Strategy

The SSL configuration is designed to be flexible while defaulting to Railway's requirements:

```

```

**SSL Decision Flow**

The current configuration explicitly disables SSL for Railway deployment:

* `DB_SSLMODE = "disable"` on [src/database/configuracion.php L7](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L7-L7)
* `DB_SSLROOTCERT = ""` on [src/database/configuracion.php L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L8-L8)

This is appropriate for Railway's PostgreSQL connection model. If migrating to a different hosting provider that requires SSL, these values can be changed to:

* `DB_SSLMODE = "require"` (or `"verify-ca"` or `"verify-full"`)
* `DB_SSLROOTCERT = "/path/to/ca-certificate.crt"` (if needed)

Sources: [src/database/configuracion.php L7-L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L7-L8)

 [src/database/conexionDB.php L15-L17](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L15-L17)

---

## Error Handling

The connection manager implements a simple but effective error handling strategy:

### Exception Catching

The entire connection creation logic is wrapped in a try-catch block on [src/database/conexionDB.php L9-L23](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L9-L23)

:

```

```

### Failure Behavior

If connection fails:

1. A `PDOException` is caught
2. The error message is displayed with an "❌" emoji prefix
3. Script execution terminates using `die()`

This approach is appropriate for critical database failures where the application cannot function without database connectivity. All subsequent operations will fail to execute, preventing partial or corrupted state.

### Error Mode Configuration

The PDO error mode is set to `PDO::ERRMODE_EXCEPTION` on [src/database/conexionDB.php L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L20-L20)

 which means:

* All database errors throw exceptions
* No need to check return values for false
* Errors can be caught at any level of the application
* Stack traces are available for debugging

Sources: [src/database/conexionDB.php L20-L22](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L20-L22)

---

## Integration with Application Code

The connection manager is used throughout the application by requiring the connection file and calling the static method:

```

```

**Application Integration Pattern**

### Typical Usage Pattern

1. **Include connection manager**: `require_once "conexionDB.php";` (or appropriate relative path)
2. **Get connection**: `$conn = conexionDB::getConexion();`
3. **Prepare statement**: `$stmt = $conn->prepare("SELECT ...");`
4. **Execute with parameters**: `$stmt->execute([...]);`
5. **Fetch results**: `$results = $stmt->fetchAll();`

### Connection Reuse

Because of the singleton pattern:

* The first call to `getConexion()` creates the connection
* All subsequent calls return the same PDO instance
* No connection pooling is needed
* Connection persists for the duration of the request

### Benefits of Centralized Configuration

| Benefit | Description |
| --- | --- |
| **Single Source of Truth** | All database credentials in one place |
| **Environment-based** | Different settings for dev/staging/production without code changes |
| **Lazy Loading** | Connection only created when first needed |
| **Consistent Error Handling** | All connection errors handled identically |
| **Easy Migration** | Change host/credentials in one location |

Sources: [src/database/conexionDB.php L2](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L2-L2)

 [src/database/conexionDB.php L7](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L7-L7)

---

## Configuration Constants Reference

### Complete Constants Table

| Constant Name | Data Type | Source | Default Value | Modifiable |
| --- | --- | --- | --- | --- |
| `DB_HOST` | string | `PGHOST` env var | N/A | Via environment |
| `DB_PORT` | string | `PGPORT` env var | N/A | Via environment |
| `DB_NAME` | string | `PGDATABASE` env var | N/A | Via environment |
| `DB_USER` | string | `PGUSER` env var | N/A | Via environment |
| `DB_PASSWORD` | string | `PGPASSWORD` env var | N/A | Via environment |
| `DB_SSLMODE` | string | Hardcoded | `"disable"` | Via code change |
| `DB_SSLROOTCERT` | string | Hardcoded | `""` | Via code change |

### Access Pattern

All constants are global and accessible after `require_once "configuracion.php"`:

```

```

However, application code should not access these directly. Instead, use `conexionDB::getConexion()` to obtain a configured PDO connection.

Sources: [src/database/configuracion.php L2-L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L2-L8)

---

## Security Considerations

### Environment Variable Protection

* Credentials are never hardcoded in source files
* Environment variables are set at the platform level (Railway)
* Credentials are not committed to version control
* Each deployment environment can have different credentials

### PDO Prepared Statements

The system uses PDO, which supports prepared statements throughout the application:

* Prevents SQL injection attacks
* Automatic parameter escaping
* Type-safe parameter binding
* See [Data Validation and SQL Security](/axchisan/El-rincon-de-ADSO/11.2-data-validation-and-sql-security) for detailed coverage

### Connection Security

* SSL can be enabled by changing `DB_SSLMODE` to `"require"`
* Currently disabled for Railway but easily configurable
* No root certificate required for Railway's connection model

Sources: [src/database/configuracion.php L1-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L9)

 [src/database/conexionDB.php L15-L17](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L15-L17)

---

## File Locations

The database configuration system consists of two files in the `src/database/` directory:

```markdown
src/database/
├── configuracion.php    # Environment variable reading and constant definition
└── conexionDB.php       # Singleton connection manager class
```

These files must be included in any PHP script that requires database access. The relative path will vary depending on the script's location:

* From `src/`: `require_once "database/conexionDB.php";`
* From `src/backend/`: `require_once "../database/conexionDB.php";`
* From `src/backend/perfil/`: `require_once "../../database/conexionDB.php";`

The `conexionDB.php` file automatically includes `configuracion.php` on [src/database/conexionDB.php L2](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L2-L2)

 so application code only needs to include the connection manager.

Sources: [src/database/configuracion.php L1](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L1)

 [src/database/conexionDB.php L1-L2](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L1-L2)