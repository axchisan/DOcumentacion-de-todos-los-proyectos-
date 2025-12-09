# Authentication & Authorization

> **Relevant source files**
> * [app/routes/__pycache__/auth.cpython-313.pyc](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/__pycache__/auth.cpython-313.pyc)
> * [app/routes/auth.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py)
> * [app/templates/base.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html)

This document covers the authentication system, user registration, login/logout flows, and role-based access control (RBAC) in the Casa Bella application. It explains how users are authenticated, how sessions are managed, and how different user roles (cliente, admin, empleado) are authorized to access specific features and routes.

For information about the specific features available to each role, see:

* Client features: [Client Features](/GroveLive/CasaBella/5-client-features)
* Admin features: [Admin Features](/GroveLive/CasaBella/6-admin-features)
* Employee features: [Employee Features](/GroveLive/CasaBella/7-employee-features)

---

## System Overview

Casa Bella implements a Flask-Login-based authentication system with a three-tier role hierarchy. The system handles user registration with automatic admin assignment for the first user, password hashing using werkzeug, session-based authentication, and role-based route protection.

**Sources:** [app/routes/auth.py L1-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L1-L87)

---

## Authentication Architecture

```

```

The authentication system is implemented as a Flask Blueprint registered at the `/auth` URL prefix. It integrates with Flask-Login for session management and werkzeug.security for password hashing.

**Sources:** [app/routes/auth.py L1-L7](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L1-L7)

 [app/routes/auth.py L9-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L9-L87)

---

## User Registration Flow

```

```

### Registration Logic

The registration process is handled by the `register()` function in [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

 Key implementation details:

| Step | Implementation | Code Reference |
| --- | --- | --- |
| Form data extraction | `request.form['nombre']`, `request.form['email']`, etc. | [app/routes/auth.py L35-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L35-L39) |
| Email uniqueness check | `Usuario.query.filter_by(email=email).first()` | [app/routes/auth.py L41-L43](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L41-L43) |
| First user detection | `Usuario.query.count() == 0` | [app/routes/auth.py L45](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L45-L45) |
| Role assignment | `'admin' if is_first_user else 'cliente'` | [app/routes/auth.py L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L46-L46) |
| Password hashing | `generate_password_hash(password)` | [app/routes/auth.py L48](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L48-L48) |
| User creation | `Usuario(nombre=..., email=..., contraseña=..., rol=..., ...)` | [app/routes/auth.py L49](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L49-L49) |
| Database commit | `db.session.add(user)` + `db.session.commit()` | [app/routes/auth.py L52-L53](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L52-L53) |

### Role Assignment Rules

* **First user**: Automatically assigned `rol='admin'`
* **Subsequent users**: Default to `rol='cliente'`
* **Employee role**: Must be manually assigned by an admin after registration (not assignable during registration)

The `especialidad` field is only stored for users with `rol='empleado'`, set to `None` for other roles [app/routes/auth.py L49](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L49-L49)

**Sources:** [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

---

## Login Flow

```

```

### Login Implementation

The login process is handled by the `login()` function in [app/routes/auth.py L9-L30](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L9-L30)

**Key steps:**

1. **User lookup**: Query the database using `Usuario.query.filter_by(email=email).first()` [app/routes/auth.py L15](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L15-L15)
2. **Password verification**: Compare hashed password using `check_password_hash(user.contraseña, password)` [app/routes/auth.py L17](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L17-L17)
3. **Session creation**: Call `login_user(user)` to establish Flask-Login session [app/routes/auth.py L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L18-L18)
4. **Role-based redirect**: Redirect to appropriate dashboard based on `user.rol` [app/routes/auth.py L21-L26](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L21-L26)

**Redirect mapping:**

| Role | Redirect Target | Route Name |
| --- | --- | --- |
| `admin` | Admin dashboard | `auth.admin_dashboard` |
| `empleado` | Employee dashboard | `auth.empleado_dashboard` |
| `cliente` | Client dashboard | `auth.cliente_dashboard` |

**Sources:** [app/routes/auth.py L9-L30](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L9-L30)

---

## Session Management

Flask-Login manages user sessions through server-side session cookies. Once `login_user(user)` is called, the user object is stored in the session and accessible via the `current_user` proxy throughout the application.

### Current User Access

The `current_user` proxy is available globally in:

* **Python routes**: Imported via `from flask_login import current_user` [app/routes/auth.py L2](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L2-L2)
* **Jinja2 templates**: Automatically injected, accessible as `current_user` [app/templates/base.html L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L39)

### Session Attributes

The `current_user` object provides access to all `Usuario` model attributes:

| Attribute | Type | Description |
| --- | --- | --- |
| `current_user.id_usuario` | int | Primary key |
| `current_user.nombre` | str | User's full name |
| `current_user.email` | str | User's email address |
| `current_user.rol` | str | One of: 'cliente', 'admin', 'empleado' |
| `current_user.telefono` | str | Phone number |
| `current_user.especialidad` | str | Employee specialty (if empleado) |
| `current_user.is_authenticated` | bool | Flask-Login property |

### Authentication State Checks

Templates use `current_user.is_authenticated` to determine if a user is logged in [app/templates/base.html L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L39)

:

```

```

**Sources:** [app/routes/auth.py L2](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L2-L2)

 [app/templates/base.html L39-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L102)

---

## Role-Based Access Control (RBAC)

### Role Hierarchy

Casa Bella implements three distinct user roles:

```

```

### Role Authorization Patterns

#### Template-Level Authorization

The base template dynamically renders navigation based on `current_user.rol` [app/templates/base.html L39-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L102)

:

```

```

#### Route-Level Authorization

Dashboard routes enforce role checks using conditional logic [app/routes/auth.py L62-L68](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L62-L68)

 [app/routes/auth.py L74-L80](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L74-L80)

:

```

```

### Role Permission Matrix

| Feature Area | cliente | admin | empleado |
| --- | --- | --- | --- |
| Browse products/services | ✓ | ✓ | ✓ |
| Shopping cart | ✓ | ✗ | ✗ |
| Book appointments | ✓ | ✗ | ✗ |
| Favorites | ✓ | ✗ | ✗ |
| Reviews | ✓ | ✗ | ✗ |
| User management | ✗ | ✓ | ✗ |
| Product/service management | ✗ | ✓ | ✗ |
| Promotion management | ✗ | ✓ | ✗ |
| Assign appointments | ✗ | ✓ | ✗ |
| Confirm/complete appointments | ✗ | ✗ | ✓ |
| Generate invoices | ✗ | ✗ | ✓ |

**Sources:** [app/templates/base.html L39-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L102)

 [app/routes/auth.py L62-L80](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L62-L80)

---

## Protected Routes and Decorators

### @login_required Decorator

Flask-Login's `@login_required` decorator protects routes that require authentication. Applied to routes in [app/routes/auth.py L63](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L63-L63)

 [app/routes/auth.py L70](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L70-L70)

 [app/routes/auth.py L75](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L75-L75)

 [app/routes/auth.py L83](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L83-L83)

:

```

```

If an unauthenticated user attempts to access a `@login_required` route, Flask-Login automatically redirects them to the login page.

### Manual Authorization Checks

Routes perform additional role checks after `@login_required`:

```

```

### Authorization Flow

```

```

**Sources:** [app/routes/auth.py L62-L80](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L62-L80)

---

## Logout Flow

The logout process is handled by the `logout()` function in [app/routes/auth.py L82-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L82-L87)

:

```

```

### Logout Sequence

1. **Call `logout_user()`**: Removes user from Flask-Login session [app/routes/auth.py L85](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L85-L85)
2. **Flash message**: Notify user of successful logout [app/routes/auth.py L86](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L86-L86)
3. **Redirect**: Send user to login page [app/routes/auth.py L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L87-L87)

After logout, `current_user.is_authenticated` becomes `False`, and the user is treated as a guest in all subsequent requests.

**Sources:** [app/routes/auth.py L82-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L82-L87)

---

## Frontend Integration

### Navigation Bar Role Switching

The base template implements conditional navigation that responds to authentication state and user role [app/templates/base.html L37-L103](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L37-L103)

:

```

```

### User Dropdown Menu

Authenticated users see a dropdown menu with profile information and role-specific actions [app/templates/base.html L154-L176](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L154-L176)

:

| Dropdown Item | Visible To | Route |
| --- | --- | --- |
| User name | All authenticated | Display only |
| User email | All authenticated | Display only |
| User role | All authenticated | Display only |
| Mi Perfil | All authenticated | `client.perfil` |
| Mis Citas | All authenticated | `client.citas` |
| Panel Admin | Admin only | `admin.admin_dashboard` |
| Cerrar Sesión | All authenticated | `auth.logout` |

### Cart and Favorites Icons

Only visible to users with `rol='cliente'` [app/templates/base.html L111-L130](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L111-L130)

:

```

```

**Sources:** [app/templates/base.html L37-L180](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L37-L180)

---

## Security Considerations

### Password Storage

Passwords are never stored in plaintext. The system uses werkzeug's `generate_password_hash()` to create salted hashes [app/routes/auth.py L48](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L48-L48)

:

```

```

The `contraseña` field in the `Usuario` model stores the hashed password, which is verified during login using `check_password_hash()` [app/routes/auth.py L17](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L17-L17)

### Session Security

* Sessions are managed server-side by Flask
* Session cookies use Flask's default security settings
* `@login_required` decorator prevents unauthorized access to protected routes

### Role Enforcement

The system enforces role-based access at multiple layers:

1. **Route-level**: Manual checks in route handlers [app/routes/auth.py L65-L67](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L65-L67)
2. **Template-level**: Conditional rendering based on `current_user.rol` [app/templates/base.html L39-L90](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L90)
3. **Blueprint-level**: Separate blueprints for different roles (auth, client, admin, employee)

**Sources:** [app/routes/auth.py L3](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L3-L3)

 [app/routes/auth.py L17](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L17-L17)

 [app/routes/auth.py L48](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L48-L48)

---

## Summary: Authentication Flow Diagram

```

```

**Sources:** [app/routes/auth.py L9-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L9-L87)

 [app/templates/base.html L37-L180](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L37-L180)