# Dashboard Screens

> **Relevant source files**
> * [client/lib/core/services/navigation_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart)
> * [client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart)
> * [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart)
> * [client/lib/presentation/screens/dashboard/instructor_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart)
> * [client/lib/presentation/screens/dashboard/student_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart)
> * [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart)

## Purpose and Scope

This document describes the role-specific dashboard screens that serve as the main entry point for authenticated users. Each dashboard displays relevant metrics, recent activity, and provides quick navigation to key features based on the user's role and permissions.

For authentication and role assignment, see [Authentication & Authorization](/axchisan/GestionInventarioSENA/3-authentication-and-authorization). For details on individual role capabilities, see [Role-Based Access Control](/axchisan/GestionInventarioSENA/3.3-role-based-access-control). For statistics and analytics features, see [Statistics Dashboard](/axchisan/GestionInventarioSENA/9.3-statistics-dashboard).

## Dashboard Architecture Overview

The system implements five distinct dashboard screens, each tailored to a specific user role:

| Role | Dashboard Screen | Route | Primary Focus |
| --- | --- | --- | --- |
| Student | `StudentDashboard` | `/student-dashboard` | Inventory verification and maintenance requests |
| Instructor | `InstructorDashboard` | `/instructor-dashboard` | Environment management and loan oversight |
| Supervisor | `SupervisorDashboardScreen` | `/supervisor-dashboard` | Verification review and maintenance monitoring |
| Admin | `AdminDashboardScreen` | `/admin-dashboard` | Warehouse inventory and loan management |
| Admin General | `GeneralAdminDashboardScreen` | `/admin-general-dashboard` | System-wide metrics and user management |

### Dashboard Routing Flow

```mermaid
flowchart TD

SplashScreen["SplashScreen"]
AuthProvider["AuthProvider.checkSession()"]
RoleCheck["RoleNavigationService.getDefaultRoute()"]
Student["StudentDashboard<br>/student-dashboard"]
Instructor["InstructorDashboard<br>/instructor-dashboard"]
Supervisor["SupervisorDashboardScreen<br>/supervisor-dashboard"]
Admin["AdminDashboardScreen<br>/admin-dashboard"]
AdminGeneral["GeneralAdminDashboardScreen<br>/admin-general-dashboard"]
QRScan["QR Scan<br>Inventory Check<br>Maintenance Request"]
EnvManage["Environment Management<br>Loan Oversight<br>Verification"]
Review["Verification Review<br>Maintenance Monitoring<br>Reports"]
Warehouse["Warehouse Operations<br>Loan Management<br>Statistics"]
System["User Management<br>System Configuration<br>Audit Logs"]

SplashScreen --> AuthProvider
AuthProvider --> RoleCheck
RoleCheck --> Student
RoleCheck --> Instructor
RoleCheck --> Supervisor
RoleCheck --> Admin
RoleCheck --> AdminGeneral
Student --> QRScan
Instructor --> EnvManage
Supervisor --> Review
Admin --> Warehouse
AdminGeneral --> System
```

**Sources:** [client/lib/core/services/navigation_service.dart L40-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L40-L58)

 [client/lib/presentation/screens/splash/splash_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/splash/splash_screen.dart)

### Navigation Service Integration

The `NavigationService` class configures routing with automatic role-based redirection:

```mermaid
flowchart TD

Router["GoRouter<br>NavigationService.router"]
RedirectGuard["redirect callback"]
AuthProvider["AuthProvider"]
RoleNavService["RoleNavigationService"]
SessionCheck["Session validation"]
AccessCheck["Permission check"]
DefaultRoute["getDefaultRoute()"]

Router --> RedirectGuard
RedirectGuard --> AuthProvider
RedirectGuard --> RoleNavService
AuthProvider --> SessionCheck
SessionCheck --> RoleNavService
RoleNavService --> AccessCheck
AccessCheck --> DefaultRoute
```

The redirect logic at [client/lib/core/services/navigation_service.dart L42-L56](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L42-L56)

 enforces that:

1. Unauthenticated users are redirected to `/login`
2. Authenticated users without route access are redirected to their role-specific default dashboard
3. The `RoleNavigationService.hasAccessToRoute()` method validates permissions

**Sources:** [client/lib/core/services/navigation_service.dart L40-L224](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L40-L224)

## Common Dashboard Patterns

All dashboard implementations share a consistent structure and set of reusable UI components.

### Dashboard Component Structure

```mermaid
flowchart TD

Dashboard["Dashboard StatefulWidget"]
State["Dashboard State"]
AppBar["SenaAppBar<br>showBackButton: false"]
NotifBadge["NotificationBadge"]
RefreshIndicator["RefreshIndicator<br>onRefresh: _fetchData()"]
ScrollView["SingleChildScrollView"]
Drawer["Navigation Drawer"]
WelcomeCard["Welcome Card<br>User info + Environment"]
StatsSection["Statistics Section<br>Metric Cards"]
ActionsGrid["Actions Grid<br>2-column GridView"]
NotificationsCard["Recent Notifications<br>Top 3 items"]
StatCard1["_buildStatCard()<br>Icon + Value + Label"]
StatCard2["_buildStatCard()"]
StatCardN["..."]
ActionCard1["_buildActionCard()<br>Icon + Title + Route"]
ActionCard2["_buildActionCard()"]
ActionCardN["..."]

Dashboard --> State
State --> AppBar
State --> RefreshIndicator
State --> Drawer
AppBar --> NotifBadge
RefreshIndicator --> ScrollView
ScrollView --> WelcomeCard
ScrollView --> StatsSection
ScrollView --> ActionsGrid
ScrollView --> NotificationsCard
StatsSection --> StatCard1
StatsSection --> StatCard2
StatsSection --> StatCardN
ActionsGrid --> ActionCard1
ActionsGrid --> ActionCard2
ActionsGrid --> ActionCardN
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L120-L409](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L120-L409)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L140-L470](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L140-L470)

### Stat Card Pattern

All dashboards use a consistent `_buildStatCard()` method to display metrics:

```mermaid
flowchart TD

StatCard["_buildStatCard()"]
Title["String title"]
Value["String value"]
Icon["IconData icon"]
Color["Color color"]
CardWidget["Card widget"]
IconWidget["Icon<br>size: 32"]
ValueText["Text<br>fontSize: 24<br>fontWeight: bold"]
TitleText["Text<br>fontSize: 12<br>grey600"]

StatCard --> Title
StatCard --> Value
StatCard --> Icon
StatCard --> Color
Title --> CardWidget
Value --> CardWidget
Icon --> CardWidget
Color --> CardWidget
CardWidget --> IconWidget
CardWidget --> ValueText
CardWidget --> TitleText
```

Example implementations:

* [client/lib/presentation/screens/dashboard/student_dashboard.dart L556-L586](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L556-L586)
* [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L617-L647](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L617-L647)
* [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L787-L817](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L787-L817)

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L556-L586](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L556-L586)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L617-L647](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L617-L647)

### Action Card Pattern

Action cards provide navigation to key features with a consistent interface:

```mermaid
flowchart TD

ActionCard["_buildActionCard()"]
Params["Parameters"]
Context["BuildContext context"]
Title["String title"]
Subtitle["String subtitle"]
IconData["IconData icon"]
Color["Color color"]
Route["String route"]
Extra["Map? extra"]
CardWidget["Card with InkWell"]
Push["context.push(route, extra: extra)"]

ActionCard --> Params
Params --> Context
Params --> Title
Params --> Subtitle
Params --> IconData
Params --> Color
Params --> Route
Params --> Extra
ActionCard --> CardWidget
CardWidget --> Push
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L588-L627](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L588-L627)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L649-L688](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L649-L688)

### Data Fetching Pattern

All dashboards follow a consistent data loading pattern:

```mermaid
sequenceDiagram
  participant Dashboard UI
  participant Dashboard State
  participant ApiService
  participant NotificationService
  participant AuthProvider

  Dashboard UI->>Dashboard State: initState()
  Dashboard State->>Dashboard State: _apiService = ApiService(authProvider)
  Dashboard State->>Dashboard State: _fetchData()
  Dashboard State->>Dashboard State: setState(() => _isLoading = true)
  loop [Fetch Environment]
    Dashboard State->>AuthProvider: currentUser.environmentId
    Dashboard State->>ApiService: getSingle(environmentsEndpoint + id)
    ApiService-->>Dashboard State: environment data
    Dashboard State->>ApiService: get(inventoryEndpoint, queryParams)
    ApiService-->>Dashboard State: items list
    Dashboard State->>Dashboard State: Calculate statistics
    Dashboard State->>NotificationService: getNotifications()
    NotificationService-->>Dashboard State: notifications
    Dashboard State->>NotificationService: getUnreadCount()
    NotificationService-->>Dashboard State: unreadCount
  end
  Dashboard State->>Dashboard State: setState(() => _isLoading = false)
  Dashboard State-->>Dashboard UI: Render dashboard
```

Key methods:

* `_fetchData()` - Main data loading orchestrator
* `_fetchEnvironment()` - Loads environment details
* `_fetchInventoryStats()` - Calculates inventory metrics
* `_fetchNotifications()` - Loads recent notifications

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L36-L131](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L36-L131)

 [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L38-L207](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L38-L207)

## Dashboard Implementations

### Student Dashboard

The `StudentDashboard` at [client/lib/presentation/screens/dashboard/student_dashboard.dart L12-L774](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L12-L774)

 provides students with basic operational capabilities.

#### Displayed Metrics

| Metric | Source | Calculation |
| --- | --- | --- |
| Equipos Totales | `_items` | Sum of all item quantities |
| Disponibles | `_items` | `quantity - quantity_damaged - quantity_missing` |
| Dañados | `_items` | Sum of `quantity_damaged` |
| Faltantes | `_items` | Sum of `quantity_missing` |

Calculation methods at [client/lib/presentation/screens/dashboard/student_dashboard.dart L86-L112](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L86-L112)

#### Available Actions

```mermaid
flowchart TD

StudentActions["Student Dashboard Actions"]
QRScan["Escanear QR<br>/qr-scan"]
InvCheck["Verificar Inventario<br>/inventory-check"]
Maintenance["Solicitar Mantenimiento<br>/maintenance-request"]
Environment["Ambiente<br>/environment-overview"]
Notifications["Notificaciones<br>/notifications"]
Feedback["Feedback<br>/feedback"]
Settings["Configuración<br>/profile"]

StudentActions --> QRScan
StudentActions --> InvCheck
StudentActions --> Maintenance
StudentActions --> Environment
StudentActions --> Notifications
StudentActions --> Feedback
StudentActions --> Settings
```

Action grid at [client/lib/presentation/screens/dashboard/student_dashboard.dart L268-L335](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L268-L335)

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L1-L774](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L1-L774)

### Instructor Dashboard

The `InstructorDashboard` at [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L12-L851](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L12-L851)

 focuses on environment management and verification oversight.

#### Displayed Metrics

| Metric | Source | Description |
| --- | --- | --- |
| Items Totales | `_inventoryStats['total_quantity']` | Total quantity across all items |
| Disponibles | `_inventoryStats['available_quantity']` | Available units (total - damaged - missing) |
| En Préstamo | `_inventoryStats['in_use']` | Items with status 'in_use' |
| Dañados | `_inventoryStats['damaged_quantity']` | Damaged units |
| Faltantes | `_inventoryStats['missing_quantity']` | Missing units |

Statistics calculation at [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L69-L117](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L69-L117)

#### Available Actions

The instructor dashboard provides 10 action cards arranged in a 2-column grid:

```mermaid
flowchart TD

InstructorActions["Instructor Dashboard Actions"]
QRScan["Escanear QR<br>/qr-scan"]
InvCheck["Verificar Inventario<br>/inventory-check"]
GenQR["Generar QR<br>/qr-generate"]
LoanRequest["Solicitar Préstamo<br>/loan-request"]
LoanHistory["Historial de Préstamos<br>/loan-history"]
Maintenance["Solicitar Mantenimiento<br>/maintenance-request"]
Environment["Ambiente<br>/environment-overview"]
Feedback["Feedback<br>/feedback"]
Settings["Configuración<br>/profile"]
Notifications["Notificaciones<br>/notifications"]

InstructorActions --> QRScan
InstructorActions --> InvCheck
InstructorActions --> GenQR
InstructorActions --> LoanRequest
InstructorActions --> LoanHistory
InstructorActions --> Maintenance
InstructorActions --> Environment
InstructorActions --> Feedback
InstructorActions --> Settings
InstructorActions --> Notifications
```

Grid implementation at [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L303-L395](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L303-L395)

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L1-L851](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L1-L851)

### Supervisor Dashboard

The `SupervisorDashboardScreen` at [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L12-L1082](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L12-L1082)

 provides oversight and monitoring capabilities.

#### Displayed Metrics

Supervisors see the most detailed statistics breakdown:

| Section | Metrics | Purpose |
| --- | --- | --- |
| Primary Stats | Total, Available, In Use, Damaged, Missing, Maintenance, Pending Requests | Quick overview |
| Detailed Breakdown | Total Units, Available Units, Damaged Units, Missing Units, Maintenance Items, In Use Items | Granular analysis |

The dashboard calculates both item counts and unit quantities:

* Item counts: Number of distinct items with each status
* Unit quantities: Total number of units (accounting for `quantity` field)

Calculation logic at [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L49-L207](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L49-L207)

#### Available Actions

```mermaid
flowchart TD

SupervisorActions["Supervisor Dashboard Actions"]
QRScan["Escanear QR<br>/qr-scan"]
InvCheck["Verificar Inventario<br>/inventory-check"]
GenQR["Generar QR<br>/qr-generate"]
Environment["Ambiente<br>/environment-overview"]
Alerts["Alertas de Inventario<br>/inventory-alerts"]
Statistics["Estadísticas<br>/statistics-dashboard"]
Reports["Generar Reportes<br>/report-generator"]
Feedback["Feedback<br>/feedback"]
Settings["Configuración<br>/profile"]
Notifications["Notificaciones<br>/notifications"]

SupervisorActions --> QRScan
SupervisorActions --> InvCheck
SupervisorActions --> GenQR
SupervisorActions --> Environment
SupervisorActions --> Alerts
SupervisorActions --> Statistics
SupervisorActions --> Reports
SupervisorActions --> Feedback
SupervisorActions --> Settings
SupervisorActions --> Notifications
```

Grid at [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L454-L544](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L454-L544)

**Sources:** [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L1-L1082](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L1-L1082)

### Admin Dashboard

The `AdminDashboardScreen` at [client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart L9-L584](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart#L9-L584)

 manages warehouse operations and loan management.

#### Data Source

Admin dashboard uses `UserManagementService.getDashboardStats()` to fetch aggregated metrics:

```mermaid
flowchart TD

AdminDashboard["AdminDashboardScreen"]
UserMgmtService["UserManagementService"]
API["Backend API"]
StatsData["Dashboard statistics"]
Inventory["inventory:<br>total_items, total_quantity,<br>available_items, damaged_items"]
Maintenance["maintenance:<br>total_active"]
Verifications["verifications:<br>recent_checks, completion_rate"]

AdminDashboard --> UserMgmtService
UserMgmtService --> API
API --> StatsData
StatsData --> Inventory
StatsData --> Maintenance
StatsData --> Verifications
```

Method at [client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart L27-L49](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart#L27-L49)

#### Displayed Metrics

| Metric | Field | Description |
| --- | --- | --- |
| Total Equipos | `inventory.total_quantity` | Total equipment units |
| Items Únicos | `inventory.total_items` | Distinct item types |
| Disponibles | `inventory.available_items` | Items with available status |
| En Uso | `inventory.in_use_items` | Items currently in use |
| Dañados | `inventory.damaged_quantity` | Damaged units |
| Mantenimiento | `maintenance.total_active` | Active maintenance requests |

#### System Status Panel

Additional metrics displayed in the "Estado del Sistema" card:

```mermaid
flowchart TD

SystemStatus["System Status Panel"]
RecentChecks["Verificaciones Recientes<br>verifications.recent_checks"]
CompletionRate["Tasa de Completitud<br>verifications.completion_rate"]
MissingItems["Items Faltantes<br>inventory.missing_quantity"]
PendingMaint["Mantenimiento Pendiente<br>maintenance.pending_requests"]
SuccessColor["Green indicator"]
WarningColor["Yellow indicator"]
ErrorColor["Red indicator"]
SuccessColor2["Green indicator"]

SystemStatus --> RecentChecks
SystemStatus --> CompletionRate
SystemStatus --> MissingItems
SystemStatus --> PendingMaint
CompletionRate --> SuccessColor
CompletionRate --> WarningColor
MissingItems --> ErrorColor
MissingItems --> SuccessColor2
```

Panel at [client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart L298-L350](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart#L298-L350)

#### Available Actions

Admin dashboard provides 10 management actions:

* Historial de Préstamos - `/loan-history`
* Gestión de Préstamos - `/loan-management`
* Gestión de Almacen - `/environment-overview`
* Escáner QR - `/qr-scan`
* Generador QR - `/qr-generator`
* Estadísticas Avanzadas - `/statistics-dashboard`
* Alertas del Sistema - `/inventory-alerts`
* Generador de Reportes - `/report-generator`
* Feedback del Sistema - `/feedback-form`
* Configuración - `/profile`

Grid at [client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart L206-L295](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart#L206-L295)

**Sources:** [client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart L1-L584](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/admin_dashboard_screen.dart#L1-L584)

### Admin General Dashboard

The `GeneralAdminDashboardScreen` at [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart L9-L515](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart#L9-L515)

 provides system-wide oversight.

#### Global Metrics

Uses `UserManagementService.getAdminDashboardStats()` to fetch system-wide statistics:

| Metric | Field | Description |
| --- | --- | --- |
| Usuarios Totales | `global_metrics.total_users` | All registered users |
| Equipos Totales | `global_metrics.total_equipment` | Total equipment across all environments |
| Ambientes | `global_metrics.total_environments` | Total environments |
| Préstamos Activos | `global_metrics.active_loans` | Currently active loans |
| Usuarios Activos | `global_metrics.active_users` | Recently active users |
| Mantenimiento Pendiente | `global_metrics.pending_maintenance` | Pending maintenance requests |

Method at [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart L27-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart#L27-L48)

#### Activity Feed

Displays recent system events in a card-based activity timeline:

```mermaid
flowchart TD

ActivityFeed["Activity Feed"]
Activity1["Backup completado<br>Icon: check_circle<br>Color: success"]
Activity2["Alerta de almacenamiento<br>Icon: warning_amber<br>Color: warning"]
Activity3["Política de contraseñas actualizada<br>Icon: security<br>Color: info"]
Activity4["Webhook de reportes activado<br>Icon: extension<br>Color: secondary"]

ActivityFeed --> Activity1
ActivityFeed --> Activity2
ActivityFeed --> Activity3
ActivityFeed --> Activity4
```

Activity list at [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart L262-L296](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart#L262-L296)

 component definition at [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart L487-L514](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart#L487-L514)

#### Available Actions

Admin general has access to 9 system-wide management features:

* Gestión de Usuarios - `/user-management`
* Solicitudes de Préstamo - `/loan-history`
* Ambientes - `/environment-overview`
* Estadísticas - `/statistics-dashboard`
* Alertas - `/inventory-alerts`
* Auditoría - `/audit-log`
* Reportes - `/report-generator`
* Feedback - `/feedback-form`
* Configuración - `/profile`

Grid at [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart L173-L254](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart#L173-L254)

**Sources:** [client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart L1-L515](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/general_admin_dashboard_screen.dart#L1-L515)

## Notification Integration

All dashboards integrate with the notification system to display recent alerts and unread counts.

### Notification Display Pattern

```mermaid
flowchart TD

Dashboard["Dashboard State"]
FetchNotifs["_fetchNotifications()"]
NotifService["NotificationService"]
NotifBadge["NotificationBadge<br>in AppBar"]
NotifCard["Recent Notifications Card"]
NotifActionCard["Notification Action Card"]
DrawerNotif["Drawer notification badge"]
RecentList["Recent 3 notifications"]
UnreadCount["_unreadNotificationsCount"]
NotifItem1["_buildNotificationItem()"]
NotifItem2["_buildNotificationItem()"]
NotifItem3["_buildNotificationItem()"]

Dashboard --> FetchNotifs
FetchNotifs --> NotifService
NotifService --> RecentList
NotifService --> UnreadCount
RecentList --> NotifCard
UnreadCount --> NotifBadge
UnreadCount --> NotifActionCard
UnreadCount --> DrawerNotif
NotifCard --> NotifItem1
NotifCard --> NotifItem2
NotifCard --> NotifItem3
```

#### Notification Color Mapping

All dashboards use consistent color coding for notification types:

```mermaid
flowchart TD

ColorMap["_getNotificationColor()"]
VerifPending["verification_pending<br>AppColors.warning"]
VerifUpdate["verification_update<br>Role-specific color"]
MaintUpdate["maintenance_update<br>AppColors.info"]
LoanApproved["loan_approved<br>AppColors.success"]
LoanRejected["loan_rejected/loan_overdue<br>AppColors.error"]
System["default (system)<br>AppColors.info"]

ColorMap --> VerifPending
ColorMap --> VerifUpdate
ColorMap --> MaintUpdate
ColorMap --> LoanApproved
ColorMap --> LoanRejected
ColorMap --> System
```

Implementation examples:

* [client/lib/presentation/screens/dashboard/student_dashboard.dart L475-L491](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L475-L491)
* [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L536-L552](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L536-L552)
* [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L704-L721](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L704-L721)

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L119-L131](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L119-L131)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L60-L84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L60-L84)

## Drawer Navigation

All dashboards include a navigation drawer with role-specific menu items.

### Drawer Structure

```mermaid
flowchart TD

Drawer["Navigation Drawer"]
DrawerHeader["DrawerHeader<br>Role-specific color<br>SENA logo + email"]
MenuItems["Menu Items"]
Dashboard["Dashboard home link"]
RoleActions["Role-specific actions"]
Divider["Divider"]
Settings["Settings/Profile"]
Logout["Logout"]
LogoutAction["Clear session"]
LoginScreen["Navigate to login"]

Drawer --> DrawerHeader
Drawer --> MenuItems
MenuItems --> Dashboard
MenuItems --> RoleActions
MenuItems --> Divider
MenuItems --> Settings
MenuItems --> Logout
Logout --> LogoutAction
Logout --> LoginScreen
```

Common drawer implementation pattern across all dashboards:

* [client/lib/presentation/screens/dashboard/student_dashboard.dart L629-L773](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L629-L773)
* [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L690-L849](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L690-L849)
* [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L860-L1082](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L860-L1082)

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L629-L773](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L629-L773)

 [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L690-L849](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L690-L849)

## Dashboard Lifecycle

### State Management Flow

```mermaid
sequenceDiagram
  participant User
  participant Dashboard Widget
  participant Dashboard State
  participant ApiService
  participant AuthProvider

  User->>Dashboard Widget: Navigate to dashboard
  Dashboard Widget->>Dashboard State: createState()
  Dashboard State->>Dashboard State: initState()
  Dashboard State->>AuthProvider: Get AuthProvider (listen: false)
  Dashboard State->>ApiService: Initialize ApiService(authProvider)
  Dashboard State->>Dashboard State: _fetchData()
  note over Dashboard State: setState(_isLoading = true)
  loop [Data Loading]
    Dashboard State->>ApiService: Fetch environment
    ApiService-->>Dashboard State: environment data
    Dashboard State->>ApiService: Fetch inventory
    ApiService-->>Dashboard State: inventory items
    Dashboard State->>ApiService: Fetch notifications
    ApiService-->>Dashboard State: notifications
  end
  note over Dashboard State: setState(_isLoading = false)
  Dashboard State-->>Dashboard Widget: Rebuild with data
  Dashboard Widget-->>User: Display dashboard
  User->>Dashboard Widget: Pull to refresh
  Dashboard Widget->>Dashboard State: onRefresh callback
  Dashboard State->>Dashboard State: _fetchData()
  note over Dashboard State: Reload all data
```

### Refresh Mechanism

All dashboards wrap their content in a `RefreshIndicator` at the top level:

```yaml
RefreshIndicator(
  onRefresh: _fetchData,
  child: SingleChildScrollView(
    // Dashboard content
  ),
)
```

This allows users to pull down to refresh all dashboard data.

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L28-L137](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L28-L137)

 [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L29-L213](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L29-L213)

## Environment Context

Most dashboards display environment-specific information when users are linked to an environment.

### Environment Linking

```mermaid
flowchart TD

User["Authenticated User"]
EnvId["user.environmentId"]
QRScan["QR Scan<br>/qr-scan"]
LinkProcess["Link to environment"]
DashboardCheck["Dashboard checks environmentId"]
WithEnv["Environment linked<br>Load environment data<br>Show stats"]
NoEnv["No environment<br>Show message<br>Prompt to scan QR"]

User --> EnvId
EnvId --> NoEnv
EnvId --> DashboardCheck
DashboardCheck --> WithEnv
NoEnv --> QRScan
QRScan --> LinkProcess
LinkProcess --> WithEnv
```

Environment check pattern at:

* [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L46-L67](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L46-L67)
* [client/lib/presentation/screens/dashboard/student_dashboard.dart L40-L50](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L40-L50)
* [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L43-L188](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L43-L188)

**Sources:** [client/lib/presentation/screens/dashboard/instructor_dashboard.dart L46-L67](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart#L46-L67)

 [client/lib/presentation/screens/dashboard/student_dashboard.dart L36-L84](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L36-L84)