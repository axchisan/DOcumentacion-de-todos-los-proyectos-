# Adding Products

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form)
> * [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java)

## Purpose and Scope

This document describes the product addition functionality implemented in the `FormularioProducto` class, a `JInternalFrame` component that provides a form-based interface for adding new products to the inventory system. Users can enter product details (name, description, price, stock quantity), save them to the database, and immediately view the updated product list in an embedded table.

For information about searching and viewing existing products, see [Product Search Interface](/BrayanTirado/Servicio-Mec-nico/5.1-product-search-interface). For the broader inventory management context, see [Inventory Management Module](/BrayanTirado/Servicio-Mec-nico/5-inventory-management-module).

---

## Component Architecture

The `FormularioProducto` class integrates several components to provide a complete product addition experience:

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L23-L51](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L23-L51)

---

## Form Fields and Layout

The form captures five product attributes through text field components:

| Field | Component | Data Type | Purpose |
| --- | --- | --- | --- |
| ID | `txtId` | Integer | Auto-generated product identifier (read-only after save) |
| Nombre | `txtNombre` | String | Product name |
| Descripción | `txtDescripcion` | String | Detailed product description |
| Precio | `txtPrecio` | Double | Unit price in local currency |
| Stock | `txtStock` | Integer | Current inventory quantity |

The form layout is defined using NetBeans GUI Builder (GroupLayout) and includes a light purple background color (`RGB(203, 204, 255)`). All labels use the Comic Sans MS font family with bold styling.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L61-L232](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L61-L232)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form L1-L260](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form#L1-L260)

---

## Save Product Workflow

The following sequence diagram illustrates the complete product save operation triggered by the `btnGuardar` button:

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L234-L252](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L234-L252)

---

## Form Field Validation and Error Handling

The `btnGuardarActionPerformed` method performs input validation when saving:

### Numeric Validation

The form expects numeric values for price and stock fields. When parsing fails, a `NumberFormatException` is caught and the user receives a dialog message: *"Por favor, ingrese valores numéricos válidos."*

```

```

### Database Error Handling

SQL exceptions during the save operation are logged using Java's standard logging framework (`java.util.logging.Logger`). The logger is initialized as a static class member:

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L234-L252](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L234-L252)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L32](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L32-L32)

---

## Product Table Display

After the form initialization and each successful save operation, the product list is automatically refreshed and displayed:

```

```

### Table Model Configuration

The table uses a `DefaultTableModel` with five columns defined at initialization:

| Column | Data | Formatting |
| --- | --- | --- |
| ID | `producto.getId()` | Plain integer |
| Nombre | `producto.getNombre()` | Plain string |
| Descripción | `producto.getDescripcion()` | Plain string |
| Precio | `producto.getPrecio()` | Currency format via `formatoPeso` |
| Stock | `producto.getStock()` | Plain integer |

The table model overrides `isCellEditable()` to return `false` for all cells, making the table read-only.

### Currency Formatting

Prices are displayed using Java's `NumberFormat.getCurrencyInstance()`, which formats the double value according to the system locale (e.g., `$1,234.56` for USD locale):

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L37-L51](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L37-L51)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L317-L337](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L317-L337)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L28](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L28-L28)

---

## Low Stock Alert System

Immediately after saving a product, the system checks whether the stock level falls below a predefined threshold:

```

```

### Alert Dialog Content

When stock is below the threshold, a modal dialog displays:

* Product name
* Current stock quantity
* Minimum threshold (umbral mínimo)
* Unit price (formatted as currency)

Example message format:

```
¡Alerta! Stock bajo para: [Product Name]
Cantidad actual: [Current Stock]
Umbral mínimo: [Threshold Value]
Precio unitario: [Formatted Price]
```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L339-L346](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L339-L346)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L243](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L243-L243)

---

## Interactive Table Selection

When a user clicks on a row in `tablaProductos`, the form fields populate with that row's data and become read-only:

### Mouse Click Event Handler

The `tablaProductosMouseClicked` event handler executes the following logic:

```

```

### Field Population Logic

Each text field is populated from the corresponding table column:

```

```

After population, all fields (including `txtId`) are set to non-editable mode by calling `setEditable(false)` on each component. This prevents accidental modification of existing product data through this form.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L264-L282](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L264-L282)

---

## Form Reset Functionality

The `btnLimpiar` button and the post-save cleanup both invoke the `limpiarCampos()` method:

### Field Clearing Process

```

```

This method performs three operations:

1. **Clear text fields**: Sets `txtId`, `txtNombre`, `txtPrecio`, `txtDescripcion`, and `txtStock` to empty strings
2. **Clear table selection**: Calls `tablaProductos.clearSelection()` to deselect any highlighted row
3. **Re-enable editing**: Sets all text fields back to `setEditable(true)`, allowing new data entry

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L303-L316](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L303-L316)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L254-L257](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L254-L257)

---

## JInternalFrame Properties

The `FormularioProducto` extends `JInternalFrame` with the following properties configured in the NetBeans form designer:

| Property | Value | Effect |
| --- | --- | --- |
| `closable` | `true` | Users can close the window |
| `iconifiable` | `true` | Users can minimize to desktop |
| `maximizable` | `true` | Users can maximize to fill desktop pane |

These properties allow the frame to be managed within the MDI container (`JDesktopPane`) of the main application window. See [Main Application Window](/BrayanTirado/Servicio-Mec-nico/3.1-main-application-window) for details on how internal frames are launched and managed.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L79-L81](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L79-L81)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form L4-L8](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.form#L4-L8)

---

## Integration with Data Access Layer

The `FormularioProducto` class maintains a single instance of `ProductoDAO` as a member variable:

```

```

### DAO Methods Used

The form interacts with the following `ProductoDAO` methods:

| Method | Purpose | Called From |
| --- | --- | --- |
| `guardarProducto(Producto)` | Inserts new product into database | `btnGuardarActionPerformed()` |
| `obtenerTodos()` | Retrieves all products as `List<Producto>` | `cargarProductos()` |

Both methods handle their own database connection lifecycle through the `ConexionBD` utility class. See [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer) for more information about the DAO pattern implementation and connection management.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L24](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L24-L24)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L241](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L241-L241)

 [src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java L319](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/inventario/FormularioProducto.java#L319-L319)