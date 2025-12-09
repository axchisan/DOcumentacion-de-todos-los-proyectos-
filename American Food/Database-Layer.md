# Database Layer

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document provides comprehensive documentation of the MySQL database schema for the American Food restaurant management system. The database `american_food` contains 9 tables that manage all aspects of restaurant operations including user authentication, menu items, orders, payments, invoicing, table management, inventory, and delivery information.

This page covers the database structure, table definitions, relationships, and constraints. For information about how the application connects to this database, see [Configuration and Bootstrap](/axchisan/AmericanFood/3-configuration-and-bootstrap). For documentation of the business workflows that utilize these tables, see [Business Workflows](/axchisan/AmericanFood/8-business-workflows).

**Sources**: [database/american_food L1-L488](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L488)

---

## Database Schema Overview

The `american_food` database implements a relational schema with the `pedidos` table serving as the central hub connecting to eight other tables. The schema supports both online and in-person dining experiences, role-based user management, and comprehensive financial tracking.

### Entity Relationship Diagram

```

```

**Sources**: [database/american_food L30-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L483)

### Table Summary

| Table Name | Primary Purpose | Related Tables |
| --- | --- | --- |
| `usuarios` | User authentication and role management | `pedidos` (via `cliente_id`) |
| `pedidos` | Central order tracking for both online and in-person orders | `usuarios`, `mesas`, `detalles_pedidos`, `pagos`, `facturas`, `informacion_entrega` |
| `detalles_pedidos` | Individual line items within orders | `pedidos`, `menu` |
| `menu` | Restaurant menu items with pricing and availability | `detalles_pedidos` |
| `mesas` | Physical table management for dine-in service | `pedidos` |
| `pagos` | Payment processing and transaction records | `pedidos` |
| `facturas` | Invoice generation for orders requiring receipts | `pedidos` |
| `informacion_entrega` | Delivery address information for online orders | `pedidos` |
| `inventario` | Ingredient and supply inventory tracking | None (standalone) |
| `configuracion` | System configuration key-value storage | None (standalone) |

**Sources**: [database/american_food L27-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L27-L483)

---

## Core Tables

### usuarios Table

The `usuarios` table manages authentication and role-based access control for all system users. It stores credentials and assigns one of five roles that determine interface access and permissions.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique user identifier |
| `nombre` | `VARCHAR(100)` | NOT NULL | User's first name |
| `apellido` | `VARCHAR(100)` | NOT NULL | User's last name |
| `correo` | `VARCHAR(100)` | NOT NULL, UNIQUE | Email address, used for uniqueness validation |
| `telefono` | `VARCHAR(20)` | NULL | Contact phone number |
| `username` | `VARCHAR(100)` | NOT NULL | Login username |
| `rol` | `ENUM('admin','cajera','cocinero','cliente','mesera')` | NOT NULL | User role determining access level |
| `contraseña` | `VARCHAR(255)` | NOT NULL | Bcrypt-hashed password (60 characters) |
| `estado` | `ENUM('activo','inactivo')` | DEFAULT 'activo' | Account status flag |

#### Indexes

* **PRIMARY KEY**: `id`
* **UNIQUE KEY**: `correo` [database/american_food L381](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L381-L381)
* **INDEX**: `idx_usuarios_rol` on `rol` field for role-based queries [database/american_food L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L382-L382)

#### Role Enumeration

The `rol` field enforces role-based access control with five distinct values:

| Role | Spanish Term | Primary Function | Interface CSS |
| --- | --- | --- | --- |
| `admin` | Administrador | Full system access, configuration | style.css |
| `cajera` | Cashier | Payment processing, invoices, sales | cajera.css |
| `cocinero` | Cook | Kitchen operations, order preparation | cocina.css |
| `mesera` | Server/Waitress | Table management, order taking | mesera.css |
| `cliente` | Customer | Online ordering, reservations | cliente.css |

**Sources**: [database/american_food L287-L308](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L308)

 [database/american_food L377-L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L377-L382)

---

### pedidos Table

The `pedidos` table is the central hub of the order management system. It tracks orders from creation through completion, supporting both online orders (with `cliente_id`) and in-person orders (with `mesa_id`).

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique order identifier |
| `mesa_id` | `INT(11)` | NULL, FK → `mesas(id)` | Table assignment for in-person orders |
| `cliente_id` | `INT(11)` | NULL, FK → `usuarios(id)` | Customer reference for online orders |
| `tipo_pedido` | `ENUM('online','presencial')` | NOT NULL, DEFAULT 'presencial' | Order type discriminator |
| `estado` | `ENUM(...)` | DEFAULT 'nuevo' | Current order state (see below) |
| `fecha_hora` | `DATETIME` | NOT NULL | Order creation timestamp |
| `total` | `DECIMAL(10,2)` | NOT NULL | Total order amount in currency |
| `notas` | `TEXT` | NULL | Optional notes or special instructions |
| `metodo_pago` | `VARCHAR(50)` | NULL | Payment method description |

#### Order State Flow

The `estado` field tracks the order lifecycle through eight possible states:

```

```

| Estado | Description | Typical Actor |
| --- | --- | --- |
| `nuevo` | Order created, awaiting payment | Sistema |
| `pendiente_pago` | Awaiting payment confirmation | Cajera |
| `pagado` | Payment received | Cajera |
| `en_preparacion` | Being prepared in kitchen | Cocinero |
| `listo` | Ready for delivery/pickup | Cocinero |
| `enviado` | Out for delivery (online orders) | Sistema |
| `entregado` | Completed and delivered | Mesera/Sistema |
| `cancelado` | Order cancelled | Any role |

#### Order Type Logic

The `tipo_pedido` field determines which foreign key is populated:

| tipo_pedido | mesa_id | cliente_id | Use Case |
| --- | --- | --- | --- |
| `presencial` | NOT NULL | NULL | Dine-in orders assigned to physical tables |
| `online` | NULL | NOT NULL | Delivery/takeout orders from registered customers |

#### Indexes

* **PRIMARY KEY**: `id`
* **FOREIGN KEY**: `mesa_id` → `mesas(id)` ON DELETE SET NULL [database/american_food L481](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L481-L481)
* **FOREIGN KEY**: `cliente_id` → `usuarios(id)` ON DELETE SET NULL [database/american_food L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L482-L482)
* **INDEX**: `idx_pedidos_estado` on `estado` field for status-based filtering [database/american_food L374](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L374-L374)

**Sources**: [database/american_food L256-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L280)

 [database/american_food L368-L374](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L368-L374)

 [database/american_food L478-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L478-L482)

---

### menu Table

The `menu` table stores the restaurant's product catalog with pricing, categorization, and availability tracking. Each item can be linked to image assets and includes automatic timestamp tracking.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique menu item identifier |
| `nombre` | `VARCHAR(100)` | NOT NULL | Item display name |
| `descripcion` | `TEXT` | NULL | Detailed item description |
| `categoria` | `ENUM('entradas','principales','postres','bebidas')` | NOT NULL | Menu category |
| `precio` | `DECIMAL(10,2)` | NOT NULL | Item price |
| `imagen` | `VARCHAR(255)` | NULL | Filename of item image in `img/` directory |
| `disponible` | `TINYINT(1)` | DEFAULT 1 | Availability: 1=available, 0=unavailable |
| `fecha_actualizacion` | `DATETIME` | DEFAULT CURRENT_TIMESTAMP ON UPDATE | Auto-updated modification timestamp |
| `ingredientes` | `TEXT` | NULL | Comma-separated ingredients list |

#### Category Classification

| categoria | Spanish Term | Item Types |
| --- | --- | --- |
| `entradas` | Appetizers/Starters | Hamburgers, hot dogs, small portions |
| `principales` | Main Courses | Wings, pizza, full meals |
| `postres` | Desserts | Cheesecake, brownies, sweet items |
| `bebidas` | Beverages | Lemonades, beer, soft drinks |

#### Image Asset Integration

The `imagen` field stores filenames that reference JPEG files in the `img/` directory. Format convention: `{timestamp}_{item_name}.{extension}`

Example values from sample data:

* `1743391251_Alitas bufalo.webp`
* `1743391294_hamburguesa_bbq.jpg`
* `1743391334_hotdog_clasico.jpg`

#### Indexes

* **PRIMARY KEY**: `id`
* **INDEX**: `idx_menu_categoria` on `categoria` field for category filtering [database/american_food L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L352)

**Sources**: [database/american_food L163-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L186)

 [database/american_food L349-L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L349-L352)

---

### mesas Table

The `mesas` table manages physical table inventory for dine-in service, tracking capacity, location, and operational status.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique table identifier |
| `numero` | `INT(11)` | NOT NULL | Display table number |
| `capacidad` | `INT(11)` | NOT NULL | Maximum seating capacity |
| `ubicacion` | `ENUM('interior','terraza','bar','vip')` | NOT NULL | Table location type |
| `estado` | `ENUM('activa','inactiva')` | DEFAULT 'activa' | Operational status |

#### Location Types

| ubicacion | Description | Typical Capacity Range |
| --- | --- | --- |
| `interior` | Indoor dining area | 2-6 seats |
| `terraza` | Outdoor terrace/patio | 4-10 seats |
| `bar` | Bar seating area | 2 seats |
| `vip` | Private/VIP section | 6-10 seats |

#### Table Status

The `estado` field indicates whether a table is available for service:

* **`activa`**: Table is operational and can be assigned to orders
* **`inactiva`**: Table is out of service (maintenance, reserved, etc.)

#### Sample Data Distribution

Based on [database/american_food L205-L223](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L205-L223)

 the restaurant has 18 tables:

| ubicacion | Count | Active | Inactive |
| --- | --- | --- | --- |
| interior | 7 | 5 | 2 |
| bar | 4 | 3 | 1 |
| terraza | 4 | 3 | 1 |
| vip | 3 | 3 | 0 |

**Sources**: [database/american_food L193-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L224)

 [database/american_food L356-L358](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L356-L358)

---

## Order Management Tables

### detalles_pedidos Table

The `detalles_pedidos` table implements the order line items, creating a many-to-many relationship between `pedidos` and `menu`. Each record represents one menu item within an order, storing quantity and price at time of purchase.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique line item identifier |
| `pedido_id` | `INT(11)` | NOT NULL, FK → `pedidos(id)` | Parent order reference |
| `plato_id` | `INT(11)` | NOT NULL, FK → `menu(id)` | Menu item reference |
| `cantidad` | `INT(11)` | NOT NULL | Quantity ordered |
| `precio_unitario` | `DECIMAL(10,2)` | NOT NULL | Unit price snapshot at order time |

#### Price Snapshot Pattern

The `precio_unitario` field stores the item price at the time of order creation, preserving historical pricing even if menu prices change later. This ensures order totals remain accurate and immutable.

#### Foreign Key Relationships

* **`pedido_id` → `pedidos(id)`**: ON DELETE CASCADE [database/american_food L456](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L456-L456) * When a parent order is deleted, all line items are automatically removed
* **`plato_id` → `menu(id)`**: No cascade action [database/american_food L457](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L457-L457) * Menu items cannot be deleted if referenced by any order history

#### Indexes

* **PRIMARY KEY**: `id`
* **FOREIGN KEY**: `pedido_id` indexed for join performance [database/american_food L324](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L324-L324)
* **FOREIGN KEY**: `plato_id` indexed for join performance [database/american_food L325](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L325-L325)

**Sources**: [database/american_food L42-L82](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L82)

 [database/american_food L320-L325](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L320-L325)

 [database/american_food L453-L457](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L457)

---

### pagos Table

The `pagos` table records payment transactions for completed orders, supporting multiple payment methods and cash change calculation.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique payment identifier |
| `pedido_id` | `INT(11)` | NOT NULL, FK → `pedidos(id)` | Associated order |
| `metodo` | `ENUM('efectivo','tarjeta','online')` | NOT NULL | Payment method |
| `monto_recibido` | `DECIMAL(10,2)` | NULL | Amount received (for cash payments) |
| `cambio` | `DECIMAL(10,2)` | NULL | Change returned (for cash payments) |
| `fecha_hora` | `DATETIME` | NOT NULL | Payment processing timestamp |

#### Payment Method Types

| metodo | Spanish Term | Fields Used |
| --- | --- | --- |
| `efectivo` | Cash | `monto_recibido`, `cambio` populated |
| `tarjeta` | Credit/Debit Card | `monto_recibido`, `cambio` NULL |
| `online` | Online Payment | `monto_recibido`, `cambio` NULL |

#### Cash Change Calculation

For `efectivo` payments, the cashier interface calculates change:

```
cambio = monto_recibido - pedidos.total
```

Example from sample data [database/american_food L245](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L245-L245)

:

* Order total: 48,900.00
* Amount received: 50,000.00
* Change: 1,100.00

#### Foreign Key Relationship

* **`pedido_id` → `pedidos(id)`**: No cascade action [database/american_food L475](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L475-L475) * Payments are preserved as financial records even if order data changes

#### Indexes

* **PRIMARY KEY**: `id`
* **FOREIGN KEY**: `pedido_id` indexed for order lookup [database/american_food L365](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L365-L365)

**Sources**: [database/american_food L231-L249](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L231-L249)

 [database/american_food L361-L365](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L361-L365)

 [database/american_food L472-L475](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L472-L475)

---

### facturas Table

The `facturas` table generates official invoices for orders, storing customer tax information and invoice metadata required for Mexican tax compliance (CFDI).

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique invoice identifier |
| `pedido_id` | `INT(11)` | NOT NULL, FK → `pedidos(id)` | Associated order |
| `nombre` | `VARCHAR(100)` | NOT NULL | Customer legal name |
| `rfc` | `VARCHAR(20)` | NOT NULL | Mexican tax ID (RFC) |
| `email` | `VARCHAR(100)` | NULL | Email for invoice delivery |
| `direccion` | `TEXT` | NULL | Billing address |
| `uso_cfdi` | `VARCHAR(10)` | NULL | CFDI usage code (Mexican tax) |
| `fecha` | `DATETIME` | NOT NULL | Invoice generation timestamp |
| `total` | `DECIMAL(10,2)` | NOT NULL | Invoice total amount |
| `estado` | `ENUM('pendiente','emitida')` | DEFAULT 'pendiente' | Invoice status |

#### Invoice States

| estado | Description |
| --- | --- |
| `pendiente` | Invoice requested but not yet generated/emitted |
| `emitida` | Invoice officially issued and sent to customer |

#### Placeholder Values

Sample data [database/american_food L107-L108](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L107-L108)

 shows pending invoices use placeholder values:

* `nombre`: "Pendiente"
* `rfc`: "Pendiente"
* Other fields: NULL

This indicates invoices are created in pending state and updated with actual customer information when emitted.

#### Foreign Key Relationship

* **`pedido_id` → `pedidos(id)`**: No cascade action [database/american_food L463](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L463-L463) * Invoices are financial documents that must be preserved independently

#### Indexes

* **PRIMARY KEY**: `id`
* **FOREIGN KEY**: `pedido_id` indexed for order lookup [database/american_food L332](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L332-L332)

**Sources**: [database/american_food L89-L109](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L109)

 [database/american_food L328-L332](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L328-L332)

 [database/american_food L460-L463](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L460-L463)

---

### informacion_entrega Table

The `informacion_entrega` table stores delivery address information for online orders, enabling the system to route orders to customer locations.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique delivery info identifier |
| `pedido_id` | `INT(11)` | NOT NULL, FK → `pedidos(id)` | Associated online order |
| `nombre` | `VARCHAR(100)` | NOT NULL | Delivery recipient name |
| `direccion` | `TEXT` | NOT NULL | Full delivery address |
| `telefono` | `VARCHAR(20)` | NOT NULL | Contact phone number |

#### Relationship to Order Types

This table is only populated for orders where `pedidos.tipo_pedido = 'online'`. In-person orders (`tipo_pedido = 'presencial'`) do not require delivery information.

#### Foreign Key Relationship

* **`pedido_id` → `pedidos(id)`**: ON DELETE CASCADE [database/american_food L469](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L469-L469) * Delivery information is deleted when the associated order is removed * Ensures no orphaned delivery records

#### Indexes

* **PRIMARY KEY**: `id`
* **FOREIGN KEY**: `pedido_id` indexed for order lookup [database/american_food L339](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L339-L339)

**Sources**: [database/american_food L116-L132](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L132)

 [database/american_food L335-L339](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L335-L339)

 [database/american_food L466-L469](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L466-L469)

---

## Supporting Tables

### inventario Table

The `inventario` table manages ingredient and supply inventory, tracking quantities, suppliers, and minimum stock levels for reorder alerts.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique inventory item identifier |
| `nombre` | `VARCHAR(100)` | NOT NULL | Item name |
| `categoria` | `VARCHAR(50)` | NOT NULL | Item category (free text) |
| `cantidad` | `DECIMAL(10,2)` | NOT NULL | Current stock quantity |
| `unidad` | `ENUM('kg','g','l','ml','unidades')` | NOT NULL | Measurement unit |
| `precio_unitario` | `DECIMAL(10,2)` | NOT NULL | Cost per unit |
| `proveedor` | `VARCHAR(100)` | NULL | Supplier name |
| `cantidad_minima` | `DECIMAL(10,2)` | NOT NULL | Minimum stock threshold |

#### Unit of Measurement

| unidad | Description | Typical Use |
| --- | --- | --- |
| `kg` | Kilograms | Bulk meats, produce |
| `g` | Grams | Spices, small portions |
| `l` | Liters | Liquids, beverages |
| `ml` | Milliliters | Sauces, oils |
| `unidades` | Units/Pieces | Individual items (e.g., buns, eggs) |

#### Inventory Management Pattern

The system can implement automatic reorder alerts by comparing `cantidad` against `cantidad_minima`:

```
IF cantidad < cantidad_minima THEN
  -- Trigger reorder alert
END IF
```

#### Standalone Table

The `inventario` table has no foreign key relationships to other tables. It operates independently for supply chain management without direct integration into the order processing workflow.

#### Indexes

* **PRIMARY KEY**: `id`

**Sources**: [database/american_food L139-L156](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L156)

 [database/american_food L342-L345](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L342-L345)

---

### configuracion Table

The `configuracion` table provides flexible key-value storage for system-wide settings and configuration parameters.

#### Schema Definition

```

```

#### Field Specifications

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `INT(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique configuration entry ID |
| `clave` | `VARCHAR(50)` | NOT NULL | Configuration key name |
| `valor` | `VARCHAR(255)` | NOT NULL | Configuration value |

#### Usage Pattern

This table implements a flexible key-value store allowing the application to store arbitrary configuration without schema changes. Example usage scenarios:

* Restaurant operating hours
* Tax rates
* Service fees
* Feature flags
* System messages

#### Current State

The table is currently empty (no sample data in [database/american_food L30-L35](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L35)

), indicating configuration may be managed through `config/configuracion.php` PHP constants instead.

#### Indexes

* **PRIMARY KEY**: `id`

**Sources**: [database/american_food L30-L35](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L35)

 [database/american_food L313-L317](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L313-L317)

---

## Database Relationships and Constraints

### Foreign Key Constraint Summary

The database implements referential integrity through foreign key constraints with specific cascade behaviors.

```

```

#### Constraint Details

| Parent Table | Child Table | Foreign Key Column | ON DELETE Behavior | Rationale |
| --- | --- | --- | --- | --- |
| `pedidos` | `detalles_pedidos` | `pedido_id` | CASCADE | Line items have no meaning without parent order |
| `menu` | `detalles_pedidos` | `plato_id` | RESTRICT (no action) | Preserve order history; prevent menu deletion |
| `pedidos` | `pagos` | `pedido_id` | RESTRICT (no action) | Financial records must be preserved |
| `pedidos` | `facturas` | `pedido_id` | RESTRICT (no action) | Tax documents must be preserved |
| `pedidos` | `informacion_entrega` | `pedido_id` | CASCADE | Delivery info tied to specific order |
| `mesas` | `pedidos` | `mesa_id` | SET NULL | Order preserved if table removed |
| `usuarios` | `pedidos` | `cliente_id` | SET NULL | Order preserved if user deleted |

**Sources**: [database/american_food L453-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L482)

---

### Cardinality Relationships

```

```

#### Relationship Descriptions

**One-to-Many Relationships:**

* One `usuarios` (customer) can create many `pedidos`
* One `mesas` (table) can host many `pedidos` over time
* One `pedidos` contains one or many `detalles_pedidos` (minimum 1 item)
* One `menu` item can appear in many `detalles_pedidos` across different orders

**One-to-Zero-or-One Relationships:**

* One `pedidos` may have zero or one `pagos` (payment optional until completed)
* One `pedidos` may have zero or one `facturas` (invoice optional)
* One `pedidos` may have zero or one `informacion_entrega` (only for online orders)

**Sources**: [database/american_food L453-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L482)

---

## Database Indexes and Performance

### Index Strategy

The database implements strategic indexes to optimize common query patterns used by the application.

#### Indexes by Table

| Table | Index Name | Columns | Type | Purpose |
| --- | --- | --- | --- | --- |
| `usuarios` | PRIMARY | `id` | Primary Key | Unique user lookup |
| `usuarios` | `correo` | `correo` | Unique | Email uniqueness, login queries |
| `usuarios` | `idx_usuarios_rol` | `rol` | Index | Role-based user filtering |
| `pedidos` | PRIMARY | `id` | Primary Key | Unique order lookup |
| `pedidos` | `mesa_id` | `mesa_id` | Foreign Key | Table-based order queries |
| `pedidos` | `cliente_id` | `cliente_id` | Foreign Key | Customer order history |
| `pedidos` | `idx_pedidos_estado` | `estado` | Index | Status-based filtering |
| `menu` | PRIMARY | `id` | Primary Key | Unique item lookup |
| `menu` | `idx_menu_categoria` | `categoria` | Index | Category browsing |
| `detalles_pedidos` | PRIMARY | `id` | Primary Key | Unique line item lookup |
| `detalles_pedidos` | `pedido_id` | `pedido_id` | Foreign Key | Order detail joins |
| `detalles_pedidos` | `plato_id` | `plato_id` | Foreign Key | Menu item usage tracking |
| `pagos` | PRIMARY | `id` | Primary Key | Unique payment lookup |
| `pagos` | `pedido_id` | `pedido_id` | Foreign Key | Payment-order joins |
| `facturas` | PRIMARY | `id` | Primary Key | Unique invoice lookup |
| `facturas` | `pedido_id` | `pedido_id` | Foreign Key | Invoice-order joins |
| `informacion_entrega` | PRIMARY | `id` | Primary Key | Unique delivery info lookup |
| `informacion_entrega` | `pedido_id` | `pedido_id` | Foreign Key | Delivery-order joins |
| `mesas` | PRIMARY | `id` | Primary Key | Unique table lookup |
| `inventario` | PRIMARY | `id` | Primary Key | Unique inventory item lookup |
| `configuracion` | PRIMARY | `id` | Primary Key | Unique config entry lookup |

#### Query Optimization Patterns

**Common Query Pattern 1 - Kitchen Interface:**

```

```

Optimized by: `idx_pedidos_estado` index [database/american_food L374](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L374-L374)

**Common Query Pattern 2 - Menu Browsing:**

```

```

Optimized by: `idx_menu_categoria` index [database/american_food L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L352)

**Common Query Pattern 3 - Order Details:**

```

```

Optimized by: Foreign key indexes on `detalles_pedidos.pedido_id` and `detalles_pedidos.plato_id`

**Common Query Pattern 4 - Customer Order History:**

```

```

Optimized by: Foreign key index on `pedidos.cliente_id` [database/american_food L373](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L373-L373)

**Sources**: [database/american_food L313-L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L313-L382)

---

## Character Encoding and Collation

### Encoding Configuration

The database uses UTF-8 encoding to support Spanish language content and international characters.

#### Database-Level Settings

```

```

[database/american_food L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L18-L18)

#### Table-Level Configuration

All tables use:

* **Character Set**: `utf8mb4`
* **Collation**: `utf8mb4_general_ci`

Format: `ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci`

#### UTF-8 Support Rationale

The `utf8mb4` character set is essential for this system because:

1. Spanish language content (mesera, cajera, cocinero roles)
2. Special characters in menu item names and descriptions
3. Customer names with accented characters
4. Mexican tax ID formats (RFC)
5. Full emoji support (4-byte UTF-8 sequences)

#### Connection-Level Encoding

The application sets UTF-8 encoding at connection time via `config/conexionDB.php`:

```

```

[config/conexionDB.php L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L16-L16)

 (referenced in overview diagrams)

**Sources**: [database/american_food L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L18-L18)

 all table definitions throughout [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql)

---

## Database Statistics

### Data Volume Summary

Based on AUTO_INCREMENT values and sample data:

| Table | Current Records | AUTO_INCREMENT | Growth Rate |
| --- | --- | --- | --- |
| `usuarios` | 4 | 8 | User registrations |
| `pedidos` | 7 | 8 | Steady order flow |
| `detalles_pedidos` | 27 | 28 | ~3.9 items per order |
| `menu` | 6 | 10 | Stable product catalog |
| `mesas` | 18 | 19 | Fixed table inventory |
| `pagos` | 4 | 5 | 57% payment completion |
| `facturas` | 2 | 3 | 29% invoice requests |
| `informacion_entrega` | 3 | 4 | 43% online orders |
| `inventario` | 1 | 2 | Low usage |
| `configuracion` | 0 | 0 | Not in use |

### Sample Data Insights

**Order Analysis** [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

:

* Total orders: 7
* Online orders: 3 (43%)
* In-person orders: 4 (57%)
* Completed orders: 4
* Orders in preparation: 3

**Menu Distribution** [database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

:

* Entradas: 2 items
* Principales: 1 item
* Postres: 2 items
* Bebidas: 1 item
* Total: 6 active menu items

**User Roles** [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

:

* Cocinero: 1
* Mesera: 1
* Cajera: 1
* Cliente: 1
* Admin: 0 (no admin users in sample data)

**Sources**: [database/american_food L54-L308](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L54-L308)

 [database/american_food L388-L446](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L388-L446)