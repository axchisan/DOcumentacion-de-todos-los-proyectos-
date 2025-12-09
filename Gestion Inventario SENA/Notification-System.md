# Notification System

> **Relevant source files**
> * [client/lib/presentation/screens/dashboard/instructor_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart)
> * [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart)
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

## Purpose and Scope

The Notification System is an event-driven mechanism that alerts users about workflow changes, pending actions, and system updates. This document covers the notification data model, automatic notification generation triggers, backend API endpoints, frontend service integration, and dashboard display components.

For information about the workflows that generate notifications, see [Inventory Verification System](/axchisan/GestionInventarioSENA/5-inventory-verification-system) and [Maintenance Request System](/axchisan/GestionInventarioSENA/7-maintenance-request-system). For user interface dashboards that display notifications, see [Dashboard Screens](/axchisan/GestionInventarioSENA/4-dashboard-screens).

---

## System Overview

The Notification System implements a push-based alert mechanism where backend workflow events automatically create notification records that users retrieve through polling. Notifications are user-specific, role-aware, and support priority levels and read/unread states.

```

```

**Sources:** [server/app/routers/notifications.py L1-L71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L1-L71)

 [server/app/routers/maintenance_requests.py L94-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L94-L153)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L119-L131](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L119-L131)

---

## Notification Data Model

The `Notification` model stores alert records for individual users. Each notification contains type classification, priority level, and read status tracking.

| Field | Type | Description |
| --- | --- | --- |
| `id` | UUID | Primary key |
| `user_id` | UUID | Foreign key to User table |
| `type` | String | Notification category (see types below) |
| `title` | String | Short notification heading |
| `message` | String | Detailed notification content |
| `is_read` | Boolean | Read status flag |
| `priority` | String | Priority level: "low", "medium", "high" |
| `created_at` | DateTime | Timestamp of creation |
| `updated_at` | DateTime | Timestamp of last update |

**Sources:** [server/app/routers/notifications.py L7-L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L7-L10)

 [server/app/schemas/notification.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/notification.py)

---

## Notification Types and Event Triggers

The system generates notifications automatically when specific workflow events occur. Each notification type corresponds to a specific user action requirement or status update.

```

```

### Notification Type Classification

| Type | Recipient Role | Trigger Event | Priority |
| --- | --- | --- | --- |
| `verification_pending` | Instructor, Supervisor | Inventory check awaits review | Medium |
| `verification_update` | Student, Instructor | Check status changes | Medium |
| `maintenance_request` | Supervisor | New maintenance request submitted | High (if urgent), Medium |
| `maintenance_update` | Request Creator | Request status changes | Medium |
| `loan_approved` | Student, Instructor | Loan request approved | Medium |
| `loan_rejected` | Student, Instructor | Loan request rejected | Medium |
| `loan_overdue` | Borrower | Loan due date passed | High |
| `system` | All Roles | General system announcements | Low/Medium |

**Sources:** [server/app/routers/maintenance_requests.py L94-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L94-L107)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L536-L552](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L536-L552)

 [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L704-L720](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L704-L720)

---

## Backend Implementation

### Notification Router Endpoints

The notifications router provides three endpoints for notification management accessible at `/api/notifications`.

```

```

**GET `/api/notifications/`**

* Retrieves all notifications for the authenticated user
* Filters by `user_id` matching current user
* Returns list of `NotificationResponse` objects
* Implementation: [server/app/routers/notifications.py L14-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L14-L24)

**POST `/api/notifications/`**

* Creates a new notification (typically used by system processes)
* Accepts `NotificationCreate` schema
* Returns created notification with 201 status
* Implementation: [server/app/routers/notifications.py L26-L41](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L26-L41)

**PUT `/api/notifications/{notif_id}`**

* Updates notification properties (primarily `is_read` flag)
* Requires notification ownership: `user_id == current_user.id`
* Accepts `NotificationUpdate` schema with optional fields
* Returns updated notification
* Implementation: [server/app/routers/notifications.py L43-L70](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L43-L70)

**Sources:** [server/app/routers/notifications.py L1-L71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L1-L71)

### Automatic Notification Generation

Notifications are created automatically within workflow routers when state transitions occur. The pattern involves querying for relevant users and creating notification records in the same database transaction.

**Maintenance Request Creation Example:**

When a maintenance request is submitted, the system identifies all supervisors in the request's environment and creates notifications for each:

```

```

Implementation logic at [server/app/routers/maintenance_requests.py L81-L106](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L106)

:

1. Query supervisors in the relevant environment: lines 82-91
2. Create notification for each supervisor: lines 94-103
3. Set priority based on request urgency: line 101
4. Commit all changes in single transaction: line 105

**Maintenance Request Update Example:**

When maintenance status changes, the original requester receives an update notification:

Implementation at [server/app/routers/maintenance_requests.py L133-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L133-L153)

:

1. Store old status before update: line 124
2. Apply updates to maintenance request: lines 126-131
3. Compare old and new status: line 134
4. Create notification if status changed: lines 144-152
5. Use status-specific messages: lines 136-141

**Sources:** [server/app/routers/maintenance_requests.py L53-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L155)

---

## Frontend Implementation

### NotificationService Client Service

The `NotificationService` class provides static methods for fetching notifications from the backend. It acts as a wrapper around the `ApiService` HTTP client.

| Method | Return Type | Description |
| --- | --- | --- |
| `getNotifications()` | `Future<List<Map<String, dynamic>>>` | Fetches all notifications for current user |
| `getUnreadCount()` | `Future<int>` | Returns count of unread notifications |

**Usage Pattern in Dashboards:**

Implementation examples:

* Instructor Dashboard: [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L119-L131](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L119-L131)
* Supervisor Dashboard: [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L191-L198](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L191-L198)

```

```

**Sources:** [client/lib/core/services/notification_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/notification_service.dart)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L119-L131](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L119-L131)

### Dashboard Integration Components

#### NotificationBadge Widget

Displays an icon button with an overlaid badge showing unread notification count. Used in app bars across all dashboard screens.

Implementation pattern at [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L149-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L149-L155)

:

* Wraps IconButton with NotificationBadge
* Displays notification bell icon
* Shows unread count badge
* Navigates to `/notifications` route on tap

#### Recent Notifications Card

Dashboard cards display the 3 most recent notifications with visual indicators for type and read status.

```

```

Implementation details:

* Card structure: [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L397-L463](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L397-L463)
* Notification item builder: [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L554-L615](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L554-L615)
* Color mapping by type: [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L536-L552](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L536-L552)

**Color Coding by Notification Type:**

| Notification Type | Color | Purpose |
| --- | --- | --- |
| `verification_pending` | Warning (Orange) | Action required |
| `verification_update` | Secondary (Green) | Status change |
| `maintenance_update` | Info (Blue) | Informational |
| `maintenance_request` | Info (Blue) | New request alert |
| `loan_approved` | Success (Green) | Positive outcome |
| `loan_rejected` | Error (Red) | Negative outcome |
| `loan_overdue` | Error (Red) | Critical issue |
| Default | Info (Blue) | System notifications |

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L396-L463](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L396-L463)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L536-L615](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L536-L615)

#### Notification Action Card

A dedicated dashboard card provides direct navigation to the notifications screen with visual unread count display.

Implementation at [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L472-L534](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L472-L534)

:

* Displays large notification icon with badge overlay
* Shows "X sin leer" text if unread notifications exist
* Tappable card navigates to `/notifications` route
* Visually consistent with other action cards on dashboard

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L472-L534](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L472-L534)

 [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L640-L702](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L640-L702)

---

## Workflow Integration Patterns

### Verification Workflow Notifications

The inventory verification workflow generates notifications at two key transition points:

**Instructor Review Stage:**
When a student completes an inventory check, the status changes to `instructor_review`, triggering a notification to the assigned instructor. The notification is created implicitly by the workflow state machine.

**Supervisor Review Stage:**
When an instructor approves a check, the status changes to `supervisor_review`, triggering a notification to the assigned supervisor.

**Completion:**
When a supervisor completes final review, the student receives a notification confirming completion.

### Maintenance Workflow Notifications

**Request Submission:**

* Implementation: [server/app/routers/maintenance_requests.py L81-L106](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L106)
* Queries all supervisors in the request's environment
* Includes general supervisors (those with `environment_id IS NULL`)
* Creates notification with priority based on request urgency
* Message format: "Se ha creado una nueva solicitud de mantenimiento: {title}. Prioridad: {priority}"

**Status Updates:**

* Implementation: [server/app/routers/maintenance_requests.py L133-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L133-L153)
* Notifies original requester when status changes
* Provides status-specific messages: * `in_progress`: "Su solicitud de mantenimiento está siendo procesada" * `completed`: "Su solicitud de mantenimiento ha sido completada" * `cancelled`: "Su solicitud de mantenimiento ha sido cancelada" * `on_hold`: "Su solicitud de mantenimiento está en espera"

### Loan Workflow Notifications

Notifications are generated when loan requests are approved or rejected, and when active loans become overdue. The implementation follows the same pattern as maintenance notifications but is handled in the loans router.

**Sources:** [server/app/routers/maintenance_requests.py L53-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L155)

---

## User Experience Flow

```

```

### Dashboard Refresh Pattern

Notifications are fetched during dashboard initialization and can be refreshed manually:

1. Dashboard `initState()` calls `_fetchNotifications()` method
2. Method executes `NotificationService.getNotifications()` and `getUnreadCount()`
3. Results stored in state variables: `_recentNotifications` and `_unreadNotificationsCount`
4. User can pull-to-refresh to reload all data including notifications
5. Dashboard displays top 3 notifications in card with unread count badge

Implementation: [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L36-L44](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L36-L44)

### Unread Count Display

The unread notification count appears in three locations:

1. **App Bar Badge**: NotificationBadge widget overlay on notification icon
2. **Notification Card Header**: Pill-shaped badge next to "Notificaciones Recientes" heading
3. **Action Card**: Shows "X sin leer" text below notification icon

All locations are updated simultaneously when `_unreadNotificationsCount` state changes.

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L119-L131](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L119-L131)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L148-L156](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L148-L156)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L413-L431](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L413-L431)

---

## Role-Based Notification Filtering

Notifications are filtered at the database query level to ensure users only receive notifications relevant to their role and assigned environment:

### Backend Filtering

The `GET /api/notifications/` endpoint filters by `user_id` matching the authenticated user, ensuring role separation at the data access layer: [server/app/routers/notifications.py L20](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L20-L20)

### Notification Creation Targeting

When creating notifications, workflows target specific roles:

**Supervisors:** Maintenance request notifications query supervisors in the relevant environment or general supervisors: [server/app/routers/maintenance_requests.py L82-L91](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L82-L91)

**Request Creators:** Status update notifications target the `user_id` of the original requester: [server/app/routers/maintenance_requests.py L135-L152](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L135-L152)

**Instructors/Supervisors:** Verification workflow notifications target the specific `instructor_id` and `supervisor_id` assigned to an inventory check.

This targeting ensures that notification delivery aligns with role-based access control and workflow assignment rules.

**Sources:** [server/app/routers/notifications.py L14-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L14-L24)

 [server/app/routers/maintenance_requests.py L81-L106](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L81-L106)