# Client Management Module

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java)

## Purpose and Scope

The Client Management Module provides comprehensive functionality for managing customer data in the El Taller de ADSO mechanical workshop application. This module consists of two primary JInternalFrame components: `MarcoClientes` for new client registration and `MarcoGestionClientes` for searching, viewing, updating, and deleting existing client records.

For information about the underlying data access layer and database schema, see [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer). For details on how clients are associated with vehicles and services, see [Vehicle Management Module](/BrayanTirado/Servicio-Mec-nico/6-vehicle-management-module) and [Service Management Module](/BrayanTirado/Servicio-Mec-nico/4-service-management-module).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L1-L210](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L1-L210)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L1-L361](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L1-L361)

---

## Module Architecture

The Client Management Module follows the standard three-tier architecture pattern used throughout the application, with presentation components delegating to a dedicated DAO class for data persistence.

```

```

**Key Components:**

| Component | Type | File Path | Responsibility |
| --- | --- | --- | --- |
| `MarcoClientes` | JInternalFrame | [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java) | New client registration form |
| `MarcoGestionClientes` | JInternalFrame | [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java) | Client search, update, and delete operations |
| `ClienteDAO` | DAO | Referenced in both classes | Data access operations for client entities |
| `Cliente` | Domain Model | Referenced in both classes | Client data structure |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L17-L26](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L17-L26)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L17-L31](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L17-L31)

---

## Client Registration Interface (MarcoClientes)

### UI Layout and Components

The `MarcoClientes` frame provides a simple registration form for adding new clients to the system. The interface uses a vertical layout with label-textfield pairs and centers all text input.

```

```

**UI Component Details:**

| Component | Property | Value |
| --- | --- | --- |
| `txtNombre` | Font | Comic Sans MS, 18pt |
| `txtDocumento` | Font | Comic Sans MS, 14pt |
| `txtTelefono` | Font | Comic Sans MS, 14pt |
| `txtCorreo` | Font | Comic Sans MS, 14pt |
| All text fields | Horizontal Alignment | CENTER |
| All text fields | Border | Line border, RGB(0, 102, 255) |
| `btnRegistrar` | Foreground | RGB(0, 102, 255) |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form L28-L237](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form#L28-L237)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L36-L162](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L36-L162)

### Registration Workflow

The client registration process is initiated by the `btnRegistrar` button, which triggers the `guardarCliente()` method.

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L164-L166](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L164-L166)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L184-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L184-L204)

### Form Validation and Data Model

The registration form implements minimal but essential validation:

**Validation Rules:**

| Field | Rule | Implementation |
| --- | --- | --- |
| `nombre` | Required, non-empty after trim | [MarcoClientes.java L185-L193](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoClientes.java#L185-L193) |
| `documento` | Required, non-empty after trim | [MarcoClientes.java L186-L193](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoClientes.java#L186-L193) |
| `telefono` | Optional | No validation |
| `correo` | Optional | No validation |
| `rol` | Automatically set to 1 | [MarcoClientes.java L189](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoClientes.java#L189-L189) |

**Cliente Object Construction:**

```

```

The `Cliente` constructor receives:

* `id = 0` (auto-generated by database)
* User-provided: `nombre`, `documento`, `telefono`, `correo`
* Hardcoded: `rol = 1` (standard client role)

**Post-Registration Cleanup:**

The `limpiarformulario()` method resets all input fields to empty strings, preparing the form for the next registration without requiring frame closure.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L184-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L184-L204)

---

## Client Management Interface (MarcoGestionClientes)

### UI Layout and Components

The `MarcoGestionClientes` frame provides comprehensive CRUD operations through a three-panel layout: search panel at top, client table in center, and edit panel at bottom.

```

```

**Component Initialization:**

The constructor [MarcoGestionClientes.java L24-L31](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L24-L31)

 performs the following initialization sequence:

1. Instantiate `ClienteDAO` object
2. Initialize Swing components via `initComponents()`
3. Configure table model via `configurartabla()`
4. Load all clients via `cargarClientes()`
5. Attach selection listener to `tablaClientes`
6. Set frame size to 800×600

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form L1-L401](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form#L1-L401)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L24-L31](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L24-L31)

### Search Functionality

The search system supports two criteria types: document ID and client name. The search is case-insensitive and uses partial matching at the DAO level.

```

```

**Search Implementation Details:**

The `buscarclientes()` method [MarcoGestionClientes.java L278-L296](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L278-L296)

 performs the following:

1. Retrieves selected criterion from combo box and converts to lowercase
2. Trims the search value from text field
3. Clears existing table rows via `modeloTabla.setRowCount(0)`
4. Calls `clienteDAO.buscarCliente(criterio, valor)`
5. Iterates through results, adding each to the table model
6. Displays informational dialog if no results found

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L278-L296](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L278-L296)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L263-L276](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L263-L276)

### Table Selection and Form Population

When a user clicks on a table row, the `seleccionarCliente()` method automatically populates the edit form fields through a `ListSelectionListener` attached in the constructor.

```

```

**Column Index Mapping:**

| Column Index | Field Name | Populated Control |
| --- | --- | --- |
| 0 | ID | Not populated (hidden from user) |
| 1 | Nombre | `txtNombre` |
| 2 | Documento | `txtDocumento` |
| 3 | Teléfono | `txtTelefono` |
| 4 | Correo | `txtCorreo` |

The selection listener is registered at [MarcoGestionClientes.java L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L29-L29)

 using a lambda expression: `tablaClientes.getSelectionModel().addListSelectionListener(e -> seleccionarCliente());`

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L29-L29)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L298-L306](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L298-L306)

### Update Operation

The update operation modifies an existing client's information based on the document number (used as the primary identifier for updates).

```

```

**Update Validation Rules:**

| Field | Validation | Error Message | Line Reference |
| --- | --- | --- | --- |
| `documento` | Must not be empty after trim | "Seleccione un cliente para actualizar" | [308-313](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/308-313) |
| `nombre` | Must not be empty after trim | "El nombre es obligatorio" | [318-321](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/318-321) |
| `telefono` | No validation | N/A | N/A |
| `correo` | No validation | N/A | N/A |

**Cliente Object for Update:**

The `Cliente` object is constructed at [MarcoGestionClientes.java L323](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L323-L323)

 with:

* `id = 0` (ignored in UPDATE operation; documento used as key)
* User-provided: `nombre`, `documento`, `telefono`, `correo`
* `rol = 1` (hardcoded, though comment suggests it should be dynamic)

**Post-Update Actions:**

1. `limpiarFormulario()` - clears all edit form fields
2. `cargarClientes()` - refreshes table to show updated data

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L308-L327](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L308-L327)

### Delete Operation with Foreign Key Constraint Handling

The delete operation includes sophisticated error handling for foreign key constraint violations, which occur when attempting to delete a client who has associated vehicles or services.

```

```

**Delete Operation Error Handling:**

The `eliminarCliente()` method [MarcoGestionClientes.java L329-L350](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L329-L350)

 implements comprehensive error handling:

| Error Type | Detection | Error Code | Message Displayed |
| --- | --- | --- | --- |
| No client selected | Empty documento field | N/A | "Seleccione un cliente para eliminar" |
| User cancellation | Confirmation dialog NO | N/A | Silent cancellation |
| Foreign key violation | Exception message contains "23503" | PostgreSQL 23503 | "No se puede eliminar el cliente porque tiene vehículos o servicios asociados" |
| Other SQL errors | Generic Exception catch | Various | "Error al eliminar cliente: " + exception message |

**PostgreSQL Foreign Key Constraint:**

The error code `23503` is PostgreSQL's standard code for foreign key violations. This occurs when:

* Client has records in the `vehiculos` table (foreign key: `cliente_id`)
* Client has records in the `servicios` table (foreign key: `cliente_id`)

The application does not implement cascading deletes, requiring users to first remove associated vehicles and services before deleting the client.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L329-L350](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L329-L350)

### Form Cleanup and Table Refresh

The `limpiarFormulario()` method serves as a central cleanup function called after successful updates, deletes, or when the user manually clicks the "Limpiar" button.

```

```

**Invocation Contexts:**

| Context | File/Line | Purpose |
| --- | --- | --- |
| After successful update | [MarcoGestionClientes.java L325](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L325-L325) | Clear form and show updated data |
| After successful delete | [MarcoGestionClientes.java L340](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L340-L340) | Clear form and remove deleted entry |
| Manual cleanup by user | [MarcoGestionClientes.java L231](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L231-L231) | Reset form to initial state |

The method also reloads the complete client list via `cargarClientes()`, ensuring the table always displays current database state after any modification.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L352-L359](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L352-L359)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L263-L276](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L263-L276)

---

## Data Flow Summary

The following diagram illustrates the complete data flow through the Client Management Module, showing how data moves from user input through the UI layer, DAO layer, and finally to the PostgreSQL database.

```

```

**Key Data Transformations:**

1. **Registration**: Form fields → `Cliente` object (rol=1) → `INSERT INTO clientes`
2. **List/Search**: `SELECT` → `List<Cliente>` → `DefaultTableModel` rows
3. **Selection**: Table row click → `getValueAt(row, col)` → Text field population
4. **Update**: Modified fields → `Cliente` object → `UPDATE clientes WHERE documento=?`
5. **Delete**: Selected documento → `DELETE FROM clientes WHERE documento=?` → Foreign key check

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L184-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L184-L204)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L263-L359](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L263-L359)