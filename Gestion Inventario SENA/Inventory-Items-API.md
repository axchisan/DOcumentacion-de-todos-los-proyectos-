# Inventory Items API

> **Relevant source files**
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

## Purpose and Scope

This page documents the backend REST API for inventory item management, implemented in the `inventory` router. The API provides endpoints for creating, reading, updating, and deleting inventory items, with role-based access control and environment-scoped filtering. All endpoints enforce authentication and apply business rules for automatic status calculation based on item quantities.

For information about the client-side inventory management UI, see [Edit Inventory Screen](/axchisan/GestionInventarioSENA/6.3-edit-inventory-screen). For the database model structure, see [Inventory Data Model](/axchisan/GestionInventarioSENA/6.2-inventory-data-model). For inventory verification workflows that use these APIs, see [Inventory Check API](/axchisan/GestionInventarioSENA/5.3-inventory-check-api).

**Sources:** [server/app/routers/inventory.py L1-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L1-L159)

---

## Router Configuration

The inventory router is registered with the FastAPI application using the `APIRouter` class with the `"inventory"` tag for API documentation grouping. All endpoints require authentication via the `get_current_user` dependency.

```yaml
Router Tag: "inventory"
Base Path: /api/inventory (configured in main.py)
Authentication: Required on all endpoints
Database: PostgreSQL via SQLAlchemy ORM
```

**Sources:** [server/app/routers/inventory.py L12](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L12-L12)

---

## Authentication and Authorization Model

### Role-Based Access Matrix

The inventory API implements hierarchical role-based access control with the following permission structure:

| Endpoint | student | instructor | supervisor | admin | admin_general |
| --- | --- | --- | --- | --- | --- |
| `GET /` | ✓ (env-scoped) | ✓ (env-scoped) | ✓ (env-scoped) | ✓ (env-scoped) | ✓ (system-wide) |
| `GET /{item_id}` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `POST /` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `PUT /{item_id}` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `PUT /{item_id}/verification` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `DELETE /{item_id}` | ✗ | ✗ | ✓ | ✓ | ✓ |

**Sources:** [server/app/routers/inventory.py L21-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L21-L158)

---

## Environment Filtering Logic

```

```

**Filtering Behavior:**

1. **Admin General with System-Wide Access**: When `current_user.role == "admin_general"` and either `system_wide=True` or `admin_access=True`, the query returns items from all environments without filtering.
2. **Explicit Environment Parameter**: If `environment_id` query parameter is provided, items are filtered to that specific environment regardless of user's assigned environment.
3. **User's Assigned Environment**: If no `environment_id` is specified, items are filtered by `current_user.environment_id`. If the user has no assigned environment and is not admin_general, a 400 error is raised.
4. **Lost Items Exclusion**: All queries filter out items with `status="lost"` using `InventoryItem.status != "lost"`.

**Sources:** [server/app/routers/inventory.py L14-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L48)

---

## Endpoint Documentation

### GET / - List Inventory Items

Retrieves a list of inventory items with optional search and environment filtering.

#### Request Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `search` | string | No | Search term for item name, internal_code, or category (case-insensitive ILIKE) |
| `environment_id` | UUID | No | Filter items by specific environment |
| `system_wide` | boolean | No | If true and user is admin_general, return all items |
| `admin_access` | boolean | No | If true and user is admin_general, return all items |

#### Response Schema

Returns `List[InventoryItemResponse]` with the following structure per item:

```

```

#### Error Responses

* **400**: User has no assigned environment and is not admin_general
* **404**: No items found (only for non-admin_general users)

**Sources:** [server/app/routers/inventory.py L14-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L48)

---

### GET /{item_id} - Get Single Item

Retrieves a specific inventory item by UUID. Does not enforce environment-based filtering, allowing users to view items across environments if they have the ID.

#### Path Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `item_id` | UUID | Yes | Unique identifier of the inventory item |

#### Response

Returns single `InventoryItemResponse` object or 404 error if not found or status is "lost".

**Sources:** [server/app/routers/inventory.py L50-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L50-L58)

---

### POST / - Create Inventory Item

Creates a new inventory item. Restricted to supervisor, admin, and admin_general roles.

#### Request Body

Accepts `InventoryItemCreate` schema with all item fields. The request must include:

```

```

#### Response

Returns the created `InventoryItemResponse` with HTTP 200 status code. The `id`, `created_at`, and `updated_at` fields are auto-generated.

#### Error Responses

* **403**: User role not authorized (only supervisor, admin, admin_general allowed)

**Sources:** [server/app/routers/inventory.py L60-L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L60-L73)

---

### PUT /{item_id} - Update Inventory Item

Updates an existing inventory item with full field update capability. Restricted to supervisor, admin, and admin_general roles.

#### Path Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `item_id` | UUID | Yes | Unique identifier of the inventory item to update |

#### Request Body

Accepts `InventoryItemUpdate` schema with partial updates (all fields optional):

```

```

#### Automatic Status Calculation

When `quantity_damaged` or `quantity_missing` are updated, the item status is automatically recalculated using the following logic:

```

```

#### Error Responses

* **403**: User role not authorized
* **404**: Item not found

**Sources:** [server/app/routers/inventory.py L75-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L75-L103)

---

### PUT /{item_id}/verification - Update During Verification

Special endpoint for updating inventory items during verification workflows. This endpoint is accessible to all authenticated roles (student, instructor, supervisor, admin, admin_general) and requires only minimal fields for quantity updates.

#### Path Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `item_id` | UUID | Yes | Unique identifier of the inventory item to update |

#### Request Body

Accepts `InventoryItemVerificationUpdate` schema with minimal fields:

```

```

#### Verification Update Logic

```

```

The automatic status calculation (identical to standard PUT endpoint) is applied after all field updates:

* If `quantity_missing > 0`, status is set to `'missing'`
* Else if `quantity_damaged > 0`, status is set to `'damaged'`
* Else if both are 0, status is set to `'available'`

This overrides any explicitly provided status value to ensure consistency.

#### Error Responses

* **403**: User not authenticated (unlikely given the permissive role check)
* **404**: Item not found

**Use Case**: This endpoint is designed specifically for the inventory verification workflow where students scan items and update quantities. The permissive role access allows all participants in the verification chain (student → instructor → supervisor) to update item data.

**Sources:** [server/app/routers/inventory.py L105-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L105-L138)

---

### DELETE /{item_id} - Delete Inventory Item

Permanently deletes an inventory item from the database. Restricted to supervisor, admin, and admin_general roles.

#### Path Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `item_id` | UUID | Yes | Unique identifier of the inventory item to delete |

#### Response

Returns success message:

```

```

#### Error Responses

* **403**: User role not authorized
* **404**: Item not found

**Note**: This is a hard delete operation. There is no soft delete mechanism using status flags.

**Sources:** [server/app/routers/inventory.py L140-L158](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L140-L158)

---

## Request Flow Architecture

```

```

**Dependency Injection Flow:**

1. **Database Session**: `db: Session = Depends(get_db)` provides SQLAlchemy session
2. **Current User**: `current_user: User = Depends(get_current_user)` validates JWT and loads user entity
3. **Request Body**: Pydantic schemas automatically validate and parse request JSON

**Sources:** [server/app/routers/inventory.py L1-L11](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L1-L11)

 [server/app/routers/inventory.py L14-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L159)

---

## Status Management State Machine

The inventory item status field follows a defined state machine with automatic transitions based on quantity values:

```

```

**Status Values:**

| Status | Trigger Condition | Description |
| --- | --- | --- |
| `available` | `quantity_damaged == 0 AND quantity_missing == 0` | Item is in good condition and available for use |
| `damaged` | `quantity_damaged > 0 AND quantity_missing == 0` | Some units are damaged but accounted for |
| `missing` | `quantity_missing > 0` | Some units are missing (takes precedence over damaged) |
| `in_maintenance` | Manual or via maintenance request | Item is undergoing repair |
| `lost` | Manual | Item is permanently lost (excluded from queries) |

**Automatic Calculation:** The status calculation logic is embedded in two endpoints:

* Standard `PUT /{item_id}` update: [server/app/routers/inventory.py L93-L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L99)
* Verification `PUT /{item_id}/verification` update: [server/app/routers/inventory.py L129-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L129-L134)

**Sources:** [server/app/routers/inventory.py L93-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L93-L103)

 [server/app/routers/inventory.py L129-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L129-L138)

---

## Integration with Other Systems

### Inventory Check System

The inventory items API is tightly integrated with the inventory verification workflow:

1. Students use `GET /` to retrieve items for their environment
2. During verification, they update quantities using `PUT /{item_id}/verification`
3. The verification endpoint applies status calculation automatically
4. Inventory check records reference item IDs via `inventory_check_items` join table

**See:** [Inventory Check API](/axchisan/GestionInventarioSENA/5.3-inventory-check-api) for verification workflow details

### Maintenance Request System

Maintenance requests reference inventory items:

1. When creating a maintenance request, `item_id` references an inventory item
2. The maintenance router validates the item exists: [server/app/routers/maintenance_requests.py L59-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L59-L62)
3. Items can have their status manually set to `in_maintenance` when a request is active
4. Upon completion, status should be updated based on repair results

**See:** [Maintenance Request API](/axchisan/GestionInventarioSENA/7.1-maintenance-request-api) for maintenance workflow details

### QR Code System

Inventory items can be identified via QR code scanning:

1. QR codes contain item UUID payload
2. Client scans QR code and extracts `item_id`
3. Client calls `GET /{item_id}` to retrieve full item details
4. Used for quick item lookup during verification or maintenance reporting

**Sources:** [server/app/routers/inventory.py L50-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L50-L58)

---

## Search Implementation

The search functionality uses case-insensitive pattern matching across three fields:

```

```

**Search Behavior:**

* **Operator**: `ILIKE` (case-insensitive LIKE in PostgreSQL)
* **Pattern**: Wildcard prefix and suffix (`%term%`)
* **Fields Searched**: `name`, `internal_code`, `category`
* **Logic**: OR operator (match in any field)

**Example**: Search term "comp" matches:

* name: "Computer Mouse"
* internal_code: "COMP-001"
* category: "Computing Equipment"

**Sources:** [server/app/routers/inventory.py L38-L44](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L38-L44)

---

## Database Schema References

### InventoryItem Model

The API operates on the `InventoryItem` SQLAlchemy model defined in the models package. Key fields:

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | UUID | Primary Key | Unique identifier |
| `environment_id` | UUID | Foreign Key | References `environments.id` |
| `name` | String | Not Null | Item name |
| `internal_code` | String | Unique | Internal tracking code |
| `category` | String | Not Null | Item category |
| `status` | String | Not Null | Current status (available, damaged, missing, in_maintenance, lost) |
| `quantity` | Integer | Default: 0 | Total quantity |
| `quantity_damaged` | Integer | Default: 0 | Number of damaged units |
| `quantity_missing` | Integer | Default: 0 | Number of missing units |
| `description` | Text | Nullable | Item description |
| `location` | String | Nullable | Physical location |
| `image_url` | String | Nullable | URL to item image |
| `created_at` | DateTime | Auto | Creation timestamp |
| `updated_at` | DateTime | Auto | Last update timestamp |

**Sources:** [server/app/routers/inventory.py L7](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L7-L7)

---

## Error Handling Patterns

### Common HTTP Status Codes

| Status Code | Condition | Endpoint |
| --- | --- | --- |
| 200 OK | Successful GET/PUT | `GET /`, `GET /{item_id}`, `PUT /{item_id}`, `PUT /{item_id}/verification` |
| 400 Bad Request | User not linked to environment | `GET /` |
| 403 Forbidden | Role not authorized for operation | `POST /`, `PUT /{item_id}`, `DELETE /{item_id}` |
| 404 Not Found | Item not found or has status "lost" | `GET /{item_id}`, `PUT /{item_id}`, `PUT /{item_id}/verification`, `DELETE /{item_id}` |
| 404 Not Found | No items found in environment | `GET /` (non-admin_general only) |

### Error Response Format

All HTTPExceptions return consistent JSON error format:

```

```

**Examples:**

* `"Ítem no encontrado"` - Item not found
* `"Rol no autorizado para agregar ítems"` - Role not authorized to add items
* `"No se ha vinculado un ambiente al usuario"` - User has no linked environment

**Sources:** [server/app/routers/inventory.py L33-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L33-L36)

 [server/app/routers/inventory.py L47](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L47-L47)

 [server/app/routers/inventory.py L57](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L57-L57)

 [server/app/routers/inventory.py L67](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L67-L67)

 [server/app/routers/inventory.py L83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L83-L83)

 [server/app/routers/inventory.py L114](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L114-L114)

 [server/app/routers/inventory.py L147-L149](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L147-L149)