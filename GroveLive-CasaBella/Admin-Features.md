# Admin Features

> **Relevant source files**
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/templates/base.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)

This document describes the administrative functionality available to users with the `admin` role. Admin features provide complete control over the Casa Bella system, including user management, inventory control, appointment coordination, promotion management, and business analytics.

For client-facing features, see [Client Features](/GroveLive/CasaBella/5-client-features). For employee operations, see [Employee Features](/GroveLive/CasaBella/7-employee-features). For authentication and role assignment, see [Authentication & Authorization](/GroveLive/CasaBella/4-authentication-and-authorization).

## Purpose and Scope

The admin system provides privileged functionality for managing all aspects of the Casa Bella business. Administrators can create and modify users, products, services, and promotions; assign employees to appointments; monitor inventory levels; and view comprehensive business analytics. All admin routes require authentication and role verification.

**Sources:** [app/routes/admin.py L1-L915](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L1-L915)

## Access Control

All admin routes enforce role-based access control through the `@login_required` decorator and explicit role checks. Each route handler begins with a validation block:

```

```

The navigation menu dynamically renders admin-specific links when `current_user.rol == 'admin'`, displaying options for user management, product/service management, promotions, and pending appointments.

**Sources:** [app/routes/admin.py L31-L33](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L31-L33)

 [app/templates/base.html L57-L78](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L57-L78)

## Admin Blueprint Structure

### Blueprint Registration

The admin blueprint is registered with the URL prefix `/admin` and contains 23 routes organized into functional categories:

```

```

**Sources:** [app/routes/admin.py L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L22-L22)

 [app/templates/base.html L62-L78](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L62-L78)

### Route Summary

| Route Pattern | Function | HTTP Methods | Purpose |
| --- | --- | --- | --- |
| `/dashboard` | `admin_dashboard()` | GET | Display analytics and metrics |
| `/gestion_usuarios` | `gestion_usuarios()` | GET | List all users |
| `/agregar_usuario` | `agregar_usuario()` | GET, POST | Create new user |
| `/editar_usuario/<id>` | `editar_usuario()` | GET, POST | Update existing user |
| `/eliminar_usuario/<id>` | `eliminar_usuario()` | GET, POST | Delete user |
| `/gestion_productos` | `gestion_productos()` | GET | List all products |
| `/agregar_producto` | `agregar_producto()` | GET, POST | Create new product |
| `/editar_producto/<id>` | `editar_producto()` | GET, POST | Update existing product |
| `/eliminar_producto/<id>` | `eliminar_producto()` | GET, POST | Delete product |
| `/gestion_servicios` | `gestion_servicios()` | GET | List all services |
| `/agregar_servicio` | `agregar_servicio()` | GET, POST | Create new service |
| `/editar_servicio/<id>` | `editar_servicio()` | GET, POST | Update existing service |
| `/eliminar_servicio/<id>` | `eliminar_servicio()` | GET, POST | Delete service |
| `/gestion_citas_pendientes` | `gestion_citas_pendientes()` | GET | View pending appointments |
| `/asignar_empleado/<id>` | `asignar_empleado()` | POST | Assign employee to appointment |
| `/gestion_promociones` | `gestion_promociones()` | GET | List all promotions |
| `/agregar_promocion` | `agregar_promocion()` | POST | Create promotion (AJAX) |
| `/editar_promocion/<id>` | `editar_promocion()` | POST | Update promotion (AJAX) |
| `/eliminar_promocion/<id>` | `eliminar_promocion()` | POST | Delete promotion (AJAX) |
| `/gestion_ingresos` | `gestion_ingresos()` | GET | View income analytics |

**Sources:** [app/routes/admin.py L28-L915](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L28-L915)

## Dashboard and Analytics

### Dashboard Overview

The `admin_dashboard()` function at [app/routes/admin.py L28-L154](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L28-L154)

 calculates comprehensive business metrics and renders them through the `dashboard_admin.html` template. The dashboard displays seven key metric cards plus an interactive income chart.

### Metric Cards

```

```

| Metric | Query | Display Color |
| --- | --- | --- |
| Usuarios | `Usuario.query.count()` | Primary (Blue) |
| Productos | `Producto.query.count()` | Success (Green) |
| Servicios | `Servicio.query.count()` | Info (Cyan) |
| Citas Pendientes | `Cita.query.filter_by(estado='pendiente').count()` | Warning (Yellow) |
| Stock Total | `func.sum(Producto.stock)` | Secondary (Gray) |
| Stock Bajo | Products where `stock <= stock_minimo` | Danger (Red) |
| Movimientos Recientes | `InventarioMovimiento` in last 7 days | Dark |

**Sources:** [app/routes/admin.py L35-L58](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L35-L58)

 [app/templates/dashboard_admin.html L24-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L24-L87)

### Income Calculation

The dashboard calculates income across four time periods (today, week, month, year) for both products and services:

#### Product Income Calculation

```

```

The product income query joins `Producto` with `InventarioMovimiento` where `tipo_movimiento == 'salida'` and calculates the sum of price multiplied by absolute quantity for each time period.

**Sources:** [app/routes/admin.py L60-L77](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L60-L77)

#### Service Income Calculation

```

```

Service income sums the price of all services where associated appointments have `estado == 'completada'`.

**Sources:** [app/routes/admin.py L79-L96](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L79-L96)

### Automated Notifications

The dashboard generates automatic notifications for critical inventory conditions:

```

```

**Sources:** [app/routes/admin.py L106-L124](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L106-L124)

### Chart Rendering

The dashboard uses Chart.js to render an interactive bar chart showing product income across time periods. The chart data is fetched via AJAX when the page loads:

```

```

The dashboard route supports dual-mode rendering: full HTML for normal requests, and JSON for AJAX requests (detected via `X-Requested-With` header).

**Sources:** [app/templates/dashboard_admin.html L121-L191](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L121-L191)

 [app/routes/admin.py L134-L141](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L134-L141)

## User Management

### User CRUD Operations

The admin blueprint provides complete CRUD operations for the `Usuario` model. User management enforces business rules such as mandatory `especialidad` for employees.

```

```

**Sources:** [app/routes/admin.py L156-L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L156-L256)

### User Creation

The `agregar_usuario()` function handles user creation with validation:

| Field | Required | Validation |
| --- | --- | --- |
| `nombre` | Yes | Non-empty string |
| `email` | Yes | Unique, valid email |
| `rol` | Yes | Must be `cliente`, `admin`, or `empleado` |
| `contraseña` | Yes | Hashed with `generate_password_hash()` |
| `telefono` | No | Optional string |
| `especialidad` | Conditional | Required if `rol == 'empleado'` |

```

```

**Sources:** [app/routes/admin.py L165-L201](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L165-L201)

### User Updates

The `editar_usuario()` function updates existing users. Password updates are optional—if the `contraseña` field is empty, the existing hash is preserved:

```

```

**Sources:** [app/routes/admin.py L203-L238](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L203-L238)

### User Deletion

User deletion includes integrity checks. If a user is referenced by other entities (appointments, sales, etc.), the database raises an `IntegrityError` which is caught and displayed to the admin:

```

```

**Sources:** [app/routes/admin.py L240-L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L240-L256)

## Product and Service Management

### Product Management

Product management includes full CRUD operations plus category assignment and inventory tracking. Products are linked to the `Categoria` model and tracked through `InventarioMovimiento`.

```

```

**Sources:** [app/routes/admin.py L258-L382](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L258-L382)

### Product Fields

| Field | Type | Required | Validation | Default |
| --- | --- | --- | --- | --- |
| `id_categoria` | FK | Yes | Must reference valid `Categoria` | None |
| `nombre` | String | Yes | Non-empty | None |
| `descripcion` | Text | No | Optional | None |
| `tipo` | String | Yes | Non-empty | None |
| `precio` | Decimal | Yes | Must be >= 0 | None |
| `stock` | Integer | Yes | Must be >= 0 | None |
| `stock_minimo` | Integer | No | Must be >= 0 | 5 |
| `estado` | Enum | No | 'activo' or 'inactivo' | 'activo' |
| `imagen_url` | String | No | Optional URL | None |

**Sources:** [app/routes/admin.py L268-L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L268-L316)

 [app/routes/admin.py L318-L364](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L318-L364)

### Service Management

Service management mirrors product management but without categories or stock tracking. Services track `duracion` (duration in minutes) instead:

```

```

**Sources:** [app/routes/admin.py L384-L493](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L384-L493)

### Service Fields

| Field | Type | Required | Validation | Default |
| --- | --- | --- | --- | --- |
| `nombre` | String | Yes | Non-empty | None |
| `descripcion` | Text | No | Optional | None |
| `precio` | Decimal | Yes | Must be >= 0 | None |
| `duracion` | Integer | Yes | Must be > 0 (minutes) | None |
| `estado` | Enum | No | 'activo' or 'inactivo' | 'activo' |
| `imagen_url` | String | No | Optional URL | None |

**Sources:** [app/routes/admin.py L393-L434](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L393-L434)

## Appointment Management

### Pending Appointment Coordination

The `gestion_citas_pendientes()` route displays all appointments with `estado='pendiente'` and `id_empleado=None`, allowing admins to assign employees. The view integrates FullCalendar for visual appointment management.

```

```

**Sources:** [app/routes/admin.py L495-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L573)

### FullCalendar Integration

The pending appointments are preprocessed into two JSON structures:

1. **Detailed events** for the event list sidebar:

```

```

1. **Day-aggregated events** for the calendar view:

```

```

**Sources:** [app/routes/admin.py L506-L541](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L506-L541)

### Employee Assignment

The `asignar_empleado()` function creates an `Asignacion` record, updates the `Cita` with the employee ID, and generates a notification:

```

```

This triggers the workflow where the assigned employee can view, confirm, and complete the appointment through the employee routes.

**Sources:** [app/routes/admin.py L543-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L543-L573)

## Promotion Management

### AJAX-Based Promotion CRUD

The promotion management system uses AJAX for all create, update, and delete operations, providing a seamless single-page experience. All promotion routes check for the `X-Requested-With: XMLHttpRequest` header.

```

```

**Sources:** [app/routes/admin.py L683-L914](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L683-L914)

### Promotion Data Model

Promotions use a polymorphic design where a promotion can apply to either a product or a service (but not both):

| Field | Type | Required | Validation |
| --- | --- | --- | --- |
| `nombre` | String | Yes | Unique promotion name |
| `descripcion` | Text | No | Optional description |
| `descuento` | Decimal | Yes | 0 < descuento <= 100 (percentage) |
| `fecha_inicio` | DateTime | Yes | Must be before `fecha_fin` |
| `fecha_fin` | DateTime | Yes | Must be after `fecha_inicio` |
| `id_producto` | FK | Conditional | Set if `item_type == 'producto'` |
| `id_servicio` | FK | Conditional | Set if `item_type == 'servicio'` |

The polymorphic constraint is enforced in the route logic: exactly one of `id_producto` or `id_servicio` must be set.

**Sources:** [app/routes/admin.py L718-L761](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L718-L761)

### Promotion Creation

The `agregar_promocion()` function expects JSON input with the following structure:

```

```

The function validates dates, descuento range, and item existence before creating the `Promocion` record:

```

```

**Sources:** [app/routes/admin.py L709-L795](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L709-L795)

### Promotion Response Format

Successful promotion operations return JSON with the promotion data formatted for immediate display:

```

```

The `estado` field is dynamically calculated based on the current date:

* **"Activa"**: `fecha_inicio <= now <= fecha_fin`
* **"Futura"**: `fecha_inicio > now`
* **"Expirada"**: `fecha_fin < now`

**Sources:** [app/routes/admin.py L773-L786](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L773-L786)

 [app/routes/admin.py L859-L872](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L859-L872)

### Promotion Notifications

All promotion operations (create, update, delete) generate `Notificacion` records for the admin:

```

```

**Sources:** [app/routes/admin.py L764-L771](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L764-L771)

 [app/routes/admin.py L850-L857](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L850-L857)

 [app/routes/admin.py L899-L906](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L899-L906)

## Income Analytics

### Dedicated Income View

The `gestion_ingresos()` route at [app/routes/admin.py L575-L681](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L575-L681)

 provides a dedicated view for analyzing income. Unlike the dashboard which shows only product income, this route supports filtering by:

* **Total**: Combined product and service income
* **Productos**: Product income only
* **Servicios**: Service income only

```

```

The route uses the same income calculation logic as the dashboard but allows dynamic filtering via the `tipo_filtro` query parameter.

**Sources:** [app/routes/admin.py L575-L681](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L575-L681)

## Error Handling

### Integrity Constraints

All delete operations include `IntegrityError` handling to prevent cascade deletion issues:

```

```

This pattern appears in:

* User deletion [app/routes/admin.py L252-L254](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L252-L254)
* Product deletion [app/routes/admin.py L378-L380](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L378-L380)
* Service deletion [app/routes/admin.py L490-L491](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L490-L491)
* Promotion deletion [app/routes/admin.py L908-L910](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L908-L910)

### Validation Errors

Form validation errors are caught early and return flash messages with specific error descriptions:

```

```

**Sources:** [app/routes/admin.py L406-L429](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L406-L429)

### AJAX Error Responses

AJAX endpoints return structured JSON error responses:

```

```

**Sources:** [app/routes/admin.py L715-L728](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L715-L728)

## Database Query Optimization

### Eager Loading

The promotions view uses `joinedload()` to prevent N+1 queries when displaying promotion details:

```

```

**Sources:** [app/routes/admin.py L691-L695](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L691-L695)

### Appointment Loading

The pending appointments view uses `joinedload()` to preload assignment data:

```

```

**Sources:** [app/routes/admin.py L501-L503](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L501-L503)

## Summary

The admin blueprint provides comprehensive management functionality through 23 routes organized into six functional areas:

1. **Dashboard**: Real-time analytics with Chart.js visualizations
2. **User Management**: Full CRUD with role-specific validation
3. **Product Management**: Inventory control with category assignments
4. **Service Management**: Service catalog with duration tracking
5. **Appointment Management**: FullCalendar-based employee assignment
6. **Promotion Management**: AJAX-powered discount management

All routes enforce `rol == 'admin'` access control and generate automated notifications for critical events (low stock, promotions, appointments). The system uses SQLAlchemy for database operations with comprehensive error handling and input validation.

**Sources:** [app/routes/admin.py L1-L915](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L1-L915)

 [app/templates/dashboard_admin.html L1-L192](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L1-L192)

 [app/templates/base.html L57-L78](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L57-L78)