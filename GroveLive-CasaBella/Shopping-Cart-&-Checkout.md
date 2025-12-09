# Shopping Cart & Checkout

> **Relevant source files**
> * [Dockerfile](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile)
> * [app/models/carrito.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py)
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/static/css/carrito.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css)
> * [app/static/js/carrito.js](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js)
> * [app/templates/carrito.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html)

## Purpose and Scope

This document describes the shopping cart and checkout system for client users in Casa Bella. It covers how products and services are added to a cart, cart management operations (viewing, updating quantities, removing items), the checkout workflow, payment processing, and invoice generation.

For information about browsing products and services before adding them to cart, see [Product & Service Catalog](/GroveLive/CasaBella/5.1-product-and-service-catalog). For information about appointment booking workflow, see [Appointment Booking](/GroveLive/CasaBella/5.3-appointment-booking). For information about viewing purchase history after checkout, see [Client Dashboard & Profile](/GroveLive/CasaBella/5.4-client-dashboard-and-profile).

---

## Shopping Cart Data Model

The shopping cart system uses a polymorphic design where both products and services can be added to the same cart through a unified interface.

### Core Entities

| Entity | Model File | Primary Key | Purpose |
| --- | --- | --- | --- |
| `Carrito` | `app/models/carrito.py` | `id_carrito` | Represents a user's shopping cart session |
| `DetalleCarrito` | `app/models/detalle_carrito.py` | `id_detalle_carrito` | Individual line items in the cart |
| `Venta` | `app/models/ventas.py` | `id_venta` | Completed purchase record |
| `DetalleVenta` | `app/models/detalle_ventas.py` | `id_detalle_venta` | Line items of a completed purchase |
| `Pago` | `app/models/pagos.py` | `id_pago` | Payment method and amount record |

### Cart State Machine

```

```

**Sources:** [app/models/carrito.py L1-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L1-L13)

 [app/routes/client.py L271-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L271-L455)

### Polymorphic Item Design

The `DetalleCarrito` model supports both products and services through nullable foreign keys:

```

```

**Sources:** [app/models/carrito.py L1-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L1-L13)

 [app/routes/client.py L156-L162](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L156-L162)

---

## Cart Context Injection

The cart is injected into all client blueprint templates through a context processor, making it globally available in the navigation bar and throughout the client interface.

```

```

The `inject_carrito()` function at [app/routes/client.py L45-L50](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L45-L50)

 queries for the active cart and returns it as a template variable. This enables the cart icon in the navigation to display the number of items without requiring explicit passing in each route.

**Sources:** [app/routes/client.py L45-L50](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L45-L50)

---

## Adding Items to Cart

### Endpoint: agregar_carrito(item_id)

The `agregar_carrito()` function at [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)

 handles adding both products and services to the cart with automatic promotion application.

```

```

### Promotion Application Logic

Promotions are applied at the time of adding to cart. The system queries for active promotions using datetime comparison:

```

```

The discounted price is stored in `DetalleCarrito.precio_unitario` at [app/routes/client.py L142-L152](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L142-L152)

 This preserves the promotional price even if the promotion expires before checkout.

### Stock Validation

For products (not services), stock availability is checked at [app/routes/client.py L135-L137](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L135-L137)

:

```

```

**Sources:** [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)

---

## Cart Management

### Viewing the Cart

The `carrito()` route at [app/routes/client.py L180-L207](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L180-L207)

 renders the shopping cart with eager loading for performance:

```

```

The query uses `joinedload` at [app/routes/client.py L189-L192](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L189-L192)

 to avoid N+1 queries when rendering cart items.

**Sources:** [app/routes/client.py L180-L207](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L180-L207)

 [app/templates/carrito.html L1-L131](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L1-L131)

### Cart Template Structure

The `carrito.html` template displays cart items in a responsive grid layout:

| Section | Purpose | Lines |
| --- | --- | --- |
| Header | Title and description | [app/templates/carrito.html L7-L9](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L7-L9) |
| Flash Messages | Success/error alerts | [app/templates/carrito.html L10-L19](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L10-L19) |
| Cart Items Loop | Display each `DetalleCarrito` | [app/templates/carrito.html L23-L55](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L23-L55) |
| Total Display | Sum of all subtotals | [app/templates/carrito.html L57-L59](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L57-L59) |
| Checkout Button | Link to `procesar_compra` | [app/templates/carrito.html L60-L62](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L60-L62) |
| Empty State | Message when no items | [app/templates/carrito.html L64-L68](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L64-L68) |

---

## Updating Cart Quantities

### AJAX Endpoint: actualizar_cantidad(detalle_id)

The quantity update system uses AJAX to provide instant feedback without page reload:

```

```

The endpoint at [app/routes/client.py L228-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L228-L269)

 validates:

1. User ownership of the cart item
2. Stock availability for products (not services)
3. Minimum quantity of 1
4. Maximum quantity of 10 for services (arbitrary limit)

### Frontend Integration

The cart template includes inline JavaScript at [app/templates/carrito.html L74-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L74-L108)

 that:

1. Attaches click listeners to increment/decrement buttons
2. Sends fetch requests to `actualizar_cantidad`
3. Updates displayed quantity and subtotal
4. Recalculates the cart total

**Sources:** [app/routes/client.py L228-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L228-L269)

 [app/templates/carrito.html L74-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L74-L108)

---

## Removing Items from Cart

### AJAX Endpoint: eliminar_del_carrito(detalle_id)

```

```

The deletion endpoint at [app/routes/client.py L209-L226](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L209-L226)

 returns JSON to enable smooth UI updates. The frontend JavaScript at [app/templates/carrito.html L110-L129](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L110-L129)

 removes the cart item element from the DOM and triggers a page reload if the cart becomes empty.

**Sources:** [app/routes/client.py L209-L226](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L209-L226)

 [app/templates/carrito.html L110-L129](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L110-L129)

---

## Checkout Process

### Endpoint: procesar_compra()

The checkout endpoint at [app/routes/client.py L271-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L271-L455)

 handles both GET (display form) and POST (process payment) requests.

```

```

### Transaction Workflow

The checkout process at [app/routes/client.py L286-L341](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L286-L341)

 executes multiple database operations in sequence:

| Step | Entity | Operation | Purpose |
| --- | --- | --- | --- |
| 1 | `Producto` | Update `stock` | Deduct purchased quantities |
| 2 | `Venta` | Create | Record sale with total |
| 3 | `DetalleVenta` | Create multiple | Copy cart items to sale |
| 4 | `Pago` | Create | Record payment method |
| 5 | `InventarioMovimiento` | Create multiple | Track inventory changes |
| 6 | `Carrito` | Update `estado` | Mark cart as completed |
| 7 | `DetalleCarrito` | Delete all | Clear cart items |

**Sources:** [app/routes/client.py L271-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L271-L455)

### IVA Calculation

The system applies a fixed 16% IVA (Value Added Tax) defined at [app/routes/client.py L42](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L42-L42)

:

```

```

The calculation at [app/routes/client.py L298-L300](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L298-L300)

:

```

```

---

## Invoice Generation

### PDF Creation with ReportLab

The invoice PDF is generated server-side using the ReportLab library at [app/routes/client.py L343-L449](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L343-L449)

```

```

### Invoice Structure

The PDF layout at [app/routes/client.py L346-L440](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L346-L440)

 includes:

| Section | Y-Coordinate Range | Content |
| --- | --- | --- |
| Logo & Header | 720-760 | Circular logo, "Casa Bella", tagline |
| Separator Line | 710 | Decorative blue line |
| Invoice Info | 680 | "FACTURA" label, date |
| Client Data | 615-660 | Name, email, phone |
| Items List | 580-y | Products/services with promotions |
| Totals | y-65 to y-30 | Subtotal, IVA, Total |
| Footer | y-115 to y-100 | Thank you message |

### Circular Logo Processing

The logo is converted to a circular shape using PIL at [app/routes/client.py L352-L365](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L352-L365)

:

```

```

### Ephemeral File Handling

The PDF is immediately deleted after sending at [app/routes/client.py L447-L448](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L447-L448)

:

```

```

This ensures no persistent file artifacts remain on the server. Invoice data is preserved only in the `Venta` and `DetalleVenta` database records.

**Sources:** [app/routes/client.py L343-L449](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L343-L449)

---

## Payment Method Selection

The checkout form at [app/routes/client.py L320-L323](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L320-L323)

 requires the user to select a payment method:

```

```

The `Pago` model records the payment method and amount, linking it to the `Venta` record through `id_venta` foreign key.

**Sources:** [app/routes/client.py L320-L324](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L320-L324)

---

## Inventory Integration

### Inventory Movements

For each product (not service) in the cart, an `InventarioMovimiento` record is created at [app/routes/client.py L327-L335](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L327-L335)

:

```

```

The `motivo` field embeds the `id_venta` to enable cascade cleanup when a purchase is deleted (see [app/routes/client.py L686](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L686-L686)

).

### Stock Deduction

Product stock is decremented during checkout at [app/routes/client.py L288-L295](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L288-L295)

:

```

```

This double-checks stock availability (first check was during `agregar_carrito`) to prevent race conditions.

**Sources:** [app/routes/client.py L286-L335](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L286-L335)

---

## Error Handling and Validation

### Role-Based Access Control

All cart endpoints verify the user role at the beginning:

```

```

This pattern appears at [app/routes/client.py L109-L111](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L109-L111)

 [app/routes/client.py L183-L185](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L183-L185)

 [app/routes/client.py L274-L276](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L274-L276)

### Ownership Verification

Cart modification endpoints verify ownership:

```

```

This prevents users from manipulating other users' carts.

### Database Transaction Rollback

All database operations use try-except blocks with rollback on error:

```

```

**Sources:** [app/routes/client.py L170-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L170-L178)

 [app/routes/client.py L219-L226](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L219-L226)

 [app/routes/client.py L450-L454](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L450-L454)

---

## Frontend Cart Interactions

### JavaScript Cart Management

The `carrito.js` file at [app/static/js/carrito.js L1-L29](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L1-L29)

 provides utility functions for cart operations, though most functionality is implemented inline in `carrito.html`.

### AJAX Total Recalculation

The `actualizarTotal()` function at [app/templates/carrito.html L101-L107](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L101-L107)

 sums all subtotals client-side:

```

```

This provides instant feedback when quantities change without requiring server round-trip.

### Responsive Design

The cart CSS at [app/static/css/carrito.css L1-L403](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L1-L403)

 implements a responsive grid layout that collapses to single column on mobile devices:

```

```

**Sources:** [app/templates/carrito.html L71-L131](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L71-L131)

 [app/static/js/carrito.js L1-L29](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L1-L29)

 [app/static/css/carrito.css L332-L348](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L332-L348)

---

## Integration with Promotion System

### Promotion Display in Cart

The cart template queries promotions at [app/templates/carrito.html L30](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L30-L30)

 and displays them alongside items:

```

```

### Promotional Pricing Persistence

The promotional price is captured at cart-add time and stored in `DetalleCarrito.precio_unitario`. This means:

* If a promotion expires between adding to cart and checkout, the customer still receives the discount
* If a promotion is created after adding to cart, the customer does not receive it (must re-add)

This design choice favors customer satisfaction over dynamic pricing.

**Sources:** [app/routes/client.py L138-L152](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L138-L152)

 [app/templates/carrito.html L30-L37](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L30-L37)

---

## Re-downloading Invoices

### Endpoint: descargar_factura(venta_id)

Users can re-download invoices from their purchase history via [app/routes/client.py L541-L669](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L541-L669)

 This endpoint:

1. Verifies the `Venta` belongs to the current user
2. Queries `DetalleVenta` records with joined products/services
3. Regenerates the PDF using the same logic as checkout
4. Checks for currently active promotions to display in the invoice
5. Returns the file and immediately deletes it

The invoice regeneration uses identical code to the checkout PDF generation, but queries historical sales data rather than cart data.

**Sources:** [app/routes/client.py L541-L669](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L541-L669)