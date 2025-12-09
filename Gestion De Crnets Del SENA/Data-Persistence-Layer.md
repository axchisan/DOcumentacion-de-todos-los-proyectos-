# Data Persistence Layer

> **Relevant source files**
> * [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)
> * [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)
> * [macos/Flutter/GeneratedPluginRegistrant.swift](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/macos/Flutter/GeneratedPluginRegistrant.swift)

## Purpose and Scope

The Data Persistence Layer implements a hybrid storage architecture that enables the SENA Digital ID Card application to function both online and offline. This layer uses two complementary storage systems: Hive for local NoSQL storage and PostgreSQL for remote, authoritative data persistence.

This document covers:

* The dual-database architecture and rationale
* Storage system characteristics (Hive vs PostgreSQL)
* The `DatabaseService` class as the abstraction layer
* Data flow patterns for read and write operations
* Connection management and environment configuration

For detailed API documentation of individual methods, see [DatabaseService Reference](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference). For information about the `Aprendiz` and `Dispositivo` data structures, see [Data Models](/axchisan/AppGestionCarnetsSENA/3.1.2-data-models-(aprendiz-and-dispositivo)). For implementation details of offline synchronization patterns, see [Offline-First Strategy](/axchisan/AppGestionCarnetsSENA/3.1.3-offline-first-strategy).

**Sources:** [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

---

## Architecture Overview

The Data Persistence Layer implements a **dual-database strategy** where local and remote storage systems serve complementary roles:

```

```

**Dual-Database Architecture**

| Storage System | Role | Data State | Access Pattern |
| --- | --- | --- | --- |
| **Hive (Local)** | Cache & Offline Storage | Current session data | Synchronous, always available |
| **PostgreSQL (Remote)** | Authoritative Source | Persistent, multi-device | Asynchronous, requires connectivity |

**Sources:** [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

 [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

---

## Storage Systems

### Hive: Local NoSQL Storage

Hive provides key-value storage for offline access and session persistence. The application uses a single Hive box named `aprendicesBox` that stores `Aprendiz` objects keyed by their identification numbers.

**Hive Box Configuration**

```

```

**Key Implementation Details:**

| Aspect | Implementation |
| --- | --- |
| Box Name | `_hiveBoxName = 'aprendicesBox'` |
| Key Type | `String` (user identification number) |
| Value Type | `Aprendiz` (custom model with TypeAdapter) |
| Access Method | `Hive.box<Aprendiz>(_hiveBoxName)` |
| Type Adapters | `AprendizAdapter` (typeId: 0), `DispositivoAdapter` (typeId: 1) |

The Hive box is accessed through a getter that provides type-safe access:

```javascript
Box<Aprendiz> get _aprendicesBox => Hive.box<Aprendiz>(_hiveBoxName);
```

**Sources:** [lib/services/database_service.dart L10-L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L10-L19)

 [lib/models/models.g.dart L9-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L9-L68)

### PostgreSQL: Remote Relational Database

PostgreSQL serves as the authoritative data store, maintaining a normalized relational schema with two primary tables:

**Database Schema**

```

```

**Connection Parameters (from .env):**

| Parameter | Environment Variable | Default Fallback |
| --- | --- | --- |
| Host | `POSTGRES_HOST` | `'default_host'` |
| Port | `POSTGRES_PORT` | `5432` |
| Database | `POSTGRES_DATABASE` | `'default_db'` |
| Username | `POSTGRES_USERNAME` | `'default_user'` |
| Password | `POSTGRES_PASSWORD` | `'default_password'` |

The connection parameters are loaded from environment variables with null-safe fallbacks:

```javascript
static String get _postgresHost => dotenv.env['POSTGRES_HOST'] ?? 'default_host';
static int get _postgresPort => int.tryParse(dotenv.env['POSTGRES_PORT'] ?? '5432') ?? 5432;
```

**Sources:** [lib/services/database_service.dart L12-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L12-L17)

 [lib/services/database_service.dart L64-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L64-L115)

---

## DatabaseService as Abstraction Layer

The `DatabaseService` class encapsulates all data persistence logic, providing a unified API that abstracts the dual-database complexity from application code. UI screens never directly access Hive or PostgreSQL; all operations flow through `DatabaseService` methods.

**DatabaseService Method Categories**

```

```

**Public API Summary:**

| Method | Parameters | Return Type | Storage Target | Purpose |
| --- | --- | --- | --- | --- |
| `validateIdentification` | `String id` | `Future<bool>` | PostgreSQL | Check if ID exists in remote system |
| `getAprendizFromLocal` | `String id` | `Future<Aprendiz?>` | Hive | Retrieve from local cache |
| `getAprendizFromPostgres` | `String id, String password` | `Future<Aprendiz?>` | PostgreSQL | Authenticate and fetch from remote |
| `saveAprendiz` | `Aprendiz aprendiz` | `Future<void>` | Both | Write-through to Hive and PostgreSQL |
| `addDevice` | `String idIdentificacion, String deviceName, String deviceId, String deviceType` | `Future<void>` | Both | Add device to user's device list |

**Sources:** [lib/services/database_service.dart L21-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L238)

---

## Data Flow Patterns

### Read Operations

The `DatabaseService` implements two distinct read patterns depending on the data source:

**Local Read Pattern (Cache-First)**

```

```

The `getAprendizFromLocal()` method provides immediate access to cached data:

```
Future<Aprendiz?> getAprendizFromLocal(String id) async {
  try {
    return _aprendicesBox.get(id);
  } catch (e) {
    return null;
  }
}
```

**Remote Read Pattern (Fetch and Cache)**

```

```

**Sources:** [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127)

 [lib/services/database_service.dart L129-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L129-L169)

 [lib/services/database_service.dart L171-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L171-L197)

### Write Operations

Write operations follow a **write-through pattern** where data is written to local storage first, then synchronized to PostgreSQL:

**Write-Through Synchronization Pattern**

```

```

The `saveAprendiz()` method orchestrates both writes:

```
Future<void> saveAprendiz(Aprendiz aprendiz) async {
  try {
    await _aprendicesBox.put(aprendiz.idIdentificacion, aprendiz);
    await _syncToPostgres(aprendiz);
  } catch (e) {}
}
```

**Sources:** [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

 [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

---

## Connection Management

### PostgreSQL Connection Lifecycle

Every database operation creates a new connection, executes queries, and closes the connection. This stateless approach ensures connections don't remain open unnecessarily:

**Connection Pattern**

```

```

Example connection lifecycle from `validateIdentification()`:

```sql
final conn = PostgreSQLConnection(
  _postgresHost,
  _postgresPort,
  _postgresDatabase,
  username: _postgresUsername,
  password: _postgresPassword,
);
await conn.open();
final results = await conn.query(
  'SELECT id_identificacion FROM aprendices WHERE id_identificacion = @id',
  substitutionValues: {'id': id},
);
await conn.close();
```

### Transaction Management

Write operations to PostgreSQL use transactions to ensure atomicity when updating related records. The `_syncToPostgres()` method demonstrates this pattern:

**Transaction Scope**

| Operation | Transaction Step | Purpose |
| --- | --- | --- |
| Upsert `aprendiz` | Step 1 | Insert or update user record |
| Delete old `dispositivos` | Step 2 | Remove existing device associations |
| Insert new `dispositivos` | Step 3 | Recreate device list from current state |

The transaction ensures that either all operations succeed or none do:

```javascript
await conn.transaction((ctx) async {
  await ctx.query(/* upsert aprendiz */);
  await ctx.query('DELETE FROM dispositivos WHERE id_identificacion = @id', ...);
  for (var dispositivo in aprendiz.dispositivos) {
    await ctx.query(/* insert dispositivo */);
  }
});
```

**Sources:** [lib/services/database_service.dart L21-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L40)

 [lib/services/database_service.dart L64-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L64-L115)

---

## Environment Configuration

### Configuration Source

Database connection parameters are loaded from environment variables using the `flutter_dotenv` package. The `DatabaseService` class reads these variables through static getters:

**Environment Variable Loading**

```

```

### Configuration Implementation

Each configuration parameter has a static getter with a fallback value:

| Getter | Environment Variable | Fallback Value | Type |
| --- | --- | --- | --- |
| `_postgresHost` | `POSTGRES_HOST` | `'default_host'` | `String` |
| `_postgresPort` | `POSTGRES_PORT` | `5432` | `int` |
| `_postgresDatabase` | `POSTGRES_DATABASE` | `'default_db'` | `String` |
| `_postgresUsername` | `POSTGRES_USERNAME` | `'default_user'` | `String` |
| `_postgresPassword` | `POSTGRES_PASSWORD` | `'default_password'` | `String` |

The port configuration includes type conversion with fallback:

```javascript
static int get _postgresPort => int.tryParse(dotenv.env['POSTGRES_PORT'] ?? '5432') ?? 5432;
```

This ensures that invalid port values default to 5432 rather than causing runtime errors.

**Sources:** [lib/services/database_service.dart L6](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L6-L6)

 [lib/services/database_service.dart L12-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L12-L17)

---

## Image Storage Strategy

Profile photos follow a hybrid storage approach distinct from other data fields:

**Image Storage Flow**

```

```

**Image Storage Locations:**

| Storage | Format | Location | Purpose |
| --- | --- | --- | --- |
| **Local (Hive)** | File path (String) | `fotoPerfilPath` field | Reference to local file |
| **Local (Filesystem)** | PNG file | Application documents directory | Actual image bytes |
| **Remote (PostgreSQL)** | Base64 string | `foto_perfil` column | Portable representation |

The `_saveImageLocally()` method handles base64 decoding and file creation:

```
Future<String?> _saveImageLocally(String base64String) async {
  try {
    final bytes = base64Decode(base64String);
    final dir = await getApplicationDocumentsDirectory();
    final file = File('${dir.path}/profile_${DateTime.now().millisecondsSinceEpoch}.png');
    await file.writeAsBytes(bytes);
    return file.path;
  } catch (e) {
    return null;
  }
}
```

**Sources:** [lib/services/database_service.dart L60-L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L60-L62)

 [lib/services/database_service.dart L157](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L157-L157)

 [lib/services/database_service.dart L199-L209](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L199-L209)