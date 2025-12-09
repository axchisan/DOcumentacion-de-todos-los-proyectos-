# Overview

> **Relevant source files**
> * [Dockerfile](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile)
> * [app/models/carrito.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py)
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/templates/base.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html)
> * [app/templates/index.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html)

## Purpose and Scope

This document provides a high-level introduction to the Casa Bella system, including its business domain, user roles, core functionality, and architectural organization. For detailed information about specific subsystems, refer to:

* Authentication and user management: [Authentication & Authorization](/GroveLive/CasaBella/4-authentication-and-authorization)
* Client-facing features: [Client Features](/GroveLive/CasaBella/5-client-features)
* Administrative capabilities: [Admin Features](/GroveLive/CasaBella/6-admin-features)
* Employee operations: [Employee Features](/GroveLive/CasaBella/7-employee-features)
* Setup and deployment: [Getting Started](/GroveLive/CasaBella/2-getting-started)

---

## What is Casa Bella?

Casa Bella is a web-based management system for a beauty salon and product distributor located in Guavatá, Colombia. The system serves as a dual-purpose platform that enables:

1. **Service booking**: Clients can browse beauty services, book appointments, and manage their service history
2. **E-commerce**: Clients can purchase beauty products through an integrated shopping cart and checkout system
3. **Business management**: Administrative staff manage inventory, appointments, users, and promotions
4. **Operational workflow**: Employees confirm and complete service appointments with integrated invoicing

The system operates on a role-based access control model with three distinct user types, each with dedicated interfaces and functionality.

**Sources:** [app/templates/index.html L1-L297](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html#L1-L297)

 [app/templates/base.html L1-L270](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L270)

---

## Business Domain

Casa Bella operates as a hybrid business combining salon services and product distribution:

### Salon Services

The salon offers beauty services including manicures, pedicures, haircuts, hair coloring, facials, and personal care treatments. Services are bookable through an appointment system with configurable business hours:

| Day | Hours |
| --- | --- |
| Monday | 9:00 AM - 7:00 PM |
| Tuesday | 8:00 AM - 7:00 PM |
| Wednesday | 9:00 AM - 7:00 PM |
| Thursday | CLOSED |
| Friday | 9:00 AM - 7:00 PM |
| Saturday | 9:00 AM - 7:00 PM |
| Sunday | 9:00 AM - 6:00 PM |

Appointment booking enforces these hours and requires 30-minute time slot increments.

### Product Distribution

The system manages inventory for beauty products including cosmetics, personal care items, and accessories. Products have stock tracking with automatic notifications for low inventory levels.

### Contact Information

* **Location**: Calle 5 4, Guavatá, Santander, Colombia
* **Email**: [xiomarawo-0298@hotmail.com](mailto:xiomarawo-0298@hotmail.com)
* **Phone**: +57 314 304 5084
* **Social Media**: Facebook and Instagram (@CasaBella_salonydistribuidora1)

**Sources:** [app/templates/index.html L42-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html#L42-L110)

 [app/templates/base.html L187-L261](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L187-L261)

 [app/routes/client.py L796-L820](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L796-L820)

---

## User Roles and Access Control

The system implements three distinct user roles with separate navigation, dashboards, and permissions:

```

```

### Cliente (Client)

Default role for registered users. Clients can:

* Browse and purchase products via shopping cart (`Carrito`, `DetalleCarrito`)
* Book and manage service appointments (`Cita`)
* Save favorites (`Guardado`) and write reviews (`Reseña`)
* View purchase history (`Venta`, `DetalleVenta`)
* Manage personal profile (`Usuario`)

**Navigation routes**: [app/templates/base.html L40-L56](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L40-L56)

### Admin (Administrator)

Assigned to the first registered user, with manual assignment capability for additional admins. Administrators can:

* Manage all users (CRUD operations on `Usuario`)
* Manage products and services (`Producto`, `Servicio`)
* Track inventory movements (`InventarioMovimiento`)
* Create and manage promotions (`Promocion`)
* Assign employees to pending appointments (`Asignacion`)
* View analytics and notifications (`Notificacion`)

**Navigation routes**: [app/templates/base.html L58-L78](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L58-L78)

### Empleado (Employee)

Manually assigned role for staff members. Employees can:

* View assigned appointments in calendar format
* Confirm pending appointments (transition `estado` from 'pendiente' to 'confirmada')
* Complete confirmed appointments (transition to 'completada')
* Generate invoices automatically or manually
* Each employee has an `especialidad` field for service specialization

**Navigation routes**: [app/templates/base.html L80-L89](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L80-L89)

**Sources:** [app/templates/base.html L39-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L102)

 [app/models/users.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/users.py)

---

## Core Functionality

The system implements four primary business workflows:

### E-Commerce Workflow

```

```

Products and services can be added to a shopping cart with automatic promotion application. The checkout process:

1. Validates stock availability for products
2. Creates a `Venta` record with associated `DetalleVenta` entries
3. Records payment via `Pago` model
4. Generates inventory movements (`InventarioMovimiento`) for stock tracking
5. Produces a PDF invoice using ReportLab library
6. Updates cart state to 'completado'

**Key routes**: `client.productos`, `client.agregar_carrito`, `client.carrito`, `client.procesar_compra`

**Sources:** [app/routes/client.py L74-L455](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L74-L455)

### Appointment Workflow

```

```

Appointments progress through a state machine (`pendiente` → `confirmada` → `completada`) with cross-role coordination:

1. Client books appointment with date/time validation against business hours
2. System creates `Notificacion` records for client and admin
3. Admin views in FullCalendar and assigns employee via `Asignacion`
4. Employee confirms appointment
5. Employee completes appointment
6. System automatically generates invoice as `Venta` with PDF

**Key models**: `Cita`, `Asignacion`, `Notificacion`

**Sources:** [app/routes/client.py L782-L855](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L782-L855)

### Inventory Management

Administrators manage products and services with stock tracking:

* Create/update/delete operations on `Producto` and `Servicio`
* Stock levels tracked via `Producto.stock` field
* Inventory movements logged in `InventarioMovimiento` (tipo='entrada' or 'salida')
* Low stock triggers automatic `Notificacion` to admins
* Products organized by `Categoria`

**Key model**: `InventarioMovimiento` tracks all stock changes with `tipo_movimiento`, `cantidad`, and `motivo` fields.

**Sources:** Data model relationships visible in system diagrams

### Promotion System

Time-bound discounts applied to products or services:

* `Promocion` model stores discount percentage, start date, end date
* Can apply to either `id_producto` or `id_servicio` (polymorphic foreign keys)
* Active promotions queried by `fecha_inicio <= now <= fecha_fin`
* Discounts applied at cart level in `DetalleCarrito.precio_unitario`
* Promotion information preserved in invoices for historical accuracy

**Key routes**: `admin.gestion_promociones`, `client.productos`, `client.servicios`

**Sources:** [app/routes/client.py L59-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L59-L94)

 [app/routes/client.py L138-L152](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L138-L152)

---

## System Architecture

### Blueprint Organization

Casa Bella follows Flask's blueprint pattern for modular organization:

| Blueprint | URL Prefix | Purpose | Key Routes |
| --- | --- | --- | --- |
| `auth` | `/auth` | Authentication, registration, dashboards | `login`, `register`, `logout`, `cliente_dashboard` |
| `client` | `/client` | E-commerce and appointments | `productos`, `servicios`, `carrito`, `citas`, `favoritos` |
| `admin` | `/admin` | Resource management | `gestion_usuarios`, `gestion_productos`, `gestion_servicios`, `gestion_promociones`, `gestion_citas_pendientes` |
| `employee` | `/employee` | Appointment operations | `empleado_dashboard`, `trabajar_citas`, `confirmar_cita`, `completar_cita`, `generar_factura` |
| `home` | `/` | Public landing page | `index` |
| `notificaciones` | `/notificaciones` | User notifications | `notificaciones_dashboard` |

**Blueprint registration**: Application factory pattern in `app/__init__.py`

For detailed blueprint architecture, see [Blueprint Organization](/GroveLive/CasaBella/3.1-blueprint-organization).

**Sources:** [app/templates/base.html L28-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L28-L102)

 [app/routes/client.py L35](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L35-L35)

### Data Model Overview

```

```

**Key design patterns**:

* **Polymorphic references**: `DetalleCarrito`, `DetalleVenta`, `Guardado` use nullable foreign keys to reference either `Producto` or `Servicio`
* **State machines**: `Carrito.estado` (activo, completado, abandonado), `Cita.estado` (pendiente, confirmada, completada)
* **Audit trail**: `InventarioMovimiento` logs all stock changes with timestamps and motives

For complete data model documentation, see [Data Models](/GroveLive/CasaBella/3.2-data-models).

**Sources:** [app/models/carrito.py L1-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L1-L13)

 system diagrams

### Frontend Architecture

The frontend uses server-side rendering with progressive enhancement:

* **Base template**: [app/templates/base.html L1-L270](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L270)  provides role-based navigation and theme system
* **Jinja2 templating**: All pages extend `base.html` with block overrides
* **CSS theming**: Dark/light mode switcher using CSS custom properties (`data-theme` attribute)
* **AJAX integration**: jQuery for dynamic cart operations, favorites, and promotions
* **External dependencies**: Bootstrap 5.3, Bootstrap Icons, Google Fonts
* **Static assets**: Organized in `app/static/` (css/, js/, images/)

**Theme toggle**: [app/static/js/theme-toggle.js](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/theme-toggle.js)

 manages `localStorage` persistence

For frontend patterns, see [Frontend Architecture](/GroveLive/CasaBella/3.3-frontend-architecture).

**Sources:** [app/templates/base.html L13-L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L13-L22)

 [app/templates/base.html L106-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L106-L108)

---

## Technology Stack

### Backend

* **Framework**: Flask (Python web framework)
* **ORM**: SQLAlchemy for database abstraction
* **Authentication**: Flask-Login for session management
* **Password hashing**: Werkzeug security utilities
* **PDF generation**: ReportLab library
* **Image processing**: Pillow (PIL) for logo manipulation

### Frontend

* **CSS Framework**: Bootstrap 5.3.0 (via CDN)
* **Icons**: Bootstrap Icons 1.10.0
* **Fonts**: Google Fonts (Playfair Display, Inter)
* **JavaScript**: jQuery for AJAX, FullCalendar for appointment visualization
* **Theme system**: CSS custom properties with JavaScript toggle

### Database

* **RDBMS**: PostgreSQL
* **Migrations**: Alembic (Flask-Migrate)
* **Connection**: SQLAlchemy engine with connection pooling

### External Integrations

* **Maps**: Google Maps embedded iframe for location display
* **Communication**: WhatsApp direct links for client contact
* **Social Media**: Facebook and Instagram links

**Sources:** [app/templates/base.html L13-L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L13-L18)

 [app/routes/client.py L1-L33](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L33)

 [Dockerfile L1-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L1-L21)

---

## Deployment Model

### Docker Containerization

The application deploys as a containerized Flask application:

```

```

**Container specifications**:

* **Base image**: `python:3.13-alpine` for minimal footprint
* **Working directory**: `/app`
* **Exposed port**: 8085
* **Command**: `flask run --host=0.0.0.0 --port=8085`
* **Dependencies**: Installed from `requirements.txt` with pip

**Environment configuration**: Database connection via environment variables (DATABASE_URL pattern expected)

**Deployment process**:

1. Build Docker image from [Dockerfile L1-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L1-L21)
2. Configure PostgreSQL connection via environment variables
3. Run migrations: `flask db upgrade`
4. Start container exposing port 8085

For setup instructions, see [Getting Started](/GroveLive/CasaBella/2-getting-started).

**Sources:** [Dockerfile L1-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L1-L21)

---

## Key Design Principles

### Role-Based Access Control

All routes enforce role checks via `@login_required` decorator and `current_user.rol` validation. Unauthorized access attempts redirect to login with flash messages.

### State Management

Business entities use explicit state enums:

* `Carrito.estado`: Enum('activo', 'completado', 'abandonado')
* `Cita.estado`: Enum('pendiente', 'confirmada', 'completada')

### Audit Trail

All inventory changes logged in `InventarioMovimiento` with:

* `tipo_movimiento`: 'entrada' or 'salida'
* `cantidad`: Numeric change amount
* `motivo`: Human-readable reason (e.g., "Venta ID: 123")

### Polymorphic Data References

Multiple tables support both products and services through nullable foreign key pairs:

* `DetalleCarrito`: `id_producto` OR `id_servicio`
* `DetalleVenta`: `id_producto` OR `id_servicio`
* `Guardado`: `id_producto` OR `id_servicio`
* `Promocion`: `id_producto` OR `id_servicio`

### Ephemeral File Generation

PDF invoices generated on-demand and immediately deleted after serving. No persistent file storage; all invoice data preserved in database records (`Venta`, `DetalleVenta`).

**Invoice generation**: [app/routes/client.py L343-L448](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L343-L448)

**Sources:** [app/routes/client.py L52-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L57)

 [app/models/carrito.py L9](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L9-L9)