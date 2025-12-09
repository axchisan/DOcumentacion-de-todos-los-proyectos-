# Business Workflows

> **Relevant source files**
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the core business workflows in the American Food restaurant management system. It covers the complete lifecycle of orders from creation to completion, payment processing, table management, and supporting operational processes. These workflows are implemented across multiple database tables and role-specific interfaces to support both online and in-person dining experiences.

For information about user roles and permissions, see [User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control). For database schema details, see [Database Layer](/axchisan/AmericanFood/2-database-layer). For role-specific UI implementations, see [Frontend Architecture](/axchisan/AmericanFood/5-frontend-architecture).

---

## Data Model Overview

The business workflows are built on a relational data model centered around the `pedidos` table, which connects to multiple supporting tables:

**Order Processing Entities**

```

```

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

 [database/american_food L231-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L231-L238)

 [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

 [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

 [database/american_food L193-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L199)

---

## Order State Machine

The `pedidos.estado` field tracks orders through eight possible states. State transitions follow business rules based on order type and payment status:

```

```

**State Definitions**:

| Estado | Description | Applicable To | Triggers |
| --- | --- | --- | --- |
| `nuevo` | Order just created, not yet paid | Both online and presencial | Order creation |
| `pendiente_pago` | Waiting for cashier payment processing | Presencial only | Set by mesera after order taking |
| `pagado` | Payment confirmed, ready for kitchen | Both | Payment recorded in `pagos` table |
| `en_preparacion` | Kitchen is preparing food | Both | Kitchen staff action |
| `listo` | Food ready for pickup/delivery | Both | Kitchen staff action |
| `enviado` | Out for delivery | Online only | Delivery dispatch |
| `entregado` | Delivered to customer or served at table | Both | Final state |
| `cancelado` | Order cancelled before completion | Both | Manual cancellation |

**Sources**: [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

---

## Order Processing Flow

### Order Creation Workflow

The system supports two distinct order creation paths that converge at the `pedidos` table:

```

```

**Code Mapping**:

| Workflow Step | Database Operation | UI Component |
| --- | --- | --- |
| Customer adds item to cart | None (client-side only) | `.cart-item` in [css/cliente.css L179-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L224) |
| Customer submits order | `INSERT INTO pedidos` with `tipo_pedido='online'`, `cliente_id` set | Cart checkout button |
| Server takes order | `INSERT INTO pedidos` with `tipo_pedido='presencial'`, `mesa_id` set | `#newOrderModal` in [css/mesera.css L402-L468](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L468) |
| Add line items | `INSERT INTO detalles_pedidos` for each item | `#selectedItemsList` |
| Add delivery info | `INSERT INTO informacion_entrega` | Checkout form in cliente interface |
| Kitchen notification | Kitchen interface queries `pedidos` WHERE `estado IN ('nuevo', 'pagado')` | `.order-card` in [css/cocina.css L1-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L6) |

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

 [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

 [css/cliente.css L161-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L238)

 [css/mesera.css L402-L468](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L468)

 [css/cocina.css L1-L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L27)

---

## Online vs In-Person Orders

The `pedidos.tipo_pedido` field determines which workflow path an order follows:

### Comparison Table

| Aspect | Online Orders (`tipo_pedido='online'`) | In-Person Orders (`tipo_pedido='presencial'`) |
| --- | --- | --- |
| **Initiated by** | Customer (cliente role) | Server (mesera role) |
| **UI Interface** | [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css) | [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css) |
| **Required Foreign Key** | `cliente_id` references `usuarios.id` | `mesa_id` references `mesas.id` |
| **Optional Foreign Key** | `mesa_id` is NULL | `cliente_id` is NULL |
| **Delivery Info** | Required: record in `informacion_entrega` | Not applicable |
| **Payment Timing** | Can be paid online before preparation | Paid at end by cashier |
| **Payment Process** | May set `estado='pagado'` immediately | Goes through `pendiente_pago` state |
| **Final State Transition** | `listo` → `enviado` → `entregado` | `listo` → `entregado` (direct) |
| **Visible To** | Customer in order history | Cashier and server interfaces |

### Implementation Details

**Online Order Characteristics**:

* Created through `.cart-sidebar` component in [css/cliente.css L161-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L164)
* Must provide delivery information stored in `informacion_entrega` table
* `pedidos.cliente_id` populated from authenticated session
* `pedidos.mesa_id` remains NULL
* Delivery address, name, and phone stored in [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

**In-Person Order Characteristics**:

* Created through `#newOrderModal` in [css/mesera.css L402-L411](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L411)
* Must be associated with a table from `mesas`
* `pedidos.mesa_id` populated from table selection
* `pedidos.cliente_id` remains NULL (anonymous customer)
* Server tracks order through `.order-card` components in [css/mesera.css L270-L356](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L270-L356)

**Query Examples**:

To retrieve all online orders:

```

```

To retrieve all in-person orders with table info:

```

```

**Sources**: [database/american_food L260](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L260-L260)

 [database/american_food L258-L259](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L258-L259)

 [database/american_food L116-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L116-L122)

 [css/cliente.css L161-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L238)

 [css/mesera.css L402-L468](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L468)

---

## Payment Processing

### Payment Methods and Recording

The `pagos` table records payment transactions with three supported methods:

```

```

**Payment Method Specifications**:

| Method | `pagos.metodo` | `monto_recibido` | `cambio` | Calculation |
| --- | --- | --- | --- | --- |
| Cash (`efectivo`) | `'efectivo'` | Amount tendered by customer | Automatically calculated | `monto_recibido - pedidos.total` |
| Card (`tarjeta`) | `'tarjeta'` | Exact order total | NULL or 0.00 | No change given |
| Online (`online`) | `'online'` | Exact order total | NULL or 0.00 | Pre-processed payment |

**Change Calculation Logic**:

For cash payments, the system computes change using this formula:

```
cambio = monto_recibido - pedidos.total
```

Example from sample data in [database/american_food L245](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L245-L245)

:

* Order total: 48900.00
* Amount received (`monto_recibido`): 50000.00
* Change (`cambio`): 1100.00

**UI Components for Payment**:

| Component | CSS Class | File | Purpose |
| --- | --- | --- | --- |
| Payment modal | `#paymentModal` | Not in provided files | Collect payment details |
| Payment method selector | Input elements | Not in provided files | Choose efectivo/tarjeta/online |
| Amount received input | Input field | Not in provided files | Enter cash tendered |
| Change display | `#changeAmount` | [css/cajera.css L226-L228](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L226-L228) | Show calculated change |
| Payment receipt | `.payment-receipt` | [css/cajera.css L241-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L241-L250) | Display transaction summary |
| Confirmation icon | `.confirmation-icon` | [css/cajera.css L236-L239](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L236-L239) | Success indicator |

**Sources**: [database/american_food L231-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L231-L238)

 [database/american_food L244-L248](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L244-L248)

 [css/cajera.css L221-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L221-L250)

---

## Invoice Generation

Invoices can be optionally generated for completed orders through the `facturas` table:

```

```

**Invoice Fields**:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `pedido_id` | INT | Yes | Foreign key to `pedidos.id` |
| `nombre` | VARCHAR(100) | Yes | Customer/company name |
| `rfc` | VARCHAR(20) | Yes | Mexican tax ID (RFC - Registro Federal de Contribuyentes) |
| `email` | VARCHAR(100) | No | Email for electronic invoice delivery |
| `direccion` | TEXT | No | Billing address |
| `uso_cfdi` | VARCHAR(10) | No | CFDI use code for Mexican tax system |
| `fecha` | DATETIME | Yes | Invoice issuance date |
| `total` | DECIMAL(10,2) | Yes | Total amount (copied from `pedidos.total`) |
| `estado` | ENUM | Yes | `'pendiente'` (pending) or `'emitida'` (issued) |

**Invoice Lifecycle**:

1. **Creation**: Cashier opens invoice modal from `.invoices-section` in [css/cajera.css L214-L219](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L214-L219)
2. **Data Entry**: Form inputs styled by `#newInvoiceForm .form-label` in [css/cajera.css L231-L233](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L231-L233)
3. **Initial State**: Invoice created with `estado='pendiente'`
4. **Processing**: System generates formal invoice document (not shown in provided code)
5. **Completion**: Status updated to `estado='emitida'`

**Sample Data** from [database/american_food L107-L108](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L107-L108)

:

```

```

**Sources**: [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

 [database/american_food L107-L108](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L107-L108)

 [css/cajera.css L214-L233](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L214-L233)

---

## Table Management

The `mesas` table tracks restaurant tables with status, capacity, and location information:

### Table Status Tracking

Tables exist in two states controlled by `mesas.estado`:

```

```

**Table States**:

| Estado | Description | Displayed In UI | Can Accept Orders |
| --- | --- | --- | --- |
| `activa` | Available for use | Yes, in `.tables-grid` | Yes |
| `inactiva` | Out of service | No (filtered out) | No |

**Operational Status** (derived from `pedidos` table join):

| Visual Status | Condition | CSS Class | Color Indicator |
| --- | --- | --- | --- |
| Available | `estado='activa'` AND no active order | `.table-item.available` | Green border ([css/mesera.css L180-L182](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L180-L182) <br> ) |
| Occupied | `estado='activa'` AND exists order WHERE `mesa_id=this.id` AND `estado NOT IN ('entregado','cancelado')` | `.table-item.occupied` | Red border ([css/mesera.css L184-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L184-L186) <br> ) |
| Reserved | `estado='activa'` AND future reservation | `.table-item.reserved` | Yellow border ([css/mesera.css L188-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L188-L190) <br> ) |

**Sources**: [database/american_food L193-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L199)

 [css/mesera.css L125-L153](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L125-L153)

 [css/mesera.css L155-L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L155-L261)

### Location Types and Capacity

Tables are organized by physical location with different capacities:

**Location Categories** (`mesas.ubicacion`):

| Ubicacion | Description | Typical Use Case | Sample Data Count |
| --- | --- | --- | --- |
| `interior` | Indoor dining area | Standard dining | 7 tables ([database/american_food L206-L219](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L206-L219) <br> ) |
| `terraza` | Outdoor terrace/patio | Outdoor dining | 4 tables |
| `bar` | Bar/counter seating | Quick meals, drinks | 4 tables |
| `vip` | Private/VIP section | Special occasions, large parties | 3 tables |

**Capacity Management**:

The `mesas.capacidad` field specifies maximum guests per table:

```

```

**Sample Capacity Distribution**:

* Bar seating: 2 person tables
* Interior: 4-6 person tables
* Terraza: 4-10 person tables
* VIP: 6-10 person tables

**UI Representation**:

Tables display in a responsive grid layout:

* Grid component: `.tables-grid` in [css/mesera.css L156-L161](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L156-L161)
* Individual card: `.table-item` in [css/mesera.css L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L173)
* Capacity indicator: Shown in `.table-info` section
* Location badge: Displayed alongside table number
* Status visualization: Color-coded left border

**Table Assignment Process**:

```

```

**Sources**: [database/american_food L193-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L224)

 [css/mesera.css L155-L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L155-L261)

 [css/mesera.css L402-L468](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L468)

 [css/mesera.css L471-L556](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L471-L556)

---

## Kitchen Order Processing

The kitchen interface displays orders filtered by preparation state and provides tools for status updates:

### Order Display and State Visualization

```

```

**State-Specific Styling** in [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

:

| Order State | CSS Class | Border Color | Interpretation |
| --- | --- | --- | --- |
| `pagado` | `.order-card.new` | Blue (#007bff) | New order, not yet started |
| `en_preparacion` | `.order-card.preparing` | Yellow (#ffc107) | Currently being prepared |
| `listo` | `.order-card.ready` | Green (#28a745) | Ready for pickup/delivery |

**Order Card Components**:

| Component | CSS Class | Content | File Reference |
| --- | --- | --- | --- |
| Card container | `.order-card` | Full order display | [css/cocina.css L1-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L6) |
| Header section | `.order-header` | Order ID, table/customer, timestamp | [css/cocina.css L12-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L12-L19) |
| Time display | `.time-elapsed` | Minutes since order placed | [css/cocina.css L21](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L21-L21) |
| Body section | `.order-body` | List of ordered items | [css/cocina.css L22](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L22-L22) |
| Item list | `.order-items` | Menu items with quantities | [css/cocina.css L23](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L23-L23) |
| Individual item | `.order-item` | Item name, quantity, notes | [css/cocina.css L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L24-L24) |
| Quantity badge | `.item-quantity` | Bold quantity display | [css/cocina.css L25](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L25-L25) |
| Special notes | `.item-notes` | Italic, gray text | [css/cocina.css L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L26-L26) |
| Footer section | `.order-footer` | Action buttons | [css/cocina.css L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L27-L27) |
| Progress bar | `.cooking-progress` | Visual progress indicator | [css/cocina.css L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L28-L28) |
| Ready indicator | `.ready-status` | Green "LISTO" text | [css/cocina.css L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L29-L29) |

**Kitchen Workflow Actions**:

1. **New Order Arrives** (`estado='pagado'`): * Displayed with `.new` class (blue border) * "Iniciar" button visible * No progress bar shown
2. **Start Preparation** → `UPDATE pedidos SET estado='en_preparacion'`: * Card changes to `.preparing` class (yellow border) * Progress bar becomes visible * "Marcar Listo" button appears
3. **Mark Ready** → `UPDATE pedidos SET estado='listo'`: * Card changes to `.ready` class (green border) * Ready status displayed prominently * "Completar" or remove button appears
4. **Delivery/Service** → `UPDATE pedidos SET estado='entregado'`: * Order removed from kitchen display * Notification sent to appropriate staff (server or delivery)

**Sources**: [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

---

## Inventory Impact

While not fully implemented in the provided code, the `inventario` table exists to track stock levels:

**Inventory Table Structure** from [database/american_food L139-L148](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L148)

:

| Field | Type | Purpose |
| --- | --- | --- |
| `nombre` | VARCHAR(100) | Ingredient/item name |
| `categoria` | VARCHAR(50) | Classification (vegetales, carnes, etc.) |
| `cantidad` | DECIMAL(10,2) | Current stock level |
| `unidad` | ENUM | Measurement unit (kg, g, l, ml, unidades) |
| `precio_unitario` | DECIMAL(10,2) | Cost per unit |
| `proveedor` | VARCHAR(100) | Supplier name |
| `cantidad_minima` | DECIMAL(10,2) | Reorder threshold |

**Expected Workflow** (not visible in provided code):

1. When order is placed, system could deduct ingredients from inventory
2. When `cantidad` falls below `cantidad_minima`, generate reorder alert
3. Inventory adjustments logged for audit trail
4. Cost calculation based on `precio_unitario` for profit analysis

**Current Implementation**: The table exists with one sample record but no foreign key relationships to `menu` or automated deduction logic are present in the schema.

**Sources**: [database/american_food L139-L156](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L156)

---

## Workflow Integration Summary

The business workflows integrate across multiple database tables and role-specific interfaces:

**Cross-Role Workflow Matrix**:

| Workflow Stage | Database Tables | Roles Involved | UI Components |
| --- | --- | --- | --- |
| Order Creation | `pedidos`, `detalles_pedidos`, `informacion_entrega` | Cliente (online), Mesera (presencial) | cliente.css cart, mesera.css modal |
| Payment Processing | `pagos`, `pedidos` (update estado) | Cajera | cajera.css payment modal |
| Invoice Generation | `facturas` | Cajera | cajera.css invoice form |
| Kitchen Preparation | `pedidos` (update estado) | Cocinero | cocina.css order cards |
| Table Management | `mesas`, `pedidos` | Mesera | mesera.css tables grid |
| Delivery Tracking | `informacion_entrega`, `pedidos` | Cajera, Cliente | Status displays |
| Order Completion | `pedidos` (final estado update) | Cocinero, Mesera, Cajera | All interfaces |

**State Transition Authority**:

| Estado | Who Can Set | When | Interface |
| --- | --- | --- | --- |
| `nuevo` | System | On order creation | Automatic |
| `pendiente_pago` | Mesera | After taking in-person order | mesera.css |
| `pagado` | Cajera / System | After payment recorded | cajera.css / automatic for online |
| `en_preparacion` | Cocinero | When starting to cook | cocina.css |
| `listo` | Cocinero | When food is ready | cocina.css |
| `enviado` | System / Delivery | When out for delivery | Not in provided code |
| `entregado` | Mesera / System | When served or delivered | mesera.css / automatic |
| `cancelado` | Admin / Cajera | Before completion | Not in provided code |

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [css/cajera.css L98-L211](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L98-L211)

 [css/cliente.css L160-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L160-L238)

 [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/mesera.css L55-L677](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L55-L677)