# Maintenance Request System

> **Relevant source files**
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/models/inventory_items.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py)
> * [server/app/models/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

## Purpose and Scope

The Maintenance Request System enables users to report damaged equipment, request repairs, and track maintenance work through a structured workflow. This system supports both item-specific maintenance (for individual equipment) and environment-general maintenance (for facility issues like plumbing or electrical problems).

This document covers the maintenance request creation, assignment, status tracking, and notification mechanisms. For information about inventory item management, see [Inventory Management](/axchisan/GestionInventarioSENA/6-inventory-management). For the notification system that propagates maintenance updates, see [Notification System](/axchisan/GestionInventarioSENA/11-notification-system).

**Sources:** [server/app/routers/maintenance_requests.py L1-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L1-L173)

 [server/app/models/maintenance_requests.py L1-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L1-L36)

---

## System Overview

The maintenance request system operates as a ticketing system where users submit requests that are reviewed and assigned by supervisors. The system automatically notifies relevant personnel at key workflow stages.

### Key Features

| Feature | Description |
| --- | --- |
| **Dual Request Types** | Supports both item-specific and environment-general requests |
| **Priority Levels** | Four priority levels: low, medium, high, urgent |
| **Status Workflow** | Five-stage workflow from pending to completion |
| **Role-Based Access** | Different permissions for students, instructors, supervisors, and admins |
| **Automatic Notifications** | Event-driven notifications for request creation and status changes |
| **Cost Tracking** | Optional cost field for completed maintenance work |
| **Image Attachments** | Support for multiple image URLs to document issues |

**Sources:** [server/app/models/maintenance_requests.py L8-L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L8-L35)

 [server/app/routers/maintenance_requests.py L14-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L14-L173)

---

## Data Model

### MaintenanceRequest Entity

```

```

**Sources:** [server/app/models/maintenance_requests.py L8-L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L8-L35)

### Field Constraints

The `MaintenanceRequest` model enforces the following constraints:

```

```

The system supports both English and Spanish priority values for internationalization. At least one of `item_id` or `environment_id` must be provided, allowing for both equipment-specific and facility-wide maintenance requests.

**Sources:** [server/app/models/maintenance_requests.py L31-L35](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L31-L35)

---

## API Endpoints

### Maintenance Requests Router

The maintenance requests router is mounted at `/api/maintenance-requests` and provides full CRUD operations with role-based access control.

```

```

**Sources:** [server/app/routers/maintenance_requests.py L16-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L16-L51)

### Create Maintenance Request

```

```

The notification system sends alerts to all supervisors who either:

* Have `environment_id` matching the request's `environment_id`
* Have `environment_id = NULL` (general supervisors)

This ensures that both environment-specific supervisors and system-wide supervisors are notified.

**Sources:** [server/app/routers/maintenance_requests.py L53-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L107)

### Update Maintenance Request

```

```

**Status change notification messages:**

* `in_progress`: "Su solicitud de mantenimiento está siendo procesada"
* `completed`: "Su solicitud de mantenimiento ha sido completada"
* `cancelled`: "Su solicitud de mantenimiento ha sido cancelada"
* `on_hold`: "Su solicitud de mantenimiento está en espera"

**Sources:** [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)

### Delete Maintenance Request

```

```

**Sources:** [server/app/routers/maintenance_requests.py L157-L172](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L157-L172)

---

## Status Workflow

### State Machine

The maintenance request follows a five-state workflow with defined transitions:

```

```

**Sources:** [server/app/models/maintenance_requests.py L19](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L19-L19)

 [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)

### Priority Levels

| Priority | Use Case | Notification Priority |
| --- | --- | --- |
| **urgent** (urgente) | Safety hazards, critical equipment failure | `high` |
| **high** (alta) | Equipment needed soon, major functionality issues | `medium` |
| **medium** (media) | Standard maintenance requests | `medium` |
| **low** (baja) | Minor issues, cosmetic repairs | `medium` |

When a request with `priority = "urgent"` is created, the supervisor notification is created with `priority = "high"`. All other requests generate `medium` priority notifications.

**Sources:** [server/app/routers/maintenance_requests.py L99-L102](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L99-L102)

---

## Role-Based Access Control

### Access Matrix

| Role | View Requests | Create Requests | Update Requests | Delete Requests | Assign Technicians |
| --- | --- | --- | --- | --- | --- |
| **student** | Own environment | ✓ | ✗ | ✗ | ✗ |
| **instructor** | Own environment | ✓ | ✗ | ✗ | ✗ |
| **supervisor** | Own or all environments | ✓ | ✓ | ✓ | ✓ |
| **admin** | All environments | ✓ | ✓ | ✓ | ✓ |
| **admin_general** | System-wide (all) | ✓ | ✓ | ✓ | ✓ |

**Access Rules:**

1. **admin_general** with `system_wide=True` or `admin_access=True`: Can view all maintenance requests across all environments
2. **admin/supervisor**: Can view all requests, optionally filtered by `environment_id`. If user has an `environment_id`, defaults to that environment
3. **instructor/student**: Can only view requests from their assigned `environment_id`. If no environment is assigned, request returns 403 Forbidden
4. Only **admin** and **supervisor** roles can update or delete requests

**Sources:** [server/app/routers/maintenance_requests.py L16-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L16-L51)

 [server/app/routers/maintenance_requests.py L109-L117](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L117)

 [server/app/routers/maintenance_requests.py L157-L164](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L157-L164)

---

## Notification Integration

### Notification Trigger Points

```

```

**Sources:** [server/app/routers/maintenance_requests.py L81-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L105)

 [server/app/routers/maintenance_requests.py L133-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L133-L153)

### Notification Model Integration

The system creates `Notification` records that reference the `notifications` table:

```

```

These notifications are consumed by the notification router at `/api/notifications` and displayed in user interfaces.

**Sources:** [server/app/routers/maintenance_requests.py L95-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L95-L103)

 [server/app/routers/maintenance_requests.py L144-L152](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L144-L152)

 [server/app/routers/notifications.py L1-L71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L1-L71)

---

## Query Filtering

### Available Query Parameters

The `GET /api/maintenance-requests/` endpoint supports the following query parameters:

| Parameter | Type | Description |
| --- | --- | --- |
| `item_id` | UUID | Filter by specific inventory item |
| `status` | String | Filter by request status (pending, assigned, etc.) |
| `environment_id` | UUID | Filter by specific environment (overrides user's default) |
| `system_wide` | Boolean | For admin_general: view all requests across environments |
| `admin_access` | Boolean | For admin_general: view all requests (alias for system_wide) |

**Filter Application Logic:**

```

```

**Sources:** [server/app/routers/maintenance_requests.py L16-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L16-L51)

---

## Schema Definitions

### MaintenanceRequestCreate

Input schema for creating new requests:

```

```

**Sources:** [server/app/schemas/maintenance_request.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/maintenance_request.py)

 (referenced by import in [server/app/routers/maintenance_requests.py L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L10-L10)

)

### MaintenanceRequestResponse

Output schema for API responses:

```

```

**Sources:** [server/app/schemas/maintenance_request.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/maintenance_request.py)

 (referenced by import in [server/app/routers/maintenance_requests.py L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L10-L10)

)

### MaintenanceRequestUpdate

Partial update schema (all fields optional):

```

```

**Sources:** [server/app/schemas/maintenance_request.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/maintenance_request.py)

 (referenced by import in [server/app/routers/maintenance_requests.py L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L10-L10)

)

---

## Integration with Inventory System

### Item-Linked Maintenance Requests

When a maintenance request is linked to an inventory item via `item_id`, the system validates that the item exists:

```

```

This creates a relationship between damaged/malfunctioning equipment and the maintenance work required. The `InventoryItem` status may be set to `"maintenance"` or `"damaged"` to reflect the item's unavailability during repair.

**Sources:** [server/app/routers/maintenance_requests.py L59-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L59-L62)

 [server/app/models/inventory_items.py L20](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/inventory_items.py#L20-L20)

### Environment-General Maintenance

For facility-wide issues (HVAC, electrical, plumbing), requests can omit `item_id` and only specify `environment_id`:

```

```

This allows tracking maintenance work that isn't tied to specific inventory items, such as:

* Building repairs
* Infrastructure issues
* General cleaning/maintenance
* Environmental concerns

**Sources:** [server/app/models/maintenance_requests.py L34](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L34-L34)

---

## Cost and Completion Tracking

### Completion Fields

When a maintenance request reaches `status = "completed"`, the following fields can be populated:

| Field | Type | Purpose |
| --- | --- | --- |
| `actual_completion` | Date | When work was actually finished |
| `cost` | Numeric(10,2) | Total cost of parts and labor |
| `notes` | Text | Additional completion details or follow-up needed |

The `estimated_completion` field can be set earlier in the workflow (during `assigned` or `in_progress` states) to track expected completion dates.

**Sources:** [server/app/models/maintenance_requests.py L22-L25](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L22-L25)

### Quantity Tracking

The `quantity_affected` field (default: 1) tracks how many units of an item require maintenance. This is useful for:

* Group items where multiple units are damaged
* Bulk repairs of similar equipment
* Reporting on scale of maintenance work

**Sources:** [server/app/models/maintenance_requests.py L27](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L27-L27)

---

## Summary

The Maintenance Request System provides a complete workflow for managing equipment repairs and facility maintenance:

1. **Flexible Request Types**: Supports both item-specific and environment-general maintenance
2. **Automated Notifications**: Supervisors are notified of new requests; requesters are notified of status changes
3. **Role-Based Access**: Different visibility and management permissions based on user role
4. **Status Workflow**: Five-state workflow from pending to completion or cancellation
5. **Priority Management**: Four priority levels with notification escalation for urgent requests
6. **Cost Tracking**: Optional fields for tracking completion dates and maintenance costs

The system integrates tightly with the inventory management system (for item-linked requests), notification system (for event alerts), and environment system (for location-based scoping).

**Sources:** [server/app/routers/maintenance_requests.py L1-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L1-L173)

 [server/app/models/maintenance_requests.py L1-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/models/maintenance_requests.py#L1-L36)