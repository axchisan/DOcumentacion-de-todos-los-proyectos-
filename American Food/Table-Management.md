# Table Management

> **Relevant source files**
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the table management system in the American Food restaurant application. It covers the database schema for physical restaurant tables, their operational states, location categorization, capacity tracking, and the UI components used by servers (meseras) to manage table assignments and status.

For information about order processing workflows that utilize table assignments, see [Order Processing Flow](/axchisan/AmericanFood/8.1-order-processing-flow). For details about the server interface that provides the primary table management UI, see [Server Interface (mesera.css)](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css)).

**Sources**: [database/american_food L190-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L190-L224)

 [css/mesera.css L1-L677](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L677)

---

## Database Schema

### mesas Table Structure

The `mesas` table stores the configuration and current state of all physical tables in the restaurant:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `int(11)` | PRIMARY KEY, AUTO_INCREMENT | Unique table identifier |
| `numero` | `int(11)` | NOT NULL | Table number displayed to staff |
| `capacidad` | `int(11)` | NOT NULL | Maximum seating capacity |
| `ubicacion` | `enum` | NOT NULL | Location type: 'interior', 'terraza', 'bar', 'vip' |
| `estado` | `enum` | DEFAULT 'activa' | Operational status: 'activa', 'inactiva' |

The table currently contains 18 records representing tables numbered 1-18 with varying capacities (2-10 seats) distributed across different restaurant zones.

**Sources**: [database/american_food L190-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L190-L199)

---

## Table States and Operational Status

### Estado Field Values

The `estado` field in the `mesas` table tracks the operational availability of tables:

* **`activa`**: Table is operational and available for seating customers
* **`inactiva`**: Table is temporarily unavailable (maintenance, reserved for events, closed section)

As of the current database snapshot, 15 tables are active and 3 tables (tables 6, 7, 12, 18) are inactive.

### Relationship with Order Assignment

The `estado` field determines whether a table appears in the available table selection for servers. Only tables with `estado='activa'` should be assignable to new orders. This is independent of whether the table is currently occupied by an active order.

**Distinction**: The `estado` field represents the table's operational configuration, not its current occupancy. A table can be `activa` but occupied by a current order, or `activa` and available for seating.

**Sources**: [database/american_food L198-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L198-L199)

 [database/american_food L205-L223](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L205-L223)

---

## Table Attributes and Location Types

### Table Numbering

The `numero` field provides a human-readable identifier displayed to staff and customers. In the current configuration, tables are numbered sequentially from 1 to 18, though the numbering scheme can be customized to match physical table labels.

### Capacity Management

The `capacidad` field stores the maximum number of guests that can be seated at each table. Current capacity distribution:

* **2 seats**: Tables 3, 7, 11, 15 (bar seating)
* **4 seats**: Tables 1, 5, 9, 13, 17 (standard tables)
* **6 seats**: Tables 2, 6, 10, 14, 18 (larger tables)
* **8 seats**: Tables 4, 12 (group seating)
* **10 seats**: Tables 8, 16 (large party seating)

### Location Categories

The `ubicacion` enum categorizes tables by restaurant zone:

| Location | Spanish | Description | Example Tables |
| --- | --- | --- | --- |
| `interior` | Interior | Main dining room | 1, 2, 6, 9, 13, 14, 18 |
| `terraza` | Terraza | Outdoor patio/terrace | 4, 8, 12, 17 |
| `bar` | Bar | Bar seating area | 3, 7, 11, 15 |
| `vip` | VIP | Private/premium section | 5, 10, 16 |

Location categories enable filtering in the server interface and can inform business analytics about zone utilization.

**Sources**: [database/american_food L194-L198](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L194-L198)

 [database/american_food L205-L223](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L205-L223)

---

## Table Assignment to Orders

### Database Relationship

Tables link to orders through the `pedidos` table's `mesa_id` field:

```

```

**Foreign Key Constraint**: The `mesa_id` field in `pedidos` references `mesas.id` with `ON DELETE SET NULL`, meaning if a table is deleted, existing orders retain their data but the table reference becomes null.

### Presencial vs Online Orders

The `mesa_id` field is only populated for presencial (in-person) orders:

* **Presencial orders** (`tipo_pedido='presencial'`): Must have `mesa_id` set, representing the physical table where customers are seated
* **Online orders** (`tipo_pedido='online'`): Have `mesa_id=NULL` since they are delivery/takeout orders with no table assignment

Current order distribution shows tables 2, 3, and 13 have been used for presencial orders.

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

 [database/american_food L479-L482](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L479-L482)

---

## User Interface Components

### Table Grid Layout

The server interface displays tables in a responsive grid using the `.tables-grid` component:

```
.tables-grid
├── display: grid
├── grid-template-columns: repeat(auto-fill, minmax(200px, 1fr))
├── gap: 1.5rem
└── contains multiple .table-item elements
```

The grid automatically adjusts column count based on viewport width, ensuring optimal display on various screen sizes.

**Sources**: [css/mesera.css L156-L161](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L156-L161)

---

### Table Item Component

#### Table Item Component Structure

Each table is rendered as a `.table-item` card with visual status indicators:

```

```

**Sources**: [css/mesera.css L163-L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L261)

#### Visual Status Indicators

Tables use color-coded left borders to indicate occupancy status:

| Status Class | Border Color | CSS Variable | Meaning |
| --- | --- | --- | --- |
| `.available` | Green | `--success-color` (#28a745) | Table is free and ready for seating |
| `.occupied` | Red | `--danger-color` (#dc3545) | Table currently has active order/customers |
| `.reserved` | Yellow | `--warning-color` (#ffc107) | Table is reserved for upcoming party |

The border is rendered as a 5px left border: `border-left: 5px solid var(--[color])`.

**Sources**: [css/mesera.css L180-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L180-L190)

#### Interactive Behavior

Table items respond to user interaction:

* **Hover effect**: Elevates card with `translateY(-5px)` and enhances shadow
* **Cursor**: Changes to `pointer` to indicate clickability
* **Click action**: Opens table details modal (see Modal Components section)

**Sources**: [css/mesera.css L175-L178](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L175-L178)

---

## Table Status Legend

The interface includes a status legend component (`.table-status-legend`) that displays color-coded dots with labels:

```

```

Each status dot is a 12px circle rendered with corresponding status color.

**Sources**: [css/mesera.css L126-L153](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L126-L153)

---

## Table Filtering

### Filter Button System

The interface provides filter buttons (`.btn-filter`) to show/hide tables by location:

```
.table-filters
├── .btn-filter (Interior)
├── .btn-filter (Terraza)
├── .btn-filter (Bar)
└── .btn-filter (VIP)
```

Filters use a toggle design:

* **Default state**: Light gray background (`--gray-light`)
* **Active state**: Primary color background (`--primary-color`), white text
* **Border radius**: 20px for pill-shaped appearance

**Sources**: [css/mesera.css L86-L105](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L86-L105)

---

## Modal Components

### Table Details Modal

The `#tableDetailsModal` displays comprehensive information about a selected table:

#### Modal Structure

```

```

**Sources**: [css/mesera.css L471-L555](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L471-L555)

#### Table Icon Large

The modal displays an enlarged table icon (70px diameter) with attached table number badge (30px diameter). The icon uses the same visual language as the grid items but at larger scale for improved visibility.

**Sources**: [css/mesera.css L481-L512](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L481-L512)

#### Action Buttons

The `.action-buttons` section provides table management operations:

* Flexible layout adapts to button count
* Buttons use `.btn` Bootstrap classes for consistency
* Gap of 0.5rem between buttons for visual separation

**Sources**: [css/mesera.css L529-L539](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L529-L539)

#### Current Order Display

If the table has an active order, the `#currentOrderList` displays order line items in a scrollable container (max-height: 200px).

**Sources**: [css/mesera.css L541-L555](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L541-L555)

---

### New Order Modal

The `#newOrderModal` provides the interface for creating orders and assigning them to tables:

#### Modal Header

The header uses primary color background with white text and 1.5rem title size, maintaining brand consistency.

**Sources**: [css/mesera.css L402-L415](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L415)

#### Order Item Selection

The `.order-items-form` section includes:

* Item selector dropdown (`.item-select`)
* Quantity input (`.item-quantity`)
* Price display (`.item-price` with gray background)
* Add button (`.add-item-btn`)

Each form row aligns these elements horizontally with responsive collapse on mobile.

**Sources**: [css/mesera.css L417-L435](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L417-L435)

#### Selected Items List

The `#selectedItemsList` displays chosen items in a scrollable list (max-height: 200px) with remove buttons. Each list item shows quantity, item name, and price with space-between justification.

**Sources**: [css/mesera.css L437-L459](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L437-L459)

---

## Responsive Design

### Mobile Adaptations

The table management interface includes responsive breakpoints:

#### 768px Breakpoint

At viewport widths ≤768px:

* Tables grid reduces to `minmax(150px, 1fr)` columns
* Table items stack vertically (flex-direction: column)
* Table icon receives bottom margin instead of right margin
* Text centers within table item

**Sources**: [css/mesera.css L642-L658](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L642-L658)

#### 576px Breakpoint

At viewport widths ≤576px:

* Filter buttons wrap and center-justify
* Order form rows stack vertically with gaps
* All column widths become 100%
* Add item button expands to full width

**Sources**: [css/mesera.css L679-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L679-L705)

---

## Integration with Order System

### Order Creation Flow

```

```

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

---

## Table Occupancy Tracking

### Determining Table Status

The system tracks table occupancy through order states in the `pedidos` table:

**Available**: No active orders with this `mesa_id`, OR all orders with this `mesa_id` have `estado` in ['entregado', 'cancelado']

**Occupied**: Has at least one order with this `mesa_id` where `estado` in ['nuevo', 'pendiente_pago', 'pagado', 'en_preparacion', 'listo']

**Reserved**: (Implementation-dependent) Likely managed through a separate reservation system or special order state

### Query Pattern

To determine which tables are currently occupied:

```

```

Tables with `active_orders > 0` should display as occupied in the UI.

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

---

## Table Management Best Practices

### Estado Field Usage

The `estado` field should be updated when:

* Tables require maintenance (set to 'inactiva')
* Sections of the restaurant open/close for service (toggle between 'activa'/'inactiva')
* Tables are temporarily removed from rotation

This field persists independently of current occupancy and should not be used for real-time availability tracking.

### Table Number Assignment

Table numbers should:

* Match physical signage in the restaurant
* Be unique within the system
* Remain stable over time (not reused for different physical tables)
* Use intuitive numbering schemes for staff efficiency

### Capacity Planning

The `capacidad` field enables:

* Optimal party-to-table matching
* Zone utilization analysis
* Service efficiency optimization
* Capacity constraint enforcement in reservation systems

**Sources**: [database/american_food L190-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L190-L224)

---

## CSS Variable Dependencies

The table management UI relies on the following CSS custom properties defined in the global scope:

| Variable | Default Value | Usage |
| --- | --- | --- |
| `--primary-color` | #FF6B35 | Modal headers, active filters, table number badges |
| `--success-color` | #28a745 | Available table indicators |
| `--danger-color` | #dc3545 | Occupied table indicators |
| `--warning-color` | #ffc107 | Reserved table indicators |
| `--gray-light` | #f8f9fa | Table icon backgrounds, inactive buttons |
| `--gray-medium` | #dee2e6 | Borders in lists and forms |
| `--gray-dark` | #6c757d | Secondary text (capacity, time) |
| `--dark-color` | #212529 | Primary text color |
| `--white` | #fff | Card backgrounds, modal content |

These variables must be defined in a parent stylesheet (typically `style.css`) for proper rendering.

**Sources**: [css/mesera.css L4-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L4-L15)