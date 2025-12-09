# Product & Service Management

> **Relevant source files**
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)
> * [app/templates/gestion_servicios.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/gestion_servicios.html)

## Purpose and Scope

This document details the administrative functionality for managing products and services in Casa Bella. It covers CRUD operations, inventory tracking, category management, stock monitoring, and validation rules for both products (physical inventory items) and services (bookable appointments).

For client-facing catalog browsing and purchasing, see [Product & Service Catalog](/GroveLive/CasaBella/5.1-product-and-service-catalog). For promotion creation and discount management, see [Promotion Management](/GroveLive/CasaBella/6.5-promotion-management). For dashboard analytics and inventory alerts, see [Admin Dashboard & Analytics](/GroveLive/CasaBella/6.1-admin-dashboard-and-analytics).

**Sources:** [app/routes/admin.py L258-L493](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L493)

---

## System Overview

The product and service management system provides administrators with centralized control over the catalog. Products represent physical inventory with stock tracking, while services represent bookable appointments with duration specifications. Both entities support state management (activo/inactivo), promotional pricing, and image URLs for catalog display.

### Product & Service Management Architecture

```

```

**Sources:** [app/routes/admin.py L258-L493](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L493)

 [app/templates/gestion_servicios.html L1-L45](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/gestion_servicios.html#L1-L45)

---

## Product Management

### Product Data Model

Products represent physical inventory items with the following attributes:

| Attribute | Type | Required | Validation | Default |
| --- | --- | --- | --- | --- |
| `id_categoria` | Integer | Yes | Must reference existing Categoria | - |
| `nombre` | String | Yes | - | - |
| `descripcion` | Text | No | - | - |
| `tipo` | String | Yes | - | - |
| `precio` | Decimal | Yes | Must be >= 0 | - |
| `stock` | Integer | Yes | Must be >= 0 | - |
| `stock_minimo` | Integer | No | Must be >= 0 | 5 |
| `estado` | String | No | - | 'activo' |
| `imagen_url` | String | No | - | - |

**Sources:** [app/routes/admin.py L268-L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L268-L316)

 [app/routes/admin.py L318-L364](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L318-L364)

### Product CRUD Operations Flow

```

```

**Sources:** [app/routes/admin.py L258-L382](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L382)

### Listing Products

The `gestion_productos()` route displays all products with their categories.

**Route:** `/admin/gestion_productos`
**Method:** GET
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L258-L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L266)

```

```

**Returns:** Renders `gestion_productos.html` with product list and available categories.

**Sources:** [app/routes/admin.py L258-L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L266)

### Adding Products

The `agregar_producto()` route creates new products with validation.

**Route:** `/admin/agregar_producto`
**Methods:** GET, POST
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L268-L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L268-L316)

**Validation Rules:**

1. **Required Fields:** `id_categoria`, `nombre`, `tipo`, `precio`, `stock` must be present
2. **Type Conversion:** * `precio` → `float` (raises ValueError if invalid) * `stock` → `int` (raises ValueError if invalid) * `stock_minimo` → `int` with default of 5 if not provided
3. **Business Rules:** * `precio >= 0` * `stock >= 0` * `stock_minimo >= 0`
4. **Default Values:** * `estado` defaults to `'activo'` if not provided
5. **Uniqueness:** IntegrityError handling for duplicate products

**Error Handling:**

| Error Type | Flash Message | HTTP Response |
| --- | --- | --- |
| Missing required fields | "Los campos obligatorios son requeridos." | 200 (re-render form) |
| Negative values | "El precio, stock y stock mínimo no pueden ser negativos." | 200 (re-render form) |
| Type conversion failure | "El precio debe ser un número decimal y el stock/stock mínimo un número entero." | 200 (re-render form) |
| IntegrityError | "El producto ya existe o hay un problema de datos." | 200 (re-render form) |

**Sources:** [app/routes/admin.py L268-L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L268-L316)

### Editing Products

The `editar_producto()` route updates existing products.

**Route:** `/admin/editar_producto/<int:id_producto>`
**Methods:** GET, POST
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L318-L364](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L318-L364)

**Behavior:**

* GET request: Renders edit form with current product data
* POST request: Validates and updates product attributes
* Uses `Producto.query.get_or_404(id_producto)` for safe retrieval
* Applies same validation rules as product creation
* Preserves existing `stock_minimo` if not provided in update

**Database Operations:**

```

```

**Sources:** [app/routes/admin.py L318-L364](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L318-L364)

### Deleting Products

The `eliminar_producto()` route removes products with referential integrity checks.

**Route:** `/admin/eliminar_producto/<int:id_producto>`
**Methods:** GET, POST
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L366-L382](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L366-L382)

**Behavior:**

* GET request: Displays confirmation page
* POST request: Attempts deletion
* Catches `IntegrityError` if product is referenced by other entities (e.g., in cart, sales, promotions)
* Rolls back transaction on error

**Referential Integrity:**
Products cannot be deleted if they are referenced in:

* `DetalleCarrito` (shopping cart items)
* `DetalleVenta` (purchase history)
* `Promocion` (active promotions)
* `Guardado` (client favorites)
* `Reseña` (product reviews)
* `InventarioMovimiento` (inventory history)

**Sources:** [app/routes/admin.py L366-L382](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L366-L382)

---

## Service Management

### Service Data Model

Services represent bookable appointments with duration specifications:

| Attribute | Type | Required | Validation | Default |
| --- | --- | --- | --- | --- |
| `nombre` | String | Yes | Unique constraint | - |
| `descripcion` | Text | No | - | - |
| `precio` | Decimal | Yes | Must be >= 0 | - |
| `duracion` | Integer | Yes | Must be > 0 (minutes) | - |
| `estado` | String | No | - | 'activo' |
| `imagen_url` | String | No | - | - |

**Sources:** [app/routes/admin.py L393-L434](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L393-L434)

 [app/routes/admin.py L436-L475](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L436-L475)

### Service CRUD Operations

```

```

**Sources:** [app/routes/admin.py L384-L493](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L384-L493)

### Listing Services

The `gestion_servicios()` route displays all available services.

**Route:** `/admin/gestion_servicios`
**Method:** GET
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L384-L391](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L384-L391)

**Template Display:** [app/templates/gestion_servicios.html L1-L45](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/gestion_servicios.html#L1-L45)

The template renders a table with columns:

* ID (id_servicio)
* Nombre
* Descripción
* Precio (formatted as currency)
* Acciones (Edit/Delete buttons)

**Sources:** [app/routes/admin.py L384-L391](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L384-L391)

 [app/templates/gestion_servicios.html L19-L43](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/gestion_servicios.html#L19-L43)

### Adding Services

The `agregar_servicio()` route creates new bookable services.

**Route:** `/admin/agregar_servicio`
**Methods:** GET, POST
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L393-L434](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L393-L434)

**Validation Rules:**

1. **Required Fields:** `nombre`, `precio`, `duracion` must be present
2. **Type Conversion:** * `precio` → `float` * `duracion` → `int` (minutes)
3. **Business Rules:** * `precio >= 0` * `duracion > 0` (must be positive)
4. **Default Values:** * `estado` defaults to `'activo'` if not provided
5. **Uniqueness:** Service `nombre` has a unique constraint

**Error Messages:**

| Condition | Flash Message |
| --- | --- |
| Missing required fields | "Los campos obligatorios (nombre, precio, duración) son requeridos." |
| Negative price or zero duration | "El precio no puede ser negativo y la duración debe ser mayor a 0." |
| Type conversion failure | "El precio debe ser un número decimal y la duración un número entero." |
| Duplicate service name | "Ya existe un servicio con ese nombre." |

**Sources:** [app/routes/admin.py L393-L434](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L393-L434)

### Editing Services

The `editar_servicio()` route updates existing service definitions.

**Route:** `/admin/editar_servicio/<int:id_servicio>`
**Methods:** GET, POST
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L436-L475](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L436-L475)

**Behavior:**

* Retrieves service using `Servicio.query.get_or_404(id_servicio)`
* Applies same validation as service creation
* Preserves existing `estado` if not provided in update
* Updates all attributes atomically via `db.session.commit()`

**Sources:** [app/routes/admin.py L436-L475](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L436-L475)

### Deleting Services

The `eliminar_servicio()` route removes services with referential integrity checks.

**Route:** `/admin/eliminar_servicio/<int:id_servicio>`
**Methods:** GET, POST
**Access Control:** Requires `current_user.rol == 'admin'`
**Implementation:** [app/routes/admin.py L477-L493](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L477-L493)

**Referential Integrity:**
Services cannot be deleted if they are referenced in:

* `Cita` (appointment bookings)
* `DetalleCarrito` (shopping cart items)
* `DetalleVenta` (purchase history)
* `Promocion` (active promotions)
* `Guardado` (client favorites)
* `Reseña` (service reviews)

**Error Handling:**
If deletion fails due to foreign key constraint, returns:

```

```

**Sources:** [app/routes/admin.py L477-L493](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L477-L493)

---

## Inventory Tracking

### Stock Management

Product stock is tracked through the `stock` and `stock_minimo` attributes. The system monitors inventory levels and generates notifications when products fall below minimum thresholds.

**Stock Metrics in Dashboard:**

| Metric | Calculation | Implementation |
| --- | --- | --- |
| Stock Total | Sum of all product stock levels | `Producto.query.with_entities(func.sum(Producto.stock)).scalar()` |
| Stock Bajo | Count of products where stock ≤ stock_minimo | `Producto.query.filter(Producto.stock <= Producto.stock_minimo).count()` |
| Movimientos Recientes | Inventory movements in last 7 days | `InventarioMovimiento.query.filter(...).count()` |

**Sources:** [app/routes/admin.py L53-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L53-L57)

 [app/routes/admin.py L670-L671](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L670-L671)

### Low Stock Notifications

The admin dashboard automatically creates notifications when stock falls below minimum levels:

**Implementation:** [app/routes/admin.py L106-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L106-L114)

```

```

**Notification Triggers:**

* Generated on every admin dashboard load if `stock_bajo > 0`
* Type: `'inventario'`
* Recipient: Current admin user

**Sources:** [app/routes/admin.py L106-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L106-L114)

### Inventory Movement Tracking

Recent inventory activity is monitored through the `InventarioMovimiento` model:

**Implementation:** [app/routes/admin.py L55-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L55-L57)

```

```

**Movement Notification:** [app/routes/admin.py L116-L124](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L116-L124)

If inventory movements occurred in the last 7 days, a notification is created:

```

```

**Sources:** [app/routes/admin.py L55-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L55-L57)

 [app/routes/admin.py L116-L124](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L116-L124)

---

## Integration with Revenue Tracking

Product and service management integrates with the admin dashboard's revenue tracking system. Revenue is calculated based on inventory movements (for products) and completed appointments (for services).

### Revenue Calculation

```

```

**Product Revenue:** [app/routes/admin.py L60-L77](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L60-L77)

**Service Revenue:** [app/routes/admin.py L79-L96](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L79-L96)

**Date Range Filtering:**

* **hoy:** `func.date(fecha) == today`
* **semana:** `func.date(fecha).between(start_week, today)`
* **mes:** `func.date(fecha).between(start_month, today)`
* **ano:** `func.date(fecha).between(start_year, today)`

**Sources:** [app/routes/admin.py L60-L96](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L60-L96)

 [app/routes/admin.py L589-L650](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L589-L650)

---

## Category Management

Products are organized using the `Categoria` model. Categories are referenced via `id_categoria` foreign key.

**Category Queries:**

* Loaded for product creation: [app/routes/admin.py L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L316-L316)
* Loaded for product editing: [app/routes/admin.py L337](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L337-L337)  [app/routes/admin.py L344](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L344-L344)  [app/routes/admin.py L364](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L364-L364)
* Loaded for product listing: [app/routes/admin.py L265](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L265-L265)

**Usage Pattern:**

```

```

**Note:** Direct category CRUD operations are not exposed in the provided admin routes. Categories are managed through seeding or direct database manipulation.

**Sources:** [app/routes/admin.py L265](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L265-L265)

 [app/routes/admin.py L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L316-L316)

 [app/routes/admin.py L337](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L337-L337)

---

## State Management

Both products and services support state management through the `estado` attribute.

**Valid States:**

* `'activo'` (default): Item is available for purchase/booking
* `'inactivo'`: Item is hidden from client catalog

**State Behavior:**

* State defaults to `'activo'` on creation if not specified
* State can be changed via edit forms
* Client-facing catalog queries filter by `estado='activo'` (see [Product & Service Catalog](/GroveLive/CasaBella/5.1-product-and-service-catalog))

**Implementation:**

* Product state: [app/routes/admin.py L302](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L302-L302)  [app/routes/admin.py L352](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L352-L352)
* Service state: [app/routes/admin.py L420](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L420-L420)  [app/routes/admin.py L463](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L463-L463)

**Sources:** [app/routes/admin.py L302](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L302-L302)

 [app/routes/admin.py L352](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L352-L352)

 [app/routes/admin.py L420](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L420-L420)

 [app/routes/admin.py L463](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L463-L463)

---

## Dashboard Integration

Product and service metrics are prominently displayed in the admin dashboard.

### Dashboard Metrics

**Implementation:** [app/templates/dashboard_admin.html L24-L88](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L24-L88)

| Metric Card | Value | Icon | Color |
| --- | --- | --- | --- |
| Usuarios | `usuarios_count` | fa-users | bg-gradient-primary |
| Productos | `productos_count` | fa-box-open | bg-gradient-success |
| Servicios | `servicios_count` | fa-concierge-bell | bg-gradient-info |
| Citas Pendientes | `citas_pendientes_count` | fa-calendar-check | bg-gradient-warning |
| Stock Total | `stock_total` | fa-warehouse | bg-gradient-secondary |
| Stock Bajo | `stock_bajo` | fa-exclamation-triangle | bg-gradient-danger |
| Movimientos Recientes | `movimientos_recientes` | fa-truck-moving | bg-gradient-dark |

**Navigation Links:**

* "Gestión de Usuarios" → `/admin/gestion_usuarios`
* "Gestión de Productos" → `/admin/gestion_productos`

**Revenue Chart:**
The dashboard includes a Chart.js visualization showing product revenue across time periods (hoy, semana, mes, año). The chart updates dynamically via AJAX to filter between product revenue, service revenue, or total revenue.

**Sources:** [app/templates/dashboard_admin.html L24-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L24-L119)

 [app/templates/dashboard_admin.html L122-L191](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L122-L191)

---

## Access Control

All product and service management routes enforce admin-only access:

```

```

**Authorization Pattern:**

1. `@login_required` decorator ensures authenticated user
2. Manual check for `current_user.rol == 'admin'`
3. Redirect to login with flash message if unauthorized

**Sources:** [app/routes/admin.py L28-L33](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L28-L33)

 [app/routes/admin.py L258-L262](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L262)

 [app/routes/admin.py L268-L272](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L268-L272)