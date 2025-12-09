# Schema Overview

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

This document provides a high-level overview of the `american_food` MySQL database schema, including all tables, their relationships, and the overall data model architecture. This page focuses on the structural organization of the database rather than individual table details. For detailed documentation of specific tables, see [Core Tables](/axchisan/AmericanFood/2.2-core-tables), [Order Management Tables](/axchisan/AmericanFood/2.3-order-management-tables), and [Supporting Tables](/axchisan/AmericanFood/2.4-supporting-tables).

## Database Configuration

The American Food system uses a MySQL database named `american_food` with the following configuration:

| Property | Value |
| --- | --- |
| Database Name | `american_food` |
| Character Set | `utf8mb4` |
| Collation | `utf8mb4_general_ci` |
| Storage Engine | InnoDB |
| SQL Mode | `NO_AUTO_VALUE_ON_ZERO` |

The `utf8mb4` character set supports full Unicode including emoji and special characters, which is essential for international restaurant names, menu items, and customer data.

**Sources:** [database/american_food L10-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L10-L18)

## Table Summary

The database consists of 9 core tables organized into functional groups:

| Table | Primary Purpose | Record Count (Sample) | Key Field(s) |
| --- | --- | --- | --- |
| `usuarios` | User authentication and role-based access control | 4 | `id`, `correo` (unique), `rol` |
| `pedidos` | Central order tracking for both online and in-person orders | 7 | `id`, `tipo_pedido`, `estado` |
| `detalles_pedidos` | Individual line items within each order | 27 | `id`, `pedido_id`, `plato_id` |
| `menu` | Restaurant menu items with pricing and availability | 6 | `id`, `categoria`, `disponible` |
| `mesas` | Physical table management for in-person dining | 18 | `id`, `numero`, `estado` |
| `pagos` | Payment processing and transaction records | 4 | `id`, `pedido_id`, `metodo` |
| `facturas` | Invoice generation for orders requiring receipts | 2 | `id`, `pedido_id`, `estado` |
| `informacion_entrega` | Delivery address details for online orders | 3 | `id`, `pedido_id` |
| `inventario` | Stock tracking for ingredients and supplies | 1 | `id`, `categoria`, `cantidad` |
| `configuracion` | System-wide configuration key-value pairs | 0 | `id`, `clave` |

**Sources:** [database/american_food L27-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L27-L297)

## Entity Relationship Diagram

The following ERD illustrates the complete database schema with all tables and their relationships:

```

```

**Sources:** [database/american_food L42-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L297)

 [database/american_food L451-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L451-L483)

## Central Hub Architecture: The pedidos Table

The `pedidos` table serves as the central hub connecting eight other tables in the database. This architectural pattern centralizes order management while maintaining flexible relationships:

```

```

**Hub Pattern Characteristics:**

1. **Inbound References**: `pedidos` receives foreign keys from `usuarios` (customer) and `mesas` (table assignment)
2. **Outbound References**: Five tables reference `pedidos.id` as a foreign key
3. **Dual Order Types**: The `tipo_pedido` field enables both online (`cliente_id` set, `mesa_id` NULL) and in-person (`mesa_id` set, `cliente_id` NULL) workflows
4. **Cascade Deletes**: Critical child data (`detalles_pedidos`, `informacion_entrega`) cascade on delete to maintain referential integrity
5. **Nullable Parents**: Parent references use `SET NULL` to preserve order history if a table or user is deleted

**Sources:** [database/american_food L256-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L280)

 [database/american_food L478-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L478-L483)

## Relationship Cardinalities

The database implements the following relationship patterns:

| Parent Table | Child Table | Cardinality | Delete Behavior | Notes |
| --- | --- | --- | --- | --- |
| `usuarios` | `pedidos` | 1:N | SET NULL | One user can place many orders; orders persist if user deleted |
| `mesas` | `pedidos` | 1:N | SET NULL | One table can have many orders; orders persist if table deleted |
| `pedidos` | `detalles_pedidos` | 1:N | CASCADE | Each order must have at least one line item; items deleted with order |
| `menu` | `detalles_pedidos` | 1:N | RESTRICT | One menu item can appear in many order lines; cannot delete menu item with active orders |
| `pedidos` | `pagos` | 1:0..1 | RESTRICT | Each order has at most one payment record; payment must exist before order deletion |
| `pedidos` | `facturas` | 1:0..1 | RESTRICT | Each order has at most one invoice; optional feature |
| `pedidos` | `informacion_entrega` | 1:0..1 | CASCADE | Each order has at most one delivery record; only for online orders |

**Conditional Relationships:**

The `pedidos` table implements mutually exclusive relationships based on `tipo_pedido`:

* **Online orders** (`tipo_pedido='online'`): `cliente_id` is set, `mesa_id` is NULL, `informacion_entrega` record exists
* **In-person orders** (`tipo_pedido='presencial'`): `mesa_id` is set, `cliente_id` may be NULL, no `informacion_entrega` record

**Sources:** [database/american_food L451-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L451-L483)

## Indexes and Performance Optimization

The schema includes strategic indexes to optimize common query patterns:

### Primary Keys

All tables use auto-incrementing integer primary keys named `id`:

```

```

**Sources:** [database/american_food L388-L446](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L388-L446)

### Foreign Key Indexes

All foreign key columns automatically have indexes created for referential integrity checks:

| Table | Foreign Key Index | Purpose |
| --- | --- | --- |
| `detalles_pedidos` | `pedido_id` | Join to `pedidos` for order details |
| `detalles_pedidos` | `plato_id` | Join to `menu` for item information |
| `facturas` | `pedido_id` | Lookup invoices by order |
| `informacion_entrega` | `pedido_id` | Lookup delivery info by order |
| `pagos` | `pedido_id` | Lookup payment by order |
| `pedidos` | `mesa_id` | Filter orders by table |
| `pedidos` | `cliente_id` | Filter orders by customer |

**Sources:** [database/american_food L320-L375](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L320-L375)

### Performance Indexes

Three additional indexes optimize common query patterns:

| Index Name | Table | Column | Purpose |
| --- | --- | --- | --- |
| `idx_menu_categoria` | `menu` | `categoria` | Fast filtering of menu items by category (entradas, principales, postres, bebidas) |
| `idx_pedidos_estado` | `pedidos` | `estado` | Fast filtering of orders by status for kitchen/cashier interfaces |
| `idx_usuarios_rol` | `usuarios` | `rol` | Fast filtering of users by role (admin, cajera, cocinero, mesera, cliente) |

**Sources:** [database/american_food L352-L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L382)

### Unique Constraints

The `usuarios` table enforces email uniqueness to prevent duplicate accounts:

```

```

**Sources:** [database/american_food L381](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L381-L381)

## Foreign Key Constraint Summary

The database enforces referential integrity through seven foreign key constraints:

| Constraint Name | Definition | Delete Behavior |
| --- | --- | --- |
| `detalles_pedidos_ibfk_1` | `detalles_pedidos.pedido_id` → `pedidos.id` | CASCADE - deleting an order removes all its line items |
| `detalles_pedidos_ibfk_2` | `detalles_pedidos.plato_id` → `menu.id` | RESTRICT - cannot delete menu items referenced in orders |
| `facturas_ibfk_1` | `facturas.pedido_id` → `pedidos.id` | RESTRICT - must delete invoice before order |
| `informacion_entrega_ibfk_1` | `informacion_entrega.pedido_id` → `pedidos.id` | CASCADE - deleting an order removes delivery info |
| `pagos_ibfk_1` | `pagos.pedido_id` → `pedidos.id` | RESTRICT - must delete payment before order |
| `pedidos_ibfk_1` | `pedidos.mesa_id` → `mesas.id` | SET NULL - order history preserved if table deleted |
| `pedidos_ibfk_2` | `pedidos.cliente_id` → `usuarios.id` | SET NULL - order history preserved if customer deleted |

**Cascade Strategy Rationale:**

* **CASCADE**: Used for mandatory child records that have no meaning without the parent (order line items, delivery info)
* **SET NULL**: Used to preserve historical records while allowing parent deletion (table/customer assignments)
* **RESTRICT**: Used to prevent accidental deletion of referenced data (payments, invoices, menu items in active orders)

**Sources:** [database/american_food L453-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L483)

## Character Encoding and Collation

All tables use consistent encoding:

| Setting | Value | Purpose |
| --- | --- | --- |
| Character Set | `utf8mb4` | Full Unicode support including emoji and special characters |
| Collation | `utf8mb4_general_ci` | Case-insensitive comparisons for Spanish text |
| Storage Engine | InnoDB | ACID transactions and foreign key support |

This configuration ensures proper handling of:

* Spanish characters (ñ, á, é, í, ó, ú)
* Menu item descriptions with special symbols
* International customer names
* Currency symbols

**Sources:** [database/american_food L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L18-L18)

 [database/american_food L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L34-L34)