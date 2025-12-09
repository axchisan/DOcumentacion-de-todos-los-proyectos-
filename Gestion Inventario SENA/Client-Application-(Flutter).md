# Client Application (Flutter)

> **Relevant source files**
> * [client/lib/core/services/api_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart)
> * [client/lib/core/services/navigation_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart)
> * [client/lib/core/services/report_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/report_service.dart)
> * [client/lib/data/models/environment_model.g.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/environment_model.g.dart)
> * [client/lib/data/models/user_model.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.dart)
> * [client/lib/data/models/user_model.g.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.g.dart)
> * [client/lib/main.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart)
> * [client/lib/presentation/providers/auth_provider.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart)
> * [client/lib/presentation/screens/auth/register_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/register_screen.dart)
> * [client/lib/presentation/screens/dashboard/student_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart)
> * [client/lib/presentation/screens/statistics/statistics_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart)

## Purpose and Scope

This document describes the Flutter client application architecture, including the service layer pattern, state management with Provider, navigation system, and screen organization. The client provides mobile and web interfaces for all user roles in the SENA Inventory Management System.

For authentication flows, see [Client-Side Authentication](/axchisan/GestionInventarioSENA/3.1-client-side-authentication). For role-based access control, see [Role-Based Access Control](/axchisan/GestionInventarioSENA/3.3-role-based-access-control). For specific dashboard implementations, see [Dashboard Screens](/axchisan/GestionInventarioSENA/4-dashboard-screens).

---

## Application Architecture

The Flutter client follows a layered architecture with clear separation of concerns:

```mermaid
flowchart TD

SCREENS["Screens<br>(LoginScreen, StudentDashboard, etc.)"]
WIDGETS["Reusable Widgets<br>(SenaAppBar, NotificationBadge)"]
AUTH_PROVIDER["AuthProvider<br>ChangeNotifier"]
LOAN_PROVIDER["LoanProvider<br>ChangeNotifier"]
THEME_SERVICE["ThemeService<br>ChangeNotifier"]
LANG_SERVICE["LanguageService<br>ChangeNotifier"]
API_SERVICE["ApiService<br>HTTP Client"]
NAV_SERVICE["NavigationService<br>GoRouter"]
REPORT_SERVICE["ReportService<br>PDF/Excel Generation"]
SESSION_SERVICE["SessionService<br>SharedPreferences"]
NOTIF_SERVICE["NotificationService<br>Local Notifications"]
ROLE_NAV_SERVICE["RoleNavigationService<br>Route Guards"]
MODELS["Data Models<br>(UserModel, InventoryItemModel, etc.)"]
LOCAL_STORAGE["SharedPreferences<br>Persistent Storage"]

SCREENS --> AUTH_PROVIDER
SCREENS --> LOAN_PROVIDER
SCREENS --> THEME_SERVICE
SCREENS --> API_SERVICE
SCREENS --> NAV_SERVICE
SCREENS --> REPORT_SERVICE
AUTH_PROVIDER --> API_SERVICE
AUTH_PROVIDER --> SESSION_SERVICE
LOAN_PROVIDER --> API_SERVICE
API_SERVICE --> AUTH_PROVIDER
NAV_SERVICE --> AUTH_PROVIDER
SESSION_SERVICE --> LOCAL_STORAGE
API_SERVICE --> MODELS
REPORT_SERVICE --> MODELS

subgraph subGraph3 ["Data Layer"]
    MODELS
    LOCAL_STORAGE
end

subgraph subGraph2 ["Service Layer"]
    API_SERVICE
    NAV_SERVICE
    REPORT_SERVICE
    SESSION_SERVICE
    NOTIF_SERVICE
    ROLE_NAV_SERVICE
    NAV_SERVICE --> ROLE_NAV_SERVICE
    REPORT_SERVICE --> API_SERVICE
end

subgraph subGraph1 ["State Management Layer"]
    AUTH_PROVIDER
    LOAN_PROVIDER
    THEME_SERVICE
    LANG_SERVICE
end

subgraph subGraph0 ["Presentation Layer"]
    SCREENS
    WIDGETS
end
```

**Sources:** [client/lib/main.dart L1-L75](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart#L1-L75)

 [client/lib/core/services/navigation_service.dart L1-L224](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L1-L224)

 [client/lib/core/services/api_service.dart L1-L720](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart#L1-L720)

### Layer Responsibilities

| Layer | Purpose | Key Components |
| --- | --- | --- |
| **Presentation** | UI rendering and user interaction | Screens, Widgets, Forms |
| **State Management** | Application state and business logic | `AuthProvider`, `LoanProvider` |
| **Service** | External communication and utilities | `ApiService`, `NavigationService`, `ReportService` |
| **Data** | Data structures and persistence | Models, `SharedPreferences` |

---

## Application Entry Point

The application initializes services and sets up the Provider tree in `main.dart`:

```mermaid
flowchart TD

MAIN["main()"]
INIT["Initialize Services<br>ThemeService, LanguageService"]
SETUP["MultiProvider Setup"]
APP["MaterialApp.router"]
AUTH["AuthProvider"]
LOAN["LoanProvider"]
THEME["ThemeService"]
LANG["LanguageService"]

MAIN --> INIT
INIT --> SETUP
SETUP --> APP
SETUP --> AUTH
SETUP --> LOAN
SETUP --> THEME
SETUP --> LANG
```

The `MultiProvider` wraps the application with the following providers:

| Provider | Type | Purpose |
| --- | --- | --- |
| `ThemeService` | Singleton | Dark/light theme management |
| `LanguageService` | Singleton | Localization support |
| `AuthProvider` | Instance | Authentication state |
| `LoanProvider` | Proxy | Loan management (depends on `AuthProvider`) |

**Sources:** [client/lib/main.dart L13-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/main.dart#L13-L43)

---

## State Management with Provider

The application uses the Provider pattern for state management. `ChangeNotifier` classes manage state and notify listeners when changes occur.

### AuthProvider Implementation

```mermaid
flowchart TD

AUTH_PROVIDER["AuthProvider<br>ChangeNotifier"]
STATE["State Variables:<br>_currentUser: UserModel?<br>_token: String?<br>_isAuthenticated: bool<br>_isLoading: bool<br>_errorMessage: String?"]
METHODS["Public Methods:<br>login(email, password)<br>register(...)<br>logout()<br>checkSession()"]
SESSION_SVC["SessionService<br>Persistent Storage"]
HTTP["HTTP POST<br>baseUrl + loginEndpoint"]
JWT["JWT Token Decode"]
NOTIFY["notifyListeners()"]

AUTH_PROVIDER --> STATE
AUTH_PROVIDER --> METHODS
METHODS --> HTTP
METHODS --> SESSION_SVC
METHODS --> JWT
HTTP --> NOTIFY
SESSION_SVC --> NOTIFY
```

**Key State Variables:**

* `_currentUser`: Holds the authenticated user's data (`UserModel`)
* `_token`: JWT access token for API requests
* `_isAuthenticated`: Boolean flag for authentication status
* `_isLoading`: Loading state during async operations
* `_errorMessage`: Holds error messages for UI display

**Core Methods:**

| Method | Parameters | Purpose |
| --- | --- | --- |
| `login()` | email, password | Authenticates user, stores token and session |
| `register()` | email, password, firstName, lastName, role, etc. | Creates new user account |
| `logout()` | - | Clears session and resets state |
| `checkSession()` | - | Validates stored session on app startup |

**Sources:** [client/lib/presentation/providers/auth_provider.dart L1-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L1-L159)

### State Change Flow

When a user logs in:

```mermaid
sequenceDiagram
  participant LoginScreen
  participant AuthProvider
  participant HTTP Client
  participant SessionService
  participant NavigationService

  LoginScreen->>AuthProvider: login(email, password)
  AuthProvider->>AuthProvider: _isLoading = true
  AuthProvider->>HTTP Client: notifyListeners()
  HTTP Client-->>AuthProvider: POST /api/auth/login
  AuthProvider->>AuthProvider: {access_token, user}
  AuthProvider->>SessionService: _token = access_token
  AuthProvider->>AuthProvider: _currentUser = UserModel.fromJson(user)
  AuthProvider-->>LoginScreen: _isAuthenticated = true
  LoginScreen->>NavigationService: saveSession(token, role, user, expiresAt)
```

**Sources:** [client/lib/presentation/providers/auth_provider.dart L26-L64](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/providers/auth_provider.dart#L26-L64)

 [client/lib/presentation/screens/auth/login_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/auth/login_screen.dart)

---

## Navigation System

The client uses `GoRouter` for declarative routing with built-in route guards and role-based access control.

### Router Configuration

```mermaid
flowchart TD

GO_ROUTER["GoRouter<br>NavigationService.router"]
REDIRECT["redirect() Function<br>Authentication & Role Check"]
ROUTES["Route Definitions"]
AUTH_CHECK["checkSession()"]
ROLE_CHECK["RoleNavigationService<br>hasAccessToRoute()"]
PUBLIC_ROUTES["Public Routes<br>/splash, /login, /register"]
AUTH_ROUTES["Protected Routes<br>/student-dashboard<br>/admin-dashboard<br>/inventory-check<br>etc."]
ALLOW["Allow Access"]
LOGIN["Redirect to /login"]
DEFAULT["Redirect to Default<br>Role Dashboard"]

GO_ROUTER --> REDIRECT
GO_ROUTER --> ROUTES
REDIRECT --> AUTH_CHECK
REDIRECT --> ROLE_CHECK
ROUTES --> PUBLIC_ROUTES
ROUTES --> AUTH_ROUTES
AUTH_CHECK --> ALLOW
AUTH_CHECK --> LOGIN
ROLE_CHECK --> ALLOW
ROLE_CHECK --> DEFAULT
```

The router configuration includes 30+ routes organized by feature:

| Route Pattern | Screen | Access Control |
| --- | --- | --- |
| `/splash` | `SplashScreen` | Public |
| `/login` | `LoginScreen` | Public |
| `/register` | `RegisterScreen` | Public |
| `/qr-scan` | `QRScanScreen` | Authenticated |
| `/inventory-check` | `InventoryCheckScreen` | Authenticated |
| `/student-dashboard` | `StudentDashboard` | Role: student |
| `/instructor-dashboard` | `InstructorDashboard` | Role: instructor |
| `/supervisor-dashboard` | `SupervisorDashboardScreen` | Role: supervisor |
| `/admin-dashboard` | `AdminDashboardScreen` | Role: admin |
| `/admin-general-dashboard` | `GeneralAdminDashboardScreen` | Role: admin_general |

**Sources:** [client/lib/core/services/navigation_service.dart L40-L219](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L40-L219)

### Route Guard Implementation

The `redirect` function in `NavigationService` implements authentication and authorization checks:

**Logic Flow:**

1. Call `authProvider.checkSession()` to validate stored session
2. If not authenticated and accessing protected route → redirect to `/login`
3. If authenticated but lacks role permission → redirect to default role dashboard
4. Otherwise, allow navigation

**Sources:** [client/lib/core/services/navigation_service.dart L42-L57](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L42-L57)

---

## Service Layer

The service layer provides reusable business logic and external integrations.

### ApiService Architecture

`ApiService` is the core HTTP client that handles all backend communication:

```mermaid
flowchart TD

API_SERVICE["ApiService"]
METHODS["HTTP Methods<br>get(), post(), put(), delete()"]
AUTH["Authorization Header<br>Bearer Token"]
ERROR_HANDLING["Error Handling<br>ApiException"]
RESPONSE_PARSING["Response Parsing<br>JSON Decode"]
AUTH_PROVIDER["AuthProvider.token"]
ERROR_TYPES["ApiErrorType enum<br>network, timeout, unauthorized,<br>authorization, validation,<br>notFound, serverError, etc."]
GET["get(endpoint, queryParams)"]
POST["post(endpoint, data)"]
PUT["put(endpoint, data)"]
DELETE["delete(endpoint)"]
GET_SINGLE["getSingle(endpoint)"]
SAFE_GET["safeGet(endpoint)"]

API_SERVICE --> METHODS
API_SERVICE --> AUTH
API_SERVICE --> ERROR_HANDLING
API_SERVICE --> RESPONSE_PARSING
AUTH --> AUTH_PROVIDER
ERROR_HANDLING --> ERROR_TYPES
METHODS --> GET
METHODS --> POST
METHODS --> PUT
METHODS --> DELETE
METHODS --> GET_SINGLE
METHODS --> SAFE_GET
```

**Key Features:**

| Feature | Implementation | Purpose |
| --- | --- | --- |
| **Auto Authorization** | Injects `Bearer ${token}` header | Authenticates all requests |
| **Role-Based Query Params** | Adds `system_wide=true` for admin_general | Enables cross-environment access |
| **Error Categorization** | `ApiErrorType` enum with 12 types | Structured error handling |
| **Redirect Handling** | Detects 301/302/307 responses | Prevents redirect loops |
| **Retry Logic** | `isRecoverable` and `retryDelay` properties | Automatic retry for transient failures |

**Sources:** [client/lib/core/services/api_service.dart L1-L720](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart#L1-L720)

### ApiService Usage Pattern

Screens and providers use `ApiService` to fetch data:

```yaml
// Initialize with AuthProvider
final _apiService = ApiService(
  authProvider: Provider.of<AuthProvider>(context, listen: false)
);

// Fetch data
final items = await _apiService.get(
  inventoryEndpoint,
  queryParams: {'environment_id': environmentId}
);

// Post data
final result = await _apiService.post(
  inventoryChecksEndpoint,
  {'status': 'pending', 'items': []}
);
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L31-L34](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L31-L34)

 [client/lib/core/services/api_service.dart L36-L65](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart#L36-L65)

### ReportService

`ReportService` generates PDF and Excel reports from aggregated data:

```mermaid
flowchart TD

REPORT_SERVICE["ReportService"]
GENERATE["generateReport()<br>reportType, format, dateRange"]
FETCH_DATA["_getReportData()<br>Calls ApiService"]
FORMAT_PDF["_generatePDFReport()<br>Uses pdf package"]
FORMAT_EXCEL["_generateExcelReport()<br>Uses excel package"]
SAVE["_savePDFFile()<br>Platform-specific saving"]
INVENTORY["_getInventoryReportData()"]
LOANS["_getLoansReportData()"]
MAINT["_getMaintenanceReportData()"]
AUDIT["_getAuditReportData()"]
STATS["_getStatisticsReportData()"]
API_SVC["ApiService.get()"]

REPORT_SERVICE --> GENERATE
GENERATE --> FETCH_DATA
GENERATE --> FORMAT_PDF
GENERATE --> FORMAT_EXCEL
FORMAT_PDF --> SAVE
FORMAT_EXCEL --> SAVE
FETCH_DATA --> INVENTORY
FETCH_DATA --> LOANS
FETCH_DATA --> MAINT
FETCH_DATA --> AUDIT
FETCH_DATA --> STATS
INVENTORY --> API_SVC
LOANS --> API_SVC
MAINT --> API_SVC
```

**Supported Report Types:**

| Report Type | Data Source | Formats |
| --- | --- | --- |
| `inventory` | Inventory items with status breakdown | PDF, Excel |
| `loans` | Loan history and statistics | PDF, Excel |
| `maintenance` | Maintenance requests with cost tracking | PDF, Excel |
| `audit` | Audit log entries with action breakdown | PDF, Excel |
| `statistics` | Comprehensive system metrics | PDF, Excel |
| `inventory_checks` | Verification history | PDF, Excel |
| `alerts` | System alerts and notifications | PDF, Excel |
| `users` | User list with role distribution | PDF, Excel |

**Sources:** [client/lib/core/services/report_service.dart L1-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/report_service.dart#L1-L105)

 [client/lib/core/services/report_service.dart L26-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/report_service.dart#L26-L103)

---

## Data Models

Data models use `json_serializable` for automatic JSON serialization:

### UserModel Example

```mermaid
classDiagram
    class UserModel {
        +String? id
        +String? email
        +String? firstName
        +String? lastName
        +String? role
        +String? phone
        +String? program
        +String? ficha
        +String? avatarUrl
        +bool? isActive
        +DateTime? lastLogin
        +DateTime? createdAt
        +DateTime? updatedAt
        +String? environmentId
        +fromJson(Map) : UserModel
        +toJson() : Map
    }
    class JsonSerializable {
        «annotation»
    }
    class JsonKey {
        «annotation»
        +String name
    }
    JsonSerializable <|.. UserModel : uses
    UserModel --> JsonKey
```

**Serialization Pattern:**

All models follow this pattern:

1. Annotate class with `@JsonSerializable()`
2. Annotate fields with `@JsonKey(name: 'snake_case_field')`
3. Implement factory constructor `fromJson(Map<String, dynamic>)`
4. Implement method `toJson() → Map<String, dynamic>`
5. Generate code with `part 'model_name.g.dart'`

**Example Models:**

| Model | File | Purpose |
| --- | --- | --- |
| `UserModel` | `user_model.dart` | User account data |
| `InventoryItemModel` | `inventory_item_model.dart` | Inventory item with quantities |
| `InventoryCheckModel` | `inventory_check_model.dart` | Verification record |
| `LoanModel` | `loan_model.dart` | Equipment loan |
| `MaintenanceRequestModel` | `maintenance_request_model.dart` | Repair request |
| `EnvironmentModel` | `environment_model.dart` | Physical location |
| `NotificationModel` | `notification_model.dart` | User notification |

**Sources:** [client/lib/data/models/user_model.dart L1-L55](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.dart#L1-L55)

 [client/lib/data/models/user_model.g.dart L1-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/user_model.g.dart#L1-L48)

---

## Screen Organization

Screens are organized by feature in the `presentation/screens` directory:

### Directory Structure

```markdown
presentation/
├── screens/
│   ├── auth/               # Authentication screens
│   │   ├── login_screen.dart
│   │   └── register_screen.dart
│   ├── dashboard/          # Role-specific dashboards
│   │   ├── student_dashboard.dart
│   │   ├── instructor_dashboard.dart
│   │   ├── supervisor_dashboard_screen.dart
│   │   ├── admin_dashboard_screen.dart
│   │   └── general_admin_dashboard_screen.dart
│   ├── inventory/          # Inventory management
│   │   ├── inventory_check_screen.dart
│   │   ├── edit_inventory_item_screen.dart
│   │   ├── inventory_alerts_screen.dart
│   │   └── AddInventoryItemScreen.dart
│   ├── loan/               # Loan management
│   │   ├── loan_request_screen.dart
│   │   ├── loan_management_screen.dart
│   │   └── loan_history_screen.dart
│   ├── maintenance/        # Maintenance requests
│   │   └── maintenance_request_screen.dart
│   ├── qr/                 # QR code functionality
│   │   ├── qr_scan_screen.dart
│   │   └── qr_code_generator_screen.dart
│   ├── statistics/         # Analytics
│   │   └── statistics_dashboard.dart
│   ├── reports/            # Report generation
│   │   └── report_generator_screen.dart
│   ├── notifications/      # Notifications
│   │   └── notifications_screen.dart
│   ├── profile/            # User profile
│   │   └── profile_screen.dart
│   └── ...
└── widgets/
    ├── common/             # Reusable widgets
    │   ├── sena_app_bar.dart
    │   ├── notification_badge.dart
    │   └── ...
    └── ...
```

**Sources:** [client/lib/core/services/navigation_service.dart L8-L37](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/navigation_service.dart#L8-L37)

### Screen Composition Pattern

Dashboard screens follow a consistent pattern:

```mermaid
flowchart TD

SCREEN["Dashboard Screen<br>StatefulWidget"]
STATE["State Variables<br>_apiService, _isLoading,<br>data collections"]
INIT["initState()<br>Initialize services,<br>fetch data"]
BUILD["build()<br>Scaffold with SenaAppBar"]
REFRESH["RefreshIndicator<br>Pull-to-refresh"]
CONTENT["Content Area<br>Cards, metrics, actions"]
DRAWER["Drawer Menu<br>Navigation links"]
FETCH_DATA["_fetchData()<br>Load from API"]
SET_STATE["setState()<br>Update UI"]
STAT_CARDS["Stat Cards<br>_buildStatCard()"]
ACTION_CARDS["Action Cards<br>_buildActionCard()"]
NOTIF_LIST["Notification List<br>Recent items"]

SCREEN --> STATE
SCREEN --> INIT
SCREEN --> BUILD
BUILD --> REFRESH
REFRESH --> CONTENT
BUILD --> DRAWER
INIT --> FETCH_DATA
FETCH_DATA --> SET_STATE
CONTENT --> STAT_CARDS
CONTENT --> ACTION_CARDS
CONTENT --> NOTIF_LIST
```

**Common Dashboard Elements:**

| Component | Purpose | Implementation |
| --- | --- | --- |
| **SenaAppBar** | Consistent header with title and actions | Reusable widget |
| **Stat Cards** | Display key metrics (total items, damaged, etc.) | Grid layout with icons |
| **Action Cards** | Navigation to key features | GridView with route links |
| **Notification Section** | Recent notifications with unread count | List with badge indicator |
| **Drawer** | Side navigation menu | Role-specific menu items |

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L1-L774](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L1-L774)

---

## Common UI Patterns

### Loading States

Screens use a consistent loading pattern:

```javascript
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: SenaAppBar(title: 'Dashboard'),
    body: _isLoading
        ? const Center(child: CircularProgressIndicator())
        : RefreshIndicator(
            onRefresh: _fetchData,
            child: SingleChildScrollView(
              child: Column(children: [...])
            )
          )
  );
}
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L139-L141](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L139-L141)

 [client/lib/presentation/screens/statistics/statistics_dashboard.dart L724-L734](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L724-L734)

### Error Handling

Screens display errors using `SnackBar`:

```javascript
try {
  final data = await _apiService.get(endpoint);
  setState(() => _data = data);
} catch (e) {
  ScaffoldMessenger.of(context).showSnackBar(
    SnackBar(content: Text('Error: $e'))
  );
}
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L76-L83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L76-L83)

### Data Refresh

Most screens implement pull-to-refresh:

```yaml
RefreshIndicator(
  onRefresh: _fetchData,  // Async method that reloads data
  child: SingleChildScrollView(...)
)
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L141-L143](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L141-L143)

---

## Platform Considerations

### Web vs Mobile Differences

The application handles platform-specific behavior:

| Feature | Web Implementation | Mobile Implementation |
| --- | --- | --- |
| **File Downloads** | Uses `html.AnchorElement` with `download` attribute | Uses `path_provider` and `Share` API |
| **Font Loading** | Fallback to basic fonts if Google Fonts fail | Uses `PdfGoogleFonts` for PDF generation |
| **Storage** | Browser localStorage via `SharedPreferences` | Device storage via `SharedPreferences` |
| **Navigation** | Browser back button integration | Hardware back button handling |

**Sources:** [client/lib/core/services/report_service.dart L503-L525](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/report_service.dart#L503-L525)

### Responsive Design

Screens adapt to different screen sizes:

```yaml
GridView.count(
  crossAxisCount: 2,  // 2 columns on mobile
  crossAxisSpacing: 12,
  mainAxisSpacing: 12,
  childAspectRatio: 1.1,
  children: [...]
)
```

**Sources:** [client/lib/presentation/screens/dashboard/student_dashboard.dart L268-L275](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/student_dashboard.dart#L268-L275)

---

## Testing Considerations

The architecture supports testability through:

1. **Dependency Injection**: Services are injected into screens and providers
2. **Provider Pattern**: State can be tested independently of UI
3. **Service Abstraction**: API calls can be mocked via `ApiService` interface
4. **Pure Functions**: Data transformations are testable without UI

**Example Testable Component:**

```python
// Service with injected dependencies
class ApiService {
  final http.Client _client;
  final AuthProvider? _authProvider;
  
  ApiService({
    http.Client? client,
    AuthProvider? authProvider
  }) : _client = client ?? http.Client(),
       _authProvider = authProvider;
}
```

**Sources:** [client/lib/core/services/api_service.dart L7-L10](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart#L7-L10)