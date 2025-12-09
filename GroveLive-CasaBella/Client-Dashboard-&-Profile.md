# Client Dashboard & Profile

> **Relevant source files**
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/static/css/dashboard_cliente.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css)
> * [app/templates/dashboard_cliente.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html)
> * [app/templates/perfil.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/perfil.html)

## Purpose and Scope

This document covers the client dashboard and profile management features in Casa Bella. The dashboard provides clients with a unified view of their purchase history and appointment history, while the profile section allows clients to update their personal information and manage their account.

For information about browsing products and services, see [Product & Service Catalog](/GroveLive/CasaBella/5.1-product-and-service-catalog). For shopping cart and checkout functionality, see [Shopping Cart & Checkout](/GroveLive/CasaBella/5.2-shopping-cart-and-checkout). For booking new appointments, see [Appointment Booking](/GroveLive/CasaBella/5.3-appointment-booking). For managing favorites and reviews, see [Favorites & Reviews](/GroveLive/CasaBella/5.5-favorites-and-reviews).

---

## Dashboard Overview

The client dashboard serves as the central hub for viewing transaction history and managing account information. It displays two primary data tables: purchase history (Ventas) and appointment history (Citas).

### Route and Access Control

The dashboard is accessed via the `/client/dashboard` route, which enforces strict role-based access control:

```yaml
Route: /client/dashboard
Handler: dashboard() in app/routes/client.py
Access: Requires authentication and rol='cliente'
```

[app/routes/client.py L457-L478](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L457-L478)

The route handler performs the following operations:

1. Validates that `current_user.rol == 'cliente'`
2. Queries all `Venta` records for the current user with eager loading
3. Queries all `Cita` records for the current user with eager loading
4. Queries active `Promocion` records to display discount information
5. Renders `dashboard_cliente.html` with the retrieved data

### Dashboard Components

```

```

**Sources:** [app/routes/client.py L457-L478](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L457-L478)

 [app/templates/dashboard_cliente.html L1-L169](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L1-L169)

### Responsive Design

The dashboard implements a responsive design pattern with separate layouts for desktop and mobile:

| View Type | Display Condition | Component |
| --- | --- | --- |
| Desktop Table | `d-none d-md-block` | `.table-responsive` with standard HTML table |
| Mobile Cards | `d-md-none` | `.table-card` with card-based layout |

[app/templates/dashboard_cliente.html L29-L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L29-L74)

 (desktop table)
[app/templates/dashboard_cliente.html L76-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L76-L110)

 (mobile cards)

The CSS transitions and animations are defined in [app/static/css/dashboard_cliente.css L1-L306](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css#L1-L306)

---

## Purchase History

### Viewing Purchase Records

Purchase history displays all completed `Venta` records associated with the authenticated user. Each purchase record shows:

* **ID Venta**: Primary key from `venta.id_venta`
* **Fecha**: Timestamp from `venta.fecha_venta`
* **Detalles**: Itemized list from `venta.detalle_ventas` * Product/Service name and quantity * Unit price (with promotion applied if applicable) * Promotion details if discount was active at purchase time
* **Total**: Final amount from `venta.total` (includes IVA)

The template iterates through `venta.detalle_ventas` and checks for matching active promotions to display discount information:

[app/templates/dashboard_cliente.html L46-L61](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L46-L61)

### Invoice Download

Each purchase provides a "Descargar Factura" button that triggers PDF invoice generation.

```

```

**Sources:** [app/routes/client.py L541-L669](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L541-L669)

The PDF generation process includes:

1. **Ownership verification**: Ensures `venta.id_usuario == current_user.id_usuario` [app/routes/client.py L548-L550](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L548-L550)
2. **Data retrieval**: Queries `DetalleVenta` with eager-loaded `producto` and `servicio` relationships [app/routes/client.py L553-L556](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L553-L556)
3. **Tax calculation**: Computes subtotal and applies `IVA_RATE = Decimal("0.16")` [app/routes/client.py L42](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L42-L42)  [app/routes/client.py L557-L558](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L557-L558)
4. **PDF creation**: Uses ReportLab with circular logo, branding, and itemized details [app/routes/client.py L562-L656](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L562-L656)
5. **Ephemeral file handling**: Creates temporary PDF in `static/`, serves it, then immediately deletes [app/routes/client.py L658-L665](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L658-L665)

### Deleting Purchase Records

The dashboard provides a "Borrar" button for each purchase. This operation cascades through related records:

```

```

**Sources:** [app/routes/client.py L671-L698](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L671-L698)

The deletion sequence at [app/routes/client.py L682-L687](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L682-L687)

:

1. Deletes all `DetalleVenta` records
2. Deletes all `Pago` records
3. Deletes all `InventarioMovimiento` records with `motivo` matching the venta ID pattern
4. Deletes the `Venta` record itself
5. Commits the transaction

**Note**: This operation does **not** restore product stock. The inventory movements are removed for record consistency, but stock quantities remain unchanged.

---

## Appointment History

### Viewing Appointments

Appointment history displays all `Cita` records for the current user, regardless of state (`pendiente`, `confirmada`, `completada`).

Each appointment record shows:

| Field | Source | Description |
| --- | --- | --- |
| ID Cita | `cita.id_cita` | Primary key |
| Servicio | `cita.servicio.nombre` | Related service name via `joinedload(Cita.servicio)` |
| Fecha y Hora | `cita.fecha_hora` | Scheduled date/time |
| Estado | `cita.estado` | Current state in workflow |
| Acciones | Cancel button | Triggers `/client/borrar_cita` |

[app/templates/dashboard_cliente.html L131-L142](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L131-L142)

 (desktop view)

### Canceling Appointments

Clients can cancel appointments via the "Cancelar" button, which sends a POST request to `/client/borrar_cita/<cita_id>`:

[app/routes/client.py L700-L722](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L700-L722)

The cancellation flow:

1. **Ownership verification**: `cita.id_usuario == current_user.id_usuario` [app/routes/client.py L707-L709](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L707-L709)
2. **Database deletion**: `db.session.delete(cita)` [app/routes/client.py L711](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L711-L711)
3. **Commit and redirect**: Saves changes and returns to dashboard [app/routes/client.py L712-L713](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L712-L713)

Appointment deletion handles `IntegrityError` exceptions, which may occur if the appointment has related records that prevent deletion (e.g., employee assignments via `Asignacion` table) [app/routes/client.py L714-L717](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L714-L717)

### Editing Appointments

While the dashboard provides view and cancel functionality, appointment editing is handled through a separate route:

```yaml
Route: /client/editar_cita/<cita_id>
Methods: GET, POST
Template: editar_cita.html
```

[app/routes/client.py L724-L780](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L724-L780)

The edit operation includes:

* Business hours validation (weekday-specific schedules)
* 30-minute increment enforcement
* Past date prevention
* State reset to `'pendiente'` after edit

---

## Profile Management

### Profile Editing Interface

The profile page allows clients to update their account information via `/client/perfil`:

```

```

**Sources:** [app/routes/client.py L480-L508](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L480-L508)

 [app/templates/perfil.html L1-L47](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/perfil.html#L1-L47)

### Field Update Logic

The profile update handler at [app/routes/client.py L486-L501](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L486-L501)

 implements a change-detection pattern:

```

```

This pattern:

* Only updates fields that have changed
* Hashes password using `werkzeug.security.generate_password_hash()` [app/routes/client.py L21](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L21-L21)
* Allows empty password field to preserve existing password
* Commits all changes in a single transaction [app/routes/client.py L502](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L502-L502)

The form template at [app/templates/perfil.html L19-L40](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/perfil.html#L19-L40)

 pre-populates fields with `usuario.nombre`, `usuario.email`, and `usuario.telefono` values, using Jinja2's `or ''` operator to handle null values gracefully.

---

## Account Deletion

### Deletion Flow and Constraints

The profile page includes an "Eliminar Perfil" button that permanently deletes the user account:

```

```

**Sources:** [app/routes/client.py L510-L539](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L510-L539)

### Deletion Constraints

The deletion operation at [app/routes/client.py L524-L525](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L524-L525)

 may fail due to foreign key constraints. The `IntegrityError` handler at [app/routes/client.py L530-L534](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L530-L534)

 informs the user:

> "No se puede borrar el perfil porque tiene datos asociados (como ventas o citas). Contacta al administrador."

This occurs when the `Usuario` record has related records in tables such as:

* `Venta` (purchases)
* `Cita` (appointments)
* `Carrito` (shopping carts)
* `Guardado` (favorites)
* `Reseña` (reviews)
* `Notificacion` (notifications)

### Post-Deletion Actions

Upon successful deletion, the route:

1. Calls `logout_user()` to terminate the Flask-Login session [app/routes/client.py L527](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L527-L527)
2. Flashes a success message: "Perfil borrado. Has sido desconectado." [app/routes/client.py L528](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L528-L528)
3. Redirects to `auth.login` [app/routes/client.py L529](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L529-L529)

The `_get_current_object()` call at [app/routes/client.py L518](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L518-L518)

 is necessary because `current_user` is a Flask-Login proxy object, and direct deletion requires the actual SQLAlchemy model instance.

---

## Data Model Integration

### Key Model Relationships

The dashboard and profile features interact with the following SQLAlchemy models:

| Model | Purpose | Key Fields |
| --- | --- | --- |
| `Usuario` | User account | `id_usuario`, `nombre`, `email`, `telefono`, `rol`, `contraseña` |
| `Venta` | Purchase record | `id_venta`, `id_usuario`, `fecha_venta`, `total` |
| `DetalleVenta` | Purchase line item | `id_venta`, `id_producto`, `id_servicio`, `cantidad`, `precio_unitario` |
| `Cita` | Appointment | `id_cita`, `id_usuario`, `id_servicio`, `fecha_hora`, `estado` |
| `Pago` | Payment record | `id_venta`, `metodo_pago`, `monto` |
| `InventarioMovimiento` | Stock movement | `id_producto`, `tipo_movimiento`, `cantidad`, `motivo` |
| `Promocion` | Active discount | `id_producto`, `id_servicio`, `descuento`, `fecha_inicio`, `fecha_fin` |

### Query Optimization

Both dashboard queries use SQLAlchemy's `joinedload()` to prevent N+1 query problems:

**Purchase History:**

```

```

[app/routes/client.py L466-L469](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L466-L469)

**Appointment History:**

```

```

[app/routes/client.py L470-L472](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L470-L472)

This eager loading ensures that accessing `venta.detalle_ventas`, `detalle.producto`, `detalle.servicio`, and `cita.servicio` does not trigger additional database queries during template rendering.

---

## Security and Access Control

### Authentication Requirements

All routes in this module require authentication via the `@login_required` decorator:

[app/routes/client.py L52](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L52)

 [app/routes/client.py L457](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L457-L457)

 [app/routes/client.py L480](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L480-L480)

 [app/routes/client.py L510](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L510-L510)

### Role Verification

Each route enforces `rol='cliente'` through a consistent pattern:

```

```

This check appears at:

* [app/routes/client.py L55-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L55-L57)
* [app/routes/client.py L460-L462](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L460-L462)
* [app/routes/client.py L483-L485](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L483-L485)
* [app/routes/client.py L513-L515](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L513-L515)

### Ownership Verification

Operations on user-specific resources (purchases, appointments) verify ownership before proceeding:

**Purchase deletion:**

```

```

[app/routes/client.py L678-L680](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L678-L680)

**Appointment cancellation:**

```

```

[app/routes/client.py L707-L709](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L707-L709)

---

## Template Structure and Styling

### Template Inheritance

Both dashboard and profile templates extend the base template:

```

```

[app/templates/dashboard_cliente.html L1](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_cliente.html#L1-L1)

 [app/templates/perfil.html L1](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/perfil.html#L1-L1)

This provides:

* Role-based navigation from `base.html`
* Theme system (dark/light mode)
* Flash message handling
* Bootstrap 5 framework

### CSS Custom Properties

The dashboard stylesheet at [app/static/css/dashboard_cliente.css L1-L306](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css#L1-L306)

 uses CSS custom properties for theming:

| Variable | Purpose |
| --- | --- |
| `--card-bg` | Card and table background color |
| `--text-primary` | Primary text color |
| `--text-secondary` | Secondary text color |
| `--border-color` | Border color for tables and cards |
| `--shadow-elegant` | Box shadow for elevation effects |
| `--gradient-primary` | Gradient for buttons and accents |
| `--primary-color` | Brand primary color |
| `--secondary-color` | Brand secondary color |

The `[data-theme="light"]` selector at [app/static/css/dashboard_cliente.css L19-L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css#L19-L22)

 overrides these variables for light mode.

### Animation System

The dashboard implements staggered fade-in animations for table rows and cards:

```

```

[app/static/css/dashboard_cliente.css L274-L294](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/dashboard_cliente.css#L274-L294)

This creates a cascading visual effect when the page loads, with each row appearing sequentially with a 0.1-second delay offset.

---

## Error Handling

### Exception Patterns

All route handlers follow a consistent error handling pattern:

```

```

This pattern ensures:

* Database rollback on failure
* Logging of error details
* User-friendly flash messages
* Graceful redirect to safe page

### Logging Configuration

The client blueprint configures logging at [app/routes/client.py L37-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L37-L39)

:

```

```

Debug statements throughout the code track:

* User actions: [app/routes/client.py L464](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L464-L464)
* Query results: [app/routes/client.py L187](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L187-L187)  [app/routes/client.py L194-L200](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L194-L200)
* Deletion operations: [app/routes/client.py L516](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L516-L516)  [app/routes/client.py L523](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L523-L523)  [app/routes/client.py L526](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L526-L526)

**Sources:** [app/routes/client.py L37-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L37-L39)

 [app/routes/client.py L457-L539](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L457-L539)