# Core Tables

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

This document provides detailed documentation of the four foundational tables in the `american_food` database: `usuarios`, `pedidos`, `menu`, and `mesas`. These tables form the operational core of the restaurant management system, handling user authentication, order processing, product catalog, and table management respectively. For information about order-related subsidiary tables, see [Order Management Tables](/axchisan/AmericanFood/2.3-order-management-tables). For configuration and inventory tables, see [Supporting Tables](/axchisan/AmericanFood/2.4-supporting-tables).

## Table Relationships

The four core tables interact to support both online and in-person dining operations. The `pedidos` table serves as the central hub, referencing both `usuarios` (for customer orders) and `mesas` (for in-person dining), while order line items reference the `menu` table for product details.

**Entity Relationship Diagram**

```

```

**Sources:** [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

 [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

 [database/american_food L193-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L199)

## usuarios Table

The `usuarios` table handles authentication and role-based access control for the entire system. Each user is assigned one of five roles that determine their interface and permissions.

### Schema Definition

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique user identifier |
| `nombre` | VARCHAR(100) | NOT NULL | User's first name |
| `apellido` | VARCHAR(100) | NOT NULL | User's last name |
| `correo` | VARCHAR(100) | NOT NULL, UNIQUE | Email address (unique login identifier) |
| `telefono` | VARCHAR(20) | NULL | Contact phone number |
| `username` | VARCHAR(100) | NOT NULL | Display username |
| `rol` | ENUM | NOT NULL | Role: `admin`, `cajera`, `cocinero`, `cliente`, `mesera` |
| `contraseña` | VARCHAR(255) | NOT NULL | Password hash (bcrypt) |
| `estado` | ENUM | DEFAULT 'activo' | Account status: `activo`, `inactivo` |

### Indexes

* **PRIMARY KEY**: `id` [database/american_food L380](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L380-L380)
* **UNIQUE KEY**: `correo` [database/american_food L381](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L381-L381)
* **INDEX**: `idx_usuarios_rol` on `rol` field [database/american_food L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L382-L382)

### Foreign Key References

The `usuarios` table is referenced by:

* `pedidos.cliente_id` - Links customer orders to user accounts [database/american_food L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L482-L482)

### Role-Based Access System

```

```

**Sources:** [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

 [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

### Sample Data Pattern

The database contains representative users for each role:

```

```

All passwords are hashed using `password_hash()` with `PASSWORD_DEFAULT` algorithm (bcrypt with cost factor 12), indicated by the `$2y$12$` prefix in the `contraseña` field [database/american_food L304-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L304-L307)

**Sources:** [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

## pedidos Table

The `pedidos` table is the central operational table that tracks all orders in the system. It supports both online orders (linked to `cliente_id`) and in-person orders (linked to `mesa_id`), implementing a dual-path order processing model.

### Schema Definition

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique order identifier |
| `mesa_id` | INT(11) | FOREIGN KEY, NULL | Reference to `mesas.id` for in-person orders |
| `cliente_id` | INT(11) | FOREIGN KEY, NULL | Reference to `usuarios.id` for online orders |
| `tipo_pedido` | ENUM | NOT NULL, DEFAULT 'presencial' | Order type: `online`, `presencial` |
| `estado` | ENUM | DEFAULT 'nuevo' | Order state (8 possible values, see below) |
| `fecha_hora` | DATETIME | NOT NULL | Order timestamp |
| `total` | DECIMAL(10,2) | NOT NULL | Total order amount in currency |
| `notas` | TEXT | NULL | Optional order notes or special instructions |
| `metodo_pago` | VARCHAR(50) | NULL | Payment method descriptor |

### Order State Machine

The `estado` field tracks orders through their lifecycle:

```

```

**Valid estados**: `nuevo`, `pendiente_pago`, `pagado`, `en_preparacion`, `listo`, `enviado`, `entregado`, `cancelado` [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

### Indexes

* **PRIMARY KEY**: `id` [database/american_food L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L371-L371)
* **INDEX**: `mesa_id` [database/american_food L372](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L372-L372)
* **INDEX**: `cliente_id` [database/american_food L373](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L373-L373)
* **INDEX**: `idx_pedidos_estado` on `estado` field [database/american_food L374](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L374-L374)

The index on `estado` optimizes queries filtering by order status, which is critical for kitchen and cashier interfaces that display orders grouped by state.

### Foreign Key Constraints

```

```

Both foreign keys use `ON DELETE SET NULL`, preserving order history even if a table or customer account is deleted [database/american_food L481-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L481-L482)

**Sources:** [database/american_food L481-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L481-L482)

### Order Type Discrimination

The `tipo_pedido` field determines which foreign key is populated:

| tipo_pedido | mesa_id | cliente_id | Use Case |
| --- | --- | --- | --- |
| `presencial` | NOT NULL | NULL | In-person dining at a specific table |
| `online` | NULL | NOT NULL | Online order with delivery information |

**Example from sample data:**

```

```

**Sources:** [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

## menu Table

The `menu` table stores the restaurant's product catalog. Each menu item includes pricing, categorization, availability status, and an image reference for display in customer-facing interfaces.

### Schema Definition

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique menu item identifier |
| `nombre` | VARCHAR(100) | NOT NULL | Product name |
| `descripcion` | TEXT | NULL | Product description |
| `categoria` | ENUM | NOT NULL | Category: `entradas`, `principales`, `postres`, `bebidas` |
| `precio` | DECIMAL(10,2) | NOT NULL | Price in currency units |
| `imagen` | VARCHAR(255) | NULL | Image filename (relative path) |
| `disponible` | TINYINT(1) | DEFAULT 1 | Availability flag (1=available, 0=out of stock) |
| `fecha_actualizacion` | DATETIME | AUTO-UPDATE | Last modification timestamp |
| `ingredientes` | TEXT | NULL | Comma-separated ingredients list |

The `fecha_actualizacion` field uses `ON UPDATE current_timestamp()` to automatically track modifications [database/american_food L171](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L171-L171)

### Indexes

* **PRIMARY KEY**: `id` [database/american_food L351](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L351-L351)
* **INDEX**: `idx_menu_categoria` on `categoria` field [database/american_food L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L352)

The category index optimizes menu browsing queries that filter by category, supporting the tabbed category navigation in `cliente.css`.

**Sources:** [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

 [database/american_food L351-L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L351-L352)

### Category Distribution

Sample data demonstrates all four categories:

| categoria | Example Items | Count |
| --- | --- | --- |
| `entradas` | hamburguesa, Hot Dog | 2 |
| `principales` | alitas bufalo | 1 |
| `postres` | tarta de manzana, brownie con helado | 2 |
| `bebidas` | limonada | 1 |

**Sources:** [database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

### Image Asset Integration

```

```

Image filenames follow the pattern: `{timestamp}_{product_name}.{ext}` [database/american_food L181-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L181-L185)

**Sources:** [database/american_food L169](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L169-L169)

 [database/american_food L181-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L181-L185)

### Foreign Key References

The `menu` table is referenced by:

* `detalles_pedidos.plato_id` - Links order line items to menu items [database/american_food L457](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L457-L457)

### Availability Management

The `disponible` field allows temporary disabling of menu items without deletion:

```

```

This supports inventory management where items can be marked unavailable when ingredients are depleted [database/american_food L184](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L184-L184)

**Sources:** [database/american_food L184](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L184-L184)

## mesas Table

The `mesas` table manages the restaurant's physical seating capacity. Each table has a defined capacity, location within the restaurant, and an operational status.

### Schema Definition

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique table identifier |
| `numero` | INT(11) | NOT NULL | Table number (display identifier) |
| `capacidad` | INT(11) | NOT NULL | Maximum seating capacity |
| `ubicacion` | ENUM | NOT NULL | Location: `interior`, `terraza`, `bar`, `vip` |
| `estado` | ENUM | DEFAULT 'activa' | Operational status: `activa`, `inactiva` |

### Indexes

* **PRIMARY KEY**: `id` [database/american_food L358](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L358-L358)

### Foreign Key References

The `mesas` table is referenced by:

* `pedidos.mesa_id` - Links in-person orders to specific tables [database/american_food L481](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L481-L481)

**Sources:** [database/american_food L193-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L199)

 [database/american_food L358](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L358-L358)

### Location-Based Organization

The restaurant space is divided into four zones:

```

```

### Sample Data Distribution

The database contains 18 tables across locations:

| ubicacion | Count | Capacity Range | estado activa | estado inactiva |
| --- | --- | --- | --- | --- |
| `interior` | 7 | 4-6 seats | 5 | 2 |
| `terraza` | 4 | 4-10 seats | 3 | 1 |
| `bar` | 4 | 2 seats | 3 | 1 |
| `vip` | 3 | 4-10 seats | 3 | 0 |

**Sources:** [database/american_food L205-L223](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L205-L223)

### Table Status Management

The `estado` field controls whether a table is available for assignment:

* **`activa`**: Table is operational and can accept orders
* **`inactiva`**: Table is temporarily unavailable (maintenance, reserved, etc.)

Example usage:

```

```

The `mesera.css` interface uses this field to color-code table status in the table management grid [database/american_food L211](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L211-L211)

**Sources:** [database/american_food L211](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L211-L211)

 [database/american_food L198](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L198-L198)

## Cross-Table Query Patterns

The core tables frequently appear together in common query patterns:

### Order Creation with User Context

```

```

### Table Assignment with Order Tracking

```

```

### Menu Item Availability for Ordering

```

```

**Sources:** [database/american_food L256-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L297)

## Character Encoding and Collation

All four core tables use UTF-8 encoding with `utf8mb4` character set and `utf8mb4_general_ci` collation:

```

```

This supports Spanish-language content throughout the application (mesera, cajera, cocinero role names, menu descriptions) and international characters in user names and addresses [database/american_food L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L34-L34)

 [database/american_food L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L173-L173)

 [database/american_food L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L199-L199)

 [database/american_food L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L266-L266)

 [database/american_food L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L297-L297)

**Sources:** [database/american_food L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L34-L34)

 [database/american_food L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L173-L173)

 [database/american_food L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L199-L199)

 [database/american_food L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L266-L266)

 [database/american_food L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L297-L297)