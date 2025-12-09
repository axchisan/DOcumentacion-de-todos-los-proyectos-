# Common Patterns and Conventions

> **Relevant source files**
> * [libs/jcalendar-1.4.jar](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/libs/jcalendar-1.4.jar)
> * [src/main/java/com/adso/el_taller_de_adso/ConexionBD.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/ConexionBD.java)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form)
> * [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java)
> * [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java)
> * [src/main/java/com/adso/el_taller_de_adso/servicios/Servicio.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/Servicio.java)
> * [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java)

## Purpose and Scope

This document describes the recurring design patterns, coding conventions, and architectural practices used consistently across the El Taller de ADSO codebase. Understanding these patterns is essential for maintaining code consistency and extending existing functionality.

For information about the overall architecture and how modules interact, see [Application Architecture](/BrayanTirado/Servicio-Mec-nico/3-application-architecture). For details about specific domain modules, refer to [Service Management](/BrayanTirado/Servicio-Mec-nico/4-service-management-module), [Inventory Management](/BrayanTirado/Servicio-Mec-nico/5-inventory-management-module), [Vehicle Management](/BrayanTirado/Servicio-Mec-nico/6-vehicle-management-module), and [Client Management](/BrayanTirado/Servicio-Mec-nico/7-client-management-module).

---

## DAO Pattern Implementation

The codebase implements the **Data Access Object (DAO) pattern** to separate persistence logic from business logic. Each major domain entity has a dedicated DAO class that encapsulates all database operations for that entity.

### DAO Structure

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L1-L300](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L1-L300)

 [src/main/java/com/adso/el_taller_de_adso/ConexionBD.java L1-L42](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/ConexionBD.java#L1-L42)

### DAO Method Naming Conventions

All DAO classes follow consistent naming patterns for CRUD operations:

| Operation | Method Name Pattern | Example |
| --- | --- | --- |
| **Retrieve All** | `getAll{Entity}()` | `getAllClientes()` returns `List<String>` |
| **Retrieve by ID** | `get{Entity}Id(String identifier)` | `getClienteId(String nombre)` returns `int` |
| **Retrieve Details** | `get{Entity}{Attribute}(String key)` | `getPrecioProducto(String nombre)` returns `double` |
| **Insert** | `add{Entity}({Entity} object)` | `addServicio(Servicio servicio, ...)` |
| **Update** | `actualizar{Entity}({Entity} object)` | `actualizarCliente(Cliente cliente)` |
| **Delete** | `eliminar{Entity}(String identifier)` | `eliminarCliente(String documento)` |
| **Search** | `buscar{Entity}(String criterio, String valor)` | `buscarCliente(String criterio, String valor)` |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L46-L203](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L46-L203)

### Connection Management Pattern

Every DAO method follows a strict pattern for managing database connections:

```

```

**Standard pattern in code:**

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/ConexionBD.java L16-L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/ConexionBD.java#L16-L29)

 [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L46-L64](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L46-L64)

---

## JInternalFrame Pattern

All module windows in the application extend `javax.swing.JInternalFrame` and follow consistent initialization and lifecycle patterns.

### JInternalFrame Lifecycle

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L19-L41](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L19-L41)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L24-L31](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L24-L31)

### Common JInternalFrame Properties

All `JInternalFrame` instances are configured with the same properties:

| Property | Value | Purpose |
| --- | --- | --- |
| `closable` | `true` | Allow window closing |
| `iconifiable` | `true` | Allow minimizing |
| `maximizable` | `true` | Allow maximizing |
| `resizable` | `true` | Allow resizing (some frames) |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form L5-L8](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.form#L5-L8)

 [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L71-L73](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L71-L73)

---

## NetBeans GUI Builder Conventions

The entire UI layer is built using NetBeans GUI Builder, which generates specific code patterns and markers that must be preserved.

### NetBeans Code Markers

```

```

**Key NetBeans conventions:**

1. **Generated code boundaries:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L48-L246](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L48-L246) * Never manually edit code between `//GEN-BEGIN:initComponents` and `//GEN-END:initComponents` * Variable declarations between `//GEN-BEGIN:variables` and `//GEN-END:variables` are auto-managed
2. **Event handler registration:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L95-L99](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L95-L99) ``` ```
3. **Custom event handler implementation:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L248-L277](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L248-L277) ``` ```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L48-L246](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L48-L246)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L38-L216](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L38-L216)

---

## Table Model Management

`DefaultTableModel` is used consistently across the application for displaying tabular data. The pattern involves initialization, population, and update operations.

### Table Model Lifecycle

```

```

**Standard table initialization pattern:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L259-L262](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L259-L262)

```

```

**Standard data loading pattern:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L263-L276](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L263-L276)

```

```

### Table Selection Handler Pattern

A common pattern is to populate form fields when a user selects a table row:

```

```

**Implementation:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L29-L29)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L298-L306](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L298-L306)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L259-L306](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L259-L306)

---

## Form Validation Pattern

All forms implement validation before performing database operations. The pattern uses early returns and `JOptionPane` dialogs for error messaging.

### Validation Flow

```

```

**Example validation implementation:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L248-L277](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L248-L277)

```

```

### Standard Validation Checks

| Validation Type | Pattern | Example |
| --- | --- | --- |
| **Null/Empty String** | `text.trim().isEmpty()` | [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L318-L321](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L318-L321) |
| **Date Selection** | `dateChooser.getDate() == null` | [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L250-L253](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L250-L253) |
| **Table Row Count** | `tableModel.getRowCount() == 0` | [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L255-L258](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L255-L258) |
| **Numeric Format** | `try { Integer.parseInt() } catch` | [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L287-L305](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L287-L305) |
| **Stock Availability** | `quantity <= availableStock` | [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L291-L299](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L291-L299) |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L248-L307](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L248-L307)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L308-L327](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L308-L327)

---

## Error Handling Conventions

The application uses a consistent approach to error handling with try-catch blocks and user-friendly error dialogs.

### Error Handling Pattern

```

```

### DAO Layer Error Handling

**Pattern 1: Print and return default:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L46-L64](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L46-L64)

```

```

**Pattern 2: Re-throw exception:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L205-L264](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L205-L264)

```

```

### UI Layer Error Handling

**Pattern: Catch and display to user:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L270-L276](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L270-L276)

```

```

### Foreign Key Constraint Handling

Special handling for database constraint violations: [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L338-L349](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L338-L349)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L46-L264](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L46-L264)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L338-L349](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L338-L349)

---

## Transaction Management

Complex operations that span multiple tables use explicit transaction management with rollback capability.

### Transaction Pattern

```

```

**Transaction implementation:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L205-L264](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L205-L264)

```

```

### Atomic Stock Update

The stock update ensures atomicity by checking availability in the same statement: [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L285-L299](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L285-L299)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L205-L299](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L205-L299)

---

## ID Resolution Pattern

The application displays user-friendly identifiers (names, license plates) in the UI but uses numeric IDs internally for database operations.

### Resolution Flow

```

```

**ComboBox population with names:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L32-L36](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L32-L36)

```

```

**ID resolution before saving:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L267-L268](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L267-L268)

```

```

**ID lookup implementation:** [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L145-L163](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L145-L163)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L32-L36](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L32-L36)

 [src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java L145-L203](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/ServicioDAO.java#L145-L203)

---

## Form Clearing Pattern

After successful CRUD operations, forms are cleared/reset to prepare for the next operation. This pattern is implemented consistently across all modules.

### Clear Form Implementation

**Pattern structure:**

```

```

**MarcoServicios clear implementation:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L334-L341](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L334-L341)

```

```

**MarcoGestionClientes clear implementation:** [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L352-L359](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L352-L359)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L334-L341](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L334-L341)

 [src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java L352-L359](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/clientes/MarcoGestionClientes.java#L352-L359)

---

## Number Formatting Pattern

Currency and numeric values are formatted consistently using `NumberFormat` to ensure proper decimal handling.

### Cost Calculation and Formatting

```

```

**Total cost calculation:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L315-L332](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L315-L332)

```

```

**Parsing formatted values:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L263-L265](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L263-L265)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L263-L265](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L263-L265)

 [src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java L315-L332](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/MarcoServicios.java#L315-L332)

---

## Domain Model Pattern

All domain entities follow a consistent JavaBean pattern with private fields and public getters/setters.

### Entity Structure

```

```

**Entity implementation:** [src/main/java/com/adso/el_taller_de_adso/servicios/Servicio.java L9-L74](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/Servicio.java#L9-L74)

```

```

### Entity Naming Conventions

| Entity | Class Name | Table Name | Key Field |
| --- | --- | --- | --- |
| Service | `Servicio` | `servicios` | `id` |
| Client | `Cliente` | `clientes` | `id` / `documento` |
| Vehicle | `Vehiculo` | `vehiculos` | `id` / `placa` |
| Product | `Producto` | `productos` | `id` / `nombre` |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/servicios/Servicio.java L9-L74](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/servicios/Servicio.java#L9-L74)

---

## Summary

The El Taller de ADSO codebase follows consistent patterns across all modules:

| Pattern | Key Classes/Files | Purpose |
| --- | --- | --- |
| **DAO Pattern** | `ServicioDAO`, `ClienteDAO`, `VehiculoDAO`, `ProductoDAO` | Encapsulate database operations |
| **Connection Management** | `ConexionBD` | Centralized database connection handling |
| **JInternalFrame** | All `Marco*` classes | MDI window containers |
| **NetBeans GUI Builder** | `.form` and generated code | Visual UI design and consistency |
| **DefaultTableModel** | All CRUD interfaces | Tabular data display |
| **Form Validation** | All save/update handlers | Input validation with early returns |
| **Error Handling** | Try-catch with `JOptionPane` | User-friendly error messages |
| **Transactions** | `ServicioDAO.addServicio` | ACID compliance for complex operations |
| **ID Resolution** | `get*Id()` methods in DAOs | Map UI identifiers to database IDs |
| **Form Clearing** | `clearForm()`, `limpiarFormulario()` | Reset UI after operations |

These patterns ensure consistency, maintainability, and predictable behavior throughout the application.