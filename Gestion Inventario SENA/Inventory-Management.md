# Inventory Management

> **Relevant source files**
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/models/inventory_items.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py)
> * [server/app/models/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

## Purpose and Scope

This document describes the inventory management system, which handles the core functionality of tracking physical equipment and assets across SENA educational environments. The system provides CRUD operations for inventory items, automatic status tracking based on item condition, and environment-based organization of assets.

For information about the inventory verification workflow (students conducting physical checks of items), see [Inventory Verification System](/axchisan/GestionInventarioSENA/5-inventory-verification-system). For details about maintenance requests related to damaged items, see [Maintenance Request System](/axchisan/GestionInventarioSENA/7-maintenance-request-system).

## Overview

The inventory management system maintains a catalog of physical assets (computers, projectors, peripherals, etc.) distributed across multiple environments. Each item tracks its current status, quantities (total, damaged, missing), and relationships to its physical location. The system supports both individual items and group items that represent multiple units of the same type.

```

```

**Sources:** [server/app/routers/inventory.py L1-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L1-L159)

 [server/app/models/inventory_items.py L1-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L1-L46)

## Inventory Item Model

The `InventoryItem` model represents a single inventory entry in the system. It includes comprehensive tracking fields for identification, categorization, status, and quantities.

### Core Fields

| Field | Type | Purpose |
| --- | --- | --- |
| `id` | UUID | Primary key identifier |
| `environment_id` | UUID | Foreign key to Environment (physical location) |
| `name` | String(100) | Item name/description |
| `serial_number` | String(100) | Unique serial number (optional) |
| `internal_code` | String(50) | Required unique internal tracking code |
| `category` | String(20) | Item category (see Categories section) |
| `brand` | String(50) | Manufacturer brand |
| `model` | String(100) | Model identifier |
| `status` | String(20) | Current status (see Status Management section) |
| `quantity` | Integer | Total quantity (default: 1) |
| `quantity_damaged` | Integer | Number of damaged units (default: 0) |
| `quantity_missing` | Integer | Number of missing units (default: 0) |
| `item_type` | String(10) | 'individual' or 'group' |

### Additional Fields

| Field | Type | Purpose |
| --- | --- | --- |
| `purchase_date` | Date | Original purchase date |
| `warranty_expiry` | Date | Warranty expiration date |
| `last_maintenance` | Date | Most recent maintenance date |
| `next_maintenance` | Date | Scheduled next maintenance |
| `image_url` | String(500) | URL to item image |
| `notes` | Text | Additional notes |
| `created_at` | Timestamp | Record creation timestamp |
| `updated_at` | Timestamp | Last update timestamp |

**Sources:** [server/app/models/inventory_items.py L9-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L9-L46)

## Categories and Status Values

### Item Categories

The system supports nine predefined equipment categories enforced by database constraints:

```

```

**Sources:** [server/app/models/inventory_items.py L39](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L39-L39)

### Status Values

The `status` field can have the following values, enforced by a database check constraint:

| Status | Meaning |
| --- | --- |
| `available` | Item is available for use/loan |
| `in_use` | Item is currently being used |
| `maintenance` | Item is undergoing maintenance |
| `damaged` | Item has damage (quantity_damaged > 0) |
| `lost` | Item is permanently lost |
| `missing` | Item is temporarily missing (quantity_missing > 0) |
| `good` | Item is in good condition (verification context) |

**Sources:** [server/app/models/inventory_items.py L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L40-L40)

## Automatic Status Management

The system automatically updates item status based on quantity fields. This logic is implemented in both the standard update endpoint and the verification-specific endpoint.

```

```

This automatic status update occurs in two locations:

1. **Standard Update Endpoint** `PUT /inventory/{item_id}` - When supervisors/admins edit items [server/app/routers/inventory.py L93-L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L99)
2. **Verification Update Endpoint** `PUT /inventory/{item_id}/verification` - When users update items during inventory checks [server/app/routers/inventory.py L129-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L129-L134)

**Sources:** [server/app/routers/inventory.py L93-L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L99)

 [server/app/routers/inventory.py L129-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L129-L134)

## API Endpoints

The inventory router exposes five main endpoints for managing inventory items:

### Endpoint Summary

```

```

**Sources:** [server/app/routers/inventory.py L14-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L158)

### GET /inventory/ - List Inventory Items

Retrieves inventory items with environment-based filtering and search capabilities.

**Query Parameters:**

* `search` (string): Search term for name, internal_code, or category
* `environment_id` (UUID): Filter by specific environment
* `system_wide` (boolean): Admin_general can view all items
* `admin_access` (boolean): Alternative flag for system-wide access

**Access Control Logic:**

```

```

All queries automatically exclude items with `status = "lost"` [server/app/routers/inventory.py L23](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L23-L23)

**Sources:** [server/app/routers/inventory.py L14-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L48)

### POST /inventory/ - Create Item

Creates a new inventory item. Restricted to users with supervisor, admin, or admin_general roles.

**Request Body:** `InventoryItemCreate` schema containing all item fields
**Response:** `InventoryItemResponse` with the created item
**Role Requirement:** supervisor, admin, or admin_general [server/app/routers/inventory.py L66-L67](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L66-L67)

**Sources:** [server/app/routers/inventory.py L60-L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L60-L73)

### PUT /inventory/{item_id} - Update Item

Updates an existing inventory item with full field modification. Includes automatic status updates based on quantity changes.

**Path Parameter:** `item_id` (UUID)
**Request Body:** `InventoryItemUpdate` schema with partial updates
**Response:** `InventoryItemResponse` with updated item
**Role Requirement:** supervisor, admin, or admin_general [server/app/routers/inventory.py L82-L83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L82-L83)

The endpoint uses `exclude_unset=True` to support partial updates, only modifying fields that are explicitly provided [server/app/routers/inventory.py L89](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L89-L89)

**Sources:** [server/app/routers/inventory.py L75-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L75-L103)

### PUT /inventory/{item_id}/verification - Update During Verification

Specialized endpoint for updating inventory items during the verification process. This endpoint accepts minimal required fields (quantities and status) and is accessible to all authenticated users, not just admins.

**Path Parameter:** `item_id` (UUID)
**Request Body:** `InventoryItemVerificationUpdate` schema containing:

* `quantity` (optional int)
* `quantity_damaged` (optional int)
* `quantity_missing` (optional int)
* `status` (optional string)

**Role Requirement:** student, instructor, supervisor, admin, or admin_general [server/app/routers/inventory.py L113-L114](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L113-L114)

This endpoint is specifically designed for the inventory verification workflow where students and instructors need to update item conditions without having full administrative privileges.

**Sources:** [server/app/routers/inventory.py L105-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L105-L138)

### DELETE /inventory/{item_id} - Delete Item

Permanently deletes an inventory item from the database.

**Path Parameter:** `item_id` (UUID)
**Response:** Success status message
**Role Requirement:** supervisor, admin, or admin_general [server/app/routers/inventory.py L146-L149](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L146-L149)

**Sources:** [server/app/routers/inventory.py L140-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L140-L158)

## Environment-Based Organization

All inventory items are associated with an `Environment` entity via the `environment_id` foreign key. This provides multi-tenant organization where each environment represents a physical location (classroom, lab, warehouse, etc.).

### Environment Relationships

```

```

The `environment_id` foreign key includes `CASCADE` deletion, meaning if an environment is deleted, all its inventory items are automatically removed [server/app/models/inventory_items.py L13](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L13-L13)

**Sources:** [server/app/models/inventory_items.py L13](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L13-L13)

 [server/app/models/inventory_items.py L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L35-L35)

## Integration with Other Systems

The inventory management system serves as a foundational data layer for multiple operational workflows.

### Maintenance Requests Integration

Maintenance requests can reference specific inventory items that require repair. The `MaintenanceRequest` model includes an optional `item_id` foreign key [server/app/models/maintenance_requests.py L12](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L12-L12)

When a maintenance request is created for an item:

1. The item's existence is validated [server/app/routers/maintenance_requests.py L60-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L60-L62)
2. The request stores both `item_id` and `environment_id` for context
3. Supervisors in the same environment receive notifications [server/app/routers/maintenance_requests.py L81-L104](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L104)

The system also supports environment-level maintenance requests (not specific to an item) by making `item_id` nullable with a constraint requiring either `item_id` or `environment_id` to be present [server/app/models/maintenance_requests.py L34](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L34-L34)

**Sources:** [server/app/routers/maintenance_requests.py L59-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L59-L107)

 [server/app/models/maintenance_requests.py L12](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L12-L12)

 [server/app/models/maintenance_requests.py L34](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L34-L34)

### Inventory Verification Integration

The inventory verification system uses the specialized `/inventory/{item_id}/verification` endpoint to update item quantities and statuses during physical checks. This integration allows:

1. Students to scan items and update their current quantities
2. Automatic status transitions based on discovered damage or missing items
3. Lower privilege requirements compared to full item editing
4. Integration with the three-stage approval workflow (student → instructor → supervisor)

See [Inventory Verification System](/axchisan/GestionInventarioSENA/5-inventory-verification-system) for detailed workflow documentation.

**Sources:** [server/app/routers/inventory.py L105-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L105-L138)

### Loan System Integration

The inventory model includes a relationship to the `Loan` model [server/app/models/inventory_items.py L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L36-L36)

 though the loan functionality is defined elsewhere. Items can be borrowed by users, with the loan system tracking which items are currently in use.

**Sources:** [server/app/models/inventory_items.py L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L36-L36)

### QR Code Integration

Each inventory item can be identified via QR codes for quick scanning. The QR system generates unique codes containing the item ID, enabling rapid item lookup during verification or maintenance workflows.

See [QR Code System](/axchisan/GestionInventarioSENA/8-qr-code-system) for details on code generation and scanning.

## Data Validation and Constraints

The `InventoryItem` model enforces several database-level constraints to maintain data integrity:

### Check Constraints

```

```

All constraints are defined at the model level using SQLAlchemy's `CheckConstraint` [server/app/models/inventory_items.py L38-L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L38-L45)

### Unique Constraints

Two fields enforce uniqueness:

* `serial_number` - Globally unique when provided (nullable)
* `internal_code` - Required and globally unique

These constraints prevent duplicate items and ensure reliable identification.

**Sources:** [server/app/models/inventory_items.py L15-L16](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L15-L16)

 [server/app/models/inventory_items.py L38-L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L38-L45)

## Role-Based Access Summary

The inventory management system implements a hierarchical role-based access control model:

| Operation | student | instructor | supervisor | admin | admin_general |
| --- | --- | --- | --- | --- | --- |
| List items (own environment) | ✓ | ✓ | ✓ | ✓ | ✓ |
| List items (all environments) | ✗ | ✗ | ✗ | ✗ | ✓ |
| Get item details | ✓ | ✓ | ✓ | ✓ | ✓ |
| Create item | ✗ | ✗ | ✓ | ✓ | ✓ |
| Update item (full) | ✗ | ✗ | ✓ | ✓ | ✓ |
| Update item (verification) | ✓ | ✓ | ✓ | ✓ | ✓ |
| Delete item | ✗ | ✗ | ✓ | ✓ | ✓ |

**Key Principles:**

* All authenticated users can view items in their environment
* Only admin_general can view items across all environments (with `system_wide` flag)
* Administrative operations (create/update/delete) require supervisor-level roles or higher
* The verification endpoint provides a lower-privilege path for updating items during checks

**Sources:** [server/app/routers/inventory.py L21](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L21-L21)

 [server/app/routers/inventory.py L66-L67](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L66-L67)

 [server/app/routers/inventory.py L82-L83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L82-L83)

 [server/app/routers/inventory.py L113-L114](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L113-L114)

 [server/app/routers/inventory.py L146-L149](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L146-L149)