# DatabaseService Reference

> **Relevant source files**
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

## Purpose and Scope

This page provides a complete API reference for the `DatabaseService` class, which serves as the central data orchestration layer in the SENA Digital ID Card application. The `DatabaseService` abstracts all data persistence operations, managing both local Hive storage and remote PostgreSQL synchronization.

This document covers:

* Public method APIs with parameters, return types, and behavior descriptions
* Internal synchronization mechanisms and private helper methods
* Environment configuration for database connections
* Data flow patterns and transaction handling

For information about the data models used by this service, see [Data Models](/axchisan/AppGestionCarnetsSENA/3.1.2-data-models-(aprendiz-and-dispositivo)). For the overall offline-first architecture strategy, see [Offline-First Strategy](/axchisan/AppGestionCarnetsSENA/3.1.3-offline-first-strategy). For security considerations related to password handling and credentials, see [Security Implementation](/axchisan/AppGestionCarnetsSENA/4.4-security-implementation).

**Sources:** [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

---

## Class Overview

The `DatabaseService` class is defined in [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

 and implements a dual-database persistence strategy. It provides synchronous access to local Hive storage and asynchronous synchronization with a remote PostgreSQL database.

### Class Architecture

```

```

**Sources:** [lib/services/database_service.dart L9-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L9-L239)

---

## Configuration

The `DatabaseService` relies on environment variables loaded from a `.env` file via `flutter_dotenv` for PostgreSQL connection parameters.

### Environment Variables

| Variable | Type | Default | Description |
| --- | --- | --- | --- |
| `POSTGRES_HOST` | String | `'default_host'` | PostgreSQL server hostname or IP address |
| `POSTGRES_PORT` | Integer | `5432` | PostgreSQL server port number |
| `POSTGRES_DATABASE` | String | `'default_db'` | Database name |
| `POSTGRES_USERNAME` | String | `'default_user'` | Database authentication username |
| `POSTGRES_PASSWORD` | String | `'default_password'` | Database authentication password |

### Configuration Access Pattern

```

```

The configuration is accessed via static getters with null-coalescing operators to provide safe defaults:

```

```

**Sources:** [lib/services/database_service.dart L12-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L12-L17)

---

## Hive Box Access

The service maintains a reference to a single Hive box for local storage:

| Constant | Value | Purpose |
| --- | --- | --- |
| `_hiveBoxName` | `'aprendicesBox'` | Box name for storing Aprendiz objects |

The box is accessed via the `_aprendicesBox` getter:

```

```

This box must be opened before the `DatabaseService` is used, typically in `main.dart` during application initialization.

**Sources:** [lib/services/database_service.dart L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L10-L10)

 [lib/services/database_service.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L19-L19)

---

## Public Methods

### validateIdentification

Validates whether an identification number exists in the PostgreSQL database.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `id` | `String` | The identification number to validate |

**Returns:** `Future<bool>` - `true` if the ID exists in the `aprendices` table, `false` otherwise

**Behavior:**

1. Opens a PostgreSQL connection
2. Executes a SELECT query on the `aprendices` table with parameterized substitution
3. Closes the connection
4. Returns `true` if any results are found, `false` if no results or on error

**SQL Query:**

```

```

**Error Handling:** Returns `false` on any exception (connection errors, query errors, etc.)

**Use Case:** Called during user registration to verify that an identification number exists in the SENA institutional database before allowing profile creation.

**Sources:** [lib/services/database_service.dart L21-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L40)

---

### saveAprendiz

Saves an Aprendiz object to local Hive storage and synchronizes to PostgreSQL.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `aprendiz` | `Aprendiz` | The Aprendiz object to save |

**Returns:** `Future<void>` - Completes when both local save and synchronization attempts finish

**Behavior:**

1. Writes the Aprendiz object to Hive using `idIdentificacion` as the key
2. Calls `_syncToPostgres` to synchronize to PostgreSQL
3. Completes silently on success or error

**Data Flow:**

```

```

**Error Handling:** Catches all exceptions and completes silently. Local data is always saved even if PostgreSQL synchronization fails.

**Use Case:** Called after registration, profile updates, or device modifications to persist changes both locally and remotely.

**Sources:** [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

---

### getAprendizFromLocal

Retrieves an Aprendiz object from local Hive storage.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `id` | `String` | The identification number (key) to retrieve |

**Returns:** `Future<Aprendiz?>` - The Aprendiz object if found, `null` if not found or on error

**Behavior:**

1. Queries the Hive box using the provided ID as the key
2. Returns the Aprendiz object if it exists
3. Returns `null` if the key doesn't exist or on error

**Error Handling:** Returns `null` on any exception

**Use Case:**

* Primary data access method for offline functionality
* Used by HomeScreen to display user profile
* Called by `addDevice` to retrieve current user data before modification
* Used by SplashScreen to detect existing sessions

**Sources:** [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127)

---

### getAprendizFromPostgres

Authenticates a user by retrieving their Aprendiz object from PostgreSQL using credentials.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `id` | `String` | The user's identification number |
| `password` | `String` | The user's hashed password (SHA-256) |

**Returns:** `Future<Aprendiz?>` - Complete Aprendiz object with devices if authentication succeeds, `null` otherwise

**Behavior:**

```

```

**SQL Query:**

```

```

**Special Processing:**

1. **Photo handling:** Base64-encoded photos from PostgreSQL are decoded and saved to local filesystem via `_saveImageLocally`
2. **Device loading:** Calls `_loadDevicesFromPostgres` to retrieve associated devices
3. **Date parsing:** Handles both String and DateTime types for `fecha_registro` field

**Error Handling:** Returns `null` on any exception (connection errors, query errors, parsing errors)

**Use Case:** Primary authentication method called by LoginScreen to validate credentials and populate local cache on successful login.

**Sources:** [lib/services/database_service.dart L129-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L129-L169)

---

### addDevice

Adds a new device to a user's device list and synchronizes to both databases.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `idIdentificacion` | `String` | The user's identification number |
| `deviceName` | `String` | Human-readable name for the device |
| `deviceId` | `String` | Unique device identifier |
| `deviceType` | `String` | Device type category |

**Returns:** `Future<void>` - Completes when operation finishes

**Behavior:**

```

```

**Steps:**

1. Retrieves the current Aprendiz from local Hive storage
2. Creates a new `Dispositivo` object with current timestamp
3. Adds the device to a copy of the dispositivos list
4. Constructs a new Aprendiz object with the updated device list
5. Calls `saveAprendiz` to persist changes locally and remotely

**Error Handling:** Catches all exceptions and completes silently. If the user is not found in local storage, the operation is skipped.

**Use Case:** Called by DeviceManagementScreen when users register new devices (smartphones, tablets, etc.)

**Sources:** [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

---

## Private Methods

### _syncToPostgres

Internal method that synchronizes an Aprendiz object to PostgreSQL.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `aprendiz` | `Aprendiz` | The Aprendiz object to synchronize |

**Returns:** `Future<void>` - Completes when synchronization finishes or fails

**Behavior:**

This method performs a transactional upsert operation in PostgreSQL:

1. **Photo encoding:** If the Aprendiz has a local photo path, reads the file and base64-encodes it
2. **Transaction start:** Opens a database transaction
3. **Aprendiz upsert:** Inserts or updates the aprendices table record
4. **Device synchronization:** * Deletes all existing devices for the user * Inserts all devices from the Aprendiz.dispositivos list
5. **Transaction commit:** Commits the transaction
6. **Connection close:** Closes the PostgreSQL connection

**SQL Operations:**

**Aprendiz Upsert:**

```

```

**Device Synchronization:**

```

```

**Transaction Guarantees:**

* All operations succeed or all fail atomically
* Device list in PostgreSQL exactly matches the Aprendiz.dispositivos list after completion

**Error Handling:** Catches all exceptions and completes silently. Failed synchronizations do not affect local Hive data.

**Sources:** [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

---

### _loadDevicesFromPostgres

Internal method that retrieves all devices for a specific user from PostgreSQL.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `idIdentificacion` | `String` | The user's identification number |

**Returns:** `Future<List<Dispositivo>>` - List of Dispositivo objects, empty list on error

**Behavior:**

1. Opens a PostgreSQL connection
2. Queries the `dispositivos` table for all devices matching the user ID
3. Closes the connection
4. Maps query results to Dispositivo objects
5. Handles both String and DateTime types for `fecha_registro` field

**SQL Query:**

```

```

**Error Handling:** Returns empty list on any exception

**Use Case:** Called by `getAprendizFromPostgres` to populate the devices list when loading a user from remote database.

**Sources:** [lib/services/database_service.dart L171-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L171-L197)

---

### _saveImageLocally

Internal helper method that decodes base64 image data and saves it to local filesystem.

**Signature:**

```

```

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `base64String` | `String` | Base64-encoded image data |

**Returns:** `Future<String?>` - Local file path to saved image, `null` on error

**Behavior:**

1. Decodes the base64 string to bytes
2. Gets the application documents directory via `path_provider`
3. Creates a unique filename using current timestamp: `profile_<milliseconds>.png`
4. Writes the bytes to the file
5. Returns the absolute file path

**Error Handling:** Returns `null` on any exception (decode errors, filesystem errors, etc.)

**Use Case:** Called by `getAprendizFromPostgres` to convert base64-encoded profile photos from PostgreSQL into local file paths that can be used by Flutter Image widgets.

**Sources:** [lib/services/database_service.dart L199-L209](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L199-L209)

---

## Data Synchronization Strategy

### Write-Through Pattern

```

```

The write-through pattern ensures:

* **Local-first:** Hive is always updated successfully
* **Best-effort sync:** PostgreSQL synchronization is attempted but failures don't roll back local changes
* **Offline tolerance:** Users can make changes offline; synchronization will fail gracefully

**Sources:** [lib/services/database_service.dart L42-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L119)

---

### Read Strategy

```

```

Two distinct read patterns:

* **Cache-first:** `getAprendizFromLocal` for offline access and general operations
* **Remote authentication:** `getAprendizFromPostgres` for login, bypassing cache to verify credentials

**Sources:** [lib/services/database_service.dart L121-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L197)

---

## Database Schema Requirements

### PostgreSQL Tables

The `DatabaseService` expects the following PostgreSQL schema:

**aprendices table:**

| Column | Type | Constraints |
| --- | --- | --- |
| `id_identificacion` | VARCHAR/TEXT | PRIMARY KEY |
| `nombre_completo` | VARCHAR/TEXT | NOT NULL |
| `programa_formacion` | VARCHAR/TEXT | NOT NULL |
| `numero_ficha` | VARCHAR/TEXT | NOT NULL |
| `tipo_sangre` | VARCHAR/TEXT | NULLABLE |
| `foto_perfil` | TEXT | NULLABLE (base64) |
| `contrasena` | VARCHAR/TEXT | NOT NULL (hashed) |
| `email` | VARCHAR/TEXT | NULLABLE |
| `fecha_registro` | TIMESTAMP | NOT NULL |

**dispositivos table:**

| Column | Type | Constraints |
| --- | --- | --- |
| `id_dispositivo` | VARCHAR/TEXT | Part of composite primary key |
| `id_identificacion` | VARCHAR/TEXT | Part of composite primary key, FOREIGN KEY |
| `nombre_dispositivo` | VARCHAR/TEXT | NOT NULL |
| `tipo_dispositivo` | VARCHAR/TEXT | NULLABLE |
| `fecha_registro` | TIMESTAMP | NOT NULL |

**Primary Keys:**

* `aprendices`: `id_identificacion`
* `dispositivos`: `(id_identificacion, id_dispositivo)`

**Sources:** [lib/services/database_service.dart L65-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L65-L114)

---

## Error Handling Philosophy

The `DatabaseService` implements a **fail-silent** error handling strategy:

| Method | Error Behavior |
| --- | --- |
| `validateIdentification` | Returns `false` |
| `saveAprendiz` | Completes silently (local save succeeds, sync may fail) |
| `getAprendizFromLocal` | Returns `null` |
| `getAprendizFromPostgres` | Returns `null` |
| `addDevice` | Completes silently |
| `_syncToPostgres` | Completes silently |
| `_loadDevicesFromPostgres` | Returns empty list |
| `_saveImageLocally` | Returns `null` |

**Rationale:**

* **Offline tolerance:** Network errors should not crash the app
* **Local-first:** Local operations always succeed when possible
* **Graceful degradation:** UI layers handle null/false results appropriately

**Limitations:** No error reporting or retry mechanisms. Calling code cannot distinguish between "not found" and "error occurred."

**Sources:** [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

---

## Usage Examples

### Registration Flow

```

```

### Login Flow

```

```

### Offline Operation

```

```

### Device Management

```

```

**Sources:** [lib/services/database_service.dart L21-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L238)

---

## Dependencies

### External Packages

| Package | Usage |
| --- | --- |
| `hive` | Local NoSQL storage |
| `postgres` | PostgreSQL database driver |
| `path_provider` | Filesystem path access for image storage |
| `flutter_dotenv` | Environment variable loading |
| `dart:convert` | Base64 encoding/decoding |
| `dart:io` | File I/O operations |

### Internal Dependencies

| Module | Usage |
| --- | --- |
| `../models/models.dart` | `Aprendiz` and `Dispositivo` model classes |

**Sources:** [lib/services/database_service.dart L1-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L7)

---

## Thread Safety and Concurrency

**Note:** The `DatabaseService` does not implement any locking or concurrency control mechanisms.

**Implications:**

* Multiple simultaneous calls to `saveAprendiz` may result in race conditions
* PostgreSQL connection objects are created per-operation (not pooled)
* Hive operations on the same box should be thread-safe per Hive's design

**Recommendation:** UI code should serialize operations on the same Aprendiz object to avoid data races.

**Sources:** [lib/services/database_service.dart L9-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L9-L239)