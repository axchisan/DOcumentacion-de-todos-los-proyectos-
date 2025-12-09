# Client Registration

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java)

## Purpose and Scope

This document describes the client registration interface provided by the `MarcoClientes` class. This module enables users to register new clients in the system by collecting basic contact and identification information. Once registered, clients can be associated with vehicles and services.

For managing existing clients (search, update, delete operations), see [Client Management Interface](/BrayanTirado/Servicio-Mec-nico/7.2-client-management-interface).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L1-L211](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L1-L211)

---

## Form Architecture

`MarcoClientes` is a `JInternalFrame` that appears within the main application's `JDesktopPane` when the user selects the "Registrar" menu item from the Clientes menu. The form is constructed using the NetBeans GUI Builder and follows the standard pattern of instantiating a DAO in the constructor and delegating business logic to that layer.

| Component | Type | Purpose |
| --- | --- | --- |
| `MarcoClientes` | `javax.swing.JInternalFrame` | Container frame for the registration form |
| `clienteDAO` | `ClienteDAO` | Data access object for client persistence |
| `jPanel1` | `JPanel` | Main content panel with light purple background (RGB: 204, 204, 255) |

The frame is configured with standard MDI capabilities: closable, iconifiable, and maximizable (lines 50-52).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L16-L26](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L16-L26)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form L1-L10](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form#L1-L10)

---

## User Interface Components

The registration form presents a simple vertical layout with four input fields and a submit button:

```

```

### Field Specifications

| Field Variable | Label Text | Data Type | Required | Format/Notes |
| --- | --- | --- | --- | --- |
| `txtNombre` | Nombres | String | Yes | Full name of the client |
| `txtDocumento` | Documento | String | Yes | Government ID or identification document number |
| `txtTelefono` | Teléfono | String | No | Contact phone number |
| `txtCorreo` | Correo | String | No | Email address |

All text fields use center alignment and have blue borders (RGB: 0, 102, 255) for visual consistency.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L39-L87](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L39-L87)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form L113-L212](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form#L113-L212)

---

## Registration Workflow

The registration process follows a linear sequence from user input to database persistence:

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L164-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L164-L204)

---

## Validation Rules

The `guardarCliente()` method implements minimal validation logic:

### Required Field Validation

Only two fields are mandatory for client registration:

* **Nombre** (Name)
* **Documento** (Document/ID)

The validation is performed at [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L190-L193](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L190-L193)

:

```

```

### Optional Fields

The following fields are optional and can be left blank:

* **Teléfono** (Phone) - may be empty string
* **Correo** (Email) - may be empty string

### Input Trimming

All field inputs are trimmed of leading and trailing whitespace before validation or persistence (lines 185-188), preventing accidental whitespace-only entries.

### No Format Validation

The form does **not** enforce format validation for:

* Email address structure
* Phone number format
* Document number patterns

These validations could be added in future enhancements but are currently absent from the implementation.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L184-L198](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L184-L198)

---

## Role Assignment

Every newly registered client is automatically assigned a default role value:

```

```

This hardcoded role assignment occurs at [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L189](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L189-L189)

 and is passed to the `Cliente` constructor. The role value of `1` typically represents a standard client/customer role, as opposed to administrative or mechanic roles.

The role system integrates with the authentication mechanism documented in [Authentication System](/BrayanTirado/Servicio-Mec-nico/2.2-authentication-system), where different roles may have different access privileges. However, the registration form does **not** provide UI controls for selecting roles - all clients registered through this interface receive the default role.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L189-L195](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L189-L195)

---

## Integration with Data Layer

The registration form delegates all database operations to `ClienteDAO`:

```

```

### DAO Instantiation

The `ClienteDAO` instance is created once in the `MarcoClientes` constructor at [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L23](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L23-L23)

:

```

```

This DAO instance is reused for all registration operations during the lifetime of the frame.

### Persistence Method Call

After validation passes, the form invokes:

```

```

The `ClienteDAO.registrarcliente()` method handles:

* Database connection acquisition via `ConexionBD`
* SQL INSERT statement execution
* Connection cleanup
* Error handling (SQLException management)

For details on the `ClienteDAO` implementation, see [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L17-L26](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L17-L26)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L195-L196](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L195-L196)

---

## Form Reset Mechanism

After successful client registration, the form automatically clears all input fields to prepare for the next entry. This is implemented in the `limpiarformulario()` method at [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L199-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L199-L204)

:

```

```

This method is invoked immediately after `clienteDAO.registrarcliente(cliente)` completes (line 197), ensuring that:

1. The user receives visual confirmation that the operation completed
2. The form is ready for rapid entry of multiple clients without manual field clearing
3. No residual data from the previous entry remains visible

**Note:** The form does **not** display a success message dialog after registration. The cleared fields serve as implicit confirmation of success. If an exception occurs in `ClienteDAO.registrarcliente()`, the fields would not be cleared, providing implicit failure feedback.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L197-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L197-L204)

---

## Component Structure and Event Binding

The relationship between UI components and business logic:

```

```

The event binding is configured in the NetBeans form designer at [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form L231-L233](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form#L231-L233)

 which generates the action listener registration code in the `initComponents()` method.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L94-L98](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L94-L98)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L164-L166](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L164-L166)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form L231-L233](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form#L231-L233)

---

## Summary

`MarcoClientes` provides a straightforward client registration interface with:

* **4 input fields**: nombre (required), documento (required), teléfono (optional), correo (optional)
* **Minimal validation**: Only checks for non-empty required fields
* **Automatic role assignment**: All clients receive role ID 1
* **DAO-based persistence**: Delegates database operations to `ClienteDAO`
* **Automatic form reset**: Clears fields after successful save
* **No confirmation dialog**: Uses form clearing as implicit success indicator

The form follows the standard patterns documented in [Common Patterns and Conventions](/BrayanTirado/Servicio-Mec-nico/9-common-patterns-and-conventions) and integrates with the overall client management workflow. For querying, updating, or deleting registered clients, use the interface described in [Client Management Interface](/BrayanTirado/Servicio-Mec-nico/7.2-client-management-interface).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java L1-L211](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.java#L1-L211)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form L1-L241](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoClientes.form#L1-L241)