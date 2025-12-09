# Order Management Tables

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This page documents the four database tables that support order processing in the American Food system: `detalles_pedidos`, `pagos`, `facturas`, and `informacion_entrega`. These tables work together with the core `pedidos` table to manage the complete lifecycle of customer orders, from line item details through payment processing and invoice generation.

For documentation of the core `pedidos`, `menu`, `mesas`, and `usuarios` tables, see [Core Tables](/axchisan/AmericanFood/2.2-core-tables). For inventory management, see [Supporting Tables](/axchisan/AmericanFood/2.4-supporting-tables).

**Sources:** [database/american_food L42-L132](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L132)

---

## Table Relationships Overview

```

```

This diagram shows the hub-and-spoke relationship pattern where `pedidos` acts as the central table. Each order can have:

* Multiple line items (`detalles_pedidos`) - **required**, 1:N relationship
* One payment record (`pagos`) - optional until order is paid
* One invoice (`facturas`) - optional, only if customer requests
* One delivery information record (`informacion_entrega`) - only for online orders

**Sources:** [database/american_food L42-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L238)

 [database/american_food L453-L476](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L476)

---

## detalles_pedidos Table

### Structure

The `detalles_pedidos` table stores individual line items for each order, implementing the 1:N relationship between orders and menu items.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `int(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique identifier for line item |
| `pedido_id` | `int(11)` | NOT NULL, FOREIGN KEY | References `pedidos.id` |
| `plato_id` | `int(11)` | NOT NULL, FOREIGN KEY | References `menu.id` |
| `cantidad` | `int(11)` | NOT NULL | Quantity ordered |
| `precio_unitario` | `decimal(10,2)` | NOT NULL | Price per unit at time of order |

**Sources:** [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

### Foreign Key Constraints

```

```

The `CASCADE` delete behavior on `pedido_id` means that when an order is deleted, all its line items are automatically removed. The `plato_id` constraint has no `ON DELETE` action, preventing deletion of menu items that are referenced in order history.

**Sources:** [database/american_food L455-L457](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L455-L457)

### Indexes

| Index | Columns | Purpose |
| --- | --- | --- |
| PRIMARY | `id` | Unique row identification |
| `pedido_id` | `pedido_id` | Optimize queries filtering by order |
| `plato_id` | `plato_id` | Optimize queries filtering by menu item |

**Sources:** [database/american_food L322-L325](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L322-L325)

### Data Patterns

The table contains 27 line items across 7 orders. Sample data shows:

```sql
-- Order #1 has 6 line items
INSERT INTO detalles_pedidos VALUES
(1, 1, 5, 1, 8000.00),   -- 1x item #5 at 8000.00
(2, 1, 4, 1, 13400.00),  -- 1x item #4 at 13400.00
(3, 1, 6, 1, 7500.00),   -- 1x item #6 at 7500.00
(4, 1, 9, 1, 6000.00),   -- 1x item #9 at 6000.00
(5, 1, 8, 1, 12000.00),  -- 1x item #8 at 12000.00
(6, 1, 7, 1, 2000.00);   -- 1x item #7 at 2000.00
```

Note that `precio_unitario` stores the price at the time of order creation, preserving historical pricing even if menu prices change later.

**Sources:** [database/american_food L54-L82](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L54-L82)

---

## pagos Table

### Structure

The `pagos` table records payment transactions for orders. Each order has at most one payment record.

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `int(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique payment identifier |
| `pedido_id` | `int(11)` | NOT NULL, FOREIGN KEY | References `pedidos.id` |
| `metodo` | `enum('efectivo','tarjeta','online')` | NOT NULL | Payment method |
| `monto_recibido` | `decimal(10,2)` | NULL | Amount received from customer |
| `cambio` | `decimal(10,2)` | NULL | Change to return |
| `fecha_hora` | `datetime` | NOT NULL | Payment timestamp |

**Sources:** [database/american_food L231-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L231-L238)

### Payment Methods

The `metodo` enum supports three payment types:

* **efectivo** (cash): Requires `monto_recibido` and calculates `cambio`
* **tarjeta** (card): Electronic payment terminal
* **online**: Digital payment platform

For cash payments, the system stores both the amount received and change given:

```
-- Cash payment example
(1, 1, 'efectivo', 50000.00, 1100.00, '2025-03-30 22:58:47')
-- Customer paid 50000.00 for a 48900.00 order, received 1100.00 change
```

**Sources:** [database/american_food L234-L248](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L234-L248)

### Foreign Key Constraints

```

```

No `ON DELETE` action is specified, meaning orders with payment records cannot be deleted (referential integrity protection).

**Sources:** [database/american_food L474-L475](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L474-L475)

### Indexes

| Index | Columns | Purpose |
| --- | --- | --- |
| PRIMARY | `id` | Unique row identification |
| `pedido_id` | `pedido_id` | Optimize payment lookups by order |

**Sources:** [database/american_food L363-L365](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L363-L365)

### Sample Data

Four payment records exist in the system:

| Payment ID | Order ID | Method | Amount Received | Change | Timestamp |
| --- | --- | --- | --- | --- | --- |
| 1 | 1 | efectivo | 50000.00 | 1100.00 | 2025-03-30 22:58:47 |
| 2 | 4 | efectivo | 40000.00 | 4600.00 | 2025-04-01 00:27:16 |
| 3 | 7 | efectivo | 30000.00 | 8500.00 | 2025-04-02 13:30:28 |
| 4 | 5 | efectivo | 20000.00 | 4500.00 | 2025-04-02 13:31:44 |

All existing payments use cash (`efectivo` method), indicating this is the primary payment method for in-person orders.

**Sources:** [database/american_food L244-L248](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L244-L248)

---

## facturas Table

### Structure

The `facturas` table manages invoice generation for orders, supporting Mexican tax compliance (CFDI - Comprobante Fiscal Digital por Internet).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `int(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique invoice identifier |
| `pedido_id` | `int(11)` | NOT NULL, FOREIGN KEY | References `pedidos.id` |
| `nombre` | `varchar(100)` | NOT NULL | Customer/business name |
| `rfc` | `varchar(20)` | NOT NULL | RFC (Mexican tax ID) |
| `email` | `varchar(100)` | NULL | Customer email |
| `direccion` | `text` | NULL | Billing address |
| `uso_cfdi` | `varchar(10)` | NULL | CFDI use code |
| `fecha` | `datetime` | NOT NULL | Invoice date |
| `total` | `decimal(10,2)` | NOT NULL | Invoice total |
| `estado` | `enum('pendiente','emitida')` | DEFAULT 'pendiente' | Invoice status |

**Sources:** [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

### Invoice States

The `estado` field tracks invoice processing:

* **pendiente**: Invoice requested but not yet issued
* **emitida**: Invoice officially issued to customer

### Foreign Key Constraints

```

```

No `ON DELETE` action protects invoices from accidental deletion when orders are removed.

**Sources:** [database/american_food L462-L463](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L462-L463)

### Indexes

| Index | Columns | Purpose |
| --- | --- | --- |
| PRIMARY | `id` | Unique row identification |
| `pedido_id` | `pedido_id` | Optimize invoice lookups by order |

**Sources:** [database/american_food L330-L332](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L330-L332)

### Sample Data

Two invoices exist, both in `pendiente` state:

```
(1, 4, 'Pendiente', 'Pendiente', NULL, NULL, NULL, '2025-04-01 00:27:16', 35400.00, 'pendiente')
(2, 7, 'Pendiente', 'Pendiente', NULL, NULL, NULL, '2025-04-02 13:30:28', 21500.00, 'pendiente')
```

Both records have placeholder values ('Pendiente') for `nombre` and `rfc`, indicating they were created as pending requests but not yet populated with actual customer tax information.

**Sources:** [database/american_food L106-L108](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L106-L108)

---

## informacion_entrega Table

### Structure

The `informacion_entrega` table stores delivery information for online orders (`tipo_pedido='online'`).

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `int(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| `pedido_id` | `int(11)` | NOT NULL, FOREIGN KEY | References `pedidos.id` |
| `nombre` | `varchar(100)` | NOT NULL | Recipient name |
| `direccion` | `text` | NOT NULL | Delivery address |
| `telefono` | `varchar(20)` | NOT NULL | Contact phone |

**Sources:** [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

### Foreign Key Constraints

```

```

The `CASCADE` delete ensures delivery information is removed when the associated order is deleted.

**Sources:** [database/american_food L467-L469](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L467-L469)

### Indexes

| Index | Columns | Purpose |
| --- | --- | --- |
| PRIMARY | `id` | Unique row identification |
| `pedido_id` | `pedido_id` | Optimize delivery info lookups by order |

**Sources:** [database/american_food L337-L339](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L337-L339)

### Sample Data

Three delivery records exist:

```
(1, 2, 'usuario', 'Guavata', '3183038190')
(2, 3, 'usuario', 'Guavata', '3183038190')
(3, 6, 'usuario', 'Guavata', '3183038190')
```

All three belong to the same customer (username 'usuario') at the same address, corresponding to online orders #2, #3, and #6.

**Sources:** [database/american_food L128-L131](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L128-L131)

---

## Order Processing Data Flow

```

```

This diagram shows how the four order management tables integrate with the order lifecycle:

1. **detalles_pedidos**: Populated immediately after order creation
2. **informacion_entrega**: Created only for online orders
3. **pagos**: Created when payment is processed
4. **facturas**: Created optionally if customer requests invoice

**Sources:** [database/american_food L42-L132](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L132)

 [database/american_food L256-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L280)

---

## Data Integrity and Constraints

### Cascade Delete Behavior

Two tables implement cascade deletion:

| Table | Constraint | Behavior |
| --- | --- | --- |
| `detalles_pedidos` | `detalles_pedidos_ibfk_1` | ON DELETE CASCADE |
| `informacion_entrega` | `informacion_entrega_ibfk_1` | ON DELETE CASCADE |

When a `pedidos` record is deleted, all associated line items and delivery information are automatically removed.

### Protected References

Two tables prevent order deletion:

| Table | Constraint | Behavior |
| --- | --- | --- |
| `pagos` | `pagos_ibfk_1` | No ON DELETE action |
| `facturas` | `facturas_ibfk_1` | No ON DELETE action |

Orders with payment records or invoices cannot be deleted, preserving financial audit trail.

**Sources:** [database/american_food L453-L476](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L476)

### Menu Item Reference

The `detalles_pedidos_ibfk_2` constraint prevents deletion of menu items (`menu.id`) that are referenced in historical orders, ensuring order history remains intact.

**Sources:** [database/american_food L457](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L457-L457)

---

## Query Patterns

### Retrieving Complete Order Details

```

```

### Checking Payment Status

```

```

### Finding Orders Requiring Delivery

```

```

**Sources:** [database/american_food L42-L132](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L132)

---

## Storage and Performance Considerations

### AUTO_INCREMENT Values

Current AUTO_INCREMENT settings:

| Table | Next ID |
| --- | --- |
| `detalles_pedidos` | 28 |
| `pagos` | 5 |
| `facturas` | 3 |
| `informacion_entrega` | 4 |

**Sources:** [database/american_food L395-L410](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L395-L410)

### Character Set

All tables use `utf8mb4` character set with `utf8mb4_general_ci` collation, supporting full Unicode including emojis and special characters in delivery addresses and invoice information.

**Sources:** [database/american_food L48-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L48-L238)

### Decimal Precision

Financial fields use `decimal(10,2)` type:

* `detalles_pedidos.precio_unitario`
* `pagos.monto_recibido`
* `pagos.cambio`
* `facturas.total`

This provides exact decimal arithmetic for up to 99,999,999.99 (sufficient for restaurant pricing).

**Sources:** [database/american_food L47-L236](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L47-L236)