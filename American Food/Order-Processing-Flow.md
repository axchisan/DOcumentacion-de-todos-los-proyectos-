# Order Processing Flow

> **Relevant source files**
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the complete order lifecycle in the American Food restaurant management system, from order creation through all processing states to final completion. It covers the technical implementation of order state management, database schema, and how different user roles interact with orders throughout their lifecycle.

For information about the distinctions between online and in-person order paths, see [Online vs In-Person Orders](/axchisan/AmericanFood/8.2-online-vs-in-person-orders). For payment processing details, see [Payment Processing](/axchisan/AmericanFood/8.3-payment-processing). For table assignment and management, see [Table Management](/axchisan/AmericanFood/8.4-table-management).

---

## Order States and Lifecycle

The system tracks orders through eight distinct states defined in the `pedidos` table `estado` field:

| State | Description | Typical Duration | Next State(s) |
| --- | --- | --- | --- |
| `nuevo` | Order created, awaiting payment | Minutes | `pendiente_pago`, `pagado` |
| `pendiente_pago` | Payment required before processing | Until payment | `pagado`, `cancelado` |
| `pagado` | Payment confirmed, ready for kitchen | Seconds | `en_preparacion` |
| `en_preparacion` | Kitchen actively preparing order | 10-30 minutes | `listo` |
| `listo` | Food ready for pickup/delivery | Minutes | `enviado`, `entregado` |
| `enviado` | Order dispatched for delivery (online only) | Varies | `entregado` |
| `entregado` | Order completed and delivered/served | Terminal | None |
| `cancelado` | Order cancelled before completion | Terminal | None |

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

---

## Order State Machine

```

```

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

---

## Database Schema

### Core Order Table

The `pedidos` table serves as the central hub for order processing:

| Field | Type | Purpose |
| --- | --- | --- |
| `id` | INT(11) | Primary key |
| `mesa_id` | INT(11) | Foreign key to `mesas` (presencial orders only) |
| `cliente_id` | INT(11) | Foreign key to `usuarios` (online orders only) |
| `tipo_pedido` | ENUM | Either `'online'` or `'presencial'` |
| `estado` | ENUM | Current state (8 possible values) |
| `fecha_hora` | DATETIME | Order creation timestamp |
| `total` | DECIMAL(10,2) | Order total amount |
| `notas` | TEXT | Customer notes/special instructions |
| `metodo_pago` | VARCHAR(50) | Payment method (legacy field) |

**Key Constraints:**

* Either `mesa_id` OR `cliente_id` is set, never both
* `mesa_id` is NULL for online orders
* `cliente_id` is NULL for presencial orders
* Foreign keys use `ON DELETE SET NULL` to preserve order history

**Sources:** [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L478-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L478-L482)

---

### Related Tables

```

```

**Relationship Cardinality:**

* **1:N** - One order has many line items (`detalles_pedidos`)
* **1:1** - One order has at most one payment record (`pagos`)
* **1:1** - One order has at most one invoice (`facturas`)
* **1:1** - One order has at most one delivery info (`informacion_entrega`)
* **N:1** - Many orders reference one menu item
* **N:1** - Many orders can be assigned to one table (over time)
* **N:1** - Many orders can be placed by one customer

**Sources:** [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

 [database/american_food L231-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L231-L238)

 [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

 [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

---

## Order Creation Process

### Creation Flow by User Role

```

```

**Sources:** [css/mesera.css L402-L469](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L469)

 [css/cliente.css L132-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L132-L238)

 [database/american_food L272-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L280)

---

## Kitchen Processing

### Kitchen Interface States

The `cocina.css` interface visualizes order states using CSS classes on the `order-card` component:

| CSS Class | Corresponds to Estado | Visual Indicator | User Action |
| --- | --- | --- | --- |
| `.order-card.new` | `nuevo` or `pagado` | Blue left border (5px) | Click "Start Preparation" |
| `.order-card.preparing` | `en_preparacion` | Yellow left border (5px) | Monitor cooking progress |
| `.order-card.ready` | `listo` | Green left border (5px) | Mark as served/dispatched |

**Component Structure:**

```
.order-card
├── .order-header
│   ├── Order ID
│   └── .order-time (.time-elapsed)
├── .order-body
│   ├── .order-items
│   │   └── .order-item (quantity + name)
│   └── .item-notes (if notas present)
└── .order-footer
    ├── .cooking-progress (for .preparing)
    └── .ready-status (for .ready)
```

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

### State Transition in Kitchen

```

```

**Sources:** [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

 [css/cocina.css L22-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L22-L29)

 [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

---

## Server and Cashier Processing

### Server Interface (mesera.css)

Servers interact with orders through multiple sections:

**1. Table Management (`tables-section`)**

* Displays `tables-grid` showing all tables from `mesas` table
* Each `table-item` shows current order status
* Clicking table opens `tableDetailsModal` with current order details

**2. Active Orders (`orders-section`)**

* Lists all orders WHERE `estado IN ('nuevo', 'en_preparacion', 'listo')`
* Each `order-card` displays: * Order header with table number and time * Order items from `detalles_pedidos` JOIN `menu` * Total from `pedidos.total` * Action buttons for order management

**3. Order Actions:**

* **"Take Payment"** - Redirects to cashier for payment processing
* **"View Details"** - Shows full order information
* **"Cancel Order"** - Sets `estado = 'cancelado'`

**Sources:** [css/mesera.css L155-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L155-L262)

 [css/mesera.css L264-L356](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L264-L356)

---

### Cashier Interface (cajera.css)

The cashier interface handles payment and order completion:

**Payment Processing Flow:**

1. Cashier selects order from `orders-section`
2. Opens payment modal
3. Enters payment details: * `metodo` (efectivo, tarjeta, online) * `monto_recibido` (amount received) * Calculates `cambio` (change) automatically
4. Submits payment: * INSERT INTO `pagos` table * UPDATE `pedidos` SET `estado = 'pagado'`
5. Optionally generates invoice: * Opens invoice modal * Collects customer tax information (nombre, RFC, email) * INSERT INTO `facturas` table with `estado = 'pendiente'`

**Sources:** [database/american_food L231-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L231-L238)

 [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

---

## Order Completion

### Final State Transitions

```

```

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 [database/american_food L272-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L280)

---

## Order Data Example

### Sample Order Record

The following shows a complete order in the database with all related records:

**pedidos table (id=1):**

```yaml
id: 1
mesa_id: 13
cliente_id: NULL
tipo_pedido: 'presencial'
estado: 'entregado'
fecha_hora: '2025-03-30 22:31:05'
total: 48900.00
notas: NULL
metodo_pago: 'efectivo'
```

**detalles_pedidos table (for pedido_id=1):**

```
id=1: pedido_id=1, plato_id=5, cantidad=1, precio_unitario=8000.00
id=2: pedido_id=1, plato_id=4, cantidad=1, precio_unitario=13400.00
id=3: pedido_id=1, plato_id=6, cantidad=1, precio_unitario=7500.00
id=4: pedido_id=1, plato_id=9, cantidad=1, precio_unitario=6000.00
id=5: pedido_id=1, plato_id=8, cantidad=1, precio_unitario=12000.00
id=6: pedido_id=1, plato_id=7, cantidad=1, precio_unitario=2000.00
```

**pagos table (for pedido_id=1):**

```yaml
id: 1
pedido_id: 1
metodo: 'efectivo'
monto_recibido: 50000.00
cambio: 1100.00
fecha_hora: '2025-03-30 22:58:47'
```

**Total Calculation:** 8000 + 13400 + 7500 + 6000 + 12000 + 2000 = 48,900.00

**Sources:** [database/american_food L272-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L280)

 [database/american_food L54-L61](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L54-L61)

 [database/american_food L244-L245](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L244-L245)

---

## Role-Specific Order Views

### Query Patterns by Role

| Role | Query Focus | WHERE Clause | Interface |
| --- | --- | --- | --- |
| **Cliente** | Own orders | `cliente_id = {current_user_id}` | `cliente.css` order-history |
| **Mesera** | Active table orders | `tipo_pedido = 'presencial' AND estado != 'entregado'` | `mesera.css` orders-section |
| **Cocinero** | Kitchen queue | `estado IN ('nuevo', 'pagado', 'en_preparacion', 'listo')` | `cocina.css` order-card |
| **Cajera** | Payment required | `estado IN ('nuevo', 'pendiente_pago')` OR all for history | `cajera.css` orders-section |
| **Admin** | All orders | No filter | Admin interface |

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 [css/cliente.css L240-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L240-L307)

 [css/mesera.css L264-L356](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L264-L356)

 [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

## Order State Summary

### Estado Field Statistics from Sample Data

| Estado | Count | Percentage | Typical Duration |
| --- | --- | --- | --- |
| `entregado` | 3 | 42.9% | Terminal state |
| `listo` | 4 | 57.1% | Awaiting final delivery |
| Other states | 0 | 0% | Transient |

**Note:** Sample data shows 7 total orders, with most in completed or near-completed states, indicating efficient order processing.

**Sources:** [database/american_food L272-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L280)

---

## Error Handling and Edge Cases

### Cancelled Orders

Orders can be cancelled at any point before `estado = 'entregado'`:

1. **Before Payment** - Direct cancellation, no payment record created
2. **After Payment** - Requires refund process (not visible in current schema)
3. **During Preparation** - Kitchen notified to stop work
4. **When Ready** - Rare but possible for customer no-show scenarios

Cancelled orders retain all related records (detalles_pedidos, pagos if exists) for audit purposes due to foreign key constraints.

### Orphaned Orders

Foreign key constraints use `ON DELETE SET NULL`:

* If a table is deleted (`mesas`), `mesa_id` becomes NULL but order persists
* If a customer is deleted (`usuarios`), `cliente_id` becomes NULL but order persists
* This preserves order history even when entities are removed

**Sources:** [database/american_food L478-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L478-L482)