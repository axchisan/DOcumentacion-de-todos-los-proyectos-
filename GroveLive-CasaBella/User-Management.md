# User Management

> **Relevant source files**
> * [app/routes/__pycache__/auth.cpython-313.pyc](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/__pycache__/auth.cpython-313.pyc)
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/routes/auth.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)

## Purpose and Scope

This document describes the user management system in Casa Bella, which handles CRUD operations for user accounts, role assignment, and access control. It covers administrative user management performed through the admin blueprint and the automatic role assignment during user registration.

For information about authentication and login/logout flows, see [Authentication & Authorization](/GroveLive/CasaBella/4-authentication-and-authorization). For information about the admin dashboard overview, see [Admin Dashboard & Analytics](/GroveLive/CasaBella/6.1-admin-dashboard-and-analytics).

---

## Overview

The Casa Bella user management system implements a three-tier role hierarchy (`admin`, `empleado`, `cliente`) with administrative controls for creating, updating, and deleting user accounts. The system enforces role-based access control and includes special logic for automatic admin assignment during initial setup.

**Sources:** [app/routes/admin.py L156-L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L156-L256)

 [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

---

## User Data Model

The `Usuario` model serves as the central entity for user management. Based on the codebase analysis, the model contains the following attributes:

| Field | Type | Description | Constraints |
| --- | --- | --- | --- |
| `id_usuario` | Integer | Primary key | Auto-increment |
| `nombre` | String | Full name | Required |
| `email` | String | Email address | Required, Unique |
| `contraseña` | String | Hashed password | Required |
| `rol` | Enum | User role | Required: `'admin'`, `'empleado'`, `'cliente'` |
| `telefono` | String | Phone number | Optional |
| `especialidad` | String | Employee specialty | Required if `rol='empleado'`, else NULL |

The model uses Werkzeug's `generate_password_hash()` for password hashing and maintains relationships with other entities including `Cita`, `Venta`, `Carrito`, `Guardado`, `Resena`, and `Notificacion`.

```

```

**Sources:** [app/routes/admin.py L165-L201](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L165-L201)

 [app/routes/admin.py L203-L238](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L203-L238)

 [app/models/users.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/users.py)

---

## Role Hierarchy and First User Logic

### Role Types

Casa Bella implements three distinct user roles:

| Role | Assignment Method | Capabilities |
| --- | --- | --- |
| `admin` | Automatic for first user; manual thereafter | Full system access, user management, inventory control, appointment assignment |
| `empleado` | Manual by admin | Appointment confirmation/completion, invoice generation |
| `cliente` | Default for new registrations | Product browsing, cart management, appointment booking |

### Automatic Admin Assignment

The registration system implements special logic to automatically assign the `admin` role to the first user:

```

```

This logic appears in [app/routes/auth.py L45-L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L45-L46)

:

```
is_first_user = Usuario.query.count() == 0
rol = 'admin' if is_first_user else 'cliente'
```

**Sources:** [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

---

## Administrative User Management Interface

### Viewing Users

The `/admin/gestion_usuarios` route provides a listing of all registered users. Access is restricted to users with `rol='admin'`.

**Route:** `admin.gestion_usuarios`
**HTTP Method:** GET
**Access Control:** `current_user.rol == 'admin'`
**Template:** `gestion_usuarios.html`

```

```

**Sources:** [app/routes/admin.py L156-L163](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L156-L163)

---

## CRUD Operations

### Creating Users

Administrators can create new users with any role through the `/admin/agregar_usuario` route.

**Route:** `admin.agregar_usuario`
**HTTP Methods:** GET, POST
**Access Control:** `current_user.rol == 'admin'`
**Template:** `agregar_usuario.html`

#### Validation Rules

| Field | Validation |
| --- | --- |
| `nombre` | Required |
| `email` | Required, must be unique |
| `rol` | Required, must be one of: `'admin'`, `'empleado'`, `'cliente'` |
| `contraseña` | Required |
| `especialidad` | Required if `rol='empleado'`, otherwise optional |

#### Process Flow

```

```

**Key Implementation Details:**

* Password hashing: [app/routes/admin.py L188](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L188-L188)  uses `generate_password_hash(contraseña)`
* Employee specialty enforcement: [app/routes/admin.py L181-L183](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L181-L183)
* Unique email constraint handled via `IntegrityError`: [app/routes/admin.py L197-L200](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L197-L200)
* Conditional specialty assignment: [app/routes/admin.py L191](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L191-L191)  sets `especialidad if rol == 'empleado' else None`

**Sources:** [app/routes/admin.py L165-L201](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L165-L201)

---

### Updating Users

The `/admin/editar_usuario/<int:id_usuario>` route handles user updates with similar validation to user creation.

**Route:** `admin.editar_usuario`
**HTTP Methods:** GET, POST
**Access Control:** `current_user.rol == 'admin'`
**Template:** `editar_usuario.html`

#### Update Process

```

```

**Key Implementation Details:**

* Password is optional during update: [app/routes/admin.py L227-L228](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L227-L228)  only hashes if `contraseña` is provided
* Usuario instance retrieved with `get_or_404()`: [app/routes/admin.py L209](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L209-L209)
* Same validation rules as creation apply: [app/routes/admin.py L217-L222](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L217-L222)

**Sources:** [app/routes/admin.py L203-L238](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L203-L238)

---

### Deleting Users

The `/admin/eliminar_usuario/<int:id_usuario>` route handles user deletion with confirmation.

**Route:** `admin.eliminar_usuario`
**HTTP Methods:** GET, POST
**Access Control:** `current_user.rol == 'admin'`
**Template:** `eliminar_usuario.html`

#### Deletion Constraints

User deletion may fail if the user has associated records in other tables (foreign key constraints). The system catches `IntegrityError` to handle this case gracefully.

```

```

**Key Implementation Details:**

* GET request shows confirmation page: [app/routes/admin.py L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L256-L256)
* POST request executes deletion: [app/routes/admin.py L247-L249](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L247-L249)
* Foreign key constraints protected by `IntegrityError`: [app/routes/admin.py L252-L254](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L252-L254)

**Sources:** [app/routes/admin.py L240-L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L240-L256)

---

## Access Control Implementation

All user management routes enforce role-based access control using the following pattern:

```

```

This check appears at the beginning of each admin route:

* User listing: [app/routes/admin.py L159-L161](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L159-L161)
* User creation: [app/routes/admin.py L168-L170](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L168-L170)
* User editing: [app/routes/admin.py L206-L208](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L206-L208)
* User deletion: [app/routes/admin.py L243-L245](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L243-L245)

The pattern relies on Flask-Login's `current_user` proxy to access the authenticated user's role attribute.

**Sources:** [app/routes/admin.py L156-L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L156-L256)

---

## Integration with Dashboard

The admin dashboard provides a card-based entry point to user management at `/admin/dashboard`. The dashboard displays:

* **User Count:** Total registered users via `Usuario.query.count()` ([app/routes/admin.py L36](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L36-L36) )
* **Management Link:** Button to access `admin.gestion_usuarios` ([app/templates/dashboard_admin.html L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L94-L94) )

```

```

**Sources:** [app/routes/admin.py L28-L154](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L28-L154)

 [app/templates/dashboard_admin.html L24-L96](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L24-L96)

---

## Error Handling and User Feedback

The user management system uses Flask's flash messaging system to provide feedback:

| Message Type | Trigger | Example Message |
| --- | --- | --- |
| `success` | Successful operation | "Usuario agregado con éxito." |
| `danger` | Validation error or database error | "Error: El email ya está en uso." |
| `danger` | Unauthorized access | "Acceso denegado. Solo para administradores." |
| `warning` | Business rule violation | "La especialidad es obligatoria para empleados." |

All flash messages are displayed in the template via Bootstrap alert components: [app/templates/dashboard_admin.html L14-L23](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L14-L23)

**Sources:** [app/routes/admin.py L165-L256](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L165-L256)

---

## Database Transaction Management

User management operations follow a consistent transaction pattern:

1. **Create/Update:** ``` ```
2. **Delete:** ``` ```
3. **Rollback on Error:** ``` ```

This pattern appears throughout the admin routes to ensure database consistency and proper error handling.

**Sources:** [app/routes/admin.py L193-L200](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L193-L200)

 [app/routes/admin.py L252-L254](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L252-L254)