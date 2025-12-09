# Audit Log Screen

> **Relevant source files**
> * [client/lib/core/services/audit_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart)
> * [client/lib/presentation/screens/audit/audit_log_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart)
> * [server/app/middleware/audit_middleware.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py)
> * [server/app/routers/reports.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/reports.py)

## Purpose and Scope

This document describes the Audit Log Screen, a client-side Flutter interface that allows authorized users to view and filter system audit logs for compliance and troubleshooting purposes. The screen displays detailed records of all API requests captured by the audit middleware, providing visibility into user actions, system changes, and operational events.

For information about the automatic audit log capture mechanism, see [Audit Middleware](/axchisan/GestionInventarioSENA/10.1-audit-middleware). For details on the backend API endpoints and client service methods for fetching audit data, see [Audit Service & APIs](/axchisan/GestionInventarioSENA/10.3-audit-service-and-apis).

**Access Control**: This screen is available only to users with `supervisor`, `admin`, or `admin_general` roles.

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L1-L727](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L1-L727)

---

## Screen Architecture

The `AuditLogScreen` is implemented as a stateful Flutter widget that displays audit logs in a scrollable, paginated list with filtering capabilities. The screen consists of four main sections: a header with statistics, filter controls, an error/loading state display, and the scrollable log list.

### Component Hierarchy Diagram

```

```

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L16-L227](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L16-L227)

---

## State Management

The screen maintains several state variables to control data loading, filtering, and pagination. All state is managed through the `_AuditLogScreenState` class.

### State Variables

| Variable | Type | Purpose |
| --- | --- | --- |
| `_auditService` | `AuditService` | Service instance for API calls |
| `_isLoading` | `bool` | Indicates initial data loading |
| `_isLoadingMore` | `bool` | Indicates pagination loading |
| `_error` | `String?` | Error message if load fails |
| `_auditLogs` | `List<AuditLog>` | Current list of logs |
| `_stats` | `AuditStats?` | Statistics data |
| `_currentPage` | `int` | Current pagination page |
| `_totalPages` | `int` | Total pages available |
| `selectedFilter` | `String` | Selected action filter |
| `selectedUser` | `String` | Selected user filter |
| `selectedDate` | `DateTime?` | Selected date filter |

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L17-L29](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L17-L29)

### Data Flow Diagram

```

```

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L38-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L38-L153)

---

## Statistics Header

The statistics header displays aggregate metrics about audit logs, providing a quick overview of system activity. It consists of a dark blue container with the SENA logo, title, and three statistic chips.

### Header Implementation

The header uses a `Container` with a fixed background color (`Color(0xFF00324D)`) and includes:

* **SENA Logo**: 50x50 pixel white-background container with the logo asset
* **Title**: "Registro de Auditoría del Sistema" in white bold text
* **Statistics Row**: Three `_buildStatChip` widgets displaying metrics

```

```

### Stat Chip Structure

Each statistic chip is built using the `_buildStatChip` method, which creates a colored container with a value and label:

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L234-L290](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L234-L290)

 [client/lib/presentation/screens/audit/audit_log_screen.dart L692-L720](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L692-L720)

---

## Filter System

The filter system provides three filter controls that allow users to narrow down the displayed audit logs by action type, user, and date. Filters are applied immediately when changed, triggering a new API request.

### Filter Controls

```

```

### Action Filter Options

The `actionFilters` list defines the available action types for filtering:

| Filter Value | Description |
| --- | --- |
| `Todas` | No filter (show all) |
| `LOGIN` | Login actions |
| `LOGOUT` | Logout actions |
| `INVENTORY_CREATE` | Inventory item creation |
| `INVENTORY_UPDATE` | Inventory item updates |
| `INVENTORY_DELETE` | Inventory item deletions |
| `LOAN_CREATE` | Loan creation |
| `LOAN_UPDATE` | Loan updates |
| `MAINTENANCE_CREATE` | Maintenance request creation |

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L31-L34](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L31-L34)

 [client/lib/presentation/screens/audit/audit_log_screen.dart L294-L408](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L294-L408)

### Filter Application Flow

The `_applyFilters()` method reconstructs the query with current filter values:

1. Set `_isLoading = true` and clear errors
2. Convert `selectedFilter` to `actionFilter` (null if "Todas")
3. Convert `selectedDate` to ISO 8601 date string
4. Call `_auditService.getAuditLogs()` with filter parameters
5. Update `_auditLogs` list and reset pagination to page 1
6. Set `_isLoading = false`

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L81-L114](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L81-L114)

---

## Log List Display

The log list uses a `ListView.builder` with infinite scroll pagination, displaying audit logs as `SenaCard` widgets. The list automatically loads more data when the user scrolls to the bottom.

### Pagination Architecture

```

```

### Pagination Implementation

The `_loadMoreData()` method handles infinite scroll:

1. **Guard Condition**: Return early if already loading or on last page
2. **Set Loading State**: `_isLoadingMore = true`
3. **Fetch Next Page**: Call `getAuditLogs(page: _currentPage + 1)` with same filters
4. **Append Results**: Add new logs to existing `_auditLogs` list
5. **Update State**: Increment `_currentPage` and set `_isLoadingMore = false`

**Pagination Constants**:

* `_perPage = 20` logs per page
* Scroll detection: `scrollInfo.metrics.pixels == scrollInfo.metrics.maxScrollExtent`

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L116-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L116-L153)

 [client/lib/presentation/screens/audit/audit_log_screen.dart L433-L686](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L433-L686)

---

## Log Card Components

Each audit log is displayed as a `SenaCard` containing comprehensive information about the action, user, entity, and response status. The card layout is organized into multiple sections with icons and color-coded indicators.

### Log Card Structure Diagram

```

```

### Action Icons Mapping

The `getActionIcon()` method maps action types to Material Design icons:

| Action Type | Icon | Flutter Icon Constant |
| --- | --- | --- |
| `loan_create`, `préstamo` | Assignment Turned In | `Icons.assignment_turned_in_outlined` |
| `loan_update`, `devolución` | Assignment Return | `Icons.assignment_return_outlined` |
| `maintenance_create`, `mantenimiento` | Build | `Icons.build_outlined` |
| `inventory_create`, `creación` | Add Circle | `Icons.add_circle_outline` |
| `inventory_update`, `modificación` | Edit | `Icons.edit_outlined` |
| `inventory_delete`, `eliminación` | Delete | `Icons.delete_outline` |
| `login` | Login | `Icons.login_outlined` |
| `logout` | Logout | `Icons.logout_outlined` |
| Default | Info | `Icons.info_outlined` |

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L170-L197](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L170-L197)

### Card Data Extraction

The card displays information extracted from the `AuditLog` object:

**Primary Display**:

* **Description**: `log.newValues['description']` or `_auditService.getLogDescription(log)`
* **Timestamp**: Formatted as `DD/MM/YYYY HH:MM` from `log.createdAt`
* **Severity Badge**: Determined by `_getSeverityFromAction(log.action)`

**HTTP Information** (if available):

* **Method and Path**: `log.newValues['request']['method']` + `log.newValues['request']['path']`
* Displayed in monospace font with grey background

**User Metadata**:

* **User Name**: `log.userName` or `log.userEmail` or "Sistema"
* **IP Address**: `log.ipAddress` or "IP desconocida"

**Entity Metadata**:

* **Entity Type**: Formatted via `_auditService.formatEntityTypeForDisplay(log.entityType)`
* **Status Code**: `log.newValues['response']['status_code']` with color coding (green for 2xx-3xx, red for errors)
* Falls back to `log.entityId` if no HTTP status

**Performance Data**:

* **Duration**: `log.newValues['duration_seconds']` displayed in seconds with 2 decimal places

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L458-L682](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L458-L682)

---

## Severity Indicators

The screen uses color-coded severity indicators to help users quickly identify the nature and importance of audit log entries. Severity is determined by analyzing the action type.

### Severity Color Mapping

The `getSeverityColor()` method defines the color scheme:

| Severity | Color | Flutter Color Constant | Use Case |
| --- | --- | --- | --- |
| `info` | Blue | `Colors.blue` | Informational actions (views, queries) |
| `warning` | Orange | `Colors.orange` | Potentially destructive actions (updates, deletes) |
| `error` | Red | `Colors.red` | Failed operations, exceptions |
| `success` | Green | `Colors.green` | Successful completions (creates, approvals) |
| Default | Grey | `Colors.grey` | Unknown action types |

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L155-L168](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L155-L168)

### Severity Determination Logic

The `_getSeverityFromAction()` method in `AuditService` analyzes the action string to determine severity:

```

```

**Keyword Lists** (from AuditService):

* **Warning**: `delete`, `update`, `modify`, `change`, `remove`
* **Error**: `error`, `fail`, `exception`, `reject`
* **Success**: `create`, `login`, `approve`, `complete`, `success`

**Sources**: [client/lib/core/services/audit_service.dart L206-L222](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L206-L222)

### StatusBadge Integration

The severity is displayed using the `StatusBadge` widget, which maps severity strings to `StatusType` enum values:

```
_getSeverityType(String? severity) {
  switch (severity?.toLowerCase()) {
    case 'info': return StatusType.info;
    case 'warning': return StatusType.warning;
    case 'error': return StatusType.error;
    case 'success': return StatusType.success;
    default: return StatusType.info;
  }
}
```

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L199-L212](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L199-L212)

---

## Internationalization and Formatting

The screen includes comprehensive formatting methods to convert technical API data into user-friendly Spanish text. These methods are provided by the `AuditService` class.

### Action Display Formatting

The `formatActionForDisplay()` method converts action constants to Spanish descriptions:

| Technical Action | Display Text (Spanish) |
| --- | --- |
| `LOGIN` | "Inicio de sesión" |
| `LOGOUT` | "Cierre de sesión" |
| `CREATE_INVENTORY_ITEM` | "Se creó un item en el inventario" |
| `UPDATE_INVENTORY_ITEM` | "Se actualizó un item del inventario" |
| `DELETE_INVENTORY_ITEM` | "Se eliminó un item del inventario" |
| `CREATE_LOAN` | "Se creó un préstamo" |
| `CREATE_MAINTENANCE_REQUEST` | "Se creó una solicitud de mantenimiento" |
| `CREATE_USER` | "Se creó un usuario" |
| `CREATE_ENVIRONMENT` | "Se creó un ambiente" |
| `CREATE_INVENTORY_CHECK` | "Se realizó una verificación de inventario" |

**Fallback Behavior**: If no mapping exists, the method formats the technical action by:

1. Replacing underscores with spaces
2. Converting to lowercase
3. Capitalizing the first letter of each word

**Sources**: [client/lib/core/services/audit_service.dart L12-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L12-L43)

 [client/lib/core/services/audit_service.dart L225-L240](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L225-L240)

### Entity Type Formatting

The `formatEntityTypeForDisplay()` method converts entity type identifiers to Spanish:

| Technical Entity | Display Text (Spanish) |
| --- | --- |
| `inventory_item` | "Elemento de Inventario" |
| `loan` | "Préstamo" |
| `user` | "Usuario" |
| `maintenance_request` | "Solicitud de Mantenimiento" |
| `environment` | "Ambiente" |
| `inventory_check` | "Verificación de Inventario" |
| `authentication` | "Autenticación" |

**Sources**: [client/lib/core/services/audit_service.dart L242-L255](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L242-L255)

---

## Error Handling

The screen implements comprehensive error handling with user-friendly error messages and retry capabilities. Errors are displayed in a dismissible container with a retry button.

### Error States

```

```

### Error Display Implementation

When an error occurs, the `_error` state variable is set with a descriptive message, which triggers the display of an error container:

**Error Container Features**:

* **Red background**: `Colors.red.withOpacity(0.1)`
* **Error icon**: `Icons.error_outline` in red
* **Error message**: Displayed in red text
* **Retry button**: Calls `_loadInitialData()` to retry the operation

**Error Types**:

1. **Initial Load Error**: "Error cargando datos de auditoría: [exception]"
2. **Filter Error**: "Error aplicando filtros: [exception]"
3. **Pagination Error**: "Error cargando más datos: [exception]" (shown as SnackBar)

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L48-L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L48-L79)

 [client/lib/presentation/screens/audit/audit_log_screen.dart L410-L430](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L410-L430)

---

## Integration with AuditService

The screen communicates with the backend through the `AuditService` class, which provides abstracted methods for fetching audit data. The service is initialized in the `initState()` method using the `AuthProvider` for authentication.

### Service Initialization

```

```

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L38-L46](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L38-L46)

### API Calls

The screen makes the following `AuditService` calls:

| Method | Parameters | Returns | Purpose |
| --- | --- | --- | --- |
| `getAuditStats()` | `days: 30` | `Map<String, dynamic>` | Fetch statistics for header |
| `getAuditLogs()` | `page`, `perPage`, `actionFilter`, `startDate`, `endDate` | `Map<String, dynamic>` | Fetch paginated logs |
| `getActionSeverity()` | `action: String` | `String` | Determine severity level |
| `formatActionForDisplay()` | `action: String` | `String` | Format action for display |
| `formatEntityTypeForDisplay()` | `entityType: String` | `String` | Format entity type |
| `getLogDescription()` | `log: AuditLog` | `String` | Get complete description |

**Concurrent Loading**: Initial data load uses `Future.wait()` to fetch statistics and logs simultaneously, improving perceived performance.

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L48-L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L48-L79)

### Backend Endpoints

The `AuditService` methods communicate with these backend endpoints (see [Audit Service & APIs](/axchisan/GestionInventarioSENA/10.3-audit-service-and-apis) for full details):

* **GET** `/api/audit-logs?page=N&per_page=20&action_filter=...&start_date=...&end_date=...`
* **GET** `/api/audit-logs/stats?days=30`

**Sources**: [client/lib/core/services/audit_service.dart L45-L90](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L45-L90)

---

## Performance Considerations

The screen implements several performance optimizations to handle large audit log datasets efficiently:

### Pagination Strategy

* **Page Size**: Fixed at 20 logs per page (`_perPage = 20`)
* **Lazy Loading**: Logs are loaded on-demand as user scrolls
* **Scroll Detection**: Uses `NotificationListener<ScrollNotification>` to detect when user reaches bottom
* **Loading Indicator**: Shows `CircularProgressIndicator` at bottom of list during pagination

### State Management

* **Incremental Updates**: New pages are appended to existing `_auditLogs` list rather than replacing it
* **Filter Reset**: Filters start fresh load from page 1 to prevent stale data
* **Guard Conditions**: `_loadMoreData()` checks `_isLoadingMore` and page bounds to prevent duplicate requests

### Memory Management

* **Dispose Pattern**: `_auditService.dispose()` is called in widget's `dispose()` method
* **List Growth**: No automatic pruning of old logs; relies on user re-filtering or navigating away

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L116-L153](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L116-L153)

 [client/lib/presentation/screens/audit/audit_log_screen.dart L722-L726](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L722-L726)

---

## Audit Log Data Model

The screen works with `AuditLog` and `AuditStats` data models defined in the `AuditService`. These models deserialize JSON responses from the backend API.

### AuditLog Class

```

```

**Key Fields**:

* **id**: UUID of the audit log record
* **userId, userName, userEmail**: User who performed the action
* **action**: Action constant (e.g., "CREATE_INVENTORY_ITEM")
* **entityType**: Type of entity affected (e.g., "inventory_item")
* **entityId**: UUID or identifier of the affected entity
* **oldValues**: Previous state (for updates)
* **newValues**: Contains request data, response data, description, duration
* **ipAddress**: Client IP address
* **createdAt**: Timestamp of the action

**Sources**: [client/lib/core/services/audit_service.dart L283-L349](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L283-L349)

### AuditStats Class

```

```

**Statistics Metrics**:

* **totalLogs**: Total number of audit logs (30-day default)
* **todayLogs**: Logs created today
* **warningLogs**: Logs with warning severity
* **errorLogs**: Logs with error severity
* **infoLogs**: Logs with info severity
* **successLogs**: Logs with success severity
* **topActions**: Most frequent actions
* **topUsers**: Most active users

**Sources**: [client/lib/core/services/audit_service.dart L351-L418](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L351-L418)

---

## Relationship to Backend Audit Middleware

The logs displayed by this screen are automatically captured by the backend `AuditMiddleware`, which intercepts all API requests. Understanding this relationship helps contextualize what users see in the UI.

### Middleware to UI Data Flow

```

```

### Action Mapping

The middleware determines action names using these methods:

* **Authentication Actions**: Hardcoded for `/auth/login`, `/auth/register`, `/auth/logout`
* **CRUD Actions**: Based on HTTP method + entity type * `POST /api/inventory` → `CREATE_INVENTORY_ITEM` * `PUT /api/inventory/{id}` → `UPDATE_INVENTORY_ITEM` * `DELETE /api/inventory/{id}` → `DELETE_INVENTORY_ITEM` * `GET /api/inventory` → `VIEW_INVENTORY_ITEM`

**Entity Type Detection**: Middleware parses the URL path to extract entity type (e.g., `/inventory-checks` → `inventory_check`)

**Sources**: [server/app/middleware/audit_middleware.py L260-L322](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L260-L322)

### Captured Data Structure

The middleware stores comprehensive request/response data in the `new_values` JSON field:

```

```

This structure is what the UI displays in each log card.

**Sources**: [server/app/middleware/audit_middleware.py L158-L218](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L158-L218)

---

## Code-to-UI Mapping

This section maps specific code entities to their visual representation in the UI to help developers understand how changes propagate.

### Widget Tree Mapping

| Code Symbol | UI Component | Visual Location |
| --- | --- | --- |
| `SenaAppBar(title: 'Registro de Auditoría')` | App bar with title and back button | Top of screen |
| `Container` at line 234 | Dark blue header with logo and stats | Below app bar |
| `Image.asset('assets/images/sena_logo.png')` | SENA logo | Top-left of header |
| `_buildStatChip('Total', ...)` | Blue chip with total count | Header statistics row |
| `_buildStatChip('Hoy', ...)` | Green chip with today count | Header statistics row |
| `_buildStatChip('Alertas', ...)` | Orange chip with warning count | Header statistics row |
| `DropdownButtonFormField` at line 302 | Action filter dropdown | First row of filters |
| `DropdownButtonFormField` at line 325 | User filter dropdown | First row of filters |
| `InkWell` at line 354 | Date picker button | Second row of filters |
| `ListView.builder` at line 445 | Scrollable log list | Main content area |
| `SenaCard` at line 463 | Individual log card | List items |
| `StatusBadge` at line 500 | Colored severity badge | Top-right of each card |

### Data Binding Mapping

| State Variable | Bound UI Element | Update Trigger |
| --- | --- | --- |
| `_stats?.totalLogs` | Total logs stat chip | `_loadInitialData()` success |
| `_stats?.todayLogs` | Today logs stat chip | `_loadInitialData()` success |
| `_stats?.warningLogs` | Alerts stat chip | `_loadInitialData()` success |
| `selectedFilter` | Action dropdown value | User selection |
| `selectedUser` | User dropdown value | User selection |
| `selectedDate` | Date picker display | User date selection |
| `_auditLogs` | ListView items | `_applyFilters()`, `_loadMoreData()` |
| `_isLoading` | Main loading indicator | Initial load, filter change |
| `_isLoadingMore` | Bottom loading indicator | Pagination |
| `_error` | Error container | Any API failure |
| `_currentPage` | Pagination state | `_loadMoreData()` |

**Sources**: [client/lib/presentation/screens/audit/audit_log_screen.dart L225-L690](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L225-L690)