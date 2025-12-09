# Data Models and Database Schema

> **Relevant source files**
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)
> * [controllers/CheckinController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)
> * [controllers/ClienteController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php)

## Purpose and Scope

This document provides a comprehensive reference for the database schema and data models used in the Hotel Benedetti management system. It documents the seven core database tables (`usuarios`, `empleados`, `habitaciones`, `reservas`, `checkin`, `checkout`, `notificaciones`) and their corresponding Model classes, describing the fields, data types, constraints, and relationships between entities.

For information about how these models are accessed by backend controllers, see [Backend Controllers](/GroveLive/hotelBenedetti/2.2-backend-controllers). For details on the specific business logic that manipulates these entities, see [Core Business Processes](/GroveLive/hotelBenedetti/4-core-business-processes).

---

## Data Layer Architecture

The data layer follows a Model-based architecture where each database table is represented by a corresponding PHP class that encapsulates database operations. Controllers instantiate these Model classes and invoke their methods to perform CRUD operations.

### Database Connection Pattern

All controllers establish database connections through the `Database` class and pass the connection to Model constructors:

```python
Database class → getConnection() → PDO connection → Model constructor
```

**Sources:** [controllers/AdminController.php L11-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L11-L15)

 [controllers/CheckinController.php L10-L13](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L10-L13)

 [controllers/CheckoutController.php L10-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L10-L15)

---

## Core Database Entities

### Usuario2 (Users Table)

The `Usuario2` model represents the `usuarios` table, which serves as the base entity for all system users. This table uses a role-based approach where the `rol` field determines user permissions.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique user identifier |
| `nombre` | VARCHAR | NOT NULL | User's first name |
| `apellido` | VARCHAR | NOT NULL | User's last name |
| `email` | VARCHAR | UNIQUE, NOT NULL | User's email address |
| `telefono` | VARCHAR | NULL | Contact phone number |
| `dni` | VARCHAR | UNIQUE, NOT NULL | National identification document |
| `password` | VARCHAR | NOT NULL | Hashed password (bcrypt) |
| `rol` | VARCHAR | NOT NULL | User role (cliente, administrador, recepcionista, mucama) |

#### Role Values

* `cliente` - Guest users who make bookings
* `administrador` - System administrators
* `recepcionista` - Front desk staff
* `mucama` - Housekeeping staff

#### Data Integrity Enforcement

The system enforces email and DNI uniqueness through explicit validation checks before creation and update operations:

* Email uniqueness: [controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48)
* DNI uniqueness: [controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61)
* Role validation for employees (only `recepcionista` and `mucama` allowed): [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

#### Password Handling

Passwords are hashed using `PASSWORD_BCRYPT` before storage: [controllers/AdminController.php L26](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L26-L26)

**Sources:** [controllers/AdminController.php L13-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L13-L15)

 [controllers/AdminController.php L64-L70](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L64-L70)

 [controllers/AdminController.php L152-L158](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L152-L158)

---

### Empleado (Employees Table)

The `Empleado` model extends user data specifically for employees, linking to the `usuarios` table via foreign key.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique employee record identifier |
| `usuario_id` | INT | FOREIGN KEY (usuarios.id), NOT NULL | Reference to user account |
| `cargo` | VARCHAR | NOT NULL | Employee position (recepcionista, mucama) |

#### Relationship with Usuario2

When creating an employee, the system:

1. Creates a user record in `usuarios` table
2. Creates an employee record in `empleados` table with `usuario_id` reference
3. Sets the `cargo` field to match the user's `rol` field

If the `empleados` record creation fails, the system rolls back by deleting the user record: [controllers/AdminController.php L88-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L88-L91)

**Sources:** [controllers/AdminController.php L14](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L14-L14)

 [controllers/AdminController.php L77-L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L77-L79)

 [controllers/AdminController.php L166-L168](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L166-L168)

---

### Habitacion2 (Rooms Table)

The `Habitacion2` model represents the `habitaciones` table, which stores all hotel room inventory and their current status.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique room identifier |
| `numero_habitacion` | VARCHAR | UNIQUE, NOT NULL | Room number (e.g., "101", "202") |
| `tipo` | VARCHAR | NOT NULL | Room type (individual, doble, suite) |
| `precio` | DECIMAL | NOT NULL | Nightly rate |
| `estado` | VARCHAR | NOT NULL | Current room status |

#### Room Status Values

The `estado` field tracks room availability and maintenance state:

* `disponible` - Available for booking
* `ocupada` - Currently occupied by a guest
* `en limpieza` - Being cleaned after checkout

Status transitions are managed through business process workflows (see [Room Cleaning and Status Management](/GroveLive/hotelBenedetti/4.4-room-cleaning-and-status-management)).

#### Room Number Uniqueness

The system enforces room number uniqueness:

* On creation: [controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207)
* On update: [controllers/AdminController.php L224-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L224-L235)

**Sources:** [controllers/AdminController.php L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L15-L15)

 [controllers/AdminController.php L210-L213](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L210-L213)

 [controllers/AdminController.php L238-L242](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L238-L242)

---

### Reserva (Reservations Table)

The `Reserva` model represents the `reservas` table, linking guests to rooms for specific date ranges.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique reservation identifier |
| `usuario_id` | INT | FOREIGN KEY (usuarios.id), NOT NULL | Guest who made the reservation (where rol='cliente') |
| `habitacion_id` | INT | FOREIGN KEY (habitaciones.id), NOT NULL | Reserved room |
| `fecha_entrada` | DATE | NOT NULL | Check-in date |
| `fecha_salida` | DATE | NOT NULL | Check-out date |
| `estado` | VARCHAR | NOT NULL | Reservation status |

#### Reservation Status Values

* `pendiente` - Awaiting receptionist confirmation
* `confirmada` - Approved by receptionist, ready for check-in
* `cancelada` - Cancelled by receptionist or system

#### Business Rule Enforcement

The `Reserva` entity enforces several business rules through status validation:

* Check-in only allowed for reservations with `estado = 'confirmada'`: [controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34)
* Check-out only allowed for reservations with `estado = 'confirmada'`: [controllers/CheckoutController.php L25-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L37)

**Sources:** [controllers/CheckinController.php L13](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L13-L13)

 [controllers/CheckinController.php L22-L26](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L26)

 [controllers/CheckoutController.php L25-L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L29)

---

### Checkin (Check-in Records Table)

The `Checkin` model represents the `checkin` table, recording when guests arrive at the hotel.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique check-in record identifier |
| `reserva_id` | INT | FOREIGN KEY (reservas.id), NOT NULL | Associated reservation |
| `fecha_checkin` | DATETIME | NOT NULL | Timestamp of check-in |
| `observaciones` | TEXT | NULL | Optional notes |

#### One-to-One Relationship with Reserva

Each reservation can have exactly one check-in record. The system prevents duplicate check-ins: [controllers/CheckinController.php L37-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L37-L47)

#### Check-in Creation

When creating a check-in:

* `reserva_id` is provided by the request
* `fecha_checkin` is set to current timestamp: [controllers/CheckinController.php L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L50-L50)
* `observaciones` field is set to null: [controllers/CheckinController.php L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L51-L51)

**Sources:** [controllers/CheckinController.php L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L12-L12)

 [controllers/CheckinController.php L49-L53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L49-L53)

---

### Checkout (Check-out Records Table)

The `Checkout` model represents the `checkout` table, recording guest departures and final payment amounts.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique check-out record identifier |
| `reserva_id` | INT | FOREIGN KEY (reservas.id), NOT NULL | Associated reservation |
| `fecha_checkout` | DATETIME | NOT NULL | Timestamp of check-out |
| `total_pagado` | DECIMAL | NOT NULL | Final payment amount |

#### One-to-One Relationship with Reserva

Each reservation can have exactly one check-out record. The system prevents duplicate check-outs: [controllers/CheckoutController.php L53-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L63)

#### Prerequisite Validation

Check-out requires that check-in has already been completed: [controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50)

#### Side Effects on Checkout

When a check-out is created, the system automatically:

1. Updates the room status to `"en limpieza"`: [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)
2. Creates a notification for the guest: [controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78)
3. Creates notifications for all housekeeping staff: [controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91)

**Sources:** [controllers/CheckoutController.php L13](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L13-L13)

 [controllers/CheckoutController.php L65-L68](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L68)

---

### Notificacion (Notifications Table)

The `Notificacion` model represents the `notificaciones` table, enabling asynchronous communication between system roles.

#### Schema

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique notification identifier |
| `usuario_id` | INT | FOREIGN KEY (usuarios.id), NOT NULL | Recipient user |
| `reserva_id` | INT | FOREIGN KEY (reservas.id), NULL | Related reservation (if applicable) |
| `mensaje` | TEXT | NOT NULL | Notification message content |
| `leida` | BOOLEAN | NOT NULL | Read/unread status |

#### Notification Creation Patterns

The system creates notifications for:

1. **Client notification on checkout**: [controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78) ``` "Se ha realizado el check-out de su reserva (ID: {reservaId}).   Total pagado: ${totalPagado}." ```
2. **Housekeeping notification on checkout**: [controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91) * Queries all users where `rol = 'mucama'` * Creates notification for each housekeeping staff member ``` "La habitación {habitacionId} necesita limpieza después del   check-out de la reserva (ID: {reservaId})." ```

**Sources:** [controllers/CheckoutController.php L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L15-L15)

 [controllers/CheckoutController.php L75-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L91)

---

## Entity Relationship Diagram

The following diagram illustrates the relationships between all database entities, showing foreign key constraints and cardinality:

**Entity Relationships with Foreign Key Constraints**

```

```

**Sources:** [controllers/AdminController.php L4-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L4-L6)

 [controllers/CheckinController.php L4-L5](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L4-L5)

 [controllers/CheckoutController.php L4-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L4-L6)

---

## Database Interaction Patterns

### Model Instantiation Pattern

Controllers follow a consistent pattern for instantiating models:

**AdminController Model Setup**

```

```

**Sources:** [controllers/AdminController.php L11-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L11-L15)

**CheckinController Model Setup**

```

```

**Sources:** [controllers/CheckinController.php L10-L13](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L10-L13)

**CheckoutController Model Setup**

```

```

**Sources:** [controllers/CheckoutController.php L10-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L10-L15)

---

### Query Patterns for Aggregations

The admin dashboard demonstrates direct SQL queries for statistics gathering:

#### Room Status Aggregation

```

```

**Sources:** [controllers/AdminController.php L261-L279](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L261-L279)

#### Employee Aggregation by Role

```

```

**Sources:** [controllers/AdminController.php L282-L290](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L282-L290)

---

## Data Validation and Constraints

### Unique Constraint Enforcement

The system enforces uniqueness through application-level validation before database operations:

#### Email Uniqueness Check (Create)

```sql
Query: SELECT id FROM usuarios WHERE email = :email
Result: If rowCount() > 0, reject with "El email ya está registrado."
```

[controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48)

#### Email Uniqueness Check (Update)

```sql
Query: SELECT id FROM usuarios WHERE email = :email AND id != :id
Result: If rowCount() > 0, reject with "El email ya está registrado."
```

[controllers/AdminController.php L124-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L124-L135)

#### DNI Uniqueness Check (Create)

```sql
Query: SELECT id FROM usuarios WHERE dni = :dni
Result: If rowCount() > 0, reject with "El DNI ya está registrado."
```

[controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61)

#### DNI Uniqueness Check (Update)

```sql
Query: SELECT id FROM usuarios WHERE dni = :dni AND id != :id
Result: If rowCount() > 0, reject with "El DNI ya está registrado."
```

[controllers/AdminController.php L138-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L138-L149)

#### Room Number Uniqueness Check (Create)

```sql
Query: SELECT id FROM habitaciones WHERE numero_habitacion = :roomNumber
Result: If rowCount() > 0, reject with "El número de habitación ya existe."
```

[controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207)

#### Room Number Uniqueness Check (Update)

```sql
Query: SELECT id FROM habitaciones WHERE numero_habitacion = :roomNumber AND id != :id
Result: If rowCount() > 0, reject with "El número de habitación ya existe."
```

[controllers/AdminController.php L224-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L224-L235)

**Sources:** [controllers/AdminController.php L38-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L61)

 [controllers/AdminController.php L124-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L124-L149)

 [controllers/AdminController.php L197-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L235)

---

### State Validation for Business Rules

#### Reservation State Requirements

The system validates reservation state before allowing certain operations:

| Operation | Required State | Validation Location |
| --- | --- | --- |
| Check-in | `estado = 'confirmada'` | [controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34) |
| Check-out | `estado = 'confirmada'` | [controllers/CheckoutController.php L25-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L37) |

#### Sequential Operation Requirements

Certain operations require previous operations to have been completed:

| Operation | Prerequisite | Validation Location |
| --- | --- | --- |
| Check-out | Check-in must exist | [controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50) |

#### Duplicate Prevention

The system prevents duplicate temporal records:

| Operation | Prevention Check | Validation Location |
| --- | --- | --- |
| Check-in | No existing check-in for reservation | [controllers/CheckinController.php L37-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L37-L47) |
| Check-out | No existing check-out for reservation | [controllers/CheckoutController.php L53-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L63) |

**Sources:** [controllers/CheckinController.php L22-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L47)

 [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)

---

### Role-Based Data Access

The system restricts employee role values to specific types:

```yaml
Allowed employee roles: ['recepcionista', 'mucama']
Validation: if (!in_array($role, ['recepcionista', 'mucama']))
Error: "Rol no válido. Solo se permiten recepcionistas y mucamas."
```

[controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 [controllers/AdminController.php L115-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L115-L121)

Administrators and clients are not stored in the `empleados` table - they exist only in the `usuarios` table with their respective `rol` values.

**Sources:** [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 [controllers/AdminController.php L115-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L115-L121)

---

## Summary

The Hotel Benedetti database schema implements a normalized relational model with seven core entities:

1. **Usuario2** - Base user authentication and identity
2. **Empleado** - Employee-specific data extending Usuario2
3. **Habitacion2** - Room inventory and status management
4. **Reserva** - Booking records linking users and rooms
5. **Checkin** - Temporal records of guest arrivals
6. **Checkout** - Temporal records of guest departures with payment
7. **Notificacion** - Asynchronous communication system

The schema enforces data integrity through:

* Application-level uniqueness validation (email, DNI, room numbers)
* Foreign key relationships between entities
* State-based business rule enforcement (reservation status, sequential operations)
* Role-based access patterns (employee types, user roles)

All database operations are performed through Model classes instantiated with a shared PDO connection, with controllers coordinating multi-table transactions and enforcing business logic constraints.