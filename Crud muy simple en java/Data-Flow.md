# Data Flow

> **Relevant source files**
> * [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)
> * [build/classes/services/alumnoDataChange.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class)
> * [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)
> * [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

## Purpose and Scope

This document traces the complete data flow through the crud3 application, from user input in the GUI through validation, model creation, service layer processing, database persistence, and back to user feedback. It details how data is collected, validated, transformed, and stored at each architectural layer.

For information about the architectural layers themselves, see [Application Layers](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3.1-application-layers). For details about specific UI components, see [Main Data Entry Form](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/4.1-main-data-entry-form-(gui.gui)). For database operations details, see [CRUD Operations](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/6.3-crud-operations).

---

## Data Flow Overview

The crud3 application follows a unidirectional data flow pattern where user input travels through multiple transformation stages before reaching the database. The flow involves five distinct layers with well-defined responsibilities.

```

```

**Sources:** [src/GUI/GUI.java L87-L169](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L87-L169)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

---

## Input Collection Phase

### Text Field Binding

The `GUI.GUI` class maintains four `JTextField` components that capture student data:

| Field Component | Field Name | Data Captured | Model Property |
| --- | --- | --- | --- |
| `txtNombre` | nombre | Student first name | `alumno.nombre` |
| `txtApellido` | apellido | Student last name | `alumno.apellido` |
| `txtTelefono` | telefono | Phone number | `alumno.telefono` |
| `txtCorreo` | Correo | Email address | `alumno.correo` |

Data remains in the text fields until the user triggers the save action by clicking `btnSave1`.

```

```

**Sources:** [src/GUI/GUI.java L37-L43](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L37-L43)

 [src/GUI/GUI.java L74-L80](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L74-L80)

 [src/GUI/GUI.java L87-L89](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L87-L89)

---

## Validation Phase

### Field-by-Field Validation Logic

The `ValidadarDatos()` method performs sequential validation on each field. Data extraction uses `getText().trim()` to retrieve and normalize input.

```

```

The validation logic is implemented at [src/GUI/GUI.java L144-L156](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L144-L156)

 Each validation failure results in:

1. A `JOptionPane` message dialog displayed to the user
2. Focus set to the invalid field using `requestFocus()`
3. Early termination of the validation process

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

---

## Model Creation Phase

### Data Transfer to Domain Object

Upon successful validation, the application creates an `alumno` instance and populates it with form data. This represents the transition from UI-layer strings to a domain model object.

```

```

The `alumno` class is a simple POJO with five fields:

| Field | Type | Purpose |
| --- | --- | --- |
| `id` | `int` | Database primary key (not set during creation) |
| `nombre` | `String` | Student first name |
| `apellido` | `String` | Student last name |
| `telefono` | `String` | Phone number |
| `correo` | `String` | Email address |

The model creation occurs at [src/GUI/GUI.java L157-L161](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L161)

 using the setter methods defined in [src/model/alumno.java L34-L60](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L34-L60)

**Sources:** [src/GUI/GUI.java L157-L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L162)

 [src/model/alumno.java L3-L62](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L3-L62)

---

## Service Layer Processing

### Persistence Orchestration

The `alumnoDataChange.guardado()` static method orchestrates database persistence. It receives the `alumno` object and executes the following sequence:

```

```

### SQL Statement Construction

The service uses a parameterized SQL INSERT statement:

```

```

This statement is defined at the beginning of the `guardado()` method. The parameter binding occurs through sequential `setString()` calls that map `alumno` object properties to SQL parameters:

1. Parameter 1 ← `alum.getNombre()`
2. Parameter 2 ← `alum.getApellido()`
3. Parameter 3 ← `alum.getTelefono()`
4. Parameter 4 ← `alum.getCorreo()`

The service does not set the `id` field as it is auto-incremented by the database.

**Sources:** [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [build/classes/services/alumnoDataChange.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class)

---

## Database Persistence Phase

### Connection Acquisition and Statement Execution

The data persistence phase involves coordination between the service layer and repository layer:

```

```

### Statement Execution Flow

The `PreparedStatement.executeUpdate()` method returns the number of affected rows:

* **Result > 0**: Record successfully inserted * `alumnoDataChange.mensaje` set to `"alumno guardado correctamente"` * Method returns `true`
* **Result = 0**: Insert failed (constraint violation, etc.) * `alumnoDataChange.mensaje` set to `"no se ha podido guardar el alumno"` * Method returns `false`
* **Exception thrown**: Database connection or SQL error * Exception message captured in `alumnoDataChange.mensaje` * Method returns `false`

**Sources:** [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

 (referenced)

---

## User Feedback Phase

### Result Messaging and UI State Management

After the service layer completes, control returns to `ValidadarDatos()` which handles user feedback:

```

```

### Field Clearing Logic

On successful save, the `LimpiarCampos()` method resets all input fields:

| Operation | Implementation |
| --- | --- |
| Clear nombre | `txtNombre.setText("")` |
| Clear apellido | `txtApellido.setText("")` |
| Clear telefono | `txtTelefono.setText("")` |
| Clear correo | `txtCorreo.setText("")` |

The field clearing logic is implemented at [src/GUI/GUI.java L173-L178](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L173-L178)

 This prepares the form for the next data entry operation.

Regardless of success or failure, a message dialog displays `alumnoDataChange.mensaje` to inform the user of the operation result at [src/GUI/GUI.java L168](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L168-L168)

**Sources:** [src/GUI/GUI.java L162-L169](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L162-L169)

 [src/GUI/GUI.java L173-L178](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L173-L178)

---

## Error Flow Handling

### Exception Propagation

The application handles errors at multiple levels:

```

```

### Error Categories

1. **Validation Errors** (Client-side) * Empty field detection at [src/GUI/GUI.java L144-L156](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L144-L156) * Immediate user feedback with field focus * No database interaction occurs
2. **Database Errors** (Service-side) * Connection failures * SQL execution errors * Constraint violations * All caught by exception handler in `alumnoDataChange.guardado()` * Error message stored in static `mensaje` field

The static `mensaje` field in `alumnoDataChange` acts as a simple result communication mechanism between the service layer and GUI layer, eliminating the need for exception propagation across layers.

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

---

## Data Transformation Summary

### Layer-by-Layer Data Format

The following table shows how student data is represented at each layer:

| Layer | Data Format | Example Representation |
| --- | --- | --- |
| **UI Layer** | `JTextField` text content | `txtNombre.getText()` → `"Juan"` |
| **Validation Layer** | Trimmed strings | `txtNombre.getText().trim()` → `"Juan"` |
| **Model Layer** | `alumno` object fields | `alum.nombre` → `"Juan"` |
| **Service Layer** | Method parameters | `guardado(alum)` |
| **JDBC Layer** | PreparedStatement parameters | `setString(1, "Juan")` |
| **Database Layer** | Column values | `nombre` column → `"Juan"` |

### Complete Flow Trace

For a single field (`nombre`), the complete transformation path is:

1. User types in `txtNombre` → `"Juan"`
2. `txtNombre.getText()` retrieves → `"Juan"`
3. `txtNombre.getText().trim()` normalizes → `"Juan"`
4. `new alumno()` creates empty object
5. `alum.setNombre("Juan")` sets → `alum.nombre = "Juan"`
6. `alum.getNombre()` retrieves → `"Juan"`
7. `setString(1, "Juan")` binds parameter
8. SQL executes: `INSERT INTO alumnoss (nombre, ...) VALUES ('Juan', ...)`
9. Database stores: `nombre` column = `"Juan"`

**Sources:** [src/GUI/GUI.java L144-L169](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L144-L169)

 [src/model/alumno.java L34-L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L34-L36)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)