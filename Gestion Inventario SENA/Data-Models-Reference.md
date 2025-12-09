# Data Models Reference

> **Relevant source files**
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/models/inventory_items.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py)
> * [server/app/models/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

This document provides a complete reference of all database models in the SENA Inventory Management System. It describes the structure, fields, relationships, constraints, and validation rules for each entity in the PostgreSQL database accessed through SQLAlchemy ORM.

For API endpoint documentation that uses these models, see [API Reference](/axchisan/GestionInventarioSENA/15-api-reference). For information about database migrations and deployment configuration, see [Deployment & Configuration](/axchisan/GestionInventarioSENA/2.3-deployment-and-configuration).

---

## Overview

The system uses SQLAlchemy ORM with PostgreSQL as the database backend. All models inherit from a common `Base` class defined in the database module and use UUID primary keys. Models are defined in `server/app/models/` and referenced by corresponding Pydantic schemas in `server/app/schemas/` for API request/response validation.

**Key Architectural Patterns:**

* UUID primary keys for all entities
* Soft deletes where applicable (status fields instead of deletion)
* Audit timestamps (`created_at`, `updated_at`) on all models
* Foreign key cascades for referential integrity
* Check constraints for enum-like validations
* Multi-tenant scoping via `environment_id`

---

## Entity Relationship Overview

```

```

**Sources:**

* Based on high-level architecture diagram provided
* [server/app/models/inventory_items.py L1-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L1-L46)
* [server/app/models/maintenance_requests.py L1-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L1-L36)

---

## User Model

The `User` model represents system users with role-based access control. Users are associated with a specific `Environment` and have one of five roles: `student`, `instructor`, `supervisor`, `admin`, or `admin_general`.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `email` | String | Unique, Not Null | User email address for authentication |
| `password_hash` | String | Not Null | Bcrypt/Argon2 hashed password |
| `role` | String(20) | Not Null, CHECK | User role: student, instructor, supervisor, admin, admin_general |
| `environment_id` | UUID | Foreign Key (nullable) | Reference to assigned Environment |
| `first_name` | String(50) | Not Null | User first name |
| `last_name` | String(50) | Not Null | User last name |
| `is_active` | Boolean | Default: True | Account active status |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Relationships

* `environment`: Many-to-One with `Environment` (user belongs to one environment)
* `inventory_checks_created`: One-to-Many with `InventoryCheck` (as student)
* `inventory_checks_reviewed`: One-to-Many with `InventoryCheck` (as instructor/supervisor)
* `maintenance_requests`: One-to-Many with `MaintenanceRequest`
* `loans`: One-to-Many with `Loan`
* `notifications`: One-to-Many with `Notification`
* `feedback`: One-to-Many with `Feedback`
* `audit_logs`: One-to-Many with `AuditLog`

### Role-Based Access

```

```

**Sources:**

* User model structure inferred from router usage
* [server/app/routers/inventory.py L21-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L21-L36)  - Role-based environment filtering
* [server/app/routers/maintenance_requests.py L26-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L26-L43)  - Role-based access control
* Role-based routing from high-level architecture diagrams

---

## Environment Model

The `Environment` model represents physical locations or spaces where inventory is stored. It serves as the primary multi-tenant scoping mechanism in the system.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `name` | String(100) | Not Null | Environment name |
| `location` | String(200) | Nullable | Physical location description |
| `is_warehouse` | Boolean | Default: False | Flag for warehouse-type environments |
| `is_active` | Boolean | Default: True | Active status |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Relationships

* `users`: One-to-Many with `User` (users assigned to environment)
* `inventory_items`: One-to-Many with `InventoryItem`
* `inventory_checks`: One-to-Many with `InventoryCheck`
* `maintenance_requests`: One-to-Many with `MaintenanceRequest`
* `schedules`: One-to-Many with `Schedule`

### Environment Scoping Pattern

```

```

**Sources:**

* [server/app/routers/inventory.py L25-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L25-L36)  - Environment scoping logic
* [server/app/routers/maintenance_requests.py L26-L42](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L26-L42)  - Role-based environment filtering
* Environment model structure inferred from router usage

---

## InventoryItem Model

The `InventoryItem` model represents physical equipment and assets tracked in the system. Items belong to a specific environment and support both individual and group quantity tracking.

### Model Definition

Defined in [server/app/models/inventory_items.py L9-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L9-L46)

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `environment_id` | UUID | Foreign Key, CASCADE | Reference to Environment |
| `name` | String(100) | Not Null | Item name |
| `serial_number` | String(100) | Unique, Nullable | Manufacturer serial number |
| `internal_code` | String(50) | Unique, Not Null | SENA internal tracking code |
| `category` | String(20) | Not Null, CHECK | Item category (see categories below) |
| `brand` | String(50) | Nullable | Manufacturer brand |
| `model` | String(100) | Nullable | Model identifier |
| `status` | String(20) | Not Null, CHECK | Current status (see statuses below) |
| `purchase_date` | Date | Nullable | Date of purchase |
| `warranty_expiry` | Date | Nullable | Warranty expiration date |
| `last_maintenance` | Date | Nullable | Last maintenance date |
| `next_maintenance` | Date | Nullable | Next scheduled maintenance |
| `image_url` | String(500) | Nullable | URL to item image |
| `notes` | Text | Nullable | Additional notes |
| `quantity` | Integer | Not Null, >= 0 | Total quantity |
| `quantity_damaged` | Integer | Not Null, >= 0 | Count of damaged units |
| `quantity_missing` | Integer | Not Null, >= 0 | Count of missing units |
| `item_type` | String(10) | Not Null, CHECK | Type: 'individual' or 'group' |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Category Enumeration

Valid values defined by check constraint [server/app/models/inventory_items.py L39](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L39-L39)

:

```
'computer', 'projector', 'keyboard', 'mouse', 'tv', 'camera', 'microphone', 'tablet', 'other'
```

### Status Enumeration

Valid values defined by check constraint [server/app/models/inventory_items.py L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L40-L40)

:

```
'available', 'in_use', 'maintenance', 'damaged', 'lost', 'missing', 'good'
```

### Status Auto-Update Logic

The item status is automatically calculated based on quantity fields [server/app/routers/inventory.py L93-L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L99)

:

```

```

**Sources:**

* [server/app/models/inventory_items.py L9-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L9-L46)
* [server/app/routers/inventory.py L93-L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L99)  - Status auto-update
* [server/app/routers/inventory.py L129-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L129-L134)  - Verification status update

### Relationships

* `environment`: Many-to-One with `Environment` [server/app/models/inventory_items.py L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L35-L35)
* `loans`: One-to-Many with `Loan` [server/app/models/inventory_items.py L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L36-L36)
* `maintenance_requests`: One-to-Many with `MaintenanceRequest` (implicit via `item_id`)
* `inventory_check_items`: One-to-Many with `InventoryCheckItem` (implicit)

### API Operations

```

```

**Sources:**

* [server/app/routers/inventory.py L14-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L158)

---

## InventoryCheck Model

The `InventoryCheck` model represents a verification session where items in an environment are inspected and their status updated. It implements a three-stage approval workflow: student → instructor → supervisor.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `environment_id` | UUID | Foreign Key, CASCADE | Reference to Environment being checked |
| `student_id` | UUID | Foreign Key | User who performed initial check |
| `instructor_id` | UUID | Foreign Key, Nullable | User who performed instructor review |
| `supervisor_id` | UUID | Foreign Key, Nullable | User who performed supervisor review |
| `schedule_id` | UUID | Foreign Key, Nullable | Associated schedule if applicable |
| `status` | String(50) | Not Null | Workflow status (see below) |
| `total_items` | Integer | Default: 0 | Total items checked |
| `items_good` | Integer | Default: 0 | Items in good condition |
| `items_damaged` | Integer | Default: 0 | Items damaged |
| `items_missing` | Integer | Default: 0 | Items missing |
| `is_clean` | Boolean | Default: True | Environment cleanliness flag |
| `is_organized` | Boolean | Default: True | Environment organization flag |
| `observations` | Text | Nullable | General observations |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Status Workflow

```

```

Status values: `draft`, `issues`, `instructor_review`, `supervisor_review`, `complete`, `rejected`

### Verification Totals Calculation

The system automatically calculates aggregate totals from individual item statuses:

```

```

This calculation is performed by the `calculate_verification_totals()` function referenced in workflow logic.

**Sources:**

* InventoryCheck model structure inferred from diagrams and router usage
* Workflow states from high-level architecture diagrams

### Relationships

* `environment`: Many-to-One with `Environment`
* `student`: Many-to-One with `User`
* `instructor`: Many-to-One with `User`
* `supervisor`: Many-to-One with `User`
* `schedule`: Many-to-One with `Schedule`
* `items`: One-to-Many with `InventoryCheckItem`
* `supervisor_reviews`: One-to-Many with `SupervisorReview`

---

## MaintenanceRequest Model

The `MaintenanceRequest` model tracks repair and maintenance needs for equipment or environments. Requests can be item-specific or environment-general.

### Model Definition

Defined in [server/app/models/maintenance_requests.py L8-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L8-L36)

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `item_id` | UUID | Foreign Key, CASCADE, Nullable | Reference to InventoryItem (optional) |
| `environment_id` | UUID | Foreign Key, CASCADE, Nullable | Reference to Environment (optional) |
| `user_id` | UUID | Foreign Key, CASCADE, Not Null | User who submitted request |
| `assigned_technician_id` | UUID | Foreign Key, SET NULL | Technician assigned to request |
| `title` | String(200) | Not Null | Request title |
| `description` | Text | Not Null | Detailed description |
| `priority` | String(20) | Not Null, CHECK | Priority level (see below) |
| `status` | String(20) | Not Null, CHECK | Current status (see below) |
| `category` | String(50) | Nullable | Maintenance category |
| `location` | String(200) | Nullable | Specific location within environment |
| `estimated_completion` | Date | Nullable | Estimated completion date |
| `actual_completion` | Date | Nullable | Actual completion date |
| `cost` | Numeric(10,2) | Nullable | Maintenance cost |
| `notes` | Text | Nullable | Additional notes |
| `images_urls` | ARRAY(String) | Nullable | Array of image URLs |
| `quantity_affected` | Integer | Default: 1 | Number of items affected |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Priority Enumeration

Valid values defined by check constraint [server/app/models/maintenance_requests.py L32](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L32-L32)

:

```
'low', 'medium', 'high', 'urgent', 'baja', 'media', 'alta', 'urgente'
```

(Supports both English and Spanish values)

### Status Enumeration

Valid values defined by check constraint [server/app/models/maintenance_requests.py L33](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L33-L33)

:

```
'pending', 'assigned', 'in_progress', 'completed', 'cancelled'
```

### Status Workflow

```

```

**Sources:**

* [server/app/models/maintenance_requests.py L8-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L8-L36)
* [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)  - Status update logic

### Constraint Logic

At least one of `item_id` or `environment_id` must be specified [server/app/models/maintenance_requests.py L34](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L34-L34)

:

```

```

This allows requests to be either:

* **Item-specific**: Repairs for a particular piece of equipment
* **Environment-general**: Repairs for the physical space itself

### Notification Integration

When a maintenance request is created or updated, the system automatically generates notifications:

**On Creation** [server/app/routers/maintenance_requests.py L81-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L105)

:

```

```

**On Status Change** [server/app/routers/maintenance_requests.py L134-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L134-L153)

:

* Notifies original requester when status changes to: `in_progress`, `completed`, `cancelled`, `on_hold`

**Sources:**

* [server/app/routers/maintenance_requests.py L53-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L107)  - Creation with notifications
* [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)  - Update with notifications

### Relationships

* `item`: Many-to-One with `InventoryItem`
* `environment`: Many-to-One with `Environment`
* `user`: Many-to-One with `User` (requester)
* `assigned_technician`: Many-to-One with `User` (technician)
* `maintenance_history`: One-to-Many with `MaintenanceHistory`

---

## Loan Model

The `Loan` model tracks equipment borrowing by users. Loans have a lifecycle from pending approval through active usage to completion.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `item_id` | UUID | Foreign Key, CASCADE | Reference to InventoryItem |
| `user_id` | UUID | Foreign Key, CASCADE | User borrowing the item |
| `status` | String(20) | Not Null | Loan status (see below) |
| `start_date` | DateTime | Not Null | Loan start date/time |
| `end_date` | DateTime | Not Null | Expected return date/time |
| `actual_return_date` | DateTime | Nullable | Actual return date/time |
| `notes` | Text | Nullable | Loan notes or conditions |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Status Workflow

```

```

Status values: `pending`, `approved`, `rejected`, `active`, `completed`, `overdue`

### Relationships

* `item`: Many-to-One with `InventoryItem`
* `user`: Many-to-One with `User`

**Sources:**

* Loan model structure inferred from diagrams and API references

---

## Notification Model

The `Notification` model delivers event-driven alerts to users about workflow state changes, pending actions, and system updates.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key, CASCADE | Recipient user |
| `type` | String(50) | Not Null | Notification type (see below) |
| `title` | String(200) | Not Null | Notification title |
| `message` | Text | Not Null | Notification message content |
| `is_read` | Boolean | Default: False | Read status |
| `priority` | String(20) | Not Null | Priority: 'low', 'medium', 'high' |
| `related_entity_id` | UUID | Nullable | ID of related entity (check, request, etc.) |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |

### Notification Types

Common notification types generated by the system:

| Type | Generated By | Description |
| --- | --- | --- |
| `inventory_check` | Inventory check workflow | Check needs review at next stage |
| `maintenance_request` | Maintenance request creation | New request submitted |
| `maintenance_update` | Maintenance request status change | Request status changed |
| `loan_request` | Loan workflow | Loan needs approval |
| `loan_approved` | Loan approval | Loan request approved |
| `loan_rejected` | Loan rejection | Loan request rejected |
| `check_completed` | Check completion | Verification completed |

### API Operations

Defined in [server/app/routers/notifications.py L1-L71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L1-L71)

```

```

**Sources:**

* [server/app/routers/notifications.py L14-L70](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L14-L70)
* Notification generation examples in [server/app/routers/maintenance_requests.py L93-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L93-L103)

### Relationships

* `user`: Many-to-One with `User`

---

## Feedback Model

The `Feedback` model collects user feedback, bug reports, feature requests, and other input about the application.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key, CASCADE | User submitting feedback |
| `type` | String(50) | Not Null, CHECK | Feedback type (see below) |
| `category` | String(100) | Nullable | Custom category |
| `title` | String(200) | Not Null | Feedback title |
| `description` | Text | Not Null | Detailed description |
| `steps_to_reproduce` | Text | Nullable | For bug reports |
| `priority` | String(20) | Not Null, CHECK | Priority: 'low', 'medium', 'high' |
| `rating` | Integer | Nullable, 1-5 | User rating of application |
| `status` | String(20) | Not Null | Processing status (see below) |
| `admin_response` | Text | Nullable | Admin response to feedback |
| `include_device_info` | Boolean | Default: False | Include device metadata |
| `include_logs` | Boolean | Default: False | Include application logs |
| `allow_follow_up` | Boolean | Default: True | Allow admin follow-up contact |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |
| `updated_at` | TIMESTAMP | Server Default | Record update timestamp |

### Type Enumeration

Valid values validated in [server/app/routers/feedback.py L63-L68](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L63-L68)

:

```
'bug', 'suggestion', 'feature', 'compliment', 'complaint', 'other'
```

Additional categories available in client [client/lib/presentation/screens/feedback/feedback_form_screen.dart L30-L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L30-L73)

:

* `suggestion` - Ideas for improvement
* `bug` - Technical problems
* `feature` - New functionality requests
* `usability` - User experience comments
* `performance` - Speed or performance issues
* `other` - General comments

### Status Enumeration

Status values for feedback lifecycle:

```
'submitted', 'reviewed', 'in_progress', 'completed', 'rejected'
```

### Status Workflow

```

```

### API Operations

Defined in [server/app/routers/feedback.py L14-L262](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L14-L262)

```

```

**Sources:**

* [server/app/routers/feedback.py L14-L262](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L14-L262)
* [client/lib/presentation/screens/feedback/feedback_form_screen.dart L1-L687](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L1-L687)

### Relationships

* `user`: Many-to-One with `User`

---

## AuditLog Model

The `AuditLog` model provides comprehensive audit trails for compliance, tracking all API requests, user actions, and system changes.

### Fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key, CASCADE, Nullable | User who performed action |
| `action` | String(100) | Not Null | Action performed |
| `resource_type` | String(50) | Not Null | Type of resource affected |
| `resource_id` | UUID | Nullable | ID of affected resource |
| `method` | String(10) | Not Null | HTTP method: GET, POST, PUT, DELETE |
| `endpoint` | String(200) | Not Null | API endpoint called |
| `status_code` | Integer | Not Null | HTTP response status code |
| `request_data` | JSON | Nullable | Request payload (sanitized) |
| `response_data` | JSON | Nullable | Response payload (sanitized) |
| `ip_address` | String(50) | Nullable | Client IP address |
| `user_agent` | String(500) | Nullable | Client user agent |
| `duration_ms` | Integer | Nullable | Request processing time (milliseconds) |
| `severity` | String(20) | Not Null | Log severity: 'info', 'warning', 'error' |
| `created_at` | TIMESTAMP | Server Default | Record creation timestamp |

### Audit Middleware Integration

The audit logging is implemented via middleware that intercepts all API requests:

```

```

### Sensitive Data Filtering

The audit middleware automatically filters sensitive fields from logged data:

* `password`
* `password_hash`
* `token`
* `access_token`
* `refresh_token`
* `secret`

### Severity Levels

| Severity | When Used | Example Actions |
| --- | --- | --- |
| `info` | Normal operations | GET requests, successful creates |
| `warning` | Potentially concerning events | Failed login attempts, validation errors |
| `error` | System errors | 500 errors, database failures |

**Sources:**

* AuditLog model structure inferred from audit system diagrams
* Audit middleware behavior from high-level architecture diagrams

### Relationships

* `user`: Many-to-One with `User`

---

## Supporting Models

### Schedule Model

Tracks scheduled verification sessions.

| Field | Type | Description |
| --- | --- | --- |
| `id` | UUID | Primary Key |
| `environment_id` | UUID | Foreign Key to Environment |
| `name` | String | Schedule name |
| `frequency` | String | Frequency: daily, weekly, monthly |
| `scheduled_time` | Time | Time of day |
| `is_active` | Boolean | Active status |
| `created_at` | TIMESTAMP | Creation timestamp |

### InventoryCheckItem Model

Represents individual item verification within a check session.

| Field | Type | Description |
| --- | --- | --- |
| `id` | UUID | Primary Key |
| `inventory_check_id` | UUID | Foreign Key to InventoryCheck |
| `inventory_item_id` | UUID | Foreign Key to InventoryItem |
| `status` | String | Item status: 'good', 'damaged', 'missing' |
| `quantity_verified` | Integer | Quantity verified |
| `notes` | Text | Item-specific notes |
| `created_at` | TIMESTAMP | Creation timestamp |

### SupervisorReview Model

Records supervisor review comments and decisions.

| Field | Type | Description |
| --- | --- | --- |
| `id` | UUID | Primary Key |
| `inventory_check_id` | UUID | Foreign Key to InventoryCheck |
| `supervisor_id` | UUID | Foreign Key to User |
| `decision` | String | Decision: 'approved', 'rejected' |
| `comments` | Text | Review comments |
| `created_at` | TIMESTAMP | Creation timestamp |

### MaintenanceHistory Model

Tracks history of maintenance work performed.

| Field | Type | Description |
| --- | --- | --- |
| `id` | UUID | Primary Key |
| `maintenance_request_id` | UUID | Foreign Key to MaintenanceRequest |
| `action` | String | Action taken |
| `performed_by` | UUID | Foreign Key to User |
| `performed_at` | TIMESTAMP | Action timestamp |
| `notes` | Text | Action notes |

**Sources:**

* Supporting model structures inferred from relationship diagrams and system architecture

---

## Database Constraints Summary

### Primary Keys

All models use UUID primary keys with automatic generation via `uuid.uuid4()`.

### Foreign Key Cascade Behavior

| Relationship | On Delete Behavior | Reasoning |
| --- | --- | --- |
| `InventoryItem.environment_id` | CASCADE | Items belong to environment |
| `User.environment_id` | SET NULL | Users can exist without environment |
| `MaintenanceRequest.item_id` | CASCADE | Request tied to item lifecycle |
| `MaintenanceRequest.assigned_technician_id` | SET NULL | Preserve request if technician deleted |
| `Notification.user_id` | CASCADE | Notifications belong to user |
| `AuditLog.user_id` | CASCADE | Audit trail tied to user |

### Check Constraints

**InventoryItem** [server/app/models/inventory_items.py L38-L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L38-L45)

:

```

```

**MaintenanceRequest** [server/app/models/maintenance_requests.py L31-L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L31-L35)

:

```

```

### Unique Constraints

| Model | Field | Purpose |
| --- | --- | --- |
| `User` | `email` | Prevent duplicate accounts |
| `InventoryItem` | `serial_number` | Unique manufacturer identifier |
| `InventoryItem` | `internal_code` | Unique SENA tracking code |

**Sources:**

* [server/app/models/inventory_items.py L38-L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L38-L45)
* [server/app/models/maintenance_requests.py L31-L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L31-L35)

---

## Schema Validation (Pydantic)

All database models have corresponding Pydantic schemas in `server/app/schemas/` for API request/response validation.

### Schema Patterns

**Create Schemas**: Accept user input for new records

* Exclude auto-generated fields (`id`, `created_at`, `updated_at`)
* May set defaults for optional fields
* Example: `InventoryItemCreate` [server/app/schemas/inventory_item.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/inventory_item.py)

**Update Schemas**: Allow partial updates

* All fields optional using `Optional[Type]`
* Use `dict(exclude_unset=True)` to apply only provided fields
* Example: `InventoryItemUpdate` [server/app/schemas/inventory_item.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/inventory_item.py)

**Response Schemas**: Return full record data

* Include all fields from database model
* Set `from_attributes = True` (formerly `orm_mode`) for SQLAlchemy compatibility
* Example: `InventoryItemResponse` [server/app/schemas/inventory_item.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/inventory_item.py)

### Specialized Schemas

**InventoryItemVerificationUpdate** [server/app/schemas/inventory_item.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/inventory_item.py)

:

* Minimal schema for verification workflow
* Only includes: `quantity`, `quantity_damaged`, `quantity_missing`, `status`
* Used by endpoint [server/app/routers/inventory.py L105-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L105-L138)

**Sources:**

* Schema usage examples in [server/app/routers/inventory.py L8](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L8-L8)
* Update pattern in [server/app/routers/inventory.py L89-L91](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L89-L91)

---

## Database Migrations

Database schema changes are managed through Alembic migrations (not shown in provided files but standard for SQLAlchemy projects).

### Migration Best Practices

1. **Create migration**: `alembic revision --autogenerate -m "description"`
2. **Review generated SQL**: Always inspect auto-generated migrations
3. **Test on development**: Apply to dev database first
4. **Backup before production**: Always backup before applying to production
5. **Document breaking changes**: Note any changes requiring data migration

### Common Migration Scenarios

* **Adding optional field**: No data migration needed
* **Adding required field**: Must provide default or migrate existing data
* **Changing enum values**: May require update statements for existing records
* **Renaming fields**: Use `op.alter_column()` to preserve data
* **Adding foreign keys**: Ensure referential integrity before applying

---

## Model Usage Examples

### Environment-Scoped Queries

```

```

**Sources:** [server/app/routers/inventory.py L23-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L23-L36)

### Status Auto-Update Pattern

```

```

**Sources:** [server/app/routers/inventory.py L93-L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L99)

### Creating Notifications on Workflow Changes

```

```

**Sources:** [server/app/routers/maintenance_requests.py L81-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L105)