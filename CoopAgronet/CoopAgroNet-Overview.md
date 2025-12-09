# CoopAgroNet Overview

> **Relevant source files**
> * [backennd/db_interaction/connection.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php)
> * [front end/index.html](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html)
> * [front end/style.css](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css)

## Purpose and Scope

This document provides a high-level introduction to the CoopAgroNet agricultural cooperative platform. It explains the system's purpose, describes its main functional areas, and maps those areas to the underlying code structure. This overview is intended to orient developers to the codebase architecture and help them understand how the different parts of the system work together.

For detailed information about specific subsystems:

* System architecture patterns and three-tier design: see [System Architecture](/axchisan/CoopAgronet/1.1-system-architecture)
* Programming languages, frameworks, and libraries: see [Technology Stack](/axchisan/CoopAgronet/1.2-technology-stack)
* Database table structures and relationships: see [Database Schema](/axchisan/CoopAgronet/1.3-database-schema)
* Backend API implementation: see [Backend System](/axchisan/CoopAgronet/2-backend-system)
* Frontend implementation: see [Frontend System](/axchisan/CoopAgronet/3-frontend-system)

**Sources:** [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)

 [backennd/db_interaction/connection.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L1-L15)

---

## What is CoopAgroNet

CoopAgroNet is a web-based management platform designed for agricultural cooperatives and associations. The system enables farmers to collaboratively manage crop data, share knowledge within a community, and access tiered subscription services. The platform is implemented as a single-page application (SPA) with a PHP backend and MySQL database.

The system name "CoopAgroNet" appears in the main page title [front L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L6-L6)

 and the database name is `CoopAgroNet` [backennd/db_interaction/connection.php L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L8-L8)

**Key Capabilities:**

* **User Authentication**: Registration and login with password hashing
* **Crop Management**: Full CRUD operations for agricultural crop records
* **Community Features**: Sharing posts and knowledge between producers
* **Support System**: Question submission linked to user accounts
* **Subscription Tiers**: Three service levels (Básico, Premium, Empresarial)

**Sources:** [front L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L6-L6)

 [front L38-L50](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L38-L50)

 [backennd/db_interaction/connection.php L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L8-L8)

---

## Core Functional Areas

The platform is organized into seven main functional sections, each implemented as a distinct area within the single HTML document and supported by specialized backend endpoints.

### Functional Area to Code Mapping

```

```

**Section Descriptions:**

| Section ID | Lines | Purpose | Backend Support |
| --- | --- | --- | --- |
| `#inicio` | [front L46-L69](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L46-L69) | Landing page with feature overview | None (static) |
| `#gestion-cultivos` | [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107) | Crop management interface with form and table | 5 PHP endpoints |
| `#comunidad` | [front L109-L132](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L109-L132) | Community posts feed | None (static) |
| `#suscripciones` | [front L134-L175](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L134-L175) | Three-tier subscription plans | None (static) |
| `#login` | [front L177-L246](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L177-L246) | Login and registration forms | 2 PHP endpoints |
| `#admin` | [front L250-L256](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L250-L256) | Admin panel placeholder | None (mock) |
| `#soporte` | [front L270-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L270-L285) | Support question form | 1 PHP endpoint |

**Sources:** [front L46-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L46-L285)

 [front L292-L296](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L296)

---

## System File Organization

The codebase is organized into frontend and backend directories with a clear separation of concerns.

### Directory Structure and Responsibilities

```

```

**Key Organizational Patterns:**

1. **Single HTML Shell**: All UI sections exist in [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)  with hash-based navigation
2. **Modular JavaScript**: Each major function has a dedicated JS file loaded at [front L292-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L300)
3. **Endpoint-per-Operation**: Each CRUD operation has its own PHP file in the backend directory
4. **Shared Connection**: All PHP endpoints include [backennd/db_interaction/connection.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L1-L15)  for database access
5. **Static Assets**: Images referenced from `graphic_resources/` directory in community posts

**Sources:** [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)

 [backennd/db_interaction/connection.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L1-L15)

---

## Application Navigation Structure

The application uses hash-based navigation where each section is identified by an anchor in the URL. Navigation is implemented through the header menu at [front L22-L31](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L22-L31)

### Navigation Menu Mapping

| Menu Item | Hash | Section ID | Icon Class |
| --- | --- | --- | --- |
| Inicio | `#inicio` | `inicio` | `fa-home` |
| Gestión de Cultivos | `#gestion-cultivos` | `gestion-cultivos` | `fa-seedling` |
| Comunidad | `#comunidad` | `comunidad` | `fa-users` |
| Suscripciones | `#suscripciones` | `suscripciones` | `fa-money-bill-wave` |
| Login/Registro | `#login` | `login` | `fa-sign-in-alt` |
| Admin | `#admin` | `admin` | `fa-user-shield` |
| Redes Sociales | `#social` | `social` | `fa-share-alt` |
| Soporte | `#soporte` | `soporte` | `fa-question-circle` |

The header navigation is defined at [front L15-L43](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L15-L43)

 and includes the logo (`CoopAgroNetcd` with tractor icon) and banner with call-to-action.

**Sources:** [front L15-L43](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L15-L43)

 [front L22-L31](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L22-L31)

---

## Data Flow Architecture

The system follows a consistent pattern for data operations across all features. User interactions in the HTML trigger JavaScript handlers that communicate with PHP endpoints, which in turn interact with the MySQL database.

### Request-Response Flow Pattern

```

```

**Common Elements:**

* **Connection Initialization**: Every PHP endpoint includes [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)  to get `$connection` object
* **Database Configuration**: Connects to localhost:3306, root user, database `CoopAgroNet` [backennd/db_interaction/connection.php L5-L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L8)
* **Response Format**: Most endpoints return JSON with `success` boolean and additional fields
* **UI Feedback**: Message elements like `#message`, `#message-box`, `#mensaje-form` display operation results

**Sources:** [backennd/db_interaction/connection.php L5-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L14)

 [front L84](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L84-L84)

 [front L217](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L217-L217)

 [front L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L281-L281)

---

## Key User Workflows

### 1. Authentication Workflow

Users register via `#register-form` [front L213-L244](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L213-L244)

 which posts to `create_acount.php`. After successful registration, they login via `#login-form` [front L182-L205](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L182-L205)

 which posts to `login.php`. The login endpoint sets `$_SESSION["user"]` and returns user data, which `login.js` uses to update the UI.

**Primary Components:**

* Forms: `#login-form`, `#register-form`
* Script: `login.js` [front L292](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L292)
* Endpoints: `login.php`, `create_acount.php`
* Table: `usuarios`

### 2. Crop Management Workflow

The crop management interface at `#gestion-cultivos` [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107)

 contains:

* **Form**: `#cropForm` with 7 input fields [front L74-L86](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L74-L86)
* **Table**: `#cropTableBody` for displaying crops [front L102](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L102-L102)
* **Scripts**: * `agregar_cultivo.js` handles form submission and populates table [front L294](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L294-L294) * `acciones_cultivos.js` handles edit/delete buttons [front L295](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L295-L295) * `crop-management.js` provides React alternative [front L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L300-L300)

**CRUD Operations:**

* Create: Form submits to `agregar_cultivo.php`
* Read: `fetchCultivos()` calls `obtener_cultivos.php`
* Update: Edit button loads data from `obtener_cultivo.php`, saves to `editar_cultivo.php`
* Delete: Delete button sends request to `eliminar_cultivo.php`

### 3. Support Question Workflow

Users submit questions via `#support-form` [front L275-L280](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L275-L280)

 which includes name, email, and question text. The `enviar_pregunta.js` script [front L293](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L293-L293)

 posts to `enviar_pregunta.php`, which verifies the email exists in `usuarios` table before inserting into `preguntas` table with `usuario_id` foreign key. Feedback displays in `#mensaje-form` element [front L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L281-L281)

**Sources:** [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107)

 [front L177-L246](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L177-L246)

 [front L270-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L270-L285)

 [front L292-L296](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L296)

---

## Technology Integration Points

The application integrates multiple external libraries loaded via CDN at the end of the HTML document:

| Technology | Version | Purpose | Load Location |
| --- | --- | --- | --- |
| Font Awesome | 6.0.0 | Icons throughout UI | [front L8-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L8-L10) |
| Boxicons | 2.1.4 | Additional icon set | [front L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L11-L11) |
| React | 18 | Alternative crop UI | [front L297](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L297-L297) |
| React DOM | 18 | React rendering | [front L298](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L298-L298) |
| Babel Standalone | Latest | JSX transpilation | [front L299](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L299-L299) |

The React component `crop-management.js` is loaded with `type="text/babel"` [front L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L300-L300)

 for runtime JSX transformation.

**Styling System:**

* Global styles: [front L1-L678](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L1-L678)
* Responsive breakpoint: 768px [front L481](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L481-L481)
* Color scheme: Green primary (`#4CAF50`, `#5cb85c`) with gradients [front L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L16-L16)  [front L129](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L129-L129)

**Sources:** [front L8-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L8-L11)

 [front L297-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L297-L300)

 [front L1-L678](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L1-L678)

---

## Development Context

CoopAgroNet represents an MVP (Minimum Viable Product) agricultural platform with several architectural characteristics that inform development decisions:

**Current State:**

* **Architecture Style**: Procedural PHP backend with no framework
* **Frontend Pattern**: Mix of vanilla JavaScript and React (suggesting incremental modernization)
* **Security Posture**: Basic authentication implemented, but SQL injection vulnerabilities exist in some endpoints (see [Security Considerations](/axchisan/CoopAgronet/4-security-considerations))
* **Scalability**: Single-server design with no pagination or caching
* **Database**: Direct mysqli queries, no ORM abstraction

**Deployment Requirements:**

* PHP with mysqli extension
* MySQL server with `CoopAgroNet` database
* Web server (Apache/nginx) serving from `front end/` directory
* Root database access (current configuration uses [backennd/db_interaction/connection.php L6-L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L6-L7) )

For setup and deployment details, see [Development and Deployment Guide](/axchisan/CoopAgronet/7-development-and-deployment-guide).

**Sources:** [backennd/db_interaction/connection.php L5-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L14)

 [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)