# System Architecture

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [config/configuracion.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php)
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the overall system architecture of the American Food restaurant management system, focusing on the three-tier design pattern, component organization, and data flow between layers. It provides a high-level view of how the presentation, application, and data layers interact to support restaurant operations.

For specific details about database schema and table relationships, see [Schema Overview](/axchisan/AmericanFood/2.1-schema-overview). For role-based access control implementation, see [User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control). For deployment configuration, see [Deployment and Configuration](/axchisan/AmericanFood/9-deployment-and-configuration).

---

## Architectural Overview

The American Food system implements a classic **three-tier architecture** consisting of:

1. **Presentation Layer**: CSS-based user interfaces with role-specific styling
2. **Application Layer**: PHP backend services handling business logic and data validation
3. **Data Layer**: MySQL database with normalized relational schema

The architecture follows a **server-side rendering** model where PHP generates HTML dynamically, with CSS providing styling and client-side JavaScript handling interactivity. Database access is centralized through a connection manager that enforces UTF-8 encoding and implements fail-fast error handling.

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

---

## System Layers Diagram

```

```

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 [database/american_food L1-L488](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L488)

---

## Layer 1: Presentation Layer

The presentation layer consists of six CSS files that define role-specific user interfaces. Each file is self-contained and provides complete styling for its designated user role.

### Global Styles (style.css)

The [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 file establishes the design system foundation:

| Component | Purpose | Key Features |
| --- | --- | --- |
| **CSS Custom Properties** | Color palette | 18 color variables defined in `:root` selector [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18) |
| **Typography** | Font system | Poppins font family, standardized heading weights [css/style.css L21-L31](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L21-L31) |
| **Navigation** | Global navbar | `.navbar`, `.navbar-dark`, `.brand-text` classes [css/style.css L44-L82](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L44-L82) |
| **Buttons** | Action elements | `.btn-primary`, `.btn-outline-primary` [css/style.css L85-L103](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L85-L103) |
| **Cart System** | Shopping cart | `.cart-sidebar`, `.cart-overlay`, `.cart-items` [css/style.css L576-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L693) |

### Role-Specific Interfaces

Each role receives a dedicated CSS file optimized for their workflow:

| Role | CSS File | Key Components | Purpose |
| --- | --- | --- | --- |
| **Authentication** | `login.css` | `.login-card`, `.login-form`, `.role-options` | User login and registration |
| **Customer** | `cliente.css` | `.welcome-banner`, `.menu-item-card`, `.cart-sidebar`, `.reservation-list` | Online ordering and reservations |
| **Cashier** | `cajera.css` | `.sales-summary`, `.order-card`, `.payment-modal`, `.invoice-section` | Payment processing and invoicing |
| **Cook** | `cocina.css` | `.order-card`, `.new`, `.preparing`, `.ready` | Order preparation tracking |
| **Server** | `mesera.css` | `.tables-grid`, `.table-item`, `.orders-list`, `.menu-section` | Table and order management |

**Sources**: [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css)

 [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)

 [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)

 [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)

 [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)

---

## Layer 2: Application Layer

The application layer contains PHP scripts that process user requests, validate inputs, and execute database operations. The layer implements a **procedural programming paradigm** with functional decomposition.

### Core Application Files

```

```

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

### User Registration Service

The [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 file demonstrates the application layer's structure:

**Request Handling**:

* Validates `$_GET['opciones']` parameter to determine operation [crud/registro_INSERT.php L5-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L7)
* Uses `switch` statement to route to appropriate handler [crud/registro_INSERT.php L30-L61](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L61)

**Input Sanitization Pipeline**:

```sql
POST data → FILTER_SANITIZE_SPECIAL_CHARS → FILTER_VALIDATE_EMAIL → Role Validation → Password Hashing → Database Insert
```

**Security Layers** (lines 33-56):

| Layer | Function | Implementation |
| --- | --- | --- |
| **Input Sanitization** | Remove harmful characters | `filter_var()` with `FILTER_SANITIZE_SPECIAL_CHARS` [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38) |
| **Email Validation** | Verify email format | `filter_var()` with `FILTER_VALIDATE_EMAIL` [crud/registro_INSERT.php L43-L45](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L45) |
| **Role Validation** | Whitelist allowed roles | Array check against `['admin', 'cajera', 'cocinero', 'cliente', 'mesera']` [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54) |
| **Password Hashing** | Secure password storage | `password_hash()` with `PASSWORD_DEFAULT` [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56) |
| **SQL Injection Prevention** | Parameterized queries | `mysqli_prepare()` with `mysqli_stmt_bind_param()` [crud/registro_INSERT.php L11-L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L16) |

**Function: `insertarUsuario()`**

The function encapsulates database insertion logic [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

:

1. Prepares SQL statement using `mysqli_prepare()`
2. Binds parameters with `mysqli_stmt_bind_param()`
3. Executes statement with `mysqli_stmt_execute()`
4. Redirects to success page or dies with error message
5. Always closes statement and connection

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

---

## Layer 3: Configuration Layer

The configuration layer provides database credentials and connection management through two specialized files.

### Configuration Constants (configuracion.php)

The [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 file defines five constants:

| Constant | Value | Purpose |
| --- | --- | --- |
| `host` | `'127.0.01'` | Database server address (localhost) |
| `user` | `'root'` | Database username |
| `password` | `''` | Database password (empty - development configuration) |
| `database` | `'american_food'` | Database name |
| `llave` | `''` | Application key (empty - not configured) |

**Security Note**: The empty password and root user indicate a development environment configuration. Production deployment requires hardening (see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations)).

**Sources**: [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

### Connection Manager (conexionDB.php)

The [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 file orchestrates database connectivity:

**Bootstrap Sequence Diagram**:

```

```

**Key Operations**:

1. **Include Configuration** [config/conexionDB.php L2](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L2) : Loads constants from `configuracion.php`
2. **Session Management** [config/conexionDB.php L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L4-L6) : Starts session only if not already active
3. **Database Connection** [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8) : Creates `mysqli` object using configuration constants
4. **Error Handling** [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14) : Implements fail-fast pattern - terminates execution on connection failure
5. **Character Encoding** [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17) : Sets UTF-8 encoding for international character support

**Global Variable**: The `$link` mysqli object becomes globally available to all scripts that include this file [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8)

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

---

## Layer 4: Data Layer

The data layer implements a relational database schema with nine core tables and a central `pedidos` table that acts as a hub.

### Database Schema: american_food

The [database/american_food L1-L488](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L488)

 file defines the complete schema:

**Core Tables**:

| Table | Primary Purpose | Key Fields | Foreign Keys |
| --- | --- | --- | --- |
| `usuarios` | Authentication and RBAC | `id`, `username`, `rol`, `contraseña` | None |
| `pedidos` | Central order tracking | `id`, `mesa_id`, `cliente_id`, `tipo_pedido`, `estado`, `total` | `mesa_id` → `mesas`, `cliente_id` → `usuarios` |
| `menu` | Product catalog | `id`, `nombre`, `categoria`, `precio`, `imagen`, `disponible` | None |
| `detalles_pedidos` | Order line items | `id`, `pedido_id`, `plato_id`, `cantidad`, `precio_unitario` | `pedido_id` → `pedidos`, `plato_id` → `menu` |
| `mesas` | Table management | `id`, `numero`, `capacidad`, `ubicacion`, `estado` | None |
| `pagos` | Payment records | `id`, `pedido_id`, `metodo`, `monto_recibido`, `cambio` | `pedido_id` → `pedidos` |
| `facturas` | Invoice generation | `id`, `pedido_id`, `nombre`, `rfc`, `total`, `estado` | `pedido_id` → `pedidos` |
| `inventario` | Stock management | `id`, `nombre`, `categoria`, `cantidad`, `unidad`, `cantidad_minima` | None |
| `informacion_entrega` | Delivery details | `id`, `pedido_id`, `nombre`, `direccion`, `telefono` | `pedido_id` → `pedidos` |

**Entity Relationship Structure**:

```

```

**Referential Integrity**:

The schema enforces data integrity through foreign key constraints [database/american_food L453-L483](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L453-L483)

:

* `detalles_pedidos` → `pedidos`: `ON DELETE CASCADE` (delete order details when order is deleted)
* `detalles_pedidos` → `menu`: No cascade (preserve menu items even if used in orders)
* `pedidos` → `mesas`: `ON DELETE SET NULL` (preserve order if table is deleted)
* `pedidos` → `usuarios`: `ON DELETE SET NULL` (preserve order if user is deleted)

**Indexes for Performance** [database/american_food L351-L383](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L351-L383)

:

* `idx_menu_categoria` on `menu.categoria`: Fast filtering by menu category
* `idx_pedidos_estado` on `pedidos.estado`: Quick lookup of orders by status
* `idx_usuarios_rol` on `usuarios.rol`: Efficient role-based queries

**Sources**: [database/american_food L1-L488](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L488)

---

## Component Organization

The system follows a modular directory structure that maps to architectural layers:

```markdown
american_food/
│
├── config/                    # Configuration Layer
│   ├── configuracion.php      # Database credentials
│   └── conexionDB.php         # Connection manager
│
├── crud/                      # Application Layer
│   └── registro_INSERT.php    # User registration service
│
├── css/                       # Presentation Layer
│   ├── style.css             # Global styles and components
│   ├── login.css             # Authentication interface
│   ├── cliente.css           # Customer interface
│   ├── cajera.css            # Cashier interface
│   ├── cocina.css            # Kitchen interface
│   └── mesera.css            # Server interface
│
├── database/                  # Data Layer Definitions
│   └── american_food (6).sql # Schema and sample data
│
└── img/                       # Static Assets
    └── *.jpg                  # Menu item images
```

**Design Patterns**:

| Pattern | Implementation | Location |
| --- | --- | --- |
| **Singleton Connection** | Single `$link` mysqli object | [config/conexionDB.php L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L8) |
| **Configuration Abstraction** | Separate credentials from logic | [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7) |
| **Fail-Fast Error Handling** | Terminate on connection failure | [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14) |
| **Role-Based UI Segregation** | Separate CSS per user role | `css/*.css` |
| **Input Validation Pipeline** | Multi-layer sanitization | [crud/registro_INSERT.php L33-L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L56) |

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

---

## Request Processing Flow

### User Registration Request Flow

The following diagram illustrates how a user registration request flows through all architectural layers:

```

```

**Key Processing Steps**:

1. **Request Validation** [crud/registro_INSERT.php L5-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L5-L7) : Check `$_GET['opciones']` parameter
2. **Input Sanitization** [crud/registro_INSERT.php L33-L38](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L38) : Apply multiple `filter_var()` filters
3. **Business Logic Validation** [crud/registro_INSERT.php L43-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L43-L54) : Verify email format, password match, role validity
4. **Password Security** [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56) : Hash password with bcrypt
5. **Database Connection** [config/conexionDB.php L2-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L17) : Establish mysqli connection with UTF-8 encoding
6. **Prepared Statement** [crud/registro_INSERT.php L11-L16](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L16) : Bind parameters to prevent SQL injection
7. **Database Insert** [crud/registro_INSERT.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L17-L17) : Execute statement
8. **Resource Cleanup** [crud/registro_INSERT.php L18-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L18-L19) : Close statement and connection
9. **User Redirect** [crud/registro_INSERT.php L23](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L23-L23) : Redirect to success page

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

---

## Architectural Patterns and Principles

### Separation of Concerns

The architecture enforces strict layer separation:

| Layer | Responsibility | Cannot Access |
| --- | --- | --- |
| **Presentation (CSS)** | Visual styling and layout | Database, Business logic |
| **Application (PHP)** | Business logic and validation | Direct HTML generation (implied by CSS-first design) |
| **Configuration** | Credentials and settings | Business logic |
| **Data (MySQL)** | Data persistence and integrity | Application logic |

### Fail-Fast Error Handling

The [config/conexionDB.php L10-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L10-L14)

 implements fail-fast pattern:

```

```

**Benefits**:

* Prevents execution with broken database connection
* Provides user-friendly error message in Spanish
* Stores message in session for display on next page load
* Uses Bootstrap CSS classes for consistent styling (`bg-warning text-dark`)

### Defense-in-Depth Security

The application implements multiple security layers [crud/registro_INSERT.php L33-L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L56)

:

```
Layer 1: Input Sanitization (FILTER_SANITIZE_SPECIAL_CHARS)
    ↓
Layer 2: Format Validation (FILTER_VALIDATE_EMAIL)
    ↓
Layer 3: Business Logic Validation (Role whitelist, password matching)
    ↓
Layer 4: Cryptographic Protection (password_hash)
    ↓
Layer 5: SQL Injection Prevention (Prepared statements)
```

### UTF-8 Character Encoding

The [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

 explicitly sets UTF-8 encoding:

```

```

This ensures proper handling of Spanish-language content (mesera, cajera, cocinero) and special characters in menu items, user names, and addresses.

**Sources**: [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

---

## Technology Stack Summary

| Layer | Technology | Version/Standard | Purpose |
| --- | --- | --- | --- |
| **Web Server** | Apache/Nginx | - | HTTP request handling |
| **Application Runtime** | PHP | 8.2.4 | Server-side scripting |
| **Database Server** | MySQL/MariaDB | 10.4.28-MariaDB | Data persistence |
| **Database API** | mysqli | PHP extension | Database connectivity |
| **Styling** | CSS3 | - | User interface design |
| **Session Management** | PHP Sessions | Native | State persistence |
| **Password Hashing** | bcrypt | PASSWORD_DEFAULT | Credential security |
| **Character Encoding** | UTF-8 | UTF8MB4 | International character support |

**Sources**: [database/american_food L7-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L7-L8)

 [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

 [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

---

## Design Constraints and Limitations

### Current Implementation Constraints

1. **Procedural Programming**: No object-oriented design, functions are not encapsulated in classes
2. **Global State**: `$link` mysqli object is global rather than dependency-injected
3. **No MVC Framework**: Direct PHP scripts rather than framework-based routing
4. **Manual Session Management**: No abstraction layer for session operations
5. **Hard-Coded Error Messages**: Spanish error messages embedded in code [config/conexionDB.php L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L11-L11)
6. **Single CRUD File**: Only registration implemented, other CRUD operations pending

### Development Environment Configuration

The [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 reveals development-stage settings:

* Empty database password
* Root user with full privileges
* Localhost-only database access (127.0.0.1)
* Empty `llave` security constant

These configurations require hardening before production deployment (see [Security Considerations](/axchisan/AmericanFood/9.1-security-considerations)).

**Sources**: [config/configuracion.php L1-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L1-L7)

 [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

---

## Future Architectural Considerations

Based on the current structure and the single implemented CRUD operation [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 the following architectural expansions are anticipated:

1. **CRUD Operation Completion**: Additional files in `crud/` directory for update, delete, and select operations
2. **View Layer**: HTML templates referenced by CSS files but not yet visible in codebase
3. **API Endpoints**: RESTful endpoints for AJAX operations (implied by cart system in [css/style.css L576-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L693) )
4. **Authentication System**: Login validation logic (implied by [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css) )
5. **Order Processing**: Kitchen and cashier backend logic (implied by [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)  and [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css) )

**Sources**: [crud/registro_INSERT.php L1-L73](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L73)

 [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)