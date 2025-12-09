# Data Validation and Business Rules

> **Relevant source files**
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)
> * [controllers/AuthController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php)
> * [controllers/CheckinController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)

## Purpose and Scope

This document describes the validation logic and business rules enforced by the Hotel Benedetti system's backend controllers. It covers input sanitization, uniqueness constraints, state-based validation rules, and cross-entity business logic that ensures data integrity and correct operational workflows.

For information about the overall controller architecture and action routing patterns, see [Controller Architecture](/GroveLive/hotelBenedetti/6.1-controller-architecture). For authentication-specific validation, see [Authentication System](/GroveLive/hotelBenedetti/3.1-authentication-system). For notification generation rules, see [Notification System](/GroveLive/hotelBenedetti/6.3-notification-system).

---

## Overview of Validation Layers

The system implements validation at multiple levels to ensure data integrity and enforce business rules:

```

```

**Diagram 1: Multi-Layer Validation Flow**

Sources: [controllers/AdminController.php L20-L28](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L28)

 [controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34)

 [controllers/CheckoutController.php L25-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L50)

---

## Input Sanitization

All user input is sanitized before being processed to prevent XSS attacks and SQL injection. The system uses two primary PHP functions applied to all POST parameters:

| Function | Purpose | Applied To |
| --- | --- | --- |
| `htmlspecialchars()` | Converts special characters to HTML entities | All text inputs |
| `strip_tags()` | Removes HTML and PHP tags | All text inputs |
| `password_hash()` | Hashes passwords using BCRYPT | Password fields |
| `(int)` / `(float)` casting | Type coercion for numeric values | IDs, prices, totals |

### Sanitization Pattern

```

```

**Diagram 2: Input Sanitization Pattern**

**Example Implementation:**

In `AdminController.php`, employee data is sanitized as follows:

* Lines 21-27: Name, lastname, email, telefono, dni, and role fields are sanitized using `htmlspecialchars(strip_tags($_POST['field']))`
* Line 26: Password is hashed using `password_hash($_POST['password'], PASSWORD_BCRYPT)`

Sources: [controllers/AdminController.php L21-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L21-L27)

 [controllers/AdminController.php L106-L112](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L106-L112)

 [controllers/AdminController.php L192-L222](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L192-L222)

---

## Uniqueness Constraints

The system enforces uniqueness constraints to prevent duplicate records in critical fields:

### Email Uniqueness

Validated before creating or updating users/employees to ensure no two accounts share the same email address.

**Add Employee Validation:**

```

```

**Update Employee Validation:**

```

```

If a matching record is found, the operation fails with error message: `"El email ya está registrado."`

Sources: [controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48)

 [controllers/AdminController.php L124-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L124-L135)

### DNI Uniqueness

Similar pattern for DNI (national identity document) validation:

**Add Employee Validation:**

```

```

**Update Employee Validation:**

```

```

Error message: `"El DNI ya está registrado."`

Sources: [controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61)

 [controllers/AdminController.php L138-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L138-L149)

### Room Number Uniqueness

Room numbers must be unique across all rooms:

**Add Room Validation:**

```

```

**Update Room Validation:**

```

```

Error message: `"El número de habitación ya existe."`

Sources: [controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207)

 [controllers/AdminController.php L224-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L224-L235)

### Uniqueness Validation Summary Table

| Entity | Field | Context | Query Pattern | Error Message |
| --- | --- | --- | --- | --- |
| Usuario2 | `email` | Add | `WHERE email = :email` | "El email ya está registrado." |
| Usuario2 | `email` | Update | `WHERE email = :email AND id != :id` | "El email ya está registrado." |
| Usuario2 | `dni` | Add | `WHERE dni = :dni` | "El DNI ya está registrado." |
| Usuario2 | `dni` | Update | `WHERE dni = :dni AND id != :id` | "El DNI ya está registrado." |
| Habitacion2 | `numero_habitacion` | Add | `WHERE numero_habitacion = :roomNumber` | "El número de habitación ya existe." |
| Habitacion2 | `numero_habitacion` | Update | `WHERE numero_habitacion = :roomNumber AND id != :id` | "El número de habitación ya existe." |

---

## Role Validation

The system restricts which roles can be assigned to employees through the administrative interface.

### Allowed Employee Roles

Only two roles can be created/updated via `AdminController.php`:

* `recepcionista` (Receptionist)
* `mucama` (Maid)

The `administrador` and `cliente` roles are excluded from employee management to prevent privilege escalation.

```

```

**Diagram 3: Role Validation Logic**

**Implementation:**

```

```

This check occurs in both `addEmployee` and `updateEmployee` actions.

Sources: [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 [controllers/AdminController.php L115-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L115-L121)

---

## State-Based Business Rules

Many operations require entities to be in specific states before they can proceed. The system validates these state requirements before allowing state transitions.

### Reservation State Requirements

```

```

**Diagram 4: Reservation State Transitions with Validation Requirements**

### Check-in Validation Rules

The `CheckinController.php` enforces the following rules:

1. **Reservation Must Exist and Be Confirmed:** ``` ``` Error if not found: `"La reserva no existe o no está confirmada."`
2. **Check-in Must Be Unique Per Reservation:** ``` ``` Error if exists: `"El check-in ya fue realizado para esta reserva."`

Sources: [controllers/CheckinController.php L22-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L47)

### Check-out Validation Rules

The `CheckoutController.php` enforces additional sequential requirements:

1. **Reservation Must Exist and Be Confirmed:** ``` ``` Error if not found: `"La reserva no existe o no está confirmada."`
2. **Check-in Must Have Been Performed:** ``` ``` Error if not exists: `"No se ha realizado el check-in para esta reserva."`
3. **Check-out Must Be Unique Per Reservation:** ``` ``` Error if exists: `"El check-out ya fue realizado para esta reserva."`

Sources: [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)

### State Validation Rule Matrix

| Operation | Required State | Validation Query | Error Message |
| --- | --- | --- | --- |
| Check-in | Reserva.estado = 'confirmada' | `WHERE id = :reservaId AND estado = 'confirmada'` | "La reserva no existe o no está confirmada." |
| Check-in | No existing check-in | `WHERE reserva_id = :reservaId` (must return 0 rows) | "El check-in ya fue realizado para esta reserva." |
| Check-out | Reserva.estado = 'confirmada' | `WHERE id = :reservaId AND estado = 'confirmada'` | "La reserva no existe o no está confirmada." |
| Check-out | Check-in exists | `WHERE reserva_id = :reservaId` (must return > 0 rows) | "No se ha realizado el check-in para esta reserva." |
| Check-out | No existing check-out | `WHERE reserva_id = :reservaId` (must return 0 rows) | "El check-out ya fue realizado para esta reserva." |

---

## Cross-Entity Validation Rules

Some business rules involve validating relationships between multiple entities.

### Employee Creation Transaction

When creating an employee, two database entities must be created atomically:

1. `Usuario2` record (authentication data)
2. `Empleado` record (role-specific data)

If the `Empleado` creation fails, the system performs a compensating transaction to delete the `Usuario2` record, maintaining database consistency.

```

```

**Diagram 5: Employee Creation Transaction with Rollback**

**Implementation Details:**

The controller creates the `Usuario2` record first (line 72), captures the returned `usuario_id` (line 74), then attempts to create the `Empleado` record (line 79). If the employee creation fails, it executes a compensating DELETE query (lines 88-91) to remove the orphaned user record.

Sources: [controllers/AdminController.php L63-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L63-L103)

---

## Validation Flow Examples

### Complete Employee Addition Flow

```

```

**Diagram 6: Complete Employee Addition Validation Flow**

Sources: [controllers/AdminController.php L20-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L103)

### Complete Check-out Flow with Validation

```

```

**Diagram 7: Complete Check-out Flow with Business Rule Enforcement**

Sources: [controllers/CheckoutController.php L20-L93](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L20-L93)

---

## Business Rule Enforcement Points

The following table maps business rules to their enforcement locations in the codebase:

| Business Rule | Controller | Action | Code Location | Validation Type |
| --- | --- | --- | --- | --- |
| Only recepcionistas and mucamas can be added as employees | AdminController | addEmployee, updateEmployee | Lines 29-35, 115-121 | Role constraint |
| Email addresses must be unique | AdminController | addEmployee, updateEmployee | Lines 38-48, 124-135 | Uniqueness constraint |
| DNI numbers must be unique | AdminController | addEmployee, updateEmployee | Lines 51-61, 138-149 | Uniqueness constraint |
| Room numbers must be unique | AdminController | addRoom, updateRoom | Lines 197-207, 224-235 | Uniqueness constraint |
| Only confirmed reservations can be checked in | CheckinController | checkin | Lines 22-34 | State requirement |
| Check-in can only occur once per reservation | CheckinController | checkin | Lines 37-47 | Idempotency rule |
| Check-out requires prior check-in | CheckoutController | checkout | Lines 40-50 | Sequential dependency |
| Check-out can only occur once per reservation | CheckoutController | checkout | Lines 53-63 | Idempotency rule |
| Check-out transitions room to "en limpieza" | CheckoutController | checkout | Line 72 | State transition rule |
| Check-out notifies cliente and mucamas | CheckoutController | checkout | Lines 75-91 | Notification rule |

---

## Error Response Format

All validation failures return a consistent JSON error response format:

```

```

Success responses follow this format:

```

```

This standardized format enables consistent error handling in frontend JavaScript code across all interfaces.

Sources: [controllers/AdminController.php L30-L33](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L30-L33)

 [controllers/CheckinController.php L29-L33](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L29-L33)

 [controllers/CheckoutController.php L32-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L32-L35)

---

## Password Security Rules

Password handling follows security best practices:

1. **Hashing Algorithm:** `PASSWORD_BCRYPT` with default cost factor
2. **Hash Storage:** Passwords are never stored in plain text
3. **Optional Updates:** When updating employees, password is only re-hashed if a new password is provided (line 111: `!empty($_POST['password'])`)

```

```

**Diagram 8: Password Hashing Logic**

Sources: [controllers/AdminController.php L26](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L26-L26)

 [controllers/AdminController.php L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L111-L111)

---

## Summary

The Hotel Benedetti system enforces data integrity through multiple validation layers:

* **Input Sanitization:** All user input is sanitized using `htmlspecialchars()` and `strip_tags()`
* **Uniqueness Constraints:** Email, DNI, and room numbers are validated for uniqueness
* **Role Constraints:** Employee roles are restricted to 'recepcionista' and 'mucama'
* **State-Based Rules:** Check-in and check-out operations validate reservation states
* **Sequential Dependencies:** Check-out requires prior check-in
* **Idempotency Rules:** Check-in and check-out can only occur once per reservation
* **Cross-Entity Validation:** Employee creation uses compensating transactions for atomicity
* **Security Rules:** Passwords are hashed using BCRYPT before storage

These validation rules are enforced at the controller level before any database operations occur, ensuring data consistency and preventing invalid state transitions throughout the system.