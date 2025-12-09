# Maintenance Request API

> **Relevant source files**
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

This document describes the backend REST API for the maintenance request system, which handles creation, retrieval, updating, and deletion of maintenance requests. This API enables users to report damaged equipment or facility issues and supports supervisor workflow management with automatic notification generation.

For the data model details, see [Maintenance Data Model](/axchisan/GestionInventarioSENA/7.2-maintenance-data-model). For client-side UI implementation, see [Dashboard Screens](/axchisan/GestionInventarioSENA/4-dashboard-screens).

## Purpose and Scope

The Maintenance Request API provides endpoints for managing maintenance requests through their complete lifecycle. It implements role-based filtering, automatic supervisor notifications, and status-driven workflow transitions. The router is located at [server/app/routers/maintenance_requests.py L1-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L1-L173)

 and is registered under the `/maintenance-requests` path prefix.

Sources: [server/app/routers/maintenance_requests.py L1-L14](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L1-L14)

## API Endpoints Overview

The router exposes four primary endpoints with role-based access control:

| HTTP Method | Endpoint | Purpose | Minimum Role | Response Model |
| --- | --- | --- | --- | --- |
| GET | `/` | List maintenance requests with filtering | student | `List[MaintenanceRequestResponse]` |
| POST | `/` | Create new maintenance request | student | `MaintenanceRequestResponse` |
| PUT | `/{request_id}` | Update maintenance request (status, assignments) | supervisor | `MaintenanceRequestResponse` |
| DELETE | `/{request_id}` | Delete maintenance request | supervisor | `{"status": "success"}` |

Sources: [server/app/routers/maintenance_requests.py L14-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L14-L173)

## Request Lifecycle and Status Workflow

```

```

**Status Lifecycle:**

1. **pending** - Initial state set at creation [server/app/routers/maintenance_requests.py L75](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L75-L75)
2. **assigned** - Supervisor assigns to technician
3. **in_progress** - Work has begun
4. **on_hold** - Temporarily paused
5. **completed** - Work finished successfully
6. **cancelled** - Request cancelled

Sources: [server/app/routers/maintenance_requests.py L53-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L107)

 [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)

## Maintenance Request Creation Flow

```

```

**Key Implementation Details:**

* Item validation is optional - requests can be environment-level issues [server/app/routers/maintenance_requests.py L59-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L59-L62)
* Request status initialized to `"pending"` [server/app/routers/maintenance_requests.py L75](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L75-L75)
* Quantity affected defaults to 1 if not specified [server/app/routers/maintenance_requests.py L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L73-L73)
* Supervisors filtered by environment or all supervisors if no environment [server/app/routers/maintenance_requests.py L82-L89](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L82-L89)
* Notification priority set to `"high"` if request priority is `"urgent"` [server/app/routers/maintenance_requests.py L101](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L101-L101)

Sources: [server/app/routers/maintenance_requests.py L53-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L107)

## Request Retrieval and Filtering

```

```

**Access Control Rules:**

* `admin_general`: Can view all requests system-wide with `system_wide=true` or `admin_access=true` [server/app/routers/maintenance_requests.py L26-L28](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L26-L28)
* `admin`/`supervisor`: Can view all requests or filter by environment [server/app/routers/maintenance_requests.py L29-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L29-L36)
* `instructor`/`student`: Can only view requests from their assigned environment [server/app/routers/maintenance_requests.py L37-L41](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L37-L41)
* Users without assigned environment (except `admin_general`) receive 403 error [server/app/routers/maintenance_requests.py L39-L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L39-L40)

**Query Parameters:**

* `item_id` (UUID, optional) - Filter by specific inventory item [server/app/routers/maintenance_requests.py L45-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L45-L46)
* `status` (string, optional) - Filter by request status [server/app/routers/maintenance_requests.py L47-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L47-L48)
* `environment_id` (UUID, optional) - Filter by environment [server/app/routers/maintenance_requests.py L21](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L21-L21)
* `system_wide` (boolean, optional) - Admin general system-wide access [server/app/routers/maintenance_requests.py L22](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L22-L22)
* `admin_access` (boolean, optional) - Admin general unrestricted access [server/app/routers/maintenance_requests.py L23](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L23-L23)

Sources: [server/app/routers/maintenance_requests.py L16-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L16-L51)

## Request Update and Notification Flow

```

```

**Update Authorization:**

* Only `admin` and `supervisor` roles can update requests [server/app/routers/maintenance_requests.py L116-L117](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L116-L117)
* Original requester (`user_id`) receives notification on status change [server/app/routers/maintenance_requests.py L134-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L134-L153)

**Status Change Notifications:**
Notifications are created with predefined messages for each terminal status:

| Status | Message Template |
| --- | --- |
| `in_progress` | "Su solicitud de mantenimiento está siendo procesada" |
| `completed` | "Su solicitud de mantenimiento ha sido completada" |
| `cancelled` | "Su solicitud de mantenimiento ha sido cancelada" |
| `on_hold` | "Su solicitud de mantenimiento está en espera" |

All status change notifications have `priority="medium"` [server/app/routers/maintenance_requests.py L150](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L150-L150)

Sources: [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)

## Request Schemas

### MaintenanceRequestCreate

Input schema for creating new maintenance requests [server/app/schemas/maintenance_request.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/maintenance_request.py)

:

```

```

### MaintenanceRequestResponse

Output schema returned by all endpoints [server/app/schemas/maintenance_request.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/maintenance_request.py)

:

```

```

### MaintenanceRequestUpdate

Input schema for updating existing requests [server/app/schemas/maintenance_request.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/maintenance_request.py)

:

All fields are optional - only provided fields are updated using `exclude_unset=True` [server/app/routers/maintenance_requests.py L126](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L126-L126)

Sources: [server/app/routers/maintenance_requests.py L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L10-L10)

## Request Deletion

```

```

**Deletion Authorization:**

* Only `admin` and `supervisor` roles can delete requests [server/app/routers/maintenance_requests.py L163-L164](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L163-L164)
* No notification sent on deletion
* Cascading effects depend on database model relationships

Sources: [server/app/routers/maintenance_requests.py L157-L172](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L157-L172)

## Notification System Integration

The Maintenance Request API integrates tightly with the Notification system to create event-driven alerts:

### Supervisor Notification on Creation

When a new request is created, the system queries for relevant supervisors and creates notifications:

**Supervisor Selection Logic:**

1. Query all users with `role == "supervisor"` [server/app/routers/maintenance_requests.py L82](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L82-L82)
2. If `environment_id` provided, filter by: * Supervisors assigned to that environment, OR * Supervisors with no environment (general supervisors) [server/app/routers/maintenance_requests.py L86-L88](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L86-L88)
3. Create notification for each matching supervisor [server/app/routers/maintenance_requests.py L94-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L94-L103)

**Notification Attributes:**

* `type`: `"maintenance_request"` [server/app/routers/maintenance_requests.py L97](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L97-L97)
* `title`: `"Nueva Solicitud de Mantenimiento"` [server/app/routers/maintenance_requests.py L98](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L98-L98)
* `message`: Includes request title and priority [server/app/routers/maintenance_requests.py L99](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L99-L99)
* `priority`: `"high"` if request priority is `"urgent"`, otherwise `"medium"` [server/app/routers/maintenance_requests.py L101](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L101-L101)
* `is_read`: `False` (unread by default) [server/app/routers/maintenance_requests.py L100](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L100-L100)

### User Notification on Status Change

When a request's status changes, the original requester receives an update:

**Status Change Detection:**

* Old status stored before update [server/app/routers/maintenance_requests.py L124](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L124-L124)
* New status checked after commit [server/app/routers/maintenance_requests.py L133](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L133-L133)
* Notification created only if status changed AND user_id exists [server/app/routers/maintenance_requests.py L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L134-L134)

**Supported Status Messages:**
Only specific terminal/progress states trigger notifications [server/app/routers/maintenance_requests.py L136-L141](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L136-L141)

 Intermediate states like "assigned" do not trigger notifications.

Sources: [server/app/routers/maintenance_requests.py L81-L106](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L106)

 [server/app/routers/maintenance_requests.py L133-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L133-L153)

 [server/app/routers/notifications.py L1-L71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L1-L71)

## Error Handling

The API implements consistent error responses:

| Status Code | Condition | Detail Message |
| --- | --- | --- |
| 403 | Unauthorized role for update/delete | `"Rol no autorizado"` |
| 403 | Student/instructor without environment | `"Usuario sin ambiente asignado"` |
| 404 | Item not found (if item_id provided) | `"Ítem no encontrado"` |
| 404 | Request not found on get/update/delete | `"Solicitud no encontrada"` |

All error responses use FastAPI's `HTTPException` for consistent formatting [server/app/routers/maintenance_requests.py L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L62-L62)

 [server/app/routers/maintenance_requests.py L117](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L117-L117)

 [server/app/routers/maintenance_requests.py L121](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L121-L121)

 [server/app/routers/maintenance_requests.py L168](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L168-L168)

Sources: [server/app/routers/maintenance_requests.py L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L40-L40)

 [server/app/routers/maintenance_requests.py L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L43-L43)

 [server/app/routers/maintenance_requests.py L60-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L60-L62)

 [server/app/routers/maintenance_requests.py L116-L121](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L116-L121)

 [server/app/routers/maintenance_requests.py L163-L168](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L163-L168)

## Database Dependencies

The router depends on several database models through SQLAlchemy ORM:

| Model | Import | Usage |
| --- | --- | --- |
| `MaintenanceRequest` | [server/app/models/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py) | Primary entity for all CRUD operations [server/app/routers/maintenance_requests.py L7](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L7-L7) |
| `Notification` | [server/app/models/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/notifications.py) | Created for supervisor/user alerts [server/app/routers/maintenance_requests.py L8](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L8-L8) |
| `User` | [server/app/models/users.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/users.py) | Queried for supervisor list, accessed via `current_user` [server/app/routers/maintenance_requests.py L9](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L9-L9) |
| `InventoryItem` | [server/app/models/inventory_items.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py) | Validated when `item_id` provided [server/app/routers/maintenance_requests.py L12](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L12-L12) |

**Session Management:**

* Database session obtained via `Depends(get_db)` dependency injection [server/app/routers/maintenance_requests.py L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L18-L18)
* Explicit commit after mutations: `db.commit()` [server/app/routers/maintenance_requests.py L78](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L78-L78)
* Refresh entity after commit to load generated fields: `db.refresh(new_request)` [server/app/routers/maintenance_requests.py L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L79-L79)

Sources: [server/app/routers/maintenance_requests.py L1-L12](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L1-L12)

 [server/app/routers/maintenance_requests.py L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L18-L18)

## Authentication Requirements

All endpoints require authentication via JWT token:

* Authentication handled by `Depends(get_current_user)` dependency [server/app/routers/maintenance_requests.py L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L24-L24)
* Returns `User` object with `id`, `role`, `environment_id` fields [server/app/routers/auth.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py)
* Token must be valid and not expired
* User must exist in database

For authentication implementation details, see [Backend Authentication API](/axchisan/GestionInventarioSENA/3.2-backend-authentication-api).

Sources: [server/app/routers/maintenance_requests.py L11](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L11-L11)

 [server/app/routers/maintenance_requests.py L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L24-L24)