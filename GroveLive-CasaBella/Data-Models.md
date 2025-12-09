# Data Models

> **Relevant source files**
> * [Dockerfile](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile)
> * [app/models/__pycache__/ventas.cpython-313.pyc](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/__pycache__/ventas.cpython-313.pyc)
> * [app/models/carrito.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py)
> * [app/models/ventas.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py)
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/static/css/generar_factura.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/generar_factura.css)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)
> * [app/templates/dashboard_empleado.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_empleado.html)
> * [app/templates/generar_factura.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/generar_factura.html)

## Purpose and Scope

This page documents the SQLAlchemy data models that form the persistence layer of Casa Bella. It covers the database schema, entity relationships, key design patterns including polymorphic references and state machines, and how models interact across the application. For information about how these models are used in specific business workflows, see [Client Features](/GroveLive/CasaBella/5-client-features), [Admin Features](/GroveLive/CasaBella/6-admin-features), and [Employee Features](/GroveLive/CasaBella/7-employee-features).

## Model Organization

Casa Bella uses SQLAlchemy ORM with a PostgreSQL database backend. All models are defined in the `app/models/` directory and inherit from `db.Model`. The models are imported throughout the application blueprints to handle CRUD operations and business logic.

### Model Files Structure

| Model File | Primary Entity | Purpose |
| --- | --- | --- |
| `users.py` | `Usuario` | User accounts with role-based access |
| `productos.py` | `Producto` | Physical inventory items |
| `servicios.py` | `Servicio` | Bookable beauty services |
| `categorias.py` | `Categoria` | Product categorization |
| `carrito.py` | `Carrito` | Shopping cart state container |
| `detalle_carrito.py` | `DetalleCarrito` | Cart line items |
| `ventas.py` | `Venta` | Sales transactions |
| `detalle_ventas.py` | `DetalleVenta` | Sales line items |
| `pagos.py` | `Pago` | Payment records |
| `citas.py` | `Cita` | Appointment bookings |
| `asignaciones.py` | `Asignacion` | Employee-appointment assignments |
| `inventario_movimientos.py` | `InventarioMovimiento` | Stock movement tracking |
| `promociones.py` | `Promocion` | Time-bound discounts |
| `notificaciones.py` | `Notificacion` | User notifications |
| `guardados.py` | `Guardado` | User favorites |
| `reseñas.py` | `Reseña` | Product/service reviews |

**Sources:** [app/routes/client.py L1-L16](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L16)

 [app/routes/admin.py L1-L11](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L1-L11)

## Entity-Relationship Overview

```

```

**Sources:** High-level Diagram 4, [app/routes/client.py L1-L16](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L16)

## Core Entity Models

### Usuario Model

The `Usuario` model implements role-based access control with three distinct roles: `cliente`, `admin`, and `empleado`.

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_usuario` | Integer | PK | Unique user identifier |
| `nombre` | String | NOT NULL | User full name |
| `email` | String | UNIQUE, NOT NULL | Authentication email |
| `contraseña` | String | NOT NULL | Hashed password (using werkzeug) |
| `rol` | Enum | NOT NULL | Role: 'cliente', 'admin', 'empleado' |
| `telefono` | String | NULLABLE | Contact phone number |
| `especialidad` | String | NULLABLE | Employee specialty (required for empleado role) |

**Role Assignment Logic:** The first registered user receives the `admin` role; subsequent registrations default to `cliente`. Admins can manually assign roles through the user management interface.

**Sources:** [app/routes/admin.py L165-L201](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L165-L201)

 [app/routes/client.py L12](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L12-L12)

### Producto Model

The `Producto` model represents physical inventory items with stock tracking.

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_producto` | Integer | PK | Unique product identifier |
| `id_categoria` | Integer | FK → Categoria | Product category |
| `nombre` | String | NOT NULL | Product name |
| `descripcion` | Text | NULLABLE | Product description |
| `tipo` | String | NOT NULL | Product type classification |
| `precio` | Numeric(10,2) | NOT NULL | Base price |
| `stock` | Integer | NOT NULL | Current stock level |
| `stock_minimo` | Integer | DEFAULT 5 | Low stock threshold |
| `estado` | String | DEFAULT 'activo' | Active/inactive status |
| `imagen_url` | String | NULLABLE | Product image URL |

**Stock Validation:** Before adding to cart, the system checks `producto.stock > 0` at [app/routes/client.py L135-L137](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L135-L137)

 During checkout, stock is decremented atomically at [app/routes/client.py L294-L295](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L294-L295)

**Sources:** [app/routes/admin.py L268-L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L268-L316)

 [app/routes/client.py L125-L143](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L125-L143)

### Servicio Model

The `Servicio` model represents bookable beauty services.

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_servicio` | Integer | PK | Unique service identifier |
| `nombre` | String | NOT NULL, UNIQUE | Service name |
| `descripcion` | Text | NULLABLE | Service description |
| `precio` | Numeric(10,2) | NOT NULL | Service price |
| `duracion` | Integer | NOT NULL | Duration in minutes |
| `estado` | String | DEFAULT 'activo' | Active/inactive status |
| `imagen_url` | String | NULLABLE | Service image URL |

**Services have no stock constraints** as they are bookable resources, not physical inventory.

**Sources:** [app/routes/admin.py L393-L434](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L393-L434)

 [app/routes/client.py L52-L72](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L72)

### Venta Model

The `Venta` model represents completed sales transactions with invoice generation.

```

```

**Nullable User Foreign Key:** The `id_usuario` field is nullable to support manual invoicing for walk-in customers without registered accounts.

**IVA Calculation:** The total includes 16% IVA tax, calculated at [app/routes/client.py L298-L300](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L298-L300)

 as `subtotal * IVA_RATE`.

**Sources:** [app/models/ventas.py L1-L12](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L1-L12)

 [app/routes/client.py L271-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L271-L455)

### Carrito Model (State Machine)

The `Carrito` model implements a state machine pattern for shopping cart lifecycle management.

```

```

#### Carrito State Machine

```

```

**State Transitions:**

* **activo → completado**: Triggered at [app/routes/client.py L338](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L338-L338)  after successful checkout
* **activo → abandonado**: Currently unused but reserved for future session management

**Active Cart Query Pattern:** Throughout the application, active carts are retrieved using:

```

```

See [app/routes/client.py L48](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L48-L48)

 [app/routes/client.py L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L114-L114)

 [app/routes/client.py L189](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L189-L189)

**Sources:** [app/models/carrito.py L1-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L1-L13)

 [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)

### Cita Model (State Machine)

The `Cita` model represents appointment bookings with a multi-state workflow spanning multiple roles.

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_cita` | Integer | PK | Unique appointment identifier |
| `id_usuario` | Integer | FK → Usuario | Client who booked |
| `id_empleado` | Integer | FK → Usuario, NULLABLE | Assigned employee |
| `id_servicio` | Integer | FK → Servicio | Service to be performed |
| `fecha_hora` | DateTime | NOT NULL | Appointment date/time |
| `estado` | Enum | NOT NULL | 'pendiente', 'confirmada', 'completada' |
| `notas` | Text | NULLABLE | Additional notes |

#### Cita State Machine

```

```

**State Transitions:**

1. **pendiente**: Created by client at [app/routes/client.py L780-L815](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L780-L815)
2. **confirmada**: Admin assigns employee at [app/routes/admin.py L543-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L543-L573)  sets `id_empleado` and creates `Asignacion` record
3. **completada**: Employee marks complete at employee route, triggers automatic invoice generation

**Business Hours Validation:** Appointments are validated against hardcoded business hours (9am-7pm weekdays, closed Thursdays) at [app/routes/client.py L740-L762](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L740-L762)

**Sources:** [app/routes/client.py L96-L105](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L96-L105)

 [app/routes/admin.py L495-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L573)

## Polymorphic Design Pattern

Casa Bella implements a polymorphic reference pattern where multiple entities can reference either `Producto` or `Servicio` through nullable foreign keys. This unifies shopping cart, sales, favorites, and reviews across both product and service domains.

### Polymorphic Entities

```

```

### DetalleCarrito Polymorphic Implementation

The `DetalleCarrito` model demonstrates the polymorphic pattern:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_detalle_carrito` | Integer | PK | Line item identifier |
| `id_carrito` | Integer | FK → Carrito | Parent cart |
| `id_producto` | Integer | FK → Producto, NULLABLE | Product reference (XOR) |
| `id_servicio` | Integer | FK → Servicio, NULLABLE | Service reference (XOR) |
| `cantidad` | Integer | NOT NULL | Quantity |
| `precio_unitario` | Numeric(10,2) | NOT NULL | Unit price with promotion applied |

**Item Type Detection Logic:** At [app/routes/client.py L124-L154](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L124-L154)

 the system determines item type:

```

```

**Template Rendering:** Templates use conditional checks to handle polymorphic references at [app/routes/client.py L189-L199](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L189-L199)

:

```

```

**Sources:** [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)

 [app/routes/client.py L271-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L271-L455)

### DetalleVenta Polymorphic Implementation

The `DetalleVenta` model mirrors the cart pattern for completed sales:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_detalle_venta` | Integer | PK | Line item identifier |
| `id_venta` | Integer | FK → Venta | Parent sale |
| `id_producto` | Integer | FK → Producto, NULLABLE | Product reference (XOR) |
| `id_servicio` | Integer | FK → Servicio, NULLABLE | Service reference (XOR) |
| `cantidad` | Integer | NOT NULL | Quantity sold |
| `precio_unitario` | Numeric(10,2) | NOT NULL | Historical unit price |

**Historical Pricing:** The `precio_unitario` field captures the price at time of sale, preserving pricing even after promotions expire. This is critical for invoice regeneration at [app/routes/client.py L541-L669](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L541-L669)

**Sources:** [app/routes/client.py L306-L317](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L306-L317)

 [app/routes/client.py L553-L556](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L553-L556)

## Inventory and Stock Management

### InventarioMovimiento Model

The `InventarioMovimiento` model provides audit trail for stock changes:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_movimiento` | Integer | PK | Movement identifier |
| `id_producto` | Integer | FK → Producto | Product affected |
| `tipo_movimiento` | Enum | NOT NULL | 'entrada' or 'salida' |
| `cantidad` | Integer | NOT NULL | Quantity (signed) |
| `fecha_movimiento` | DateTime | DEFAULT NOW | Timestamp |
| `motivo` | String | NULLABLE | Reason for movement |

**Automatic Movement Creation:** During checkout at [app/routes/client.py L327-L335](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L327-L335)

 the system creates a `salida` movement:

```

```

**Deletion Tracking:** When a sale is deleted at [app/routes/client.py L686](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L686-L686)

 movements are cleaned up using the `motivo` field:

```

```

**Dashboard Metrics:** Admin dashboard calculates revenue from movements at [app/routes/admin.py L60-L77](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L60-L77)

:

```

```

**Sources:** [app/routes/client.py L327-L335](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L327-L335)

 [app/routes/admin.py L55-L77](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L55-L77)

### Low Stock Notifications

The admin dashboard monitors `Producto.stock <= Producto.stock_minimo` at [app/routes/admin.py L54](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L54-L54)

 and creates notifications:

```

```

**Sources:** [app/routes/admin.py L54](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L54-L54)

 [app/routes/admin.py L106-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L106-L114)

## Time-Bound Promotions

### Promocion Model

The `Promocion` model creates discounts that automatically activate and deactivate based on date ranges:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_promocion` | Integer | PK | Promotion identifier |
| `id_producto` | Integer | FK → Producto, NULLABLE | Promoted product (XOR) |
| `id_servicio` | Integer | FK → Servicio, NULLABLE | Promoted service (XOR) |
| `nombre` | String | NOT NULL | Promotion name |
| `descuento` | Numeric(5,2) | NOT NULL | Discount percentage (0-100) |
| `fecha_inicio` | DateTime | NOT NULL | Activation timestamp |
| `fecha_fin` | DateTime | NOT NULL | Expiration timestamp |

**Active Promotion Query Pattern:**

```

```

This pattern appears throughout the codebase at [app/routes/client.py L138-L140](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L138-L140)

 [app/routes/client.py L147-L149](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L147-L149)

**Price Calculation with Promotion:**

```

```

At [app/routes/client.py L142-L143](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L142-L143)

 The discounted price is stored in `DetalleCarrito.precio_unitario`, preserving the historical discount even after the promotion expires.

**Filtering Products/Services by Active Promotions:** At [app/routes/client.py L59-L68](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L59-L68)

:

```

```

**Sources:** [app/routes/client.py L59-L72](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L59-L72)

 [app/routes/client.py L138-L152](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L138-L152)

## Supporting Models

### Pago Model

Records payment method and amount for each sale:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_pago` | Integer | PK | Payment identifier |
| `id_venta` | Integer | FK → Venta | Associated sale |
| `metodo_pago` | String | NOT NULL | Payment method (e.g., 'efectivo', 'tarjeta') |
| `monto` | Numeric(10,2) | NOT NULL | Payment amount |
| `fecha_pago` | DateTime | DEFAULT NOW | Payment timestamp |

Created during checkout at [app/routes/client.py L320-L324](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L320-L324)

**Sources:** [app/routes/client.py L320-L324](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L320-L324)

### Asignacion Model

Records the admin's assignment of an employee to an appointment:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_asignacion` | Integer | PK | Assignment identifier |
| `id_cita` | Integer | FK → Cita | Appointment |
| `id_empleado` | Integer | FK → Usuario | Assigned employee |
| `fecha_asignacion` | DateTime | DEFAULT NOW | Assignment timestamp |
| `notas` | Text | NULLABLE | Admin notes |

Created at [app/routes/admin.py L558-L560](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L558-L560)

 during employee assignment.

**Sources:** [app/routes/admin.py L558-L560](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L558-L560)

### Notificacion Model

System-generated notifications for users:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_notificacion` | Integer | PK | Notification identifier |
| `id_usuario` | Integer | FK → Usuario | Recipient |
| `mensaje` | Text | NOT NULL | Notification message |
| `tipo` | String | NOT NULL | Type: 'inventario', 'cita', etc. |
| `fecha` | DateTime | DEFAULT NOW | Creation timestamp |
| `leida` | Boolean | DEFAULT FALSE | Read status |

**Notification Types:**

* **inventario**: Low stock alerts at [app/routes/admin.py L108-L113](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L108-L113)
* **cita**: Appointment assignment at [app/routes/admin.py L565-L571](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L565-L571)

**Sources:** [app/routes/admin.py L108-L124](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L108-L124)

 [app/routes/admin.py L565-L571](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L565-L571)

### Guardado Model

User favorites for products and services (polymorphic):

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_guardado` | Integer | PK | Favorite identifier |
| `id_usuario` | Integer | FK → Usuario | User who saved |
| `id_producto` | Integer | FK → Producto, NULLABLE | Saved product (XOR) |
| `id_servicio` | Integer | FK → Servicio, NULLABLE | Saved service (XOR) |
| `fecha_guardado` | DateTime | DEFAULT NOW | Save timestamp |

Managed via AJAX endpoints in the client blueprint.

**Sources:** [app/routes/client.py L14](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L14-L14)

### Reseña Model

User reviews for products and services (polymorphic):

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_resena` | Integer | PK | Review identifier |
| `id_usuario` | Integer | FK → Usuario | Reviewer |
| `id_producto` | Integer | FK → Producto, NULLABLE | Reviewed product (XOR) |
| `id_servicio` | Integer | FK → Servicio, NULLABLE | Reviewed service (XOR) |
| `calificacion` | Integer | 1-5 | Star rating |
| `comentario` | Text | NULLABLE | Review text |
| `fecha` | DateTime | DEFAULT NOW | Review timestamp |

**Sources:** [app/routes/client.py L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L13-L13)

### Categoria Model

Product categorization:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id_categoria` | Integer | PK | Category identifier |
| `nombre` | String | NOT NULL, UNIQUE | Category name |
| `descripcion` | Text | NULLABLE | Category description |

Used in product management at [app/routes/admin.py L265-L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L265-L266)

**Sources:** [app/routes/admin.py L265-L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L265-L266)

## Model Relationships and Lazy Loading

Casa Bella uses SQLAlchemy relationships with explicit lazy loading strategies. Common patterns:

### Eager Loading with joinedload

For performance optimization when loading related entities:

```

```

At [app/routes/client.py L189-L192](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L189-L192)

 [app/routes/client.py L277-L280](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L277-L280)

### Backref Relationships

Models define bidirectional relationships using `backref`:

```

```

From [app/models/ventas.py L11](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L11-L11)

 [app/models/carrito.py L12](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L12-L12)

**Sources:** [app/models/ventas.py L11](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L11-L11)

 [app/models/carrito.py L12](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L12-L12)

 [app/routes/client.py L189-L192](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L189-L192)

## Key Design Decisions

### 1. Nullable Foreign Keys for Flexibility

Several models use nullable foreign keys to support edge cases:

* `Venta.id_usuario` is nullable at [app/models/ventas.py L6](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L6-L6)  to support walk-in customer invoices
* Polymorphic foreign keys (`id_producto`, `id_servicio`) are structurally nullable with business logic ensuring exactly one is populated

### 2. Enum-Based State Management

PostgreSQL enums are used for state fields to ensure data integrity:

```

```

At [app/models/carrito.py L9](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L9-L9)

### 3. Historical Price Preservation

`DetalleCarrito.precio_unitario` and `DetalleVenta.precio_unitario` store calculated prices (with promotions applied) rather than references to `Producto.precio` or `Servicio.precio`. This ensures invoice regeneration shows correct historical prices even after base prices or promotions change.

### 4. Audit Trail via Motivo Field

The `InventarioMovimiento.motivo` field at [app/routes/client.py L333](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L333-L333)

 enables deletion cascade by query:

```

```

This avoids complex foreign key cascade configurations.

### 5. Polymorphic Pattern Over Table Inheritance

Rather than using SQLAlchemy's joined table inheritance, Casa Bella implements polymorphism through nullable foreign keys. This simplifies queries and avoids complex join chains while maintaining flexibility to add items to carts, sales, favorites, and reviews.

**Sources:** [app/models/ventas.py L6](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/ventas.py#L6-L6)

 [app/models/carrito.py L9](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L9-L9)

 [app/routes/client.py L333](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L333-L333)

 [app/routes/client.py L686](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L686-L686)