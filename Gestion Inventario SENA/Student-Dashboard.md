# Student Dashboard

> **Relevant source files**
> * [client/lib/core/services/navigation_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart)
> * [client/lib/presentation/screens/dashboard/instructor_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart)
> * [client/lib/presentation/screens/dashboard/student_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart)
> * [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart)

## Purpose and Scope

This document describes the Student Dashboard (`StudentDashboard` widget), which serves as the primary interface for users with the `student` role. The dashboard provides students with a personalized view of their assigned environment's inventory, quick access to key operational features, and recent notifications. For information about other role-specific dashboards, see [Instructor Dashboard](/axchisan/GestionInventarioSENA/4.2-instructor-dashboard), [Supervisor Dashboard](/axchisan/GestionInventarioSENA/4.3-supervisor-dashboard), and [Admin Dashboards](/axchisan/GestionInventarioSENA/4.4-admin-dashboards). For details on the underlying authentication and role-based routing, see [Role-Based Access Control](/axchisan/GestionInventarioSENA/3.3-role-based-access-control).

---

## Overview

The Student Dashboard is implemented in [client/lib/presentation/screens/dashboard/student_dashboard.dart L12-L774](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L12-L774)

 as a stateful Flutter widget. It is the default landing screen for authenticated users with the `student` role and is accessed via the `/student-dashboard` route defined in [client/lib/core/services/navigation_service.dart L127-L129](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L127-L129)

The dashboard displays:

* A welcome card showing the user's name and assigned environment
* Four statistical metric cards summarizing inventory status
* Seven action cards providing navigation to key features
* A recent notifications panel showing the three most recent notifications
* A navigation drawer for accessing all available routes

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L1-L774](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L1-L774)

 [client/lib/core/services/navigation_service.dart L127-L129](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L127-L129)

---

## Component Architecture

The following diagram illustrates the widget hierarchy and key components of the Student Dashboard:

```

```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L12-L118](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L12-L118)

---

## Data Loading and State Management

### Initialization Sequence

Upon screen initialization, the dashboard performs the following data loading sequence:

```

```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L28-L84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L28-L84)

### Key Data Loading Methods

| Method | Purpose | API Endpoint | Line Reference |
| --- | --- | --- | --- |
| `_fetchData()` | Orchestrates all data loading | Multiple | [37-84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/37-84) |
| `_fetchEnvironment()` | Loads environment details | `GET /api/environments/{id}` | Implicit via `getSingle()` |
| `_fetchInventory()` | Loads environment items | `GET /api/inventory?environment_id={id}` | [55-58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/55-58) |
| `_fetchNotifications()` | Loads recent notifications | Via `NotificationService` | [60-61](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/60-61) |

The environment ID is retrieved from `authProvider.currentUser.environmentId` at [38-50](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/38-50)

 If no environment is linked, the dashboard displays a prompt to scan a QR code at [44-48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/44-48)

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L37-L84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L37-L84)

---

## Statistics Calculation

The dashboard displays four key inventory metrics calculated from the loaded items list:

```

```

### Calculation Logic

Each calculation method uses `fold()` to aggregate values across all items:

* **Total Items** [86-88](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/86-88) : Sums the `quantity` field of all items
* **Damaged Items** [90-95](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/90-95) : Sums the `quantity_damaged` field
* **Missing Items** [97-102](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/97-102) : Sums the `quantity_missing` field
* **Available Items** [104-112](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/104-112) : For each item, computes `quantity - quantity_damaged - quantity_missing`, then sums the results

The calculated statistics are stored in `_inventoryStats` map at [66-71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/66-71)

 with keys: `'total'`, `'available'`, `'damaged'`, `'missing'`.

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L86-L112](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L86-L112)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L66-L71](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L66-L71)

---

## UI Components

### Welcome Card

Displays at the top of the dashboard [148-199](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/148-199)

 showing:

* SENA logo in a circular container
* Welcome message with user's first name from `authProvider.currentUser.firstName`
* Environment name and location from `_environment['name']` and `_environment['location']`
* Fallback message "Escanea un QR para vincular un ambiente" if no environment is linked

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L148-L199](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L148-L199)

### Statistics Cards

Four metric cards arranged in a 2x2 grid [210-252](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/210-252)

 built using the `_buildStatCard()` helper method [556-586](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/556-586)

 Each card displays:

* An icon representing the metric type
* The numeric value in large, bold text
* A descriptive label below the value

The cards use color coding:

* **Total Items**: `AppColors.primary`
* **Available**: `AppColors.success`
* **Damaged**: `AppColors.warning`
* **Missing**: `AppColors.error`

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L210-L252](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L210-L252)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L556-L586](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L556-L586)

### Action Cards Grid

Seven action cards in a 2-column grid [268-335](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/268-335)

 each navigating to a different route:

| Card Title | Route | Extra Parameters | Line Reference |
| --- | --- | --- | --- |
| Escanear QR | `/qr-scan` | None | [276-283](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/276-283) |
| Verificar Inventario | `/inventory-check` | None | [284-291](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/284-291) |
| Solicitar Mantenimiento | `/maintenance-request` | `environmentId` | [292-300](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/292-300) |
| Ambiente | `/environment-overview` or `/qr-scan` | `environmentId`, `environmentName` | [301-316](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/301-316) |
| Notificaciones | `/notifications` | None | [317](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/317) |
| Feedback | `/feedback` | None | [318-325](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/318-325) |
| Configuración | `/profile` | None | [326-333](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/326-333) |

The Notifications card is built with a special `_buildNotificationActionCard()` method [411-473](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/411-473)

 that displays an unread count badge.

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L268-L335](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L268-L335)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L411-L473](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L411-L473)

### Notifications Panel

A card displaying the three most recent notifications [337-402](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/337-402)

 For each notification, it shows:

* A colored circle indicator based on notification type
* Title and message text
* Bold styling for unread notifications
* An unread indicator dot for unread items

Notification types are color-coded by `_getNotificationColor()` [475-491](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/475-491)

:

| Type | Color |
| --- | --- |
| `verification_pending` | `AppColors.warning` |
| `verification_update` | `AppColors.primary` |
| `maintenance_update` | `AppColors.info` |
| `loan_approved` | `AppColors.success` |
| `loan_rejected`, `loan_overdue` | `AppColors.error` |
| Default | `AppColors.info` |

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L337-L402](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L337-L402)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L475-L491](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L475-L491)

### Navigation Drawer

A side drawer [629-773](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/629-773)

 providing access to all student routes:

* Dashboard (home)
* Escanear QR
* Verificar Inventario
* Solicitar Mantenimiento
* Ambiente
* Notificaciones (with unread badge)
* Feedback
* Configuración
* Cerrar Sesión (logout)

The drawer header displays the SENA logo, "Panel de Aprendiz" title, and the user's email.

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L629-L773](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L629-L773)

---

## Available Student Actions

The following diagram maps the student dashboard's action cards to their corresponding routes and backend endpoints:

```

```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L268-L335](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L268-L335)

 [client/lib/core/services/navigation_service.dart L58-L217](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L58-L217)

---

## Refresh Functionality

The dashboard implements pull-to-refresh using Flutter's `RefreshIndicator` widget [141-142](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/141-142)

 When the user pulls down on the screen, it calls `_fetchData()` again, reloading all data from the API.

The loading state is managed by the `_isLoading` boolean flag [23](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/23)

 which displays a `CircularProgressIndicator` [139-140](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/139-140)

 while data is being fetched.

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L139-L142](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L139-L142)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L23](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L23-L23)

---

## Error Handling

The dashboard implements error handling in the `_fetchData()` method [37-84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/37-84)

:

1. **No Environment Linked**: If `user.environmentId` is null, displays a SnackBar at [44-48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/44-48)  prompting the user to scan a QR code
2. **API Errors**: Caught in the try-catch block at [76-83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/76-83)  displays an error message via SnackBar
3. **Loading State**: Always sets `_isLoading = false` in the finally block to ensure the loading indicator disappears

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L37-L84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L37-L84)

---

## Integration with Other Systems

The Student Dashboard integrates with multiple system components:

| Component | Integration Point | Purpose |
| --- | --- | --- |
| `AuthProvider` | [32](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/32) <br>  [38](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/38) <br>  [122](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/122) | Retrieves current user information and environment ID |
| `ApiService` | [31-32](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/31-32) | Makes HTTP requests to backend endpoints |
| `NotificationService` | [60-61](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/60-61) | Fetches notifications and unread count |
| `SenaAppBar` | [126-137](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/126-137) | Provides consistent app bar with notifications badge |
| `NavigationService` | Via `context.push()` / `context.go()` | Handles route navigation |

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L1-L774](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L1-L774)

---

## Access Control

The Student Dashboard is protected by role-based access control. The route is only accessible to users with the `student` role, enforced by the `NavigationService` redirect logic in [client/lib/core/services/navigation_service.dart L42-L57](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L42-L57)

If an authenticated user without the `student` role attempts to access `/student-dashboard`, they are redirected to their role's default route via `RoleNavigationService.getDefaultRoute()`. For more details on role-based routing, see [Role-Based Access Control](/axchisan/GestionInventarioSENA/3.3-role-based-access-control).

**Sources:** [client/lib/core/services/navigation_service.dart L42-L57](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L42-L57)