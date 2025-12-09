# Client Features

> **Relevant source files**
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/static/css/dashboard_cliente.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css)
> * [app/templates/dashboard_cliente.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html)
> * [app/templates/index.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html)
> * [app/templates/perfil.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/perfil.html)

## Purpose and Scope

This document provides a comprehensive overview of all customer-facing functionality in the Casa Bella system. The client features encompass the complete customer journey from browsing products and services to checkout, appointment booking, profile management, and review submission.

This page focuses on the **client blueprint** implementation and its associated templates. For information about the underlying data models and database schema, see [Data Models](/GroveLive/CasaBella/3.2-data-models). For authentication and role-based access control mechanisms, see [Authentication & Authorization](/GroveLive/CasaBella/4-authentication-and-authorization). For detailed subsystem documentation, see:

* [Product & Service Catalog](/GroveLive/CasaBella/5.1-product-and-service-catalog)
* [Shopping Cart & Checkout](/GroveLive/CasaBella/5.2-shopping-cart-and-checkout)
* [Appointment Booking](/GroveLive/CasaBella/5.3-appointment-booking)
* [Client Dashboard & Profile](/GroveLive/CasaBella/5.4-client-dashboard-and-profile)
* [Favorites & Reviews](/GroveLive/CasaBella/5.5-favorites-and-reviews)

---

## Client Blueprint Architecture

The client features are implemented in the `client` blueprint, which serves as the primary interface for users with the `cliente` role. All routes in this blueprint require authentication and role verification.

**Blueprint Structure**

```

```

**Sources:** [app/routes/client.py L1-L40](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L40)

---

## Authentication and Authorization Pattern

Every client route follows a consistent authentication and authorization pattern:

**Authorization Check Pattern**

| Step | Implementation | Line Reference |
| --- | --- | --- |
| 1. Login Required | `@login_required` decorator | Lines 53, 76, 98, etc. |
| 2. Role Verification | `if current_user.rol != 'cliente'` | Lines 55, 77, 99, etc. |
| 3. Redirect on Failure | `return redirect(url_for('auth.login'))` | Lines 56, 78, 100, etc. |

**Example from servicios route:**

```

```

This pattern ensures that only authenticated users with the `cliente` role can access customer-facing features.

**Sources:** [app/routes/client.py L52-L72](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L72)

 [app/routes/client.py L74-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L74-L94)

---

## Shopping Cart Context Processor

The client blueprint provides a context processor that injects the active shopping cart into all templates, enabling the cart indicator in the navigation bar.

```

```

**Implementation:**

```

```

This context processor executes for every request within the client blueprint, making the `carrito` variable available in all templates.

**Sources:** [app/routes/client.py L45-L50](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L45-L50)

---

## Browse and Discovery Features

### Product and Service Listing

Clients can browse the complete catalog of products and services with optional filtering by active promotions.

**Browse Routes Flow**

```

```

**Promotion Filtering Logic:**

The system supports filtering by active promotions through a URL query parameter. When `?filter=promotions` is appended to the URL, the route performs a join with the `Promocion` table and filters by the promotion date range:

```

```

**Sources:** [app/routes/client.py L52-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L94)

### Appointment Preview

The `/citas` route provides the appointment booking interface:

```

```

The route accepts an optional `servicio_id` query parameter to pre-select a service when redirected from the service catalog.

**Sources:** [app/routes/client.py L96-L104](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L96-L104)

---

## Shopping Cart Management

### Cart Operations

The shopping cart system supports four primary operations: add, view, update quantity, and remove items.

**Cart Operations Diagram**

```

```

**Sources:** [app/routes/client.py L106-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L269)

### Add to Cart Implementation Details

The `agregar_carrito` route handles several critical operations:

**1. Cart Creation or Retrieval:**

```

```

**2. Polymorphic Item Detection:**
The system determines whether the item is a product or service:

```

```

**3. Promotion Application:**
Active promotions are automatically detected and applied:

```

```

**4. Cart Detail Creation:**

```

```

**Sources:** [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)

### AJAX Cart Operations

The cart system exposes two AJAX endpoints for dynamic cart updates without page reloads:

| Endpoint | Method | Returns | Purpose |
| --- | --- | --- | --- |
| `/eliminar_del_carrito/<detalle_id>` | POST | JSON | Remove item from cart |
| `/actualizar_cantidad/<detalle_id>` | POST | JSON | Increment/decrement quantity |

**JSON Response Format:**

```

```

**Sources:** [app/routes/client.py L209-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L209-L269)

---

## Checkout and Invoice Generation

### Checkout Process Flow

The checkout process involves multiple database operations executed within a single transaction.

```

```

**Sources:** [app/routes/client.py L271-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L271-L455)

### Transaction Integrity

The checkout process uses database transactions to ensure atomicity:

```

```

**Sources:** [app/routes/client.py L285-L454](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L285-L454)

### Invoice PDF Generation

The system uses **ReportLab** to generate professional invoices with the following features:

**Invoice Components:**

| Component | Implementation | Lines |
| --- | --- | --- |
| Circular Logo | PIL image processing with mask | 352-371 |
| Company Header | Bold text with gradient line | 373-380 |
| Client Information | Name, email, phone | 387-392 |
| Itemized List | Products/services with promotions | 394-428 |
| Financial Summary | Subtotal, IVA (16%), Total | 430-435 |
| Footer Message | Brand tagline | 437-439 |

**Logo Processing:**
The system creates a circular logo using PIL:

```

```

**IVA Rate Configuration:**
The tax rate is defined as a module-level constant:

```

```

**Temporary File Cleanup:**
Invoices are immediately deleted after serving to prevent disk accumulation:

```

```

**Sources:** [app/routes/client.py L42](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L42-L42)

 [app/routes/client.py L343-L449](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L343-L449)

---

## Dashboard and Profile Management

### Client Dashboard

The dashboard provides a unified view of purchase history and appointment history.

**Dashboard Data Loading**

```

```

**Eager Loading Pattern:**
The dashboard uses `joinedload` to prevent N+1 query problems:

```

```

**Sources:** [app/routes/client.py L457-L478](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L457-L478)

 [app/templates/dashboard_cliente.html L1-L169](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L1-L169)

### Dashboard Template Structure

The dashboard template uses responsive design with separate desktop and mobile views:

**Responsive Table Strategy:**

| Screen Size | Display Mode | Lines |
| --- | --- | --- |
| Desktop (≥768px) | Bootstrap table | 29-74 |
| Mobile (<768px) | Card-based layout | 76-110 |

The CSS uses media queries to toggle visibility:

```

```

**Sources:** [app/templates/dashboard_cliente.html L29-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L29-L110)

 [app/static/css/dashboard_cliente.css L207-L223](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css#L207-L223)

### Profile Management

The profile route supports updating user information and password changes:

```

```

**Sources:** [app/routes/client.py L480-L508](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L480-L508)

 [app/templates/perfil.html L1-L47](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/perfil.html#L1-L47)

### Account Deletion

The `borrar_perfil` route implements secure account deletion with cascade handling:

**Deletion Flow:**

```

```

If deletion fails due to foreign key constraints (e.g., existing purchases or appointments), the user receives a message to contact the administrator.

**Sources:** [app/routes/client.py L510-L539](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L510-L539)

---

## History Management

### Invoice Download

Clients can re-download invoices for past purchases:

```

```

The PDF generation logic is duplicated from the checkout process, ensuring historical invoices reflect promotion information at the time of purchase.

**Sources:** [app/routes/client.py L541-L669](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L541-L669)

### Purchase Deletion

The `borrar_compra` route allows clients to delete purchase records:

**Cascade Deletion Logic:**

```

```

Note: This does **not** restore product stock. Stock adjustments remain permanent for audit purposes.

**Sources:** [app/routes/client.py L671-L698](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L671-L698)

### Appointment Management

Clients can cancel appointments through the dashboard:

```

```

**Sources:** [app/routes/client.py L700-L722](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L700-L722)

---

## Favorites System

### Favorites Routes

The favorites system allows clients to save products and services for later reference.

**Favorites Operations:**

| Route | Method | Purpose | Lines |
| --- | --- | --- | --- |
| `/favoritos` | GET | List all favorites | 856-878 |
| `/agregar_favorito/<tipo>/<item_id>` | POST | Add to favorites | 880-914 |
| `/eliminar_favorito/<guardado_id>` | POST | Remove from favorites | 916-934 |

**Polymorphic Favorites Implementation:**

```

```

**Sources:** [app/routes/client.py L880-L914](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L880-L914)

### Favorites Display

The favorites page uses eager loading to display saved items:

```

```

**Sources:** [app/routes/client.py L856-L878](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L856-L878)

---

## Review System

### Review Operations

Clients can create, view, edit, and delete reviews for purchased products and services.

**Review Routes:**

```

```

**Sources:** [app/routes/client.py L936-L1075](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L936-L1075)

### Review Creation

The review creation process validates that the user has purchased the product or used the service:

```

```

**Review Data Structure:**

| Field | Type | Purpose |
| --- | --- | --- |
| `id_usuario` | FK | Links to reviewer |
| `id_producto` | FK (nullable) | Links to product |
| `id_servicio` | FK (nullable) | Links to service |
| `calificacion` | Integer | Star rating |
| `comentario` | Text | Review text |

**Sources:** [app/routes/client.py L964-L1009](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L964-L1009)

---

## Appointment Creation and Editing

### Create Appointment

The appointment creation route validates business hours and prevents double-booking:

```

```

**Business Hours Validation:**

| Day | Hours | Weekday Index |
| --- | --- | --- |
| Monday | 9:00 AM - 7:00 PM | 0 |
| Tuesday | 8:00 AM - 7:00 PM | 1 |
| Wednesday | 9:00 AM - 7:00 PM | 2 |
| Thursday | CLOSED | 3 |
| Friday | 9:00 AM - 7:00 PM | 4 |
| Saturday | 9:00 AM - 7:00 PM | 5 |
| Sunday | 9:00 AM - 6:00 PM | 6 |

**Time Slot Validation:**

```

```

**Sources:** [app/routes/client.py L763-L862](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L763-L862)

### Edit Appointment

The edit appointment route reuses the same validation logic:

```

```

**Sources:** [app/routes/client.py L724-L853](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L724-L853)

---

## Key Data Models Used

The client blueprint interacts with multiple SQLAlchemy models:

| Model | Purpose | Key Relationships |
| --- | --- | --- |
| `Usuario` | Current user (via `current_user`) | Has many Carrito, Venta, Cita, Guardado, Reseña |
| `Producto` | Product catalog | Referenced by DetalleCarrito, DetalleVenta, Guardado, Reseña |
| `Servicio` | Service catalog | Referenced by DetalleCarrito, DetalleVenta, Cita, Guardado, Reseña |
| `Carrito` | Shopping cart container | Has many DetalleCarrito |
| `DetalleCarrito` | Individual cart items | References Producto or Servicio |
| `Venta` | Purchase record | Has many DetalleVenta, has one Pago |
| `DetalleVenta` | Purchase line items | References Producto or Servicio |
| `Pago` | Payment information | Belongs to Venta |
| `Cita` | Appointment record | References Servicio, Usuario (client and employee) |
| `Promocion` | Active discounts | References Producto or Servicio |
| `Guardado` | Favorites | References Producto or Servicio |
| `Reseña` | Reviews | References Producto or Servicio |
| `InventarioMovimiento` | Stock tracking | References Producto |
| `Notificacion` | User notifications | References Usuario |

**Sources:** [app/routes/client.py L1-L16](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L16)

---

## Error Handling Patterns

The client blueprint implements consistent error handling:

**Try-Except Pattern:**

```

```

**Logging Configuration:**
The module configures Python's logging module:

```

```

All error paths include `logger.error()` calls for debugging and audit purposes.

**Sources:** [app/routes/client.py L33-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L33-L39)

 [app/routes/client.py L170-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L170-L178)

---

## Summary of Client Routes

| Route Pattern | Methods | Purpose | Authentication |
| --- | --- | --- | --- |
| `/productos` | GET | Browse product catalog | Required, cliente role |
| `/servicios` | GET | Browse service catalog | Required, cliente role |
| `/citas` | GET | Appointment booking form | Required, cliente role |
| `/crear_cita` | POST | Create appointment | Required, cliente role |
| `/editar_cita/<id>` | GET, POST | Edit appointment | Required, cliente role |
| `/borrar_cita/<id>` | POST | Cancel appointment | Required, cliente role |
| `/agregar_carrito/<id>` | GET | Add item to cart | Required, cliente role |
| `/carrito` | GET | View shopping cart | Required, cliente role |
| `/actualizar_cantidad/<id>` | POST | Update cart quantity (AJAX) | Required, cliente role |
| `/eliminar_del_carrito/<id>` | POST | Remove from cart (AJAX) | Required, cliente role |
| `/procesar_compra` | GET, POST | Checkout process | Required, cliente role |
| `/dashboard` | GET | View history | Required, cliente role |
| `/perfil` | GET, POST | Edit profile | Required, cliente role |
| `/borrar_perfil` | POST | Delete account | Required, cliente role |
| `/descargar_factura/<id>` | GET | Download invoice PDF | Required, cliente role |
| `/borrar_compra/<id>` | POST | Delete purchase record | Required, cliente role |
| `/favoritos` | GET | View favorites | Required, cliente role |
| `/agregar_favorito/<tipo>/<id>` | POST | Add to favorites (AJAX) | Required, cliente role |
| `/eliminar_favorito/<id>` | POST | Remove from favorites | Required, cliente role |
| `/resenas` | GET | View reviews | Required, cliente role |
| `/agregar_resena` | POST | Create review | Required, cliente role |
| `/editar_resena/<id>` | GET, POST | Edit review | Required, cliente role |
| `/borrar_resena/<id>` | POST | Delete review | Required, cliente role |

**Sources:** [app/routes/client.py L52-L1075](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L1075)