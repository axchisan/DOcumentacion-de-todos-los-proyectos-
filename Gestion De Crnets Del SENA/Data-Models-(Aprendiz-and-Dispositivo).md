# Data Models (Aprendiz and Dispositivo)

> **Relevant source files**
> * [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)
> * [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)
> * [macos/Flutter/GeneratedPluginRegistrant.swift](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/macos/Flutter/GeneratedPluginRegistrant.swift)

## Purpose and Scope

This document provides comprehensive documentation of the two core data models used throughout the application: `Aprendiz` (apprentice/student) and `Dispositivo` (device). These models define the structure of user profile data and registered device data, and are persisted in both the local Hive database and the remote PostgreSQL database.

This page covers:

* Class structure and field definitions for both models
* Hive type adapter code generation for local persistence
* JSON serialization for data interchange
* The one-to-many relationship between Aprendiz and Dispositivo entities

For information about how these models are consumed by the database service layer, see [DatabaseService Reference](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference). For details on the overall persistence strategy, see [Offline-First Strategy](/axchisan/AppGestionCarnetsSENA/3.1.3-offline-first-strategy).

**Sources:** [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

---

## Model Overview and Relationships

The application uses two primary data models defined in [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)

:

| Model | Purpose | Hive Type ID | Primary Key |
| --- | --- | --- | --- |
| `Aprendiz` | Represents a SENA apprentice user profile | 0 | `idIdentificacion` |
| `Dispositivo` | Represents a registered device belonging to an apprentice | 1 | `idDispositivo` + `idIdentificacion` (composite) |

These models form a **one-to-many relationship**: each `Aprendiz` can have zero or more `Dispositivo` objects stored in the `dispositivos` field (a `List<Dispositivo>`).

### Relationship Diagram

```

```

**Sources:** [lib/models/models.dart L5-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L5-L106)

---

## Aprendiz Model

The `Aprendiz` class represents a complete user profile for a SENA apprentice. It is defined with Hive annotations for local persistence and includes all authentication, identification, and profile information.

### Class Definition

The `Aprendiz` class is annotated with `@HiveType(typeId: 0)` at [lib/models/models.dart L5](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L5-L5)

 indicating it is the first registered Hive type in the application.

### Field Specifications

| Field | Type | Hive Field ID | Nullable | Description |
| --- | --- | --- | --- | --- |
| `idIdentificacion` | `String` | 0 | No | Unique identification number (primary key) |
| `nombreCompleto` | `String` | 1 | No | Full name of the apprentice |
| `programaFormacion` | `String` | 2 | No | Training program name |
| `numeroFicha` | `String` | 3 | No | Training group/cohort number |
| `tipoSangre` | `String?` | 4 | Yes | Blood type (optional) |
| `fotoPerfilPath` | `String?` | 5 | Yes | Local file system path to profile photo |
| `contrasena` | `String` | 6 | No | SHA-256 hashed password |
| `email` | `String?` | 7 | Yes | Email address (optional) |
| `fechaRegistro` | `DateTime` | 8 | No | Registration timestamp |
| `dispositivos` | `List<Dispositivo>` | 9 | No | List of registered devices (defaults to empty list) |

**Sources:** [lib/models/models.dart L6-L39](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L6-L39)

### Constructor

The `Aprendiz` constructor defined at [lib/models/models.dart L28-L39](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L28-L39)

 requires all non-nullable fields and provides a default empty list for `dispositivos`:

```

```

### JSON Serialization

The model provides bidirectional JSON conversion:

* **toJson()** at [lib/models/models.dart L41-L52](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L41-L52)  - Converts the `Aprendiz` instance to a `Map<String, dynamic>`, serializing the `fechaRegistro` to ISO 8601 string format and recursively calling `toJson()` on each `Dispositivo` in the list.
* **fromJson()** at [lib/models/models.dart L54-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L54-L67)  - Factory constructor that creates an `Aprendiz` instance from a JSON map, parsing the ISO 8601 date string and recursively deserializing the `dispositivos` list.

**Sources:** [lib/models/models.dart L41-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L41-L67)

### Usage in DatabaseService

The `Aprendiz` model is used throughout [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

 in the following ways:

```

```

**Sources:** [lib/services/database_service.dart L42-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L169)

Key database operations:

1. **Local Storage** - The Hive box at [lib/services/database_service.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L19-L19)  uses `idIdentificacion` as the key: `_aprendicesBox.put(aprendiz.idIdentificacion, aprendiz)`
2. **PostgreSQL Persistence** - The `_syncToPostgres` method at [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)  performs an UPSERT operation on the `aprendices` table, encoding the profile photo as base64 for storage.
3. **Retrieval from PostgreSQL** - The `getAprendizFromPostgres` method at [lib/services/database_service.dart L129-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L129-L169)  constructs an `Aprendiz` object from query results and loads associated devices via `_loadDevicesFromPostgres`.

**Sources:** [lib/services/database_service.dart L19-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L19-L169)

---

## Dispositivo Model

The `Dispositivo` class represents a single device registered to an apprentice. It is defined with Hive annotations and establishes a foreign key relationship to `Aprendiz` via `idIdentificacion`.

### Class Definition

The `Dispositivo` class is annotated with `@HiveType(typeId: 1)` at [lib/models/models.dart L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L70-L70)

 making it the second registered Hive type.

### Field Specifications

| Field | Type | Hive Field ID | Nullable | Description |
| --- | --- | --- | --- | --- |
| `idDispositivo` | `String` | 0 | No | Unique device identifier (part of composite primary key) |
| `idIdentificacion` | `String` | 1 | No | Foreign key to Aprendiz (part of composite primary key) |
| `nombreDispositivo` | `String` | 2 | No | User-assigned device name |
| `fechaRegistro` | `DateTime` | 3 | No | Device registration timestamp |
| `tipoDispositivo` | `String?` | 4 | Yes | Device type/category (optional) |

**Sources:** [lib/models/models.dart L71-L89](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L71-L89)

### Constructor

The `Dispositivo` constructor at [lib/models/models.dart L83-L89](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L83-L89)

 requires all fields except the optional `tipoDispositivo`:

```

```

### JSON Serialization

Similar to `Aprendiz`, the model provides JSON conversion methods:

* **toJson()** at [lib/models/models.dart L91-L97](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L91-L97)  - Converts to JSON with ISO 8601 date serialization
* **fromJson()** at [lib/models/models.dart L99-L105](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L99-L105)  - Factory constructor for JSON deserialization

**Sources:** [lib/models/models.dart L91-L105](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L91-L105)

### Usage in DatabaseService

The `Dispositivo` model is managed through the parent `Aprendiz` object but has its own PostgreSQL table:

```

```

**Sources:** [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

 [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)

Key characteristics of device management:

1. **Immutable Updates** - The `addDevice` method at [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)  creates a new `Aprendiz` instance with the updated device list rather than mutating the existing object.
2. **Cascade Deletion and Insertion** - The `_syncToPostgres` method at [lib/services/database_service.dart L95](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L95)  deletes all existing devices for the apprentice and re-inserts the complete list, ensuring synchronization.
3. **Loading from PostgreSQL** - The private method `_loadDevicesFromPostgres` at [lib/services/database_service.dart L171-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L171-L197)  queries the `dispositivos` table and constructs a list of `Dispositivo` objects.

**Sources:** [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)

 [lib/services/database_service.dart L171-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L171-L197)

 [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

---

## Hive Type Adapters

Both models use Hive's code generation to create type adapters for binary serialization. The generated adapters are located in [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)

### Code Generation Configuration

The models file includes a part directive at [lib/models/models.dart L3](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L3-L3)

:

```

```

This instructs the Dart build system to include the generated code. Generation is triggered by the `hive_generator` and `build_runner` packages listed in `pubspec.yaml`.

### Adapter Structure

```

```

**Sources:** [lib/models/models.dart L1-L6](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L6)

 [lib/models/models.g.dart L1-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L114)

### AprendizAdapter

Defined at [lib/models/models.g.dart L9-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L9-L68)

 the `AprendizAdapter` implements:

* **typeId: 0** - Identifies this adapter to Hive
* **read()** method at [lib/models/models.g.dart L14-L31](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L14-L31)  - Deserializes binary data into an `Aprendiz` instance by reading 10 fields
* **write()** method at [lib/models/models.g.dart L34-L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L34-L57)  - Serializes an `Aprendiz` instance to binary format, writing field count (10) followed by each field's index and value

**Sources:** [lib/models/models.g.dart L9-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L9-L68)

### DispositivoAdapter

Defined at [lib/models/models.g.dart L70-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L70-L114)

 the `DispositivoAdapter` implements:

* **typeId: 1** - Identifies this adapter to Hive
* **read()** method at [lib/models/models.g.dart L75-L87](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L75-L87)  - Deserializes binary data into a `Dispositivo` instance by reading 5 fields
* **write()** method at [lib/models/models.g.dart L90-L103](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L90-L103)  - Serializes a `Dispositivo` instance to binary format, writing field count (5) followed by each field's index and value

**Sources:** [lib/models/models.g.dart L70-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L70-L114)

### Adapter Registration

These adapters must be registered with Hive before opening any boxes. The registration occurs in `main.dart` during application initialization:

```

```

This registration enables Hive to correctly serialize and deserialize instances of both model classes when storing to and reading from the local Hive database.

**Sources:** [lib/models/models.g.dart L9-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L9-L114)

---

## JSON Serialization Methods

Both models implement standard JSON serialization for data interchange, particularly when communicating with PostgreSQL (which stores some data as JSON) or for potential API integration.

### Serialization Flow

```

```

**Sources:** [lib/models/models.dart L41-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L41-L67)

 [lib/models/models.dart L91-L105](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L91-L105)

### Key Characteristics

1. **DateTime Handling** - Both `fechaRegistro` fields are serialized to ISO 8601 strings via `toIso8601String()` at [lib/models/models.dart L50](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L50-L50)  and [lib/models/models.dart L95](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L95-L95)  and parsed back using `DateTime.parse()`.
2. **Nested Object Serialization** - The `Aprendiz.toJson()` method recursively serializes the `dispositivos` list by mapping each element through `Dispositivo.toJson()` at [lib/models/models.dart L51](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L51-L51)
3. **Nullable Field Handling** - Optional fields (`tipoSangre`, `fotoPerfilPath`, `email`, `tipoDispositivo`) are included in JSON output even when null, allowing for explicit null representation.

**Sources:** [lib/models/models.dart L41-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L41-L67)

 [lib/models/models.dart L91-L105](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L91-L105)

---

## Model Usage Patterns

### Immutability Pattern

Both models use Dart's implicit immutability through `final` fields. All fields are marked `final` at [lib/models/models.dart L7-L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L7-L26)

 and [lib/models/models.dart L72-L81](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L72-L81)

 preventing modification after construction.

When updates are needed, the pattern is to create a new instance with modified values, as demonstrated in `DatabaseService.addDevice()` at [lib/services/database_service.dart L223-L234](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L223-L234)

:

```

```

**Sources:** [lib/models/models.dart L7-L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L7-L26)

 [lib/models/models.dart L72-L81](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L72-L81)

 [lib/services/database_service.dart L223-L234](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L223-L234)

### Composite Key Relationship

The relationship between `Aprendiz` and `Dispositivo` uses a composite primary key strategy:

| Database | Key Strategy |
| --- | --- |
| **Hive** | `Aprendiz` uses `idIdentificacion` as box key. `Dispositivo` objects are embedded in the `dispositivos` list. |
| **PostgreSQL** | `aprendices` table uses `id_identificacion` as primary key. `dispositivos` table uses composite key `(id_identificacion, id_dispositivo)` with foreign key to `aprendices`. |

This is enforced in the PostgreSQL schema through the `ON CONFLICT` clause at [lib/services/database_service.dart L101](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L101-L101)

:

```

```

**Sources:** [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)

### Data Synchronization Behavior

The models participate in a write-through cache pattern:

1. **Write Path**: `saveAprendiz()` writes to Hive immediately, then asynchronously syncs to PostgreSQL
2. **Read Path**: Authenticated reads fetch from PostgreSQL and cache to Hive; subsequent reads use local cache
3. **Device List Handling**: The complete device list is deleted and re-inserted on each sync to maintain consistency

**Sources:** [lib/services/database_service.dart L42-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L119)

---

## Summary

The `Aprendiz` and `Dispositivo` models form the data backbone of the SENA Digital ID Card application:

* **Aprendiz** represents complete user profiles with 10 fields including authentication data, personal information, and a collection of registered devices
* **Dispositivo** represents individual devices with 5 fields establishing a foreign key relationship to Aprendiz
* Both models support **dual persistence** through Hive type adapters (binary serialization for local storage) and JSON serialization (for PostgreSQL and potential API use)
* The relationship follows **immutability patterns** requiring new instance creation for updates
* Code generation via `hive_generator` produces type adapters automatically from annotations
* Models integrate tightly with `DatabaseService` for coordinated dual-database operations

**Sources:** [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

 [lib/models/models.g.dart L1-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L114)

 [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)