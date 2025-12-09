# Online vs In-Person Orders

> **Relevant source files**
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document explains the dual-track order processing system in the American Food restaurant management application. The system supports two distinct order types: **online orders** (placed by customers for delivery or pickup) and **in-person orders** (taken by servers for dine-in customers). This page details how these order types are distinguished in the database, what fields are required for each, and how they diverge in their data flow and user interfaces.

For the complete order lifecycle including state transitions, see [Order Processing Flow](/axchisan/AmericanFood/8.1-order-processing-flow). For payment processing details, see [Payment Processing](/axchisan/AmericanFood/8.3-payment-processing). For table management specifics, see [Table Management](/axchisan/AmericanFood/8.4-table-management).

---

## Order Type Distinction

The system distinguishes between online and in-person orders using the `tipo_pedido` field in the `pedidos` table. This enum field accepts two values:

| Order Type | `tipo_pedido` Value | Primary Key Field | Associated Table |
| --- | --- | --- | --- |
| Online Orders | `'online'` | `cliente_id` (NOT NULL) | `informacion_entrega` |
| In-Person Orders | `'presencial'` | `mesa_id` (NOT NULL) | `mesas` |

**Field Exclusivity**: The system enforces a mutual exclusivity pattern where online orders populate `cliente_id` and leave `mesa_id` as NULL, while in-person orders populate `mesa_id` and leave `cliente_id` as NULL. This prevents orders from being associated with both a table and a customer account simultaneously.

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

---

## Database Schema for Orders

The `pedidos` table structure accommodates both order types through nullable foreign key fields:

```

```

**Key Observations**:

* `mesa_id` references the `mesas` table and is populated only for `tipo_pedido='presencial'`
* `cliente_id` references the `usuarios` table and is populated only for `tipo_pedido='online'`
* `informacion_entrega` table exists exclusively for online orders requiring delivery
* Both foreign keys use `ON DELETE SET NULL` to preserve order history if tables or users are deleted

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L190-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L190-L199)

 [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

 [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

---

## Online Order Path

Online orders are placed by customers through the `cliente.css` interface. These orders are intended for delivery or pickup and do not involve table assignments.

### Data Flow for Online Orders

```

```

### Online Order Required Fields

| Field | Table | Purpose | Example Value |
| --- | --- | --- | --- |
| `cliente_id` | `pedidos` | Links order to customer account | `4` |
| `tipo_pedido` | `pedidos` | Marks as online order | `'online'` |
| `mesa_id` | `pedidos` | Must be NULL | `NULL` |
| `nombre` | `informacion_entrega` | Delivery recipient name | `'usuario'` |
| `direccion` | `informacion_entrega` | Delivery address | `'Guavata'` |
| `telefono` | `informacion_entrega` | Contact number | `'3183038190'` |

### Customer Interface Components

The `cliente.css` stylesheet defines the customer-facing interface for online orders:

**Menu Browsing**:

* `.menu-item-card` [css/cliente.css L109-L118](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L109-L118)  - Displays menu items with images
* `.menu-categories` [css/cliente.css L93-L95](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L93-L95)  - Category filtering navigation
* `.nav-pills` [css/cliente.css L97-L107](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L97-L107)  - Category selection tabs

**Shopping Cart**:

* `.cart-sidebar` [css/cliente.css L161-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L164)  - Sticky sidebar for cart
* `.cart-items` [css/cliente.css L166-L170](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L166-L170)  - Scrollable item list
* `.cart-item` [css/cliente.css L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L185)  - Individual cart item display
* `.cart-total` [css/cliente.css L231-L237](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L231-L237)  - Total price calculation

**Order Management**:

* `.order-history` [css/cliente.css L240-L243](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L240-L243)  - Past orders display
* `.order-item` [css/cliente.css L245-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L245-L250)  - Individual order card

**Sources**: [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

 [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

 [database/american_food L128-L131](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L128-L131)

---

## In-Person Order Path

In-person orders are created by servers through the `mesera.css` interface. These orders are linked to physical tables in the restaurant and do not require customer accounts.

### Data Flow for In-Person Orders

```

```

### In-Person Order Required Fields

| Field | Table | Purpose | Example Value |
| --- | --- | --- | --- |
| `mesa_id` | `pedidos` | Links order to table | `13` |
| `tipo_pedido` | `pedidos` | Marks as in-person order | `'presencial'` |
| `cliente_id` | `pedidos` | Must be NULL | `NULL` |
| `metodo_pago` | `pedidos` | Payment method used | `'efectivo'` |

### Server Interface Components

The `mesera.css` stylesheet defines the server interface for managing in-person orders:

**Table Management**:

* `.tables-grid` [css/mesera.css L156-L161](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L156-L161)  - Grid layout for all tables
* `.table-item` [css/mesera.css L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L173)  - Individual table card
* `.table-icon` [css/mesera.css L192-L202](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L192-L202)  - Visual table representation
* `.table-number` [css/mesera.css L209-L223](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L209-L223)  - Table identifier badge
* `.table-status` [css/mesera.css L241-L255](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L241-L255)  - Available/occupied/reserved indicator

**Order Creation**:

* `.menu-items-grid` [css/mesera.css L359-L363](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L359-L363)  - Menu display for order-taking
* `#newOrderModal` [css/mesera.css L402-L404](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L404)  - Modal dialog for new orders
* `.order-items-form` [css/mesera.css L417-L420](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L417-L420)  - Item selection interface

**Order Tracking**:

* `.orders-list` [css/mesera.css L264-L268](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L264-L268)  - Active orders display
* `.order-card` [css/mesera.css L270-L276](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L270-L276)  - Individual order details
* `.order-header` [css/mesera.css L282-L287](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L282-L287)  - Order metadata display

**Sources**: [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

 [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

 [database/american_food L205-L223](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L205-L223)

---

## Field Population Patterns

The following table illustrates how key fields are populated differently based on order type:

| Field | Online Orders | In-Person Orders |
| --- | --- | --- |
| `tipo_pedido` | `'online'` | `'presencial'` |
| `mesa_id` | `NULL` | `1-18` (references `mesas.id`) |
| `cliente_id` | `1-7` (references `usuarios.id` where `rol='cliente'`) | `NULL` |
| `metodo_pago` | Set during payment or NULL initially | Typically `'efectivo'` or `'tarjeta'` |
| `notas` | Customer delivery instructions | Server notes about table/order |
| Additional Tables | `informacion_entrega` required | None required |

### Actual Data Examples

**Online Order Example** (Order ID 2):

```yaml
pedido_id: 2
mesa_id: NULL
cliente_id: 4
tipo_pedido: 'online'
estado: 'listo'
total: 49764.00
```

Associated `informacion_entrega` record:

```yaml
pedido_id: 2
nombre: 'usuario'
direccion: 'Guavata'
telefono: '3183038190'
```

**In-Person Order Example** (Order ID 1):

```yaml
pedido_id: 1
mesa_id: 13
cliente_id: NULL
tipo_pedido: 'presencial'
estado: 'entregado'
metodo_pago: 'efectivo'
total: 48900.00
```

No `informacion_entrega` record exists for this order.

**Sources**: [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

 [database/american_food L128-L131](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L128-L131)

---

## Convergence Point: Order Processing

After creation, both online and in-person orders converge into the unified order processing pipeline. The distinction remains in the `tipo_pedido` field, but subsequent operations treat both types identically:

```

```

### Shared Processing Components

Both order types utilize:

* **Kitchen Display**: `cocina.css` interface shows all orders regardless of type [Page 5.3.3](/axchisan/AmericanFood/5.3.3-kitchen-interface-(cocina.css))
* **Order Items**: `detalles_pedidos` table stores line items identically for both types
* **Payment Recording**: `pagos` table handles payments without distinguishing order type
* **Invoice Generation**: `facturas` table can be used for both online and in-person orders
* **State Management**: The `estado` enum field tracks order progress identically

The primary operational difference is the fulfillment destination: online orders reference `informacion_entrega` for delivery, while in-person orders reference `mesa_id` for table service.

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

 [database/american_food L228-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L238)

 [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

---

## Query Patterns for Filtering Order Types

Applications can filter orders by type using the `tipo_pedido` field:

**Retrieve All Online Orders**:

```

```

**Retrieve All In-Person Orders**:

```

```

**Get Online Order with Delivery Info**:

```

```

**Get In-Person Order with Table Info**:

```

```

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

 [database/american_food L190-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L190-L199)

---

## Interface Access Patterns

The system enforces role-based access to order creation:

| User Role | Can Create Online Orders | Can Create In-Person Orders | Interface |
| --- | --- | --- | --- |
| `cliente` | Yes (own account) | No | `cliente.css` |
| `mesera` | No | Yes | `mesera.css` |
| `cajera` | No | No (view/process only) | `cajera.css` |
| `cocinero` | No | No (view only) | `cocina.css` |
| `admin` | Yes (all accounts) | Yes | Combined access |

Customers (`rol='cliente'`) authenticate through the login system and can only create orders associated with their own `cliente_id`. Servers (`rol='mesera'`) can create orders for any table but cannot create online orders for customer accounts.

**Sources**: [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

 [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

 [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

---

## Summary

The American Food system implements a flexible dual-track order architecture:

* **Database Differentiation**: The `tipo_pedido` enum field and mutually exclusive `mesa_id`/`cliente_id` foreign keys distinguish order types at the data layer
* **Interface Segregation**: Separate CSS-defined interfaces (`cliente.css` for online, `mesera.css` for in-person) provide role-optimized workflows
* **Data Requirements**: Online orders require `informacion_entrega` records; in-person orders require `mesas` references
* **Unified Processing**: After creation, both order types flow through the same kitchen preparation and payment processing pipelines
* **Role Enforcement**: User roles determine which order type can be created through access control

This architecture allows the restaurant to handle both delivery/pickup customers and dine-in customers using a single database schema and shared business logic, while maintaining clear separation at the point of order entry.