# Device Management

> **Relevant source files**
> * [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)
> * [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
> * [lib/screens/device_management_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)
> * [macos/Flutter/GeneratedPluginRegistrant.swift](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/macos/Flutter/GeneratedPluginRegistrant.swift)

## Purpose and Scope

This document describes the device management system that allows authenticated apprentices (Aprendiz) to register, view, and manage personal electronic devices for access control at SENA training facilities. The system enforces business rules including a maximum of 10 devices per user and unique device identification.

For information about user authentication and session management, see [Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system). For details on the underlying data persistence layer, see [DatabaseService Reference](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference). For information about the data models, see [Data Models (Aprendiz and Dispositivo)](/axchisan/AppGestionCarnetsSENA/3.1.2-data-models-(aprendiz-and-dispositivo)).

---

## System Overview

The device management feature is implemented through the `DeviceManagementScreen` class, which provides a user interface for registering devices associated with an apprentice's account. Each device is represented by a `Dispositivo` model instance that stores identification details, device type, and registration timestamp. All device operations are persisted through the `DatabaseService`, which synchronizes data between local Hive storage and the remote PostgreSQL database.

**Sources:** [lib/screens/device_management_screen.dart L1-L474](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L1-L474)

 [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

---

## Device Management Architecture

```

```

**Diagram: Component Architecture for Device Management**

This diagram illustrates the layered architecture of the device management system. The UI layer (`DeviceManagementScreen`) manages user input through text controllers and maintains a local representation of devices in the `_devices` list. All persistence operations are delegated to the `DatabaseService` singleton instance, which abstracts the dual-storage pattern (Hive + PostgreSQL). Device data is embedded within the `Aprendiz` model as a list of `Dispositivo` objects, requiring the entire `Aprendiz` object to be updated when devices are added or removed.

**Sources:** [lib/screens/device_management_screen.dart L8-L43](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L8-L43)

 [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

 [lib/models/models.dart L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L26-L26)

---

## Data Model: Dispositivo

The `Dispositivo` class represents an individual device registered to an apprentice's account. It is a Hive-serializable model with type adapter support.

### Field Definitions

| Field | Type | Hive Field ID | Description |
| --- | --- | --- | --- |
| `idDispositivo` | `String` | 0 | Unique device identifier (serial number) |
| `idIdentificacion` | `String` | 1 | Foreign key to Aprendiz owner |
| `nombreDispositivo` | `String` | 2 | Device name or brand |
| `fechaRegistro` | `DateTime` | 3 | Registration timestamp |
| `tipoDispositivo` | `String?` | 4 | Device type category (nullable) |

**Sources:** [lib/models/models.dart L70-L89](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L70-L89)

### Device Type Categories

The system supports the following predefined device types, defined in `DeviceManagementScreen`:

* **Portátil** - Laptop computers
* **Tablet** - Tablet devices
* **Teléfono** - Mobile phones
* **Cargador** - Charging devices
* **Mouse** - Computer mice
* **Teclado** - Keyboards
* **Audífonos** - Headphones
* **Otro** - Other device types

Each type is associated with a Material icon via the `_getIconForType()` method.

**Sources:** [lib/screens/device_management_screen.dart L24-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L24-L33)

 [lib/screens/device_management_screen.dart L189-L208](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L189-L208)

### Hive Type Adapter

The `DispositivoAdapter` class (type ID 1) provides binary serialization for local storage. It is auto-generated by `hive_generator` from the `@HiveType` and `@HiveField` annotations.

**Sources:** [lib/models/models.g.dart L70-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L70-L114)

---

## Device Operations Data Flow

```

```

**Diagram: Device Operation Sequences**

This sequence diagram demonstrates the three primary workflows: loading devices on screen initialization, adding a new device, and removing an existing device. All operations follow the pattern of retrieving the `Aprendiz` object from local storage, modifying its `dispositivos` list, and persisting the entire updated object. The persistence layer automatically handles synchronization to PostgreSQL through the `_syncToPostgres()` method.

**Sources:** [lib/screens/device_management_screen.dart L44-L65](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L44-L65)

 [lib/screens/device_management_screen.dart L75-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L75-L127)

 [lib/screens/device_management_screen.dart L129-L187](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L129-L187)

 [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

---

## Business Rules and Validation

### Maximum Device Limit

The system enforces a hard limit of 10 devices per apprentice, defined by the constant `_maxDevices`.

```

```

Validation occurs in `_addDevice()` before creating the device:

**Sources:** [lib/screens/device_management_screen.dart L36](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L36-L36)

 [lib/screens/device_management_screen.dart L79-L87](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L79-L87)

### Unique Device ID Enforcement

Each device must have a unique `idDispositivo` within the user's device list. Duplicate detection is performed client-side by checking the existing `_devices` list:

**Sources:** [lib/screens/device_management_screen.dart L89-L97](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L89-L97)

### Required Field Validation

All three input fields must be non-empty before a device can be added:

* Device ID (`_deviceIdController`)
* Device Name (`_deviceNameController`)
* Device Type (`_selectedType`)

**Sources:** [lib/screens/device_management_screen.dart L76-L78](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L76-L78)

 [lib/screens/device_management_screen.dart L119-L126](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L119-L126)

---

## DatabaseService Integration

### addDevice() Method

The `addDevice()` method in `DatabaseService` orchestrates the device addition workflow:

```
Future<void> addDevice(
  String idIdentificacion,
  String deviceName,
  String deviceId,
  String deviceType
)
```

**Implementation steps:**

1. Retrieve the current `Aprendiz` object from local Hive storage
2. Create a new `Dispositivo` instance with the provided parameters
3. Append the new device to the existing `dispositivos` list
4. Create an updated `Aprendiz` object with the modified device list
5. Call `saveAprendiz()` to persist changes to both Hive and PostgreSQL

**Sources:** [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

### PostgreSQL Synchronization

The `_syncToPostgres()` method handles device persistence to the remote database. It uses a **replace-all strategy** for device synchronization:

1. **Delete existing devices**: `DELETE FROM dispositivos WHERE id_identificacion = @id`
2. **Re-insert all devices**: Iterate through `aprendiz.dispositivos` and insert each device

This approach ensures the PostgreSQL database matches the local Hive state exactly, avoiding complex differential sync logic.

**Sources:** [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)

### Database Schema

The PostgreSQL `dispositivos` table structure:

| Column | Type | Constraints |
| --- | --- | --- |
| `id_dispositivo` | Variable | Primary Key (composite) |
| `id_identificacion` | Variable | Primary Key (composite), Foreign Key to aprendices |
| `nombre_dispositivo` | Variable | Not Null |
| `tipo_dispositivo` | Variable | Nullable |
| `fecha_registro` | Timestamp | Not Null |

The composite primary key `(id_identificacion, id_dispositivo)` enforces uniqueness at the database level.

**Sources:** [lib/services/database_service.dart L98-L105](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L98-L105)

---

## User Interface Components

### Screen Layout Structure

```

```

**Diagram: UI Component Hierarchy**

The screen follows a vertical layout with two main sections: device list display and device addition form. The list section has a fixed height of 200 pixels and displays either registered devices or an empty state message. The form section contains three input fields and a submit button, all styled with `AppColors.senaGreen` borders.

**Sources:** [lib/screens/device_management_screen.dart L211-L473](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L211-L473)

### Device List Item

Each device in the list is rendered as a container with the following information:

* Device type icon (from `_getIconForType()`)
* Device name
* Device ID (prefixed with "ID: ")
* Device type (prefixed with "Tipo: ")
* Delete button (red trash icon)

The container uses `AppColors.lightGray` background and 8-pixel border radius.

**Sources:** [lib/screens/device_management_screen.dart L279-L333](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L279-L333)

### Device Type Dropdown

The device type selector is implemented as a `DropdownButtonFormField<String>` with predefined options from the `_deviceTypes` list. Each dropdown item displays:

* An icon representing the device type
* The device type name

The currently selected type is stored in the `_selectedType` state variable, initialized to 'Portátil'.

**Sources:** [lib/screens/device_management_screen.dart L390-L432](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L390-L432)

---

## State Management

### State Variables

The `_DeviceManagementScreenState` class maintains the following state:

| Variable | Type | Purpose |
| --- | --- | --- |
| `_deviceIdController` | `TextEditingController` | Manages device ID input |
| `_deviceNameController` | `TextEditingController` | Manages device name input |
| `_deviceTypeController` | `TextEditingController` | Manages device type input (unused) |
| `_devices` | `List<Map<String, dynamic>>` | In-memory cache of device data for display |
| `_selectedType` | `String` | Currently selected device type in dropdown |
| `_dbService` | `DatabaseService` | Singleton instance for data operations |

**Sources:** [lib/screens/device_management_screen.dart L18-L35](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L18-L35)

### Lifecycle Methods

**`initState()`**: Calls `_loadDevices()` to populate the device list when the screen is first created.

**`dispose()`**: Disposes of text controllers to prevent memory leaks.

**Sources:** [lib/screens/device_management_screen.dart L38-L73](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L38-L73)

### Data Refresh Pattern

The `_loadDevices()` method implements the data refresh pattern used throughout the screen:

1. Retrieve the current `Aprendiz` object from local storage via `DatabaseService.getAprendizFromLocal()`
2. Map `aprendiz.dispositivos` list to the `_devices` display format
3. Call `setState()` to trigger UI rebuild

This method is invoked after successful add and remove operations to ensure the UI reflects the persisted state.

**Sources:** [lib/screens/device_management_screen.dart L44-L65](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L44-L65)

---

## User Feedback Mechanisms

### SnackBar Messages

The screen provides user feedback through `SnackBar` widgets with contextual colors:

| Event | Message | Background Color |
| --- | --- | --- |
| Device limit reached | "Has alcanzado el límite de 10 dispositivos" | `AppColors.red` |
| Duplicate device ID | "El ID del dispositivo ya está registrado" | `AppColors.red` |
| Missing required fields | "Por favor completa todos los campos" | `AppColors.red` |
| Device added successfully | "Dispositivo agregado exitosamente" | `AppColors.senaGreen` |
| Device removed | "Dispositivo eliminado" | `AppColors.red` |

**Sources:** [lib/screens/device_management_screen.dart L80-L126](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L80-L126)

 [lib/screens/device_management_screen.dart L170-L175](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L170-L175)

### Delete Confirmation Dialog

Device removal requires user confirmation through an `AlertDialog` with:

* Title: "Eliminar Dispositivo"
* Content: Confirmation message with device name
* Actions: "Cancelar" button and "Eliminar" button (red text)

This prevents accidental deletion of devices.

**Sources:** [lib/screens/device_management_screen.dart L131-L185](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L131-L185)

---

## Navigation

The screen is accessed from the `HomeScreen` via named route navigation:

```

```

The `Aprendiz` object is passed as a route argument and accessed via the widget constructor:

```

```

The user can return to the previous screen using the back button in the AppBar.

**Sources:** [lib/screens/device_management_screen.dart L8-L11](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L8-L11)

 [lib/screens/device_management_screen.dart L221-L224](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L221-L224)

---

## Offline Capability

The device management system operates fully offline after initial user authentication. All device operations (add, remove, list) are performed against the local Hive database first, with synchronization to PostgreSQL occurring asynchronously through the `_syncToPostgres()` method. If the synchronization fails due to network unavailability, the operation still succeeds locally, and the data will be synced when connectivity is restored on the next `saveAprendiz()` call.

**Sources:** [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

 [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

---

## Error Handling

Error handling in the device management system follows a silent failure pattern:

* **DatabaseService methods**: Wrap operations in try-catch blocks that return null or empty lists on failure
* **UI operations**: Display user-friendly error messages via SnackBars for validation failures
* **Synchronization errors**: Silently caught in `_syncToPostgres()`, allowing local operations to succeed even if remote sync fails

No exceptions are propagated to the UI layer, ensuring the application remains stable during network issues or database errors.

**Sources:** [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

 [lib/screens/device_management_screen.dart L75-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart#L75-L127)