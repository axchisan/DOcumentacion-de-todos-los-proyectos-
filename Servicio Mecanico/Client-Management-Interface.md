# Client Management Interface

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/clientes/Cliente.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/Cliente.java)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java)

## Purpose and Scope

The `MarcoGestionClientes` class provides a comprehensive interface for performing CRUD operations (search, update, delete) on existing client records in the database. This internal frame allows users to search for clients, view their details in a table, edit their information, and remove client records.

For initial client registration, see [Client Registration](/BrayanTirado/Servicio-Mec-nico/7.1-client-registration). This document covers post-registration management of client data only.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L1-L360](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L1-L360)

---

## UI Layout Architecture

The `MarcoGestionClientes` frame uses a `BorderLayout` with three primary panels arranged vertically:

| Panel | Border Position | Purpose |
| --- | --- | --- |
| `panelBusqueda` | `PAGE_START` | Search controls with criteria selection |
| `panelClientes` (JScrollPane) | `CENTER` | Table displaying client records |
| `panelEdicion` | `PAGE_END` | Edit form with action buttons |

### UI Component Structure

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form L30-L400](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form#L30-L400)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L40-L216](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L40-L216)

---

## Search Functionality

### Search Criteria

The interface supports two search criteria selectable via the `cbCriterioBusqueda` combo box:

* **Documento**: Search by client identification document
* **Nombre**: Search by client name

Both searches use case-insensitive partial matching via PostgreSQL's `ILIKE` operator with `%` wildcards.

### Search Implementation Flow

```

```

The search method clears the existing table content by setting row count to 0, then populates it with search results. The `buscarCliente` method in `ClienteDAO` constructs a dynamic SQL query based on the selected criterion.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L278-L296](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L278-L296)

 [src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java L62-L85](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java#L62-L85)

---

## Table Display Configuration

### Table Structure

The `tablaClientes` displays client records with the following columns:

| Column Index | Column Name | Data Type | Editable | Source Field |
| --- | --- | --- | --- | --- |
| 0 | ID | Integer | No | `cliente.getId()` |
| 1 | Nombre | String | No | `cliente.getNombre()` |
| 2 | Documento | String | No | `cliente.getDocumento()` |
| 3 | Teléfono | String | No | `cliente.getTelefono()` |
| 4 | Correo | String | No | `cliente.getCorreo()` |

All columns are configured as non-editable via the `isCellEditable` override in the table model definition.

### Initial Data Loading

The `cargarClientes()` method is called during frame initialization [MarcoGestionClientes.java L28](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L28-L28)

 and executes the following:

1. Clears existing table rows: `modeloTabla.setRowCount(0)`
2. Retrieves all clients via `clienteDAO.listarClientes()`
3. Iterates through results and adds rows to `modeloTabla`

Note: The `cargarClientes()` method at [MarcoGestionClientes.java L266-L275](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L266-L275)

 adds 6 values per row (including `rol`) but the table only displays 5 columns, meaning the `rol` column is present in the model but not visible in the UI.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L192-L211](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L192-L211)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L259-L276](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L259-L276)

---

## Client Selection and Form Population

### Selection Listener Mechanism

A `ListSelectionListener` is attached to the table's selection model during frame construction:

```
tablaClientes.getSelectionModel().addListSelectionListener(e -> seleccionarCliente());
```

This listener invokes `seleccionarCliente()` whenever a user selects a row in the table.

### Form Population Process

```

```

The method reads values directly from the `modeloTabla` at the selected row index and populates the corresponding text fields. The document field (`txtDocumento`) serves as the primary identifier for subsequent update and delete operations.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L29-L29)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L298-L306](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L298-L306)

---

## Update Operation

### Update Workflow

```

```

### Key Implementation Details

1. **Document as Primary Key**: The update operation uses `documento` as the WHERE clause parameter, not the database `id` [ClienteDAO.java L88-L95](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/ClienteDAO.java#L88-L95)
2. **Fixed Role Assignment**: The update operation hardcodes `rol = 1` when constructing the `Cliente` object [MarcoGestionClientes.java L323](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L323-L323)
3. **Non-Updatable Field**: The `documento` field itself cannot be updated through this interface; it serves only as the identifier
4. **Validation Rules**: * `documento` must not be empty (indicates a client is selected) * `nombre` must not be empty

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L308-L327](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L308-L327)

 [src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java L87-L105](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java#L87-L105)

---

## Delete Operation

### Delete Operation with Foreign Key Handling

The delete functionality includes special handling for foreign key constraint violations, which occur when attempting to delete a client who has associated vehicles or services.

```

```

### Foreign Key Constraint Detection

The error handling specifically checks for PostgreSQL error code `23503`, which indicates a foreign key violation:

```

```

This prevents deletion of clients who have:

* Associated vehicle records in the `vehiculos` table
* Associated service records in the `servicios` table

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L329-L350](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L329-L350)

 [src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java L106-L122](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java#L106-L122)

---

## Form Management

### Form Clear Operation

The `limpiarFormulario()` method resets the interface to its initial state:

| Action | Implementation |
| --- | --- |
| Clear name field | `txtNombre.setText("")` |
| Clear document field | `txtDocumento.setText("")` |
| Clear phone field | `txtTelefono.setText("")` |
| Clear email field | `txtCorreo.setText("")` |
| Clear search field | `txtBuscar.setText("")` |
| Reload table | `cargarClientes()` |

This method is invoked after successful update operations, successful delete operations, and when the user clicks the `btnLimpiar` button.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L352-L359](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L352-L359)

---

## Data Access Integration

### ClienteDAO Method Usage

The `MarcoGestionClientes` frame interacts with `ClienteDAO` through the following methods:

```

```

### Database Operations Summary

| DAO Method | SQL Operation | Parameters | Return Type |
| --- | --- | --- | --- |
| `listarClientes()` | `SELECT * FROM clientes` | None | `List<Cliente>` |
| `buscarCliente()` | `SELECT * FROM clientes WHERE {criterio} ILIKE ?` | criterio, valor | `List<Cliente>` |
| `actualizarCliente()` | `UPDATE clientes SET ... WHERE documento = ?` | Cliente object | void (shows JOptionPane) |
| `eliminarCliente()` | `DELETE FROM clientes WHERE documento = ?` | documento | void (shows JOptionPane or throws) |

All DAO methods use `ConexionBD.conectar()` to obtain database connections and implement try-with-resources for automatic connection cleanup.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java L39-L122](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/ClienteDAO.java#L39-L122)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L18-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L18-L30)

---

## Component Initialization

### Frame Setup Sequence

The constructor performs the following initialization sequence:

1. **DAO Instantiation**: Creates new `ClienteDAO` instance [MarcoGestionClientes.java L25](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L25-L25)
2. **UI Generation**: Calls `initComponents()` (NetBeans-generated method) [MarcoGestionClientes.java L26](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L26-L26)
3. **Table Configuration**: Calls `configurartabla()` to initialize the table model [MarcoGestionClientes.java L27](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L27-L27)
4. **Data Loading**: Calls `cargarClientes()` to populate initial data [MarcoGestionClientes.java L28](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L28-L28)
5. **Event Listener**: Attaches selection listener to table [MarcoGestionClientes.java L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L29-L29)
6. **Size Setting**: Sets preferred frame size to 800x600 [MarcoGestionClientes.java L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.java#L30-L30)

The frame is configured as closable, iconifiable, maximizable, and resizable through properties defined in the form file [MarcoGestionClientes.form L5-L8](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/MarcoGestionClientes.form#L5-L8)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L24-L31](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L24-L31)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form L4-L12](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form#L4-L12)