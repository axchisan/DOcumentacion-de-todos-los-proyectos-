# Product Search Interface

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form)
> * [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java)

This document describes the `BuscarProducto` interface, which provides search and viewing capabilities for the product inventory. The interface displays all products in a tabular format and allows users to search for specific products by name. This is a read-only view; for adding new products, see [Adding Products](/BrayanTirado/Servicio-Mec-nico/5.2-adding-products).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L1-L219](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L1-L219)

---

## Overview

`BuscarProducto` is a `JInternalFrame` that operates within the MDI desktop environment of `AplicacionPrincipal`. It is instantiated when the user selects the search option from the Inventarios menu and provides a simple interface for locating products within the system's inventory.

### Key Characteristics

| Characteristic | Details |
| --- | --- |
| **Class Type** | `javax.swing.JInternalFrame` |
| **Primary Function** | Search and view product inventory |
| **Data Access** | `ProductoDAO` |
| **Search Method** | By product name (partial or exact match) |
| **Display Format** | JTable with formatted currency values |
| **Edit Capability** | None (read-only view) |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L11-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L11-L30)

---

## Component Architecture

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L12-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L12-L30)

---

## Data Model and Display

### Table Structure

The product table is implemented using `DefaultTableModel` with five columns configured at initialization:

```
Column Index | Column Name   | Data Type | Formatting
-------------|---------------|-----------|-------------
0            | ID            | Integer   | None
1            | Nombre        | String    | None
2            | Descripción   | String    | None
3            | Precio        | Double    | Currency (COP)
4            | Stock         | Integer   | None
```

The table model is created with predefined column headers and zero initial rows:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L24-L25](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L24-L25)

### Currency Formatting

Prices are displayed using Colombia's currency format via `NumberFormat.getCurrencyInstance()`. This formatter is stored as an instance variable `formatoPeso` and applied when populating table rows:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L16-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L16-L204)

### Table Selection Behavior

The table is configured with single-row selection mode and custom selection colors:

* Selection background: RGB(200, 255, 200) - light green
* Selection foreground: RGB(0, 0, 0) - black

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L27-L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L27-L29)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L14-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L14-L208)

---

## Initialization Workflow

```

```

The initialization process follows these steps:

1. **Constructor invocation** - `BuscarProducto()` is called
2. **Component initialization** - `initComponents()` sets up GUI elements from the NetBeans `.form` file
3. **Table model setup** - Creates `DefaultTableModel` with column headers
4. **Product loading** - `cargarProductos()` retrieves all products
5. **Table population** - `actualizarTabla()` formats and displays data

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L22-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L22-L208)

---

## Search Operation

### Search Flow

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L147-L171](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L147-L171)

### Search Implementation Details

The search operation is triggered by the `btnbuscarActionPerformed` event handler:

**Input Validation:**

* Retrieves text from `txtBuscar.getText().trim()`
* Checks if the input is empty
* Displays error dialog if validation fails

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L150-L154](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L150-L154)

**Database Query:**

* Calls `dao.buscarPorNombre(nombre)` to search by product name
* Returns a `Producto` object if found, or `null` if not found

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L156](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L156-L156)

**Result Handling:**
When a product is found, the system performs the following operations:

1. **Remove from current position** - `listaProductos.remove(productoEncontrado)`
2. **Insert at top** - `listaProductos.add(0, productoEncontrado)`
3. **Refresh display** - `actualizarTabla()`
4. **Highlight row** - `resaltarProductoEnTabla(productoEncontrado.getId())`
5. **Show confirmation** - Display success message with product name

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L158-L162](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L158-L162)

**Error Handling:**

* `SQLException` exceptions are logged using Java's logging framework
* User-friendly error dialogs are displayed for database errors
* When product not found, the search field is cleared

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L164-L170](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L164-L170)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L147-L171](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L147-L171)

---

## Row Highlighting Mechanism

The `resaltarProductoEnTabla` method provides visual feedback by selecting and scrolling to the found product:

```

```

**Implementation:**

* Iterates through all rows in `tablaProductos`
* Compares the ID value in column 0 with the target ID
* Uses `setRowSelectionInterval(i, i)` to select the matching row
* Calls `scrollRectToVisible` to ensure the row is visible in the viewport
* Breaks immediately after finding and highlighting the row

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L176-L185](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L176-L185)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L176-L185](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L176-L185)

---

## Table Update Process

The `actualizarTabla` method refreshes the table display with the current product list:

**Process:**

1. **Clear existing rows** - `modeloTabla.setRowCount(0)` removes all rows
2. **Iterate product list** - Loop through `listaProductos`
3. **Format data** - Apply currency formatting to price field
4. **Add rows** - Insert each product as a new table row

**Row Data Structure:**

```
Object[] row = {
    p.getId(),              // Column 0: Integer ID
    p.getNombre(),          // Column 1: String name
    p.getDescripcion(),     // Column 2: String description
    formatoPeso.format(p.getPrecio()),  // Column 3: Formatted price
    p.getStock()            // Column 4: Integer stock
}
```

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L197-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L197-L208)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L197-L208](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L197-L208)

---

## UI Component Mapping

### Component Hierarchy

```

```

### Component Properties

| Component | Variable Name | Type | Key Properties |
| --- | --- | --- | --- |
| Main Panel | `jPanel1` | `JPanel` | Background: RGB(204, 204, 255) - light purple |
| Search Label | `jLabel6` | `JLabel` | Font: Comic Sans MS, 12pt, BoldText: "Buscar por ID" |
| Search Field | `txtBuscar` | `JTextField` | Border: EtchedBorder |
| Search Button | `btnbuscar` | `JButton` | Font: Comic Sans MS, 14pt, BoldForeground: RGB(0, 102, 255)Border: SoftBevelBorder |
| Table Container | `jScrollPane1` | `JScrollPane` | Border: EtchedBorder |
| Product Table | `tablaProductos` | `JTable` | Border: SoftBevelBorder (raised)SelectionMode: SINGLE_SELECTION |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L36-L216](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L36-L216)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form L44-L169](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form#L44-L169)

---

## Event Handlers

### Mouse Click Handler

The `tablaProductosMouseClicked` method is registered as a mouse listener but currently has no implementation:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L143-L145](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L143-L145)

This handler was likely intended for future functionality such as displaying detailed product information when a row is clicked.

### Text Field Action Handler

The `txtBuscarActionPerformed` method handles the Enter key press in the search field but is currently empty:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L173-L175](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L173-L175)

This could be implemented to trigger the search operation when the user presses Enter, providing an alternative to clicking the search button.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L143-L175](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L143-L175)

---

## Integration with ProductoDAO

The `BuscarProducto` interface relies on `ProductoDAO` for all database operations:

### DAO Methods Used

| Method | Purpose | Return Type |
| --- | --- | --- |
| `obtenerTodos()` | Retrieve all products | `List<Producto>` |
| `buscarPorNombre(String)` | Search by product name | `Producto` |

Both methods throw `SQLException` which is caught and handled in the UI layer.

### Data Flow

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L12-L189](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L12-L189)

---

## Logging and Error Handling

### Logger Configuration

The class uses Java's standard logging framework:

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L20](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L20-L20)

### Error Scenarios

| Error Type | Cause | Handling | User Feedback |
| --- | --- | --- | --- |
| Empty Search | User clicks search with blank field | Validation check | "Por favor, ingrese un nombre para buscar." |
| Product Not Found | No matching product in database | Null check | "No se encontró ningún producto con ese nombre: {nombre}" |
| Database Error | SQLException during query | Try-catch block | "Error al buscar en la base de datos." + logging |
| Load Error | SQLException during initialization | Try-catch block | "Error al cargar los productos." + logging |

**Logging Examples:**

* Search errors: `logger.log(Level.SEVERE, "Error al buscar producto", ex)`
* Load errors: `logger.log(Level.SEVERE, "Error al cargar productos", ex)`

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L167-L194](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L167-L194)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L20-L195](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L20-L195)

---

## NetBeans Form Designer Integration

The UI layout is managed by NetBeans GUI Builder, as indicated by the `.form` file and generated code sections:

### Generated Code Markers

The `initComponents()` method is enclosed in fold markers:

```xml
// <editor-fold defaultstate="collapsed" desc="Generated Code">
// ... generated code ...
// </editor-fold>
```

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L33-L141](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L33-L141)

### Form Metadata

The `.form` file contains:

* Component hierarchy definitions
* Layout manager configurations (GroupLayout)
* Property settings (fonts, colors, borders)
* Event handler registrations

[src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form L1-L172](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form#L1-L172)

**Important Note:** The generated code sections should not be manually edited. Any UI changes should be made through the NetBeans visual designer to prevent corruption of the form.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L33-L141](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L33-L141)

 [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form L1-L172](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.form#L1-L172)

---

## Design Patterns and Conventions

### Patterns Implemented

1. **DAO Pattern** - Database operations delegated to `ProductoDAO`
2. **MVC Separation** - UI (View), DAO (Controller), Producto (Model)
3. **Lazy Loading** - Products loaded only once at initialization
4. **Table Model Pattern** - `DefaultTableModel` manages table data

### Notable Implementation Details

* **Immutable Search Results:** Search operation modifies the order of `listaProductos` rather than creating a filtered view
* **No Pagination:** All products are loaded and displayed at once
* **Single Selection:** Only one table row can be selected at a time
* **Currency Formatting:** Consistent use of Colombian Peso format for all price displays
* **Visual Feedback:** Custom selection colors and auto-scrolling provide clear user feedback

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java L1-L219](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/BuscarProducto.java#L1-L219)