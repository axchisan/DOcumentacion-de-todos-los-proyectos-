# User Roles and Access Control

> **Relevant source files**
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the role-based access control (RBAC) system implemented in the American Food restaurant management application. It covers the five distinct user roles, their permissions, authentication flow, and how role-specific interfaces are served to different users. For information about the authentication interface components, see page 5.2. For details on specific business workflows each role performs, see page 8.

---

## Role Definitions

The system implements five user roles, each with specific responsibilities and access levels:

| Role | Spanish Name | Primary Responsibilities | Interface File | Database Value |
| --- | --- | --- | --- | --- |
| Administrator | `admin` | Full system access, manage all entities | `style.css` (shared) | `admin` |
| Cashier | `cajera` | Process payments, manage invoices, view sales | `cajera.css` | `cajera` |
| Cook | `cocinero` | View orders, update preparation status | `cocina.css` | `cocinero` |
| Server/Waitress | `mesera` | Manage tables, take orders, coordinate service | `mesera.css` | `mesera` |
| Customer | `cliente` | Browse menu, place online orders, make reservations | `cliente.css` | `cliente` |

**Sources:** [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

 [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 [css/cajera.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L10)

 [css/cliente.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L10)

 [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

 [css/mesera.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L10)

---

## Database Schema for User Roles

### usuarios Table Structure

The `usuarios` table stores all user accounts with their assigned roles. The role is enforced at the database level using an ENUM constraint.

```

```

**Key Fields:**

* `rol`: ENUM field restricting values to five valid roles
* `contraseña`: Stores bcrypt password hash (via `password_hash()`)
* `estado`: Controls whether account is active or disabled
* `correo`: Unique constraint ensures one account per email

**Sample Data:**

| id | nombre | username | rol | estado |
| --- | --- | --- | --- | --- |
| 1 | cocina | cocina | cocinero | activo |
| 2 | Mesera | mesera | mesera | activo |
| 3 | cajera | cajera | cajera | activo |
| 4 | usuario | usuario | cliente | activo |

**Sources:** [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

 [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

---

## Role Validation During Registration

### Allowed Roles Whitelist

The registration system validates user roles against a hardcoded whitelist to prevent privilege escalation or invalid role assignment.

```

```

### Code Implementation

The role validation occurs in the user registration handler:

**Sanitization and Validation:**

* Lines 33-38: All POST inputs sanitized using `FILTER_SANITIZE_SPECIAL_CHARS` and `FILTER_SANITIZE_EMAIL`
* Line 43-45: Email validation using `FILTER_VALIDATE_EMAIL`
* Line 47-49: Password confirmation check

**Role Validation:**

* Line 51: Defines `$allowed_roles` array with five permitted values
* Line 52-54: Uses `in_array()` to verify submitted role is in whitelist
* Terminates execution with error message if role is invalid

**Password Security:**

* Line 56: Passwords hashed using `password_hash($password, PASSWORD_DEFAULT)` (bcrypt algorithm)

**Database Insertion:**

* Line 57-59: SQL INSERT prepared statement includes all seven user fields
* Line 16: Uses `str_repeat("s", count($params))` for dynamic parameter binding
* Line 17: Executes with all parameters bound to prevent SQL injection

**Sources:** [crud/registro_INSERT.php L33-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L59)

 [crud/registro_INSERT.php L10-L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L10-L28)

---

## Authentication and Session Management

```

```

### Session Initialization

The connection manager initializes PHP sessions if not already active:

* Line 2-5: Checks `session_status()` to determine if session exists
* Line 3-4: Calls `session_start()` if no active session found
* This ensures `$_SESSION` array is available for storing user role and authentication state

**Sources:** [config/conexionDB.php L2-L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L5)

 [crud/registro_INSERT.php L23-L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L23-L24)

---

## Role-Specific Interfaces

Each role receives a tailored CSS-based interface optimized for their job function. The system uses separate CSS files to completely change the UI layout and available features.

### Interface Mapping

```

```

### Cashier Interface (cajera.css)

**Purpose:** Financial operations and payment processing

**Key Components:**

* `.cajera-header`: Header with cashier identification (lines 39-69)
* `.sales-summary`: Dashboard cards showing sales metrics (lines 72-96)
* `.orders-section`: Order cards for payment processing (lines 99-190)
* `.payment-details-section`: Payment modal with change calculation (lines 222-228)
* `.invoices-section`: Invoice management interface (lines 214-219)
* `.notifications-sidebar`: Alert sidebar for new orders (lines 264-359)

**Color Scheme:**

* Primary color: `#1a3c34` (dark green)
* Accent color: `#ffd700` (gold)

**Sources:** [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)

### Kitchen Interface (cocina.css)

**Purpose:** Streamlined order status visualization and updates

**Key Components:**

* `.order-card`: Compact order cards with state indicators (lines 1-6)
* State classes: `.new`, `.preparing`, `.ready` with colored left borders (lines 8-10)
* `.order-header`: Order number and elapsed time (lines 12-19)
* `.order-body`: Item list with quantities (lines 22-26)
* `.cooking-progress`: Progress bar for preparation tracking (line 28)

**Design Philosophy:** Minimalist interface focused on quick status updates with clear visual state differentiation through border colors.

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

### Server Interface (mesera.css)

**Purpose:** Table management and order coordination

**Key Components:**

* `.mesera-header`: Server identification banner (lines 18-52)
* `.tables-grid`: Grid layout for table status visualization (lines 156-202)
* `.table-item`: Interactive table cards with status indicators (lines 163-261) * `.available`: Green left border (`--success-color`) * `.occupied`: Red left border (`--danger-color`) * `.reserved`: Yellow left border (`--warning-color`)
* `.orders-list`: Active orders for assigned tables (lines 264-356)
* `.menu-items-grid`: Menu browsing for order taking (lines 359-400)
* Modal components: Table details modal and new order modal (lines 402-469)

**Table Status Legend:**

* `.status-dot.available`: Green indicator (line 143-145)
* `.status-dot.occupied`: Red indicator (line 147-149)
* `.status-dot.reserved`: Yellow indicator (line 151-153)

**Sources:** [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

### Customer Interface (cliente.css)

**Purpose:** Self-service ordering and account management

**Key Components:**

* `.welcome-banner`: Personalized greeting header (lines 4-18)
* `.menu-categories`: Category filter navigation (lines 93-107)
* `.menu-item-card`: Product cards with images and pricing (lines 109-158)
* `.cart-sidebar`: Sticky shopping cart panel (lines 161-237) * `.cart-items`: Scrollable item list (lines 166-169) * `.cart-item`: Individual cart entry with quantity controls (lines 179-224) * `.cart-summary`: Total calculation and checkout button (lines 226-237)
* `.reservation-list`: Reservation management (lines 55-90)
* `.order-history`: Past order tracking (lines 240-306)

**Responsive Behavior:**

* Offcanvas mobile cart (lines 309-322)
* Breakpoints at 992px, 768px, 576px (lines 325-371)

**Sources:** [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

---

## Access Control Patterns by Role

### Data Access Matrix

| Role | pedidos | pagos | facturas | mesas | menu | usuarios | inventario |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **admin** | Full | Full | Full | Full | Full | Full | Full |
| **cajera** | Read/Update (payment status) | Create/Read | Create/Read | Read | Read | None | None |
| **cocinero** | Read/Update (preparation status) | None | None | None | Read | None | None |
| **mesera** | Create/Read/Update | None | None | Read/Update | Read | None | None |
| **cliente** | Create (online orders) | None | None | None | Read | Read (own) | None |

### Permission Enforcement Mechanisms

```

```

### Role-Specific Data Operations

**Cashier (cajera) Operations:**

* **Payment Processing:** Create records in `pagos` table with `metodo` (efectivo/tarjeta/online), `monto_recibido`, and `cambio` calculation
* **Invoice Generation:** Create records in `facturas` table linking to `pedido_id`
* **Order Status:** Update `pedidos.estado` from `pendiente_pago` to `pagado` after payment
* **View Access:** Read `pedidos`, `pagos`, `facturas` for sales reporting

**Cook (cocinero) Operations:**

* **Order Status Updates:** Modify `pedidos.estado` through state transitions: * `nuevo` → `en_preparacion` (when starting) * `en_preparacion` → `listo` (when complete)
* **Read Access:** View `pedidos` and associated `detalles_pedidos` for preparation instructions
* **Menu Reference:** Read-only access to `menu` table for item details

**Server (mesera) Operations:**

* **Order Creation:** Insert into `pedidos` with `tipo_pedido='presencial'` and assigned `mesa_id`
* **Table Management:** Update `mesas.estado` (activa/inactiva) based on occupancy
* **Order Details:** Insert into `detalles_pedidos` linking `pedido_id`, `plato_id`, and quantities
* **Coordination:** Read `pedidos` to track order status and coordinate with kitchen

**Customer (cliente) Operations:**

* **Online Orders:** Create `pedidos` with `tipo_pedido='online'`, `cliente_id` set, and `mesa_id` NULL
* **Delivery Info:** Create corresponding `informacion_entrega` record with address and contact details
* **Order Tracking:** Read own `pedidos` records filtered by `cliente_id`
* **Menu Browsing:** Read-only access to `menu` table for product selection

**Sources:** [database/american_food L232-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L232-L238)

 (pagos table), [database/american_food L89-L100](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L89-L100)

 (facturas table), [database/american_food L257-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L257-L266)

 (pedidos table), [database/american_food L193-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L193-L199)

 (mesas table)

---

## Role Assignment and Management

### New User Registration

The registration flow differs by intended role:

**Customer Self-Registration:**

* Customers can register themselves through the public registration form
* Role defaults to `cliente` or is explicitly selected during signup
* Immediate account activation with `estado='activo'`
* Access granted to customer interface immediately after email verification

**Staff Account Creation:**

* Staff roles (`admin`, `cajera`, `cocinero`, `mesera`) are pre-created by administrators
* Not exposed through public registration interface
* Credentials distributed manually to staff members
* Implies existence of admin interface for user management (not visible in provided files)

**Sources:** [crud/registro_INSERT.php L32-L60](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L32-L60)

 [database/american_food L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L296-L296)

### Account States

Users can have one of two states in the `usuarios.estado` field:

| Estado | Meaning | Effect |
| --- | --- | --- |
| `activo` | Active account | User can authenticate and access system |
| `inactivo` | Disabled account | Login attempts fail, existing sessions invalidated |

This provides soft delete functionality, preserving historical data while revoking access.

**Sources:** [database/american_food L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L296-L296)

---

## Security Considerations

### Password Storage

* All passwords stored as bcrypt hashes via `password_hash($password, PASSWORD_DEFAULT)`
* `PASSWORD_DEFAULT` currently resolves to bcrypt (cost factor 10-12)
* Verification during login uses `password_verify()` function (implied, not visible in cluster)
* Plain text passwords never stored or logged

**Sources:** [crud/registro_INSERT.php L56](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L56-L56)

### SQL Injection Prevention

* All database operations use prepared statements via `mysqli_prepare()`
* Parameters bound using `mysqli_stmt_bind_param()` with type specifications
* User input never directly interpolated into SQL queries
* Role validation prevents unauthorized database access

**Sources:** [crud/registro_INSERT.php L11-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L11-L18)

### Input Sanitization

The registration handler implements multi-layer input filtering:

1. **Sanitization:** All inputs pass through `filter_var()` with `FILTER_SANITIZE_SPECIAL_CHARS` or `FILTER_SANITIZE_EMAIL`
2. **Validation:** Email addresses validated with `FILTER_VALIDATE_EMAIL`
3. **Whitelist Checking:** Role values checked against `$allowed_roles` array
4. **Password Confirmation:** Client-side and server-side password matching (line 47-49)

**Sources:** [crud/registro_INSERT.php L33-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L33-L54)

### Session Security

* Sessions initialized via `conexionDB.php` before any database operations
* `session_status()` check prevents duplicate session initialization
* Session data stores user role for authorization checks on subsequent requests
* Fail-fast pattern terminates execution on database connection failure, preventing operation with invalid state

**Sources:** [config/conexionDB.php L2-L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L2-L5)

---

## Role-Based UI Component Visibility

### Common Pattern: CSS-Based Interface Segregation

The system uses complete CSS file replacement rather than conditional rendering within a single interface. Each role loads a different primary stylesheet that defines entirely different layouts and components.

**Foundation Layer (style.css):**

* Global styles and CSS custom properties shared across all interfaces
* Base components like navbar, buttons, forms
* Cart sidebar system (reused in customer and server interfaces)

**Role Layer (cajera.css, cocina.css, mesera.css, cliente.css):**

* Override global styles with role-specific layouts
* Define role-specific components not present in other interfaces
* Implement unique color schemes and branding per role

This approach provides complete UI separation without requiring server-side rendering logic or JavaScript-based component hiding.

**Sources:** [css/style.css L3-L520](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L520)

 (foundation), [css/cajera.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L10)

 [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

 [css/mesera.css L1-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L15)

 [css/cliente.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L10)

---

## Integration with Business Workflows

### Order Type Routing

Role assignments directly influence order routing through the `pedidos.tipo_pedido` field:

**Online Orders (`tipo_pedido='online'`):**

* Created by customers (rol='cliente')
* Requires `cliente_id` foreign key to `usuarios` table
* Requires `informacion_entrega` record for delivery
* `mesa_id` is NULL

**In-Person Orders (`tipo_pedido='presencial'`):**

* Created by servers (rol='mesera')
* Requires `mesa_id` foreign key to `mesas` table
* `cliente_id` is NULL
* No delivery information needed

Both order types converge at kitchen (cocinero) for preparation and cashier (cajera) for payment processing.

**Sources:** [database/american_food L260](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L260-L260)

 (tipo_pedido ENUM), [database/american_food L258-L259](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L258-L259)

 (mesa_id and cliente_id)

### Cross-Role Collaboration

```

```

**State Transitions by Role:**

* **Mesera/Cliente:** Create orders in `nuevo` state
* **Cajera:** Transition `nuevo` → `pendiente_pago` → `pagado`
* **Cocinero:** Transition `pagado` → `en_preparacion` → `listo`
* **Mesera:** Transition `listo` → `entregado` (presencial orders)
* **Sistema:** Transition `listo` → `enviado` → `entregado` (online orders)

**Sources:** [database/american_food L261](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L261-L261)

 (estado ENUM with all states)