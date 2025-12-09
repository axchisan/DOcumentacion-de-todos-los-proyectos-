# Role Definitions

> **Relevant source files**
> * [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document defines the five user roles in the American Food restaurant management system and specifies their responsibilities, permissions, and access patterns. Each role corresponds to a specific job function within restaurant operations and determines which interface components, database tables, and business operations a user can access.

For information about the authentication process and how users are assigned roles, see [Authentication Flow](/axchisan/AmericanFood/7.2-authentication-flow). For details about the role-specific user interfaces, see [Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system). For the complete order processing workflow across roles, see [Order Processing Flow](/axchisan/AmericanFood/8.1-order-processing-flow).

## Role-Based Access Control Overview

The system implements role-based access control (RBAC) through the `rol` field in the `usuarios` table. Five roles are supported, each designed for a distinct operational function. Role validation occurs at two points: during user registration and during session authentication.

**Role Validation in Registration**

```

```

**Sources**: [crud/registro_INSERT.php L38-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L38-L59)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

The role validation whitelist is defined at [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

:

```

```

The database enforces this constraint through an ENUM type at [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

:

```

```

## Role Definitions

### Admin Role

**Database Identifier**: `admin`

**Responsibilities**:

* Full system access and configuration
* Manage all database tables
* Oversee all business operations
* Configure system settings in `configuracion` table

**Access Pattern**:

* Uses global styles from `style.css` rather than role-specific interface
* Can access and modify all data across the system
* Implied supervisory access to all other role functions

**Database Access**:

| Table | Access Level |
| --- | --- |
| usuarios | Full CRUD |
| menu | Full CRUD |
| pedidos | Full CRUD |
| detalles_pedidos | Full CRUD |
| pagos | Full CRUD |
| facturas | Full CRUD |
| mesas | Full CRUD |
| inventario | Full CRUD |
| informacion_entrega | Full CRUD |
| configuracion | Full CRUD |

**Sample User**: No admin users exist in the current database sample data [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

**Sources**: [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

### Cajera Role (Cashier)

**Database Identifier**: `cajera`

**Responsibilities**:

* Process payments for in-person orders
* Generate invoices (`facturas` table)
* View sales summaries and analytics
* Manage cash transactions with change calculation
* Handle payment methods: `efectivo`, `tarjeta`, `online`

**User Interface**: `cajera.css` provides specialized cashier interface

**Primary Workflow**:

1. View orders requiring payment (`pedidos` with `estado = 'pendiente_pago'`)
2. Process payment through payment modal
3. Record transaction in `pagos` table
4. Optionally generate invoice in `facturas` table
5. Update order `estado` to `'pagado'`

**Database Access**:

| Table | Access Level | Operations |
| --- | --- | --- |
| pedidos | Read/Update | View orders, update payment status |
| pagos | Create/Read | Record payments, view payment history |
| facturas | Create/Read | Generate invoices, view invoice history |
| menu | Read | View prices for order verification |

**Sample User**:

* ID: 3
* Username: `cajera`
* Email: `cajera@gmail.com`

**Sources**: [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

 [database/american_food L306](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L306-L306)

### Cocinero Role (Cook/Chef)

**Database Identifier**: `cocinero`

**Responsibilities**:

* View incoming orders requiring preparation
* Update order preparation status
* Manage cooking workflow through state transitions
* Track order completion

**User Interface**: `cocina.css` provides kitchen-focused interface with order cards

**Primary Workflow**:

```

```

**Order State Management**:

* `nuevo`: New order appears in kitchen queue
* `en_preparacion`: Cook has started preparing the order
* `listo`: Food is ready for delivery/pickup

**Database Access**:

| Table | Access Level | Operations |
| --- | --- | --- |
| pedidos | Read/Update | View orders, update estado field |
| detalles_pedidos | Read | View order line items and quantities |
| menu | Read | View dish details and descriptions |

**Sample User**:

* ID: 1
* Username: `cocina`
* Email: `cocina@gmail.com`

**Sources**: [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

 [database/american_food L304](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L304-L304)

### Mesera Role (Server/Waitress)

**Database Identifier**: `mesera`

**Responsibilities**:

* Manage table assignments and status
* Take orders for in-person dining
* Create orders linked to specific tables
* Deliver completed orders to customers
* Coordinate between customers and kitchen

**User Interface**: `mesera.css` provides table management and order-taking interface

**Primary Workflow**:

1. Assign customers to table in `mesas` table
2. Take order and select menu items
3. Create `pedidos` record with `tipo_pedido = 'presencial'` and `mesa_id` set
4. Monitor order status updates from kitchen
5. Deliver food when `estado = 'listo'`

**Database Access**:

| Table | Access Level | Operations |
| --- | --- | --- |
| mesas | Read/Update | Manage table status, view capacity/location |
| pedidos | Create/Read/Update | Create orders, view order status |
| detalles_pedidos | Create/Read | Add order line items |
| menu | Read | Browse menu for order-taking |
| usuarios | Read | View customer information |

**Table Management**:

* Monitor table `estado`: `activa` / `inactiva`
* Track table `ubicacion`: `interior`, `terraza`, `bar`, `vip`
* Manage table `capacidad` for seating assignments

**Sample User**:

* ID: 2
* Username: `mesera`
* Email: `mesera@gmail.com`

**Sources**: [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

 [database/american_food L305](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L305-L305)

 [database/american_food L194-L198](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L194-L198)

### Cliente Role (Customer)

**Database Identifier**: `cliente`

**Responsibilities**:

* Browse restaurant menu
* Place online orders with delivery information
* Make table reservations
* View order history
* Manage shopping cart

**User Interface**: `cliente.css` provides customer-facing e-commerce interface

**Primary Workflow**:

1. Browse `menu` table items by category
2. Add items to shopping cart (cart-sidebar component)
3. Provide delivery information for `informacion_entrega` table
4. Submit order creating `pedidos` record with `tipo_pedido = 'online'` and `cliente_id` set
5. Track order status through customer interface

**Database Access**:

| Table | Access Level | Operations |
| --- | --- | --- |
| menu | Read | Browse available dishes |
| pedidos | Create/Read | Place orders, view order history |
| detalles_pedidos | Create/Read | Specify order items |
| informacion_entrega | Create | Provide delivery details |

**Registration Pattern**:

* Only role that uses public self-registration via `registro_INSERT.php`
* Other roles (staff) are pre-created by administrators

**Sample User**:

* ID: 4
* Username: `usuario`
* Email: `usuario@gmail.com`

**Sources**: [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

 [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

 [database/american_food L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L307-L307)

## Role-to-Interface Mapping

The following diagram shows how authenticated users are routed to role-specific interfaces based on their `rol` field value:

```

```

**Sources**: [database/american_food L287-L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L296)

 [crud/registro_INSERT.php L51](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L51)

## Role Permission Matrix

The following table summarizes the permissions for each role across the primary database tables:

| Table | Admin | Cajera | Cocinero | Mesera | Cliente |
| --- | --- | --- | --- | --- | --- |
| usuarios | CRUD | - | - | R | R (own) |
| menu | CRUD | R | R | R | R |
| pedidos | CRUD | RU | RU | CRU | CR (own) |
| detalles_pedidos | CRUD | R | R | CR | CR (own) |
| pagos | CRUD | CR | - | - | - |
| facturas | CRUD | CR | - | - | - |
| mesas | CRUD | - | - | RU | - |
| inventario | CRUD | - | - | - | - |
| informacion_entrega | CRUD | R | - | - | C (own) |
| configuracion | CRUD | - | - | - | - |

**Legend**: C=Create, R=Read, U=Update, D=Delete, (own)=Only own records

**Sources**: [database/american_food L1-L488](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L488)

## Role Storage and Validation

### Database Schema

The `rol` field in the `usuarios` table is defined as an ENUM type at [database/american_food L294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L294-L294)

:

```

```

This provides database-level enforcement of valid role values. The field is indexed for performance at [database/american_food L382](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L382-L382)

:

```

```

### Application-Level Validation

During user registration, the application validates the role against a whitelist at [crud/registro_INSERT.php L51-L54](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L51-L54)

:

```

```

### Role Assignment Flow

```

```

**Sources**: [crud/registro_INSERT.php L30-L60](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L30-L60)

 [database/american_food L287-L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L296)

## Role-Specific Data Access Patterns

### Order Type Discrimination

The system uses the `tipo_pedido` field to distinguish between online and in-person orders:

**Online Orders (Cliente)**:

* `tipo_pedido = 'online'`
* `cliente_id` is set (references `usuarios.id`)
* `mesa_id` is NULL
* Requires `informacion_entrega` record

**In-Person Orders (Mesera)**:

* `tipo_pedido = 'presencial'`
* `mesa_id` is set (references `mesas.id`)
* `cliente_id` is NULL
* No `informacion_entrega` needed

Query pattern from [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

:

```

```

**Sources**: [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

 [database/american_food L272-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L272-L279)

## Sample Users by Role

The database includes pre-configured users for testing each role (from [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

):

| ID | Username | Email | Role | Password (hashed) |
| --- | --- | --- | --- | --- |
| 1 | cocina | [cocina@gmail.com](mailto:cocina@gmail.com) | cocinero | bcrypt hash |
| 2 | mesera | [mesera@gmail.com](mailto:mesera@gmail.com) | mesera | bcrypt hash |
| 3 | cajera | [cajera@gmail.com](mailto:cajera@gmail.com) | cajera | bcrypt hash |
| 4 | usuario | [usuario@gmail.com](mailto:usuario@gmail.com) | cliente | bcrypt hash |

No admin user exists in the sample data, indicating admin accounts are created separately outside the standard registration flow.

**Sources**: [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

## Role State Field

All users have an additional `estado` field at [database/american_food L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L296-L296)

:

```

```

This allows administrators to disable user accounts without deleting them, preserving historical data integrity while preventing authentication. All sample users have `estado = 'activo'`.

**Sources**: [database/american_food L296](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L296-L296)

 [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)