# Payment Processing

> **Relevant source files**
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the payment processing system in the American Food restaurant management system. It covers the data structures (`pagos` and `facturas` tables), supported payment methods, change calculation logic, invoice generation, and the cashier interface components used to process payments.

For information about the complete order lifecycle, see [Order Processing Flow](/axchisan/AmericanFood/8.1-order-processing-flow). For details on the cashier role and permissions, see [Role Definitions](/axchisan/AmericanFood/7.1-role-definitions).

---

## Overview

The payment processing system handles financial transactions for both in-person (presencial) and online orders. Payments are managed primarily by cashier (cajera) role users through a dedicated interface. The system supports three payment methods, automatically calculates change for cash transactions, and optionally generates invoices (facturas) for orders.

**Sources:** [database/american_food L228-L249](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L249)

---

## Payment Data Model

### The pagos Table

The `pagos` table stores all payment transaction records with the following structure:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique payment identifier |
| `pedido_id` | INT(11) | NOT NULL, FOREIGN KEY → pedidos(id) | References the order being paid |
| `metodo` | ENUM | NOT NULL, VALUES: 'efectivo', 'tarjeta', 'online' | Payment method used |
| `monto_recibido` | DECIMAL(10,2) | NULL | Amount received from customer (cash only) |
| `cambio` | DECIMAL(10,2) | NULL | Change given to customer (cash only) |
| `fecha_hora` | DATETIME | NOT NULL | Timestamp of payment completion |

The table establishes a 1:1 relationship with the `pedidos` table through the `pedido_id` foreign key constraint.

**Sources:** [database/american_food L228-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L238)

### Sample Payment Records

```
ID | pedido_id | metodo    | monto_recibido | cambio  | fecha_hora
---|-----------|-----------|----------------|---------|-------------------
1  | 1         | efectivo  | 50000.00      | 1100.00 | 2025-03-30 22:58:47
2  | 4         | efectivo  | 40000.00      | 4600.00 | 2025-04-01 00:27:16
3  | 7         | efectivo  | 30000.00      | 8500.00 | 2025-04-02 13:30:28
4  | 5         | efectivo  | 20000.00      | 4500.00 | 2025-04-02 13:31:44
```

**Sources:** [database/american_food L244-L248](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L244-L248)

---

## Payment Methods

### Supported Payment Types

The system supports three payment methods defined in the `pagos.metodo` ENUM field:

| Method | Value | Description | Change Calculation |
| --- | --- | --- | --- |
| Cash | `efectivo` | Physical cash payment | Required: `monto_recibido` and `cambio` fields populated |
| Card | `tarjeta` | Credit/debit card payment | Not applicable: `monto_recibido` and `cambio` are NULL |
| Online | `online` | Digital payment (online orders) | Not applicable: `monto_recibido` and `cambio` are NULL |

### Payment Method Constraints

* **Cash payments** (`efectivo`): Must record `monto_recibido` (amount received) and calculate `cambio` (change)
* **Card/Online payments** (`tarjeta`, `online`): The `monto_recibido` and `cambio` fields remain NULL as exact amounts are processed

The `pedidos` table also maintains a `metodo_pago` VARCHAR(50) field that mirrors the payment method for quick reference.

**Sources:** [database/american_food L234](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L234-L234)

 [database/american_food L265](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L265-L265)

---

## Change Calculation

### Mathematical Process

For cash (`efectivo`) payments, the system calculates change using the formula:

```
cambio = monto_recibido - pedidos.total
```

Where:

* `monto_recibido`: Amount of cash received from customer
* `pedidos.total`: Total cost of the order (including all items)
* `cambio`: Amount returned to customer

### Example Calculation

From order ID 7:

* Order total: 21500.00
* Amount received: 30000.00
* Change calculated: 30000.00 - 21500.00 = 8500.00

**Sources:** [database/american_food L247](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L247-L247)

 [database/american_food L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L279-L279)

---

## Invoice Generation

### The facturas Table

The `facturas` table stores invoice (factura) records for orders requiring fiscal documentation:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique invoice identifier |
| `pedido_id` | INT(11) | NOT NULL, FOREIGN KEY → pedidos(id) | References the paid order |
| `nombre` | VARCHAR(100) | NOT NULL | Customer/business name |
| `rfc` | VARCHAR(20) | NOT NULL | Tax registration number (Mexican RFC) |
| `email` | VARCHAR(100) | NULL | Email for invoice delivery |
| `direccion` | TEXT | NULL | Billing address |
| `uso_cfdi` | VARCHAR(10) | NULL | CFDI usage code (Mexican tax code) |
| `fecha` | DATETIME | NOT NULL | Invoice issue date/time |
| `total` | DECIMAL(10,2) | NOT NULL | Invoice total (matches order total) |
| `estado` | ENUM | DEFAULT 'pendiente', VALUES: 'pendiente', 'emitida' | Invoice status |

**Sources:** [database/american_food L86-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L86-L100)

### Invoice States

The `estado` field tracks invoice generation progress:

* **`pendiente`**: Invoice requested but not yet generated/issued
* **`emitida`**: Invoice successfully generated and delivered

### Placeholder Invoice Records

The system creates placeholder invoices with "Pendiente" values when payment is processed but full customer invoice details are not yet provided:

```

```

**Sources:** [database/american_food L107-L108](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L107-L108)

---

## Cashier Interface Components

### Payment Modal Styles

The cashier interface (`cajera.css`) provides styled components for payment processing:

#### Payment Details Section

Located at [css/cajera.css L221-L228](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L221-L228)

 the `.payment-details-section` provides the container for payment input forms within a modal dialog.

#### Change Amount Display

The `#changeAmount` input field [css/cajera.css L226-L228](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L226-L228)

 displays the calculated change with a distinct background color (`#f5f5f5`) to indicate it's read-only/calculated.

#### Payment Confirmation Modal

Styled components for post-payment confirmation:

* `.confirmation-icon`: Large (3rem) green (#28a745) checkmark icon [css/cajera.css L236-L239](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L236-L239)
* `.payment-receipt`: Receipt display container with light background (#f9f9f9) [css/cajera.css L241-L246](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L241-L246)

**Sources:** [css/cajera.css L221-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L221-L250)

### Order Card Components

The cashier sees orders in card format with payment actions:

* `.order-card`: Container for each order [css/cajera.css L127-L133](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L127-L133)
* `.order-footer`: Contains total and action buttons [css/cajera.css L172-L176](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L172-L176)
* `.order-actions`: Button group for payment processing [css/cajera.css L187-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L187-L190)

**Sources:** [css/cajera.css L127-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L127-L190)

---

## Payment Processing Workflow

### Complete Payment Flow

```

```

**Sources:** [css/cajera.css L221-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L221-L250)

 [database/american_food L228-L249](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L249)

 [database/american_food L86-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L86-L100)

---

## Database Relationships

### Payment and Invoice Relationships to Orders

```

```

**Key Constraints:**

* `pagos.pedido_id` → `pedidos.id`: Payment must reference valid order
* `facturas.pedido_id` → `pedidos.id`: Invoice must reference valid order
* Each order can have at most one payment record
* Each order can have at most one invoice record
* Payment is typically created when order estado changes to 'pagado'

**Sources:** [database/american_food L361-L365](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L361-L365)

 [database/american_food L461-L463](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L461-L463)

 [database/american_food L472-L475](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L472-L475)

---

## Cashier UI Component Mapping

### UI Elements to Data Fields

```

```

**Component Descriptions:**

| UI Component | CSS Selector | Maps To Database | Purpose |
| --- | --- | --- | --- |
| Order Card | `.order-card` | `pedidos` record | Displays order awaiting payment |
| Order Header | `.order-header` | `pedidos.id`, `pedidos.fecha_hora` | Shows order identifier and time |
| Order Footer | `.order-footer` | `pedidos.total` | Displays amount to be paid |
| Payment Details Section | `.payment-details-section` | `pagos` record | Container for payment inputs |
| Method Input | (form select) | `pagos.metodo` | Selects payment method |
| Amount Received | (form input) | `pagos.monto_recibido` | Cash amount from customer |
| Change Display | `#changeAmount` | `pagos.cambio` | Calculated change (read-only) |
| Invoice Form | `#newInvoiceForm` | `facturas` record | Optional invoice generation |
| Confirmation Icon | `.confirmation-icon` | N/A | Visual payment success indicator |
| Payment Receipt | `.payment-receipt` | `pagos` record | Transaction summary display |

**Sources:** [css/cajera.css L127-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L127-L250)

 [database/american_food L228-L249](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L249)

 [database/american_food L86-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L86-L100)

---

## Payment State Transitions

### Order Estado Changes During Payment

```

```

The payment process occurs when an order transitions from `pendiente_pago` to `pagado` estado. This triggers:

1. Creation of `pagos` record with transaction details
2. Update of `pedidos.estado` to 'pagado'
3. Update of `pedidos.metodo_pago` to match `pagos.metodo`
4. Optional creation of `facturas` record if invoice requested

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

---

## Payment Processing Constraints

### Business Rules

1. **Payment Required Before Delivery**: Orders must have estado='pagado' and a corresponding `pagos` record before moving to later estados
2. **Single Payment Per Order**: Foreign key constraint ensures one-to-one relationship between `pedidos` and `pagos`
3. **Change Only for Cash**: The `monto_recibido` and `cambio` fields should only be populated when `metodo='efectivo'`
4. **Invoice Optional**: Invoices are optional; not all payments generate `facturas` records
5. **Total Consistency**: When an invoice exists, `facturas.total` should match `pedidos.total`
6. **Payment Immutability**: Once created, `pagos` records are typically not modified (append-only pattern)

### Database Constraints

```

```

**Sources:** [database/american_food L472-L475](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L472-L475)

 [database/american_food L461-L463](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L461-L463)

---

## Summary

The payment processing system provides a complete transaction management solution with:

* **Three payment methods**: efectivo (cash with change calculation), tarjeta (card), online (digital)
* **Change automation**: Automatic calculation for cash payments
* **Invoice generation**: Optional fiscal documentation via `facturas` table
* **Cashier interface**: Dedicated UI components in `cajera.css` for payment workflows
* **Data integrity**: Foreign key constraints ensuring payment-order relationships
* **Audit trail**: Immutable payment records with timestamps

The system maintains a clear separation between payment processing (`pagos`) and invoice generation (`facturas`), allowing flexible fiscal documentation while ensuring all transactions are properly recorded.

**Sources:** [database/american_food L228-L249](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L249)

 [database/american_food L86-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L86-L100)

 [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)