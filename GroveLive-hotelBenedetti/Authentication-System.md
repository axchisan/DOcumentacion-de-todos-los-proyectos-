# Authentication System

> **Relevant source files**
> * [assets/css/StyleIndex.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css)
> * [assets/css/StyleLogin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css)
> * [assets/img/loginfondo.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/loginfondo.jpg)
> * [assets/js/login.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js)
> * [controllers/AuthController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php)

## Purpose and Scope

The Authentication System handles user login and registration for the Hotel Benedetti platform. This system manages credential validation, session creation, and role-based routing to appropriate interfaces. It provides both client-side and server-side validation, implements secure password handling, and controls access based on user roles (cliente, administrador, recepcionista, mucama).

For information about role-specific interfaces after authentication, see [Administrator Interface](/GroveLive/hotelBenedetti/3.2-administrator-interface), [Receptionist Interface](/GroveLive/hotelBenedetti/3.3-receptionist-interface), [Maid Interface](/GroveLive/hotelBenedetti/3.4-maid-interface), and [Client Interface](/GroveLive/hotelBenedetti/3.5-client-interface).

## System Architecture Overview

The authentication system follows a two-tier client-server architecture with JavaScript handling client-side validation and PHP controllers managing server-side authentication and session management.

```

```

**Sources:** [assets/js/login.js L1-L133](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L1-L133)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

 [assets/css/StyleLogin.css L1-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L1-L196)

 [assets/css/StyleIndex.css L1-L169](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css#L1-L169)

## Authentication Flow

### Login Process

The login flow involves client-side form validation, AJAX submission to the server, server-side authentication, session creation, and role-based redirection.

```

```

**Sources:** [assets/js/login.js L8-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L8-L48)

 [controllers/AuthController.php L41-L42](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L41-L42)

### Client-Side Login Implementation

The `login()` function in `login.js` handles form data collection, validation, and AJAX communication.

| Step | Function | File Location | Description |
| --- | --- | --- | --- |
| 1 | Data Collection | [assets/js/login.js L9-L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L9-L11) | Retrieves email, password, and rol from form fields |
| 2 | Required Field Check | [assets/js/login.js L13-L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L13-L16) | Validates all fields are non-empty |
| 3 | Email Format Validation | [assets/js/login.js L18-L22](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L18-L22) | Uses regex `/^[^\s@]+@[^\s@]+\.[^\s@]+$/` to validate email |
| 4 | AJAX Request | [assets/js/login.js L24-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L24-L27) | Sends POST request with URLSearchParams to AuthController.php |
| 5 | Response Parsing | [assets/js/login.js L28-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L28-L37) | Converts response text to JSON |
| 6 | Success Handling | [assets/js/login.js L39-L40](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L39-L40) | Redirects to role-specific page using `window.location.href` |
| 7 | Error Handling | [assets/js/login.js L42](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L42-L42) | Displays error message via `showError()` |

**Sources:** [assets/js/login.js L8-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L8-L48)

### Server-Side Login Processing

The `AuthController.php` acts as a request router that delegates authentication logic to `UsuarioController`.

#### AuthController Request Handling

```

```

**Sources:** [controllers/AuthController.php L30-L57](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L30-L57)

The AuthController performs critical output buffering management to ensure clean JSON responses:

| Operation | Lines | Purpose |
| --- | --- | --- |
| Start Output Buffering | [controllers/AuthController.php L7-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L7-L20) | Captures any accidental output |
| Clear Existing Buffers | [controllers/AuthController.php L17-L19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L17-L19) | Removes all output buffer levels |
| Pre-Response Buffer Check | [controllers/AuthController.php L60-L64](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L60-L64) | Logs and clears unexpected output |
| Exception Buffer Check | [controllers/AuthController.php L68-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L68-L72) | Handles output in error scenarios |
| Final Buffer Flush | [controllers/AuthController.php L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L79-L79) | Sends clean JSON response |

**Sources:** [controllers/AuthController.php L7-L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L7-L79)

## Registration Process

### Registration Flow

New users register through the client interface, with the system automatically assigning the `cliente` role.

```

```

**Sources:** [assets/js/login.js L50-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L50-L115)

 [controllers/AuthController.php L43-L45](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L43-L45)

### Client-Side Registration Validation

The registration process enforces stricter validation than login:

| Validation Rule | Implementation | Error Message |
| --- | --- | --- |
| All Fields Required | [assets/js/login.js L58-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L58-L61) | "Por favor, completa todos los campos." |
| Email Format | [assets/js/login.js L63-L67](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L63-L67) | "Por favor, ingresa un correo electrónico válido." |
| Password Length | [assets/js/login.js L69-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L69-L72) | "La contraseña debe tener al menos 6 caracteres." |

The `register()` function constructs a URLSearchParams object with all registration data:

```yaml
action: "register"
nombre: [user input]
apellido: [user input]
dni: [user input]
telefono: [user input]
email: [user input]
password: [user input]
```

**Sources:** [assets/js/login.js L50-L82](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L50-L82)

### Form Toggling System

The authentication page supports seamless switching between login and registration forms:

```

```

**Sources:** [assets/js/login.js L117-L127](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L117-L127)

## Session Management and Role-Based Routing

Upon successful authentication, the server stores user information in PHP session variables and determines the appropriate redirect URL based on the user's role.

### Session Data Structure

The `UsuarioController` (referenced by AuthController) stores the following session variables:

| Session Variable | Purpose | Set During |
| --- | --- | --- |
| `$_SESSION['usuario_id']` | Unique user identifier | Login |
| `$_SESSION['nombre']` | User's first name | Login |
| `$_SESSION['apellido']` | User's last name | Login |
| `$_SESSION['email']` | User's email address | Login |
| `$_SESSION['rol']` | User's role (cliente/administrador/recepcionista/mucama) | Login |

**Sources:** [controllers/AuthController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L2-L2)

### Role-Based Redirect Mapping

After successful authentication, users are redirected to their role-specific interface:

| Role | Redirect Path | Interface Purpose |
| --- | --- | --- |
| `cliente` | `../views/cliente.php` | Guest booking and reservation management |
| `administrador` | `../views/admin.php` | Employee and room administration |
| `recepcionista` | `../views/recepcionista.php` | Front desk check-in/check-out operations |
| `mucama` | `../views/mucama.php` | Housekeeping task management |

The redirect URL is returned in the JSON response `data.redirect` field and processed by JavaScript:

```

```

**Sources:** [assets/js/login.js L39-L40](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L39-L40)

 [controllers/AuthController.php L41-L45](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L41-L45)

## Visual Design System

### Login Page Design

The login page (`StyleLogin.css`) implements a dark-themed centered modal design with purple accent colors.

#### Layout Structure

```

```

**Sources:** [assets/css/StyleLogin.css L9-L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L9-L51)

#### Key Design Elements

| Element | CSS Properties | Purpose |
| --- | --- | --- |
| Body Background | `background-image: url('../img/fondohotelcliente2.webp')` | Hotel-themed visual |
| Background Overlay | `background-color: rgba(35, 39, 42, 0.8)` | Improves content readability |
| Container | `background-color: #2c2f33; border-radius: 8px` | Dark card styling |
| Input Fields | `background-color: #36393f; color: #dcddde` | Dark theme consistency |
| Input Focus | `border-color: #9b59b6; box-shadow: 0 0 5px rgba(155, 89, 182, 0.5)` | Purple accent on focus |
| Primary Button | `background-color: #9b59b6; border-radius: 20px` | Purple rounded buttons |
| Cancel Button | `background-color: #f44336` | Red for destructive action |
| Links | `color: #9b59b6` | Purple accent for consistency |

**Sources:** [assets/css/StyleLogin.css L86-L157](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L86-L157)

### Index/Welcome Page Design

The index page (`StyleIndex.css`) provides a welcoming landing page before authentication.

#### Layout Comparison

| Aspect | Login Page | Index Page |
| --- | --- | --- |
| Background Image | `fondohotelcliente2.webp` | `fondo2.jpg` |
| Primary Element | Centered `.container` (400px) | Centered `main` (800px max) |
| Layout Type | Flexbox center | Block with auto margins |
| Main Component | Form with fields | Welcome section with button |
| Button Style | Full width, rounded | Inline-block, rounded |

**Sources:** [assets/css/StyleIndex.css L1-L169](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css#L1-L169)

 [assets/css/StyleLogin.css L1-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L1-L196)

#### Welcome Section Structure

```

```

**Sources:** [assets/css/StyleIndex.css L39-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css#L39-L106)

## Client-Side Error Handling

The `showError()` function provides consistent error messaging across both login and registration forms:

```

```

**Sources:** [assets/js/login.js L129-L133](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L129-L133)

### Error Message Catalog

| Error Source | Element ID | Typical Messages |
| --- | --- | --- |
| Login validation | `login-error` | "Por favor, completa todos los campos.""Por favor, ingresa un correo electrónico válido." |
| Login server error | `login-error` | "Usuario no encontrado""Contraseña incorrecta""Rol no válido" |
| Registration validation | `register-error` | "Por favor, completa todos los campos.""Por favor, ingresa un correo electrónico válido.""La contraseña debe tener al menos 6 caracteres." |
| Registration server error | `register-error` | "Email o DNI ya registrado" |
| Network error | Either | "Error al iniciar sesión: [error]""Error al registrar: [error]" |

**Sources:** [assets/js/login.js L14-L113](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L14-L113)

## Server-Side Security Measures

### Request Processing Security

The AuthController implements several security practices:

```

```

**Sources:** [controllers/AuthController.php L7-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L7-L80)

### Parameter Validation

All POST parameters are checked for existence before use to prevent undefined variable warnings:

```

```

**Sources:** [controllers/AuthController.php L32-L39](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L32-L39)

### Error Logging Configuration

| Configuration | Value | Purpose |
| --- | --- | --- |
| `display_errors` | 0 | Hide errors from client |
| `display_startup_errors` | 0 | Hide startup errors from client |
| `log_errors` | 1 | Enable error logging |
| `error_log` | `__DIR__ . '/../../logs/error.log'` | Log file location |

**Sources:** [controllers/AuthController.php L11-L14](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L11-L14)

## Responsive Design

Both authentication pages implement mobile-responsive breakpoints:

### Breakpoint Strategy

```

```

### Login Page Responsive Adjustments

| Screen Size | Container Width | Logo Size | Font Adjustments |
| --- | --- | --- | --- |
| Desktop | 400px | 150px | Default (16px inputs, 24px titles) |
| Mobile (≤480px) | 90% | 120px | 14px inputs, 20px titles, 8px padding |

**Sources:** [assets/css/StyleLogin.css L174-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L174-L196)

### Index Page Responsive Adjustments

| Screen Size | Main Padding | Title Size | Button Padding |
| --- | --- | --- | --- |
| Desktop | 40px 20px | 28px | 12px 25px |
| Tablet (≤768px) | 20px 15px | 24px | 10px 20px |
| Mobile (≤480px) | 20px 15px | 20px | 8px 15px |

**Sources:** [assets/css/StyleIndex.css L121-L169](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css#L121-L169)

## Event Listener Registration

The authentication system uses DOM event listeners to handle user interactions:

```

```

**Sources:** [assets/js/login.js L1-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L1-L6)

## Summary

The Authentication System provides a secure, user-friendly entry point to the Hotel Benedetti platform. It combines client-side validation for immediate feedback, server-side security for data integrity, and role-based routing for access control. The system's dark-themed visual design with purple accents creates a cohesive brand experience, while responsive breakpoints ensure accessibility across devices. All communication occurs through AJAX requests returning JSON responses, eliminating full page reloads and providing a modern single-page application experience.