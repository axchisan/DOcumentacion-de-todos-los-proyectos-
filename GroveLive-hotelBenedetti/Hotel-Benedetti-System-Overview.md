# Hotel Benedetti System Overview

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)
> * [controllers/AuthController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php)

## Purpose and Scope

This document provides a high-level introduction to the Hotel Benedetti hotel management system, explaining its purpose, architecture, and key components. It serves as the entry point for understanding how the system operates and how its various subsystems interact.

For detailed information about specific subsystems, see:

* System Architecture details: [System Architecture](/GroveLive/hotelBenedetti/2-system-architecture)
* User role-specific interfaces: [User Roles and Access Control](/GroveLive/hotelBenedetti/3-user-roles-and-access-control)
* Business process workflows: [Core Business Processes](/GroveLive/hotelBenedetti/4-core-business-processes)
* Frontend implementation details: [Frontend Implementation](/GroveLive/hotelBenedetti/5-frontend-implementation)
* Backend API details: [Backend API Design](/GroveLive/hotelBenedetti/6-backend-api-design)

---

## System Purpose

Hotel Benedetti is a web-based hotel management system designed to streamline hotel operations through role-based interfaces. The system manages the complete guest lifecycle from room browsing and booking through check-in, occupancy, check-out, and room cleaning. It provides specialized interfaces for four distinct user roles: administrators who manage staff and rooms, receptionists who handle guest interactions, housekeeping staff (mucamas) who maintain room cleanliness, and clients who book and manage reservations.

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

---

## System Architecture Overview

The Hotel Benedetti system follows a three-tier Model-View-Controller (MVC) architecture with clear separation between presentation, application logic, and data persistence layers.

### Three-Tier Architecture Mapping

```

```

**Sources:** [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

---

## Core System Components

### Presentation Layer Components

| Interface | HTML View | CSS Stylesheet | JavaScript Module | Primary Purpose |
| --- | --- | --- | --- | --- |
| Administrator | `admin.html` | `StyleAdmin.css` | `admin.js` | Employee and room management, dashboard analytics |
| Client | `cliente.html` | `StyleCliente.css` | `cliente.js` | Room browsing, booking creation, notifications |
| Receptionist | `recepcionista.html` | `StyleRecepcionista.css` | `script_recepcionista.js` | Reservation confirmation, check-in/check-out |
| Maid | `mucama.html` | `StyleMucama.css` | `mucama.js` | Room cleaning notifications, status updates |

Each interface implements a consistent dark theme with purple accents (`#9b59b6`) defined in [assets/css/StyleAdmin.css L96-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L96-L97)

 and [assets/css/StyleCliente.css L73-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L73-L78)

 applied over a background image with overlay defined in [assets/css/StyleAdmin.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L31)

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

### Application Layer Components

The PHP controller layer handles HTTP requests via action-based routing. Each controller validates input, enforces business rules, interacts with model classes, and returns JSON responses.

```

```

The `AdminController.php` implements action routing at [controllers/AdminController.php L17-L18](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L18)

 for POST requests and [controllers/AdminController.php L256-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L256-L257)

 for GET requests, dispatching to specific handler methods based on the `action` parameter.

**Sources:** [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

### Data Layer Components

The data layer consists of PHP model classes that encapsulate database operations. Each model corresponds to a database table:

| Model Class | Database Table | Primary Responsibilities |
| --- | --- | --- |
| `Usuario2` | `usuarios` | User authentication, profile management |
| `Empleado` | `empleados` | Employee records, role assignment |
| `Habitacion2` | `habitaciones` | Room inventory, status tracking |
| `Reserva` | `reservas` | Booking records, date management |
| `Checkin` | `checkin` | Check-in timestamps, validation |
| `Checkout` | `checkout` | Check-out timestamps, payment tracking |
| `Notificacion` | `notificaciones` | Inter-role communication, alerts |

The `Database` class provides connection management via `getConnection()` as referenced in [controllers/AdminController.php L11-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L11-L12)

**Sources:** [controllers/AdminController.php L3-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L3-L6)

 [controllers/AuthController.php L3-L4](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L3-L4)

---

## Role-Based Access Control

The system implements role-based access control through the `rol` field in the `usuarios` table. After successful authentication via `AuthController.php`, users are routed to role-specific interfaces.

### Authentication and Role Routing Flow

```

```

The login action is handled in [controllers/AuthController.php L41-L42](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L41-L42)

 which delegates to `UsuarioController`. Role validation for employee creation is enforced at [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 and [controllers/AdminController.php L115-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L115-L121)

 restricting employee roles to `recepcionista` and `mucama`.

**Sources:** [controllers/AuthController.php L30-L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L30-L51)

 [controllers/AdminController.php L20-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L103)

### User Roles Summary

| Role | Primary Functions | Interface Features |
| --- | --- | --- |
| **Administrador** | Employee management, room management, analytics | Dashboard with Chart.js visualizations, CRUD operations for employees and rooms |
| **Recepcionista** | Reservation confirmation, check-in/check-out processing | Reservation list with confirm/cancel actions, check-in/out forms |
| **Mucama** | Room cleaning status updates | Cleaning notification list, mark-as-cleaned functionality |
| **Cliente** | Room browsing, booking creation | Room gallery, booking form, notification center |

For detailed documentation of each interface, see [User Roles and Access Control](/GroveLive/hotelBenedetti/3-user-roles-and-access-control) and its subsections.

**Sources:** [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)

 [assets/css/StyleAdmin.css L39-L112](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L39-L112)

---

## Request-Response Communication Pattern

All client-server communication uses asynchronous JavaScript `fetch()` calls with JSON payloads. The pattern is consistent across all interfaces:

```

```

The admin employee submission flow is implemented in [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266)

 which constructs a `URLSearchParams` object and sends it to `AdminController.php`. The controller processes the request at [controllers/AdminController.php L20-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L103)

 sanitizes inputs using `htmlspecialchars()` and `strip_tags()`, validates business rules, and returns JSON responses.

All controllers set the content type header to JSON at [controllers/AdminController.php L8](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L8-L8)

 and [controllers/AuthController.php L8](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L8-L8)

**Sources:** [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266)

 [controllers/AdminController.php L8-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L8-L103)

 [controllers/AuthController.php L7-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L7-L80)

---

## Dashboard Analytics System

The administrator interface includes a real-time dashboard that displays hotel statistics and visualizations using Chart.js library.

### Dashboard Data Flow

```

```

The dashboard statistics are loaded via [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

 which fetches data from the `getDashboardStats` action. The backend queries room counts by status at [controllers/AdminController.php L261-L279](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L261-L279)

 and employee counts by cargo at [controllers/AdminController.php L282-L290](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L282-L290)

 Chart instances are created using Chart.js at [assets/js/admin.js L64-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L64-L90)

 for the pie chart and [assets/js/admin.js L93-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L93-L124)

 for the bar chart.

**Sources:** [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

 [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

---

## Visual Design System

The system implements a cohesive dark theme across all interfaces with consistent color usage and component styling.

### Color Palette

| Color Code | Usage | CSS References |
| --- | --- | --- |
| `#9b59b6` | Primary accent (buttons, links, hover states) | [assets/css/StyleAdmin.css L96](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L96-L96) <br>  [assets/css/StyleCliente.css L73](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L73-L73) |
| `#8e44ad` | Darker purple (hover effects) | [assets/css/StyleAdmin.css L209](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L209-L209) <br>  [assets/css/StyleCliente.css L81](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L81-L81) |
| `#23272a` | Background overlay | [assets/css/StyleAdmin.css L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L29-L29) <br>  [assets/css/StyleCliente.css L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L29-L29) |
| `#2c2f33` | Header/footer background | [assets/css/StyleCliente.css L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L41-L41) |
| `#36393f` | Card backgrounds | [assets/css/StyleCliente.css L122](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L122-L122) |
| `#dcddde` | Primary text color | [assets/css/StyleCliente.css L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L16-L16) |
| `#36A2EB` | Edit buttons (blue) | [assets/css/StyleAdmin.css L253](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L253-L253) |
| `#FF6384` | Delete buttons (red) | [assets/css/StyleAdmin.css L259](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L259-L259) |

All interfaces use a fixed background image (`fondo2.jpg`) with a semi-transparent dark overlay defined in [assets/css/StyleAdmin.css L9-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L9-L31)

 and [assets/css/StyleCliente.css L9-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L9-L31)

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

---

## Technology Stack

| Layer | Technologies | Purpose |
| --- | --- | --- |
| **Frontend** | HTML5, CSS3, JavaScript (ES6+) | User interface rendering and interaction |
| **Styling** | Custom CSS, responsive design with media queries | Visual presentation and mobile adaptability |
| **Client Libraries** | SweetAlert2, Chart.js | User notifications and data visualization |
| **Backend** | PHP 7.4+ | Server-side application logic |
| **Database** | MySQL via PDO | Data persistence and retrieval |
| **Architecture Pattern** | MVC (Model-View-Controller) | Code organization and separation of concerns |
| **Communication** | AJAX with `fetch()` API, JSON | Asynchronous client-server data exchange |
| **Authentication** | Session-based with `password_hash()` / `password_verify()` | Secure user authentication |

Database connectivity is managed through the `Database` class using PDO, instantiated in controllers like [controllers/AdminController.php L11-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L11-L12)

 and [controllers/AuthController.php L23-L24](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L23-L24)

**Sources:** [controllers/AdminController.php L1-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L15)

 [controllers/AuthController.php L1-L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L29)

 [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

---

## Key Business Workflows

The system orchestrates several critical workflows that span multiple components:

### Workflow Overview

| Workflow | Entry Point | Key Controllers | Status Transitions |
| --- | --- | --- | --- |
| **Room Booking** | `cliente.js` → `ClienteController.php` | `ClienteController`, `ReservaController` | Available → Reserved (pending) |
| **Reservation Confirmation** | `script_recepcionista.js` → `ReservaController.php` | `ReservaController` | Pending → Confirmed |
| **Check-In** | `script_recepcionista.js` → `CheckinController.php` | `CheckinController` | Confirmed → Checked In (Room: Ocupada) |
| **Check-Out** | `script_recepcionista.js` → `CheckoutController.php` | `CheckoutController` | Checked In → Checked Out (Room: En Limpieza) |
| **Room Cleaning** | `mucama.js` → `MucamaController.php` | `MucamaController`, `AdminController` | En Limpieza → Disponible |

For detailed process documentation, see [Core Business Processes](/GroveLive/hotelBenedetti/4-core-business-processes) and its subsections.

**Sources:** [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

---

## System Entry Points

Users access the system through role-specific entry points after authentication:

| Role | Login Flow | Landing Page | Session Data |
| --- | --- | --- | --- |
| All roles | `index.html` or `login.html` → `AuthController.php` | Role-specific redirect | `$_SESSION['user_id']`, `$_SESSION['rol']` |
| `administrador` | After login | `admin.html` → Dashboard section | Full admin privileges |
| `recepcionista` | After login | `recepcionista.html` → Reservations list | Reception operations only |
| `mucama` | After login | `mucama.html` → Notifications list | Cleaning operations only |
| `cliente` | After login or registration | `cliente.html` → Welcome section | Booking and viewing only |

Session initialization occurs in [controllers/AuthController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L2-L2)

 via `session_start()`, with user data stored in session variables by `UsuarioController` after successful authentication.

**Sources:** [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

 [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)

---

## Data Validation and Security

The system implements multi-layer validation:

### Input Validation Layers

```

```

Email uniqueness is validated at [controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48)

 before employee creation and at [controllers/AdminController.php L124-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L124-L135)

 before updates. DNI uniqueness is checked at [controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61)

 and [controllers/AdminController.php L138-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L138-L149)

 Role validation restricts employee roles to `recepcionista` or `mucama` at [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 and [controllers/AdminController.php L115-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L115-L121)

Password hashing uses `PASSWORD_BCRYPT` at [controllers/AdminController.php L26](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L26-L26)

 for employee creation and [controllers/AdminController.php L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L111-L111)

 for updates.

**Sources:** [controllers/AdminController.php L20-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L149)

 [controllers/AuthController.php L30-L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L30-L51)

---

## Responsive Design Strategy

All interfaces implement mobile-first responsive design with breakpoints at 768px and 480px.

### Responsive Breakpoints

| Breakpoint | Target Devices | Key Adjustments |
| --- | --- | --- |
| Default (>768px) | Desktop | Full layout, multi-column grids, full navigation |
| `@media (max-width: 768px)` | Tablets | Single-column layouts, reduced font sizes, vertical navigation |
| `@media (max-width: 480px)` | Mobile phones | Further size reductions, optimized touch targets, minimal UI |

The admin interface implements responsive breakpoints at [assets/css/StyleAdmin.css L280-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L334)

 for tablets and [assets/css/StyleAdmin.css L336-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L336-L380)

 for mobile devices. The client interface has similar breakpoints at [assets/css/StyleCliente.css L399-L433](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L433)

 and [assets/css/StyleCliente.css L435-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L435-L486)

For detailed responsive design patterns, see [Responsive Design Implementation](/GroveLive/hotelBenedetti/5.3-responsive-design-implementation).

**Sources:** [assets/css/StyleAdmin.css L280-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L380)

 [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

---

## Development and Deployment Context

The system is structured with the following directory organization:

```python
hotelbenedetti/
├── assets/
│   ├── css/          # Stylesheet files (StyleAdmin.css, StyleCliente.css, etc.)
│   ├── js/           # JavaScript modules (admin.js, cliente.js, etc.)
│   └── img/          # Image assets (fondo2.jpg, logos, room images)
├── controllers/      # PHP controller layer (AdminController.php, AuthController.php, etc.)
├── models/           # PHP model classes (Usuario2.php, Habitacion2.php, etc.)
├── views/            # HTML interface files (admin.html, cliente.html, etc.)
└── ddbb/             # Database connection class (database.php)
```

The repository is hosted at `https://github.com/GroveLive/hotelBenedetti`.

**Sources:** [controllers/AdminController.php L3-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L3-L6)

 [controllers/AuthController.php L3-L4](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L3-L4)

 [assets/css/StyleAdmin.css L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L11-L11)