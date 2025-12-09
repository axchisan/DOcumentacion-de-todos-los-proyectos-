# Inventory Management Module

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form)
> * [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java)
> * [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form)
> * [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java)

## Purpose and Scope

The Inventory Management Module provides the functionality for managing products and parts inventory within the mechanic workshop application. This module enables users to add new products, search for existing products, view product details, track stock levels, and receive alerts for low inventory. It consists of two primary JInternalFrame components that integrate with the `ProductoDAO` data access layer to perform CRUD operations on the `productos` table in the PostgreSQL database.

For information about how products are associated with services and stock is decremented during service creation, see [Service Management Module](/BrayanTirado/Servicio-Mec-nico/4-service-management-module). For details about the main application window that hosts these internal frames, see [Main Application Window](/BrayanTirado/Servicio-Mec-nico/3.1-main-application-window).

---

## Module Architecture

The Inventory Management Module follows the standard MDI pattern used throughout the application, with JInternalFrame components launched from the main menu and displayed in the central JDesktopPane.

```

```

**Sources:** [High-Level Architecture Diagrams](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/High-Level Architecture Diagrams)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L1-L219](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L1-L219)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L1-L363](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L1-L363)

---

## Component Overview

| Component | Purpose | Key Features | Importance Score |
| --- | --- | --- | --- |
| `BuscarProducto` | Product search and lookup | Search by name, table display, row selection | 19.55 |
| `FormularioProducto` | Product creation and viewing | Add products, view all products, low stock alerts | 11.39 |
| `ProductoDAO` | Data access layer | CRUD operations on productos table | Referenced |
| `Producto` | Domain model | Encapsulates product attributes | Referenced |

**Sources:** [High-Level System Architecture Diagrams](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/High-Level System Architecture Diagrams)

---

## Product Search Interface (BuscarProducto)

### Overview

The `BuscarProducto` class provides a search interface for locating products by name. It displays all products in a JTable and allows users to search for specific products, which are then highlighted in the table.

### UI Components

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L11-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L11-L30)

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L17-L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L17-L29)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form L1-L173](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form#L1-L173)

### Initialization Process

The constructor initializes the table model with five columns and loads all products from the database:

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L22-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L22-L30)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L187-L195](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L187-L195)

### Search Workflow

The search functionality is triggered by the `btnbuscar` button and searches by product name:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L147-L171](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L147-L171)

**Search Process:**

1. User enters product name in `txtBuscar` text field
2. User clicks "Buscar" button
3. `btnbuscarActionPerformed()` validates input
4. `ProductoDAO.buscarPorNombre()` queries database
5. If found: * Product is moved to top of `listaProductos` (line 158-159) * Table is refreshed via `actualizarTabla()` (line 160) * Row is highlighted via `resaltarProductoEnTabla()` (line 161) * Success message displayed (line 162)
6. If not found: * Error message displayed (line 164) * Search field cleared (line 165)

### Table Display Logic

The `actualizarTabla()` method populates the JTable with formatted data:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L197-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L197-L208)

**Key Features:**

* Currency formatting using `NumberFormat.getCurrencyInstance()` (line 16, 204)
* Table cleared before repopulation (line 198)
* Iterates through `listaProductos` to add rows (lines 199-207)
* Price displayed in localized currency format

### Row Highlighting

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L176-L185](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L176-L185)

The `resaltarProductoEnTabla()` method:

* Iterates through table rows to find matching product ID
* Sets row selection using `setRowSelectionInterval()` (line 180)
* Scrolls the table to make the selected row visible (line 181)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L147-L185](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L147-L185)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L197-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L197-L208)

---

## Product Addition Interface (FormularioProducto)

### Overview

The `FormularioProducto` class provides a form for adding new products to the inventory. It includes input validation, automatic table refresh, and low stock alerting capabilities.

### UI Component Structure

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L23-L51](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L23-L51)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form L1-L261](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form#L1-L261)

### Initialization

[src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L37-L51](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L37-L51)

**Initialization Steps:**

1. Calls `initComponents()` to generate NetBeans-designed UI
2. Creates `DefaultTableModel` with custom `isCellEditable()` override (lines 41-48) * All cells are non-editable to prevent direct table editing
3. Sets model on `tablaProductos` (line 49)
4. Calls `cargarProductos()` to load initial data (line 50)

### Save Operation Workflow

The save operation validates input, persists to database, refreshes the table, and checks for low stock conditions:

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L234-L252](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L234-L252)

### Save Handler Implementation

[src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L234-L252](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L234-L252)

**Key Steps:**

1. **Data Binding**: Maps text field values to `Producto` object (lines 237-240)
2. **Validation**: Catches `NumberFormatException` for invalid numeric input (line 245)
3. **Persistence**: Calls `dao.guardarProducto(producto)` (line 241)
4. **Refresh**: Calls `cargarProductos()` to update table (line 242)
5. **Alert Check**: Calls `verificarStockBajo(producto)` (line 243)
6. **Reset**: Calls `limpiarCampos()` to clear form (line 244)

### Low Stock Alert System

[src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L339-L346](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L339-L346)

The `verificarStockBajo()` method provides proactive inventory management:

**Alert Logic:**

* Compares current stock against `getUmbralBajoStock()` threshold
* Displays modal dialog if stock is below threshold
* Dialog includes: * Product name * Current stock quantity * Minimum threshold * Unit price (formatted as currency)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L339-L346](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L339-L346)

### Row Selection and View Mode

[src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L264-L282](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L264-L282)

When a user clicks a row in the table:

**Process:**

1. Gets selected row index via `tablaProductos.getSelectedRow()` (line 266)
2. Populates all text fields with row data (lines 268-272)
3. Removes currency symbols and commas from price for display (line 271)
4. **Sets all fields to non-editable** (lines 275-279) * This prevents accidental modifications to existing products * Implements a view-only mode for table selections

**Design Decision:** The form supports only product creation, not editing. Clicking a table row enters "view mode" where fields are populated but disabled. Users must click "Limpiar" to return to creation mode.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L264-L282](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L264-L282)

### Clear Form Operation

[src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L303-L316](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L303-L316)

The `limpiarCampos()` method:

* Clears all text fields (lines 304-308)
* Clears table selection (line 309)
* **Re-enables all fields for editing** (lines 312-315)
* Triggered by "Limpiar" button or after successful save

---

## Table Management Pattern

Both `BuscarProducto` and `FormularioProducto` follow a consistent pattern for table management:

### Common Table Structure

| Column Index | Column Name | Data Type | Format |
| --- | --- | --- | --- |
| 0 | ID | Integer | Plain |
| 1 | Nombre | String | Plain |
| 2 | Descripción | String | Plain |
| 3 | Precio | Double | Currency |
| 4 | Stock | Integer | Plain |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L24](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L24-L24)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L41](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L41-L41)

### Data Loading Pattern

```

```

**Implementation in BuscarProducto:**

* [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L187-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L187-L208)

**Implementation in FormularioProducto:**

* [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L317-L337](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L317-L337)

### Currency Formatting

Both classes use `NumberFormat.getCurrencyInstance()` for consistent price display:

```

```

Applied in `actualizarTabla()` when populating price column:

```

```

This provides localized currency formatting (e.g., "$1,234.56" in US locale).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L16](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L16-L16)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L28](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L28-L28)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L204-L204)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L333](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L333-L333)

---

## Data Access Layer Integration

### ProductoDAO Interface

Both UI components interact with `ProductoDAO` which provides the following methods:

| Method | Purpose | Used By |
| --- | --- | --- |
| `obtenerTodos()` | Retrieves all products from database | Both classes |
| `buscarPorNombre(String)` | Searches for product by exact name | BuscarProducto |
| `guardarProducto(Producto)` | Inserts new product into database | FormularioProducto |
| `getProductoStock(String)` | Gets stock quantity by name | Referenced in services |
| `getPrecioProducto(String)` | Gets price by name | Referenced in services |
| `getProductoDescription(String)` | Gets description by name | Referenced in services |

### Database Schema Reference

The inventory module operates on the `productos` table with the following structure (inferred from usage):

| Column | Type | Purpose |
| --- | --- | --- |
| id | Serial/Integer | Primary key, auto-generated |
| nombre | String | Product name, used for searches |
| descripcion | String | Product description |
| precio | Double/Numeric | Unit price |
| stock | Integer | Current inventory quantity |
| umbral_bajo_stock | Integer | Low stock threshold (referenced) |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L12-L13](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L12-L13)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L24-L25](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L24-L25)

---

## Connection Management

Both classes follow the standard connection pattern defined by the application architecture:

```

```

**Connection Lifecycle:**

1. DAO method called from UI component
2. `ConexionBD.conectar()` obtains database connection
3. SQL operations performed
4. `ConexionBD.cerrar()` releases connection
5. Results returned to UI component

**Sources:** [High-Level Architecture Diagram 3](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/High-Level Architecture Diagram 3)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L10](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L10-L10)

---

## Error Handling

### Exception Handling Pattern

Both classes implement consistent error handling:

**BuscarProducto:**
[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L167-L170](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L167-L170)

* Catches `SQLException` from DAO operations
* Logs errors using `java.util.logging.Logger` (line 168)
* Displays user-friendly error dialog (line 169)

**FormularioProducto:**
[src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L245-L249](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L245-L249)

* Catches `NumberFormatException` for invalid numeric input (line 245)
* Catches `SQLException` from DAO operations (line 247)
* Uses static logger instance for consistent logging (line 32)

### Logger Configuration

Both classes use `java.util.logging.Logger`:

```

```

**Usage:** `logger.log(java.util.logging.Level.SEVERE, "message", exception);`

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L20](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L20-L20)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L32](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L32-L32)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L168](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L168-L168)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L248](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L248-L248)

---

## NetBeans GUI Builder Integration

Both forms are designed using NetBeans GUI Builder, evidenced by:

### Form Files

* `BuscarProducto.form` - XML definition of search UI layout
* `FormularioProducto.form` - XML definition of product form layout

### Generated Code Markers

Both Java files contain standard NetBeans markers:

```

```

### Layout Manager

Both forms use `GroupLayout`, the default layout manager for NetBeans GUI Builder, which provides complex nested layout groups for precise component positioning.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form L1-L173](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form#L1-L173)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form L1-L261](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form#L1-L261)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L33-L141](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L33-L141)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L60-L232](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L60-L232)

---

## Integration with Service Module

The inventory module integrates with the service creation workflow for stock management:

```

```

**Stock Decrement Process:**

1. Service creation workflow retrieves product list from `ProductoDAO`
2. User selects products and quantities
3. `ServicioDAO.addServicio()` performs transaction: * Inserts service record * Inserts service-product associations * **Decrements stock in productos table**
4. If stock insufficient, transaction rolls back

This ensures inventory consistency between services and available stock.

**Sources:** [High-Level Architecture Diagram 4](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/High-Level Architecture Diagram 4)

---

## Summary

The Inventory Management Module provides essential product management capabilities through two complementary interfaces:

* **BuscarProducto**: Enables quick product lookup by name with table-based results display and row highlighting
* **FormularioProducto**: Supports new product creation with validation, automatic table refresh, and proactive low stock alerts

Both components follow consistent patterns for table management, currency formatting, error handling, and DAO integration. The module enforces a view-only pattern when displaying existing products, focusing on creation rather than editing. Integration with the Service Management Module ensures stock levels are automatically updated when products are used in services.

**Key Design Characteristics:**

* MDI JInternalFrame architecture
* NetBeans GUI Builder-designed forms
* DAO pattern for data access
* Consistent currency formatting
* Comprehensive error handling with logging
* Transaction-safe stock management through service integration

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L1-L219](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L1-L219)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L1-L363](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L1-L363)