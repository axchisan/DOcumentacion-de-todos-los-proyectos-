# Invoice Generation

> **Relevant source files**
> * [app/models/__pycache__/ventas.cpython-313.pyc](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/__pycache__/ventas.cpython-313.pyc)
> * [app/models/ventas.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py)
> * [app/routes/employee.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py)
> * [app/static/css/generar_factura.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/generar_factura.css)
> * [app/templates/dashboard_empleado.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_empleado.html)
> * [app/templates/generar_factura.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/generar_factura.html)
> * [app/templates/trabajar_citas.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html)

## Purpose and Scope

This document explains the invoice generation system in Casa Bella, which enables employees to create invoices for completed services and walk-in sales. The system supports two invoice generation workflows: automatic invoice generation upon appointment completion, and manual invoice creation for customers without appointments. Both workflows generate PDF invoices using ReportLab and create corresponding database records for sales tracking.

For information about the employee appointment workflow that triggers automatic invoicing, see [Appointment Workflow](/GroveLive/CasaBella/7.1-appointment-workflow). For information about the sales data model, see [Data Models](/GroveLive/CasaBella/3.2-data-models).

**Sources:** [app/routes/employee.py L1-L451](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L1-L451)

---

## Invoice Generation Types

Casa Bella implements two distinct invoice generation workflows:

| Invoice Type | Trigger | Customer Association | Use Case |
| --- | --- | --- | --- |
| **Automatic Invoice** | Appointment completion | Registered user (`id_usuario` required) | Service appointments booked through the system |
| **Manual Invoice** | Employee-initiated | Walk-in customer (no `id_usuario`) | Products/services sold without prior appointment |

Both invoice types create identical database structures (`Venta`, `DetalleVenta`, `Pago`) and generate PDFs with the same visual format. The key difference is that automatic invoices are tied to a specific `Cita` and registered `Usuario`, while manual invoices accept customer details as form input and allow `id_usuario` to be NULL.

**Sources:** [app/routes/employee.py L146-L278](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L146-L278)

 [app/routes/employee.py L280-L447](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L280-L447)

---

## Automatic Invoice Generation Workflow

### Route and Access Control

The automatic invoice generation is triggered through the route `/employee/generar_factura/<int:id_cita>`. This endpoint is protected by `@login_required` and verifies that `current_user.rol == 'empleado'`.

```

```

**Sources:** [app/routes/employee.py L116-L144](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L116-L144)

 [app/routes/employee.py L146-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L146-L178)

### Validation Requirements

Before generating an invoice, the system validates multiple conditions:

```

```

**Sources:** [app/routes/employee.py L148-L175](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L148-L175)

### Database Transaction

The invoice generation creates three interconnected records atomically:

```

```

**Sources:** [app/routes/employee.py L177-L196](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L177-L196)

### Tax Calculation

The system applies a fixed 16% IVA (Value Added Tax) rate, defined as a module-level constant:

| Component | Calculation | Type |
| --- | --- | --- |
| `IVA_RATE` | `Decimal("0.16")` | Module constant |
| `subtotal` | `Decimal(str(servicio.precio))` | Service base price |
| `iva` | `subtotal * IVA_RATE` | Tax amount |
| `total` | `subtotal + iva` | Final invoice total |

**Sources:** [app/routes/employee.py L33-L34](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L33-L34)

 [app/routes/employee.py L177-L179](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L177-L179)

---

## Manual Invoice Generation Workflow

### Route and Form Handling

Manual invoice generation uses the route `/employee/generar_factura_manual` with both GET (display form) and POST (process invoice) methods. The form allows employees to enter customer details and select multiple products/services.

```

```

**Sources:** [app/routes/employee.py L280-L451](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L280-L451)

### Item Processing Logic

The manual invoice system handles both products and services with polymorphic logic:

```

```

**Sources:** [app/routes/employee.py L298-L333](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L298-L333)

### Customer Data Handling

Manual invoices accept customer information without requiring registration:

| Field | Source | Required | Database Mapping |
| --- | --- | --- | --- |
| `nombre_cliente` | Form input | Yes | Rendered in PDF only |
| `email_cliente` | Form input | No | Rendered in PDF only |
| `telefono_cliente` | Form input | No | Rendered in PDF only |
| `id_usuario` | N/A | No | Set to `None` in `Venta` |

The `Venta` model supports NULL `id_usuario` values to accommodate walk-in customers:

**Sources:** [app/routes/employee.py L288-L296](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L288-L296)

 [app/models/ventas.py L6](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L6-L6)

---

## PDF Generation Process

### ReportLab Canvas Initialization

Both invoice types use identical PDF generation logic with ReportLab's `canvas.Canvas`:

```

```

For manual invoices, the filename format is `factura_manual_{venta.id_venta}_{timestamp}.pdf`.

**Sources:** [app/routes/employee.py L198-L200](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L198-L200)

 [app/routes/employee.py L365-L367](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L365-L367)

### PDF Layout Structure

The invoice PDF follows a structured layout:

```

```

**Sources:** [app/routes/employee.py L201-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L201-L269)

 [app/routes/employee.py L368-L438](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L368-L438)

### Circular Logo Creation

The invoice includes a circular company logo processed using PIL (Pillow):

```

```

The logo is loaded from `app/static/images/casa-bella-logo.jpeg` and rendered at position (50, 720) with an 80x80 pixel size.

**Sources:** [app/routes/employee.py L203-L224](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L203-L224)

 [app/routes/employee.py L370-L391](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L370-L391)

---

## Database Transaction Flow

### Entity Relationships in Invoice Creation

The invoice generation creates a complete transactional record across multiple tables:

```

```

**Sources:** [app/routes/employee.py L181-L195](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L181-L195)

 [app/routes/employee.py L340-L363](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L340-L363)

 [app/models/ventas.py L3-L12](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L3-L12)

### Atomic Transaction Steps

For automatic invoices:

1. **Flush venta to get ID**: `db.session.flush()` ensures `venta.id_venta` is available
2. **Create detail records**: `DetalleVenta` uses the flushed `id_venta`
3. **Create payment record**: `Pago` also uses the flushed `id_venta`
4. **Commit all**: Single `db.session.commit()` ensures atomicity

For manual invoices, additional steps occur before commit:

1. **Update product stock**: `producto.stock -= cantidad`
2. **Create inventory movement**: `InventarioMovimiento` with `tipo_movimiento='salida'` and negative `cantidad`
3. **Commit all changes atomically**

**Sources:** [app/routes/employee.py L181-L196](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L181-L196)

 [app/routes/employee.py L314-L363](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L314-L363)

### Inventory Movement Tracking

Manual invoices create `InventarioMovimiento` records for products (but not services):

| Field | Value | Purpose |
| --- | --- | --- |
| `id_producto` | Product ID | Links movement to specific product |
| `tipo_movimiento` | `'salida'` | Indicates stock reduction |
| `cantidad` | `-cantidad` | Negative value for stock decrease |
| `motivo` | `f'Venta manual ID: {timestamp}'` | Audit trail for manual sales |

**Sources:** [app/routes/employee.py L315-L321](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L315-L321)

---

## File Management and Cleanup

### Temporary File Strategy

The system uses an ephemeral file strategy for invoice PDFs:

```

```

**Key points:**

* PDFs are created in `app/static/` directory
* Files are immediately deleted after `send_file()` returns
* No persistent file storage is maintained
* Invoice data persists only in database (`Venta`, `DetalleVenta`, `Pago`)

**Sources:** [app/routes/employee.py L269-L278](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L269-L278)

 [app/routes/employee.py L438-L447](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L438-L447)

### Error Handling

If the PDF file is not created successfully, a `FileNotFoundError` is raised:

```

```

This check occurs after `canvas.save()` but before `send_file()` to ensure the file was written to disk.

**Sources:** [app/routes/employee.py L271-L272](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L271-L272)

 [app/routes/employee.py L440-L441](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L440-L441)

---

## User Interface Components

### Appointment Completion Interface

Employees complete appointments through a FullCalendar-based interface in `trabajar_citas.html`:

```

```

The modal dynamically generates action buttons based on appointment state:

**Sources:** [app/templates/trabajar_citas.html L76-L101](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L76-L101)

### Manual Invoice Form

The manual invoice form (`generar_factura.html`) uses dynamic JavaScript to add/remove items:

| Form Component | Purpose | Implementation |
| --- | --- | --- |
| Customer fields | Capture walk-in customer data | Text inputs for nombre, email, telefono |
| Items container | Dynamic item rows | JavaScript clones `.item-row` template |
| Item selector | Choose product/service | `<select>` with all `Producto` and `Servicio` |
| Quantity input | Set item quantity | `<input type="number" min="1">` |
| Add item button | Clone item row | JavaScript `cloneNode(true)` |
| Remove item button | Delete item row | JavaScript `parentElement.remove()` |

The form uses array inputs (`items[]`, `cantidades[]`) to submit multiple items in a single POST request.

**Sources:** [app/templates/generar_factura.html L19-L51](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/generar_factura.html#L19-L51)

 [app/templates/generar_factura.html L56-L70](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/generar_factura.html#L56-L70)

---

## Error Handling and Validation

### Access Control Validation

All invoice routes perform role-based access control:

```

```

**Sources:** [app/routes/employee.py L149-L151](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L149-L151)

 [app/routes/employee.py L283-L285](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L283-L285)

### Business Logic Validation

The system validates multiple business rules:

| Validation | Location | Error Message |
| --- | --- | --- |
| Appointment completed | `generar_factura` | "La cita debe estar completada para generar una factura." |
| Employee ownership | `generar_factura` | "No tienes permiso para generar esta factura." |
| Customer exists | `generar_factura` | "No se encontró un cliente asociado a esta cita." |
| Service has price | `generar_factura` | "El servicio asociado a la cita no tiene precio definido." |
| Product stock available | `generar_factura_manual` | "Stock insuficiente para el producto {nombre}." |
| Valid quantity | `generar_factura_manual` | "La cantidad para el item {id} debe ser mayor a 0." |
| Customer name provided | `generar_factura_manual` | "Debe ingresar el nombre del cliente." |

**Sources:** [app/routes/employee.py L154-L175](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L154-L175)

 [app/routes/employee.py L294-L326](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L294-L326)

### Database Error Handling

Manual invoice creation includes explicit error handling for database operations:

```

```

This ensures that database failures are logged and the transaction is rolled back.

**Sources:** [app/routes/employee.py L340-L349](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L340-L349)

---

## Logging and Debugging

The employee blueprint configures Python's logging module to track invoice generation:

```

```

Key log points:

1. **Invoice generation start**: `logger.debug(f"Generando factura para cita {id_cita}. Cliente: {cliente.nombre}, ID Usuario: {cita.id_usuario}")`
2. **Venta creation**: `logger.debug(f"Creando venta: id_usuario=None, total={total}")`
3. **Venta ID assignment**: `logger.debug(f"Venta creada con ID: {venta.id_venta}")`
4. **Database errors**: `logger.error(f"Error al crear venta: {str(e)} - Datos: id_usuario={None}, total={total}")`

**Sources:** [app/routes/employee.py L29-L31](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L29-L31)

 [app/routes/employee.py L170](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L170-L170)

 [app/routes/employee.py L338-L346](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L338-L346)