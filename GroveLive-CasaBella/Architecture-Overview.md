# Architecture Overview

> **Relevant source files**
> * [app/routes/__pycache__/auth.cpython-313.pyc](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/__pycache__/auth.cpython-313.pyc)
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/routes/auth.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py)
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/routes/employee.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py)
> * [app/templates/base.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)
> * [app/templates/trabajar_citas.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html)

## Purpose and Scope

This document provides a high-level overview of the Casa Bella system architecture, explaining how the Flask application is organized, how blueprints interact, and how the frontend and backend components communicate. This page focuses on structural patterns and system organization rather than specific features.

For detailed information about individual subsystems, see:

* Blueprint structure and URL routing: [Blueprint Organization](/GroveLive/CasaBella/3.1-blueprint-organization)
* Database schema and SQLAlchemy models: [Data Models](/GroveLive/CasaBella/3.2-data-models)
* Template inheritance and static assets: [Frontend Architecture](/GroveLive/CasaBella/3.3-frontend-architecture)
* User authentication flows: [Authentication & Authorization](/GroveLive/CasaBella/4-authentication-and-authorization)

## System Architecture

Casa Bella is a Flask-based web application that follows a modular blueprint architecture with role-based access control. The system serves three distinct user roles—clients, administrators, and employees—each with dedicated feature sets and UI workflows.

### Application Stack

```

```

**Sources**: [app/templates/base.html L1-L270](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L270)

 [app/routes/client.py L1-L35](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L35)

 [app/routes/admin.py L1-L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L1-L22)

 [app/routes/employee.py L1-L27](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L1-L27)

 [app/routes/auth.py L1-L7](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L1-L7)

## Blueprint Architecture

Casa Bella uses Flask's blueprint pattern to organize functionality into five modular components:

| Blueprint | URL Prefix | Primary Role | Key Responsibilities |
| --- | --- | --- | --- |
| `auth` | `/auth` | Authentication | Login, logout, registration, session management |
| `client` | `/client` | Client features | Products, services, cart, checkout, appointments |
| `admin` | `/admin` | Administration | User/product/service management, analytics, appointment assignment |
| `employee` | `/employee` | Employee operations | Appointment fulfillment, invoice generation |
| `home` | `/` | Public pages | Landing page, public catalog |

### Blueprint Registration and Routing

```

```

**Sources**: [app/routes/auth.py L7](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L7-L7)

 [app/routes/client.py L35](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L35-L35)

 [app/routes/admin.py L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L22-L22)

 [app/routes/employee.py L27](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L27-L27)

## Role-Based Access Control

The system implements three-tier access control enforced through the `current_user.rol` attribute checked at the route level.

### User Role Hierarchy

```

```

### Access Control Implementation

Each protected route implements role checking using Flask-Login's `@login_required` decorator combined with manual role validation:

```

```

**Pattern observed in**:

* Client routes: [app/routes/client.py L52-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L57)  [app/routes/client.py L74-L79](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L74-L79)
* Admin routes: [app/routes/admin.py L28-L33](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L28-L33)  [app/routes/admin.py L156-L161](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L156-L161)
* Employee routes: [app/routes/employee.py L36-L41](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L36-L41)  [app/routes/employee.py L46-L51](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L46-L51)

### Dynamic Navigation

The base template renders role-specific navigation based on `current_user.rol`:

| User Role | Navigation Items |
| --- | --- |
| Guest | Inicio, Servicios, Productos, Login, Register |
| Cliente | Inicio, Servicios, Productos, Reservar Cita, Dashboard Cliente, Carrito, Favoritos |
| Admin | Inicio, Panel Admin, Gestión Usuarios, Gestión Productos, Gestión Servicios, Gestión Promociones, Citas Pendientes |
| Empleado | Inicio, Panel Empleado, Trabajar Citas |

**Sources**: [app/templates/base.html L39-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L102)

## Request-Response Flow

### Server-Side Rendering Flow

```

```

**Sources**: [app/routes/client.py L74-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L74-L94)

### AJAX-Enhanced Flow

For dynamic features like cart updates and promotion filtering, the application uses hybrid server-rendered pages with AJAX enhancements:

```

```

**Sources**: [app/routes/client.py L228-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L228-L269)

## Data Access Patterns

### SQLAlchemy ORM Integration

All blueprints interact with the database through SQLAlchemy models. Common patterns include:

**Basic Queries**:

```

```

**Sources**: [app/routes/admin.py L209](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L209-L209)

 [app/routes/client.py L88](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L88-L88)

 [app/routes/employee.py L54-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L54-L57)

### Context Processors

The client blueprint injects cart data into all template contexts:

```

```

**Sources**: [app/routes/client.py L45-L50](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L45-L50)

## Frontend-Backend Integration

### Template Inheritance Pattern

All pages extend `base.html`, which provides:

* Role-based navigation
* Theme toggle functionality
* Cart/favorites badges
* Notification bell
* User dropdown menu

```

```

**Sources**: [app/templates/base.html L1-L24](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L24)

 [app/templates/base.html L183-L184](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L183-L184)

### Progressive Enhancement Strategy

Pages are initially rendered server-side with full content, then enhanced with JavaScript for dynamic features:

1. **Initial Load**: Server renders complete HTML with data
2. **JavaScript Enhancement**: Add AJAX handlers for cart updates, filtering, etc.
3. **Graceful Degradation**: Forms still work with full page refresh if JavaScript fails

**Example - Product Filtering**:

* Base URL: `/client/productos` - Shows all products
* Filtered URL: `/client/productos?filter=promotions` - Server-side filter
* AJAX calls enhance sorting/updates without full reload

**Sources**: [app/routes/client.py L74-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L74-L94)

 [app/routes/admin.py L134-L140](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L134-L140)

## Service Integration Patterns

### PDF Generation

Both client and employee blueprints use ReportLab for invoice generation with an ephemeral file pattern:

1. Generate PDF to temporary file in `app/static/`
2. Send file to client using `send_file()`
3. Immediately delete file after serving
4. Invoice data persists only in `Venta` and `DetalleVenta` tables

**Sources**: [app/routes/client.py L343-L449](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L343-L449)

 [app/routes/employee.py L198-L278](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L198-L278)

### External CDN Resources

The base template loads external dependencies:

* Bootstrap 5.3.0 CSS/JS from CDN
* Bootstrap Icons from CDN
* Google Fonts (Playfair Display, Inter)
* FullCalendar library for appointment views

**Sources**: [app/templates/base.html L13-L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L13-L18)

 [app/templates/trabajar_citas.html L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L5-L5)

## State Management

### Shopping Cart Lifecycle

```

```

The `Carrito` model tracks state through the `estado` field:

* `'activo'`: Current shopping session
* `'completado'`: Successfully checked out
* `'abandonado'`: Reserved for future use

**Sources**: [app/routes/client.py L114-L121](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L114-L121)

 [app/routes/client.py L338-L341](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L338-L341)

### Appointment State Machine

```

```

State transitions enforce business rules:

* Only `'pendiente'` citas can be confirmed by employees
* Only `'confirmada'` citas can be completed
* Only `'completada'` citas generate invoices

**Sources**: [app/routes/employee.py L98-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L98-L114)

 [app/routes/employee.py L116-L144](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L116-L144)

## Error Handling and User Feedback

### Flash Message Pattern

All blueprints use Flask's flash messaging for user feedback:

```

```

Messages are displayed in templates through standardized blocks:

**Sources**: [app/routes/client.py L168](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L168-L168)

 [app/routes/admin.py L195](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L195-L195)

 [app/templates/dashboard_admin.html L14-L23](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L14-L23)

### Exception Handling

Routes implement try-except blocks with transaction rollback:

```

```

**Sources**: [app/routes/client.py L285-L294](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L285-L294)

 [app/routes/admin.py L197-L200](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L197-L200)

## Logging and Debugging

All route modules configure Python logging:

```

```

Debug statements track:

* User actions and IDs
* Database query results
* Cart state changes
* Invoice generation steps

**Sources**: [app/routes/client.py L37-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L37-L39)

 [app/routes/admin.py L24-L26](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L24-L26)

 [app/routes/employee.py L29-L31](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L29-L31)