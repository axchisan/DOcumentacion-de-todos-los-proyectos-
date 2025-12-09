# Statistics Dashboard

> **Relevant source files**
> * [client/lib/core/services/api_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart)
> * [client/lib/core/services/report_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/report_service.dart)
> * [client/lib/data/models/environment_model.g.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/environment_model.g.dart)
> * [client/lib/presentation/screens/dashboard/instructor_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/instructor_dashboard.dart)
> * [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart)
> * [client/lib/presentation/screens/statistics/statistics_dashboard.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart)

## Purpose and Scope

The Statistics Dashboard provides a real-time analytics interface with role-specific metrics, visualizations, and insights for monitoring system performance. It displays comprehensive statistics about inventory, loans, maintenance requests, verification checks, users, and environments based on the authenticated user's role.

This page documents the client-side statistics dashboard screen and its data aggregation logic. For generating downloadable reports, see [Report Generation](/axchisan/GestionInventarioSENA/9.1-report-generation). For backend report generation APIs, see [Report Service & APIs](/axchisan/GestionInventarioSENA/9.2-report-service-and-apis).

---

## System Architecture

The Statistics Dashboard is a Flutter screen that aggregates data from multiple API endpoints and presents role-specific visualizations. It operates independently from the report generation system but shares similar data sources.

```mermaid
flowchart TD

SD["StatisticsDashboard<br>Widget"]
AUTH["AuthProvider<br>User Role"]
API["ApiService<br>HTTP Client"]
LOAD["_loadStatistics()"]
INV["_loadInventoryStatistics()"]
LOAN["_loadLoanStatistics()"]
MAINT["_loadMaintenanceStatistics()"]
CHECK["_loadCheckStatistics()"]
USER["_loadUserStatistics()"]
ENV["_loadEnvironmentStatistics()"]
MONTHLY["_loadMonthlyLoanData()"]
CAT["_loadCategoryDistribution()"]
INVAPI["/api/inventory"]
LOANAPI["/api/loans"]
MAINTAPI["/api/maintenance-requests"]
CHECKAPI["/api/inventory-checks"]
USERAPI["/api/users"]
ENVAPI["/api/environments"]
METRICS["_buildMetricsSection()"]
CHARTS["Chart Widgets"]
PERIOD["Period Filter"]
CARDS["Metric Cards"]
BAR["Bar Charts"]
PIE["Pie Charts"]

SD --> LOAD
INV --> INVAPI
LOAN --> LOANAPI
MAINT --> MAINTAPI
CHECK --> CHECKAPI
USER --> USERAPI
ENV --> ENVAPI
SD --> METRICS
SD --> CHARTS
SD --> PERIOD
API --> INV
API --> LOAN
API --> MAINT
API --> CHECK
API --> USER
API --> ENV

subgraph subGraph3 ["Display Components"]
    METRICS
    CHARTS
    PERIOD
    CARDS
    BAR
    PIE
    METRICS --> CARDS
    CHARTS --> BAR
    CHARTS --> PIE
end

subgraph subGraph2 ["Backend APIs"]
    INVAPI
    LOANAPI
    MAINTAPI
    CHECKAPI
    USERAPI
    ENVAPI
end

subgraph subGraph1 ["Data Aggregation"]
    LOAD
    INV
    LOAN
    MAINT
    CHECK
    USER
    ENV
    MONTHLY
    CAT
    LOAD --> INV
    LOAD --> LOAN
    LOAD --> MAINT
    LOAD --> CHECK
    LOAD --> USER
    LOAD --> ENV
    LOAD --> MONTHLY
    LOAD --> CAT
end

subgraph subGraph0 ["Client Layer"]
    SD
    AUTH
    API
    AUTH --> SD
end
```

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1-L1800](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1-L1800)
* [client/lib/core/services/api_service.dart L1-L720](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart#L1-L720)

---

## Role-Based Configuration

The dashboard uses a `roleBasedContent` map to define what metrics, charts, and sections each user role can access. This configuration drives the entire UI rendering logic.

### Configuration Structure

```mermaid
flowchart TD

RBC["roleBasedContent<br>Map<String, Map>"]
SUP["supervisor"]
ADM["admin"]
ADM_GEN["admin_general"]
SUPTITLE["title: Panel de Supervisor"]
SUPPRIM["primaryMetrics:<br>checkStats, maintenanceStats"]
SUPCHARTS["charts:<br>monthlyChecks, maintenanceByPriority"]
SUPSECT["sections:<br>verification_summary,<br>maintenance_overview"]
ADMTITLE["title: Panel de Administrador"]
ADMPRIM["primaryMetrics:<br>loanStats, inventoryStats"]
ADMCHARTS["charts:<br>monthlyLoans, categoryDistribution"]
ADMSECT["sections:<br>loan_management,<br>inventory_overview"]
AGTITLE["title: Panel de Administrador General"]
AGPRIM["primaryMetrics:<br>userStats, loanStats,<br>inventoryStats, maintenanceStats"]
AGCHARTS["charts:<br>monthlyLoans, categoryDistribution,<br>userActivity"]
AGSECT["sections:<br>system_overview,<br>user_management"]

SUP --> SUPTITLE
SUP --> SUPPRIM
SUP --> SUPCHARTS
SUP --> SUPSECT
ADM --> ADMTITLE
ADM --> ADMPRIM
ADM --> ADMCHARTS
ADM --> ADMSECT
ADM_GEN --> AGTITLE
ADM_GEN --> AGPRIM
ADM_GEN --> AGCHARTS
ADM_GEN --> AGSECT

subgraph subGraph3 ["Admin General Config"]
    AGTITLE
    AGPRIM
    AGCHARTS
    AGSECT
end

subgraph subGraph2 ["Admin Config"]
    ADMTITLE
    ADMPRIM
    ADMCHARTS
    ADMSECT
end

subgraph subGraph1 ["Supervisor Config"]
    SUPTITLE
    SUPPRIM
    SUPCHARTS
    SUPSECT
end

subgraph subGraph0 ["Role Configuration Map"]
    RBC
    SUP
    ADM
    ADM_GEN
    RBC --> SUP
    RBC --> ADM
    RBC --> ADM_GEN
end
```

The configuration is defined at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L40-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L40-L62)

:

| Role | Primary Metrics | Charts | Sections |
| --- | --- | --- | --- |
| `supervisor` | checkStats, maintenanceStats | monthlyChecks, maintenanceByPriority | verification_summary, maintenance_overview, environment_status |
| `admin` | loanStats, inventoryStats | monthlyLoans, categoryDistribution | loan_management, inventory_overview, alerts_monitoring |
| `admin_general` | userStats, loanStats, inventoryStats, maintenanceStats | monthlyLoans, categoryDistribution, userActivity | system_overview, user_management, complete_statistics |

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L40-L62](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L40-L62)

---

## Data Loading Pipeline

The statistics dashboard loads data through a coordinated pipeline that fetches from multiple endpoints and aggregates results based on the selected time period.

```mermaid
sequenceDiagram
  participant StatisticsDashboard
  participant _loadStatistics()
  participant ApiService
  participant FastAPI Backend

  StatisticsDashboard->>_loadStatistics(): User changes period or refreshes
  _loadStatistics()->>_loadStatistics(): Calculate startDate and endDate
  _loadStatistics()->>_loadStatistics(): Build queryParams with dates
  loop [Parallel Data Fetching]
    _loadStatistics()->>ApiService: _loadInventoryStatistics()
    ApiService->>FastAPI Backend: GET /api/inventory?start_date=X&end_date=Y
    FastAPI Backend-->>ApiService: List[InventoryItem]
    ApiService-->>_loadStatistics(): inventoryStats
    _loadStatistics()->>ApiService: _loadLoanStatistics()
    ApiService->>FastAPI Backend: GET /api/loans?start_date=X&end_date=Y
    FastAPI Backend-->>ApiService: List[Loan] or Stats Map
    ApiService-->>_loadStatistics(): loanStats
    _loadStatistics()->>ApiService: _loadMaintenanceStatistics()
    ApiService->>FastAPI Backend: GET /api/maintenance-requests?start_date=X&end_date=Y
    FastAPI Backend-->>ApiService: List[MaintenanceRequest] or Stats Map
    ApiService-->>_loadStatistics(): maintenanceStats
    _loadStatistics()->>ApiService: _loadCheckStatistics()
    ApiService->>FastAPI Backend: GET /api/inventory-checks?start_date=X&end_date=Y
    FastAPI Backend-->>ApiService: List[InventoryCheck]
    ApiService-->>_loadStatistics(): checkStats
    _loadStatistics()->>ApiService: _loadUserStatistics()
    _loadStatistics()->>ApiService: _loadEnvironmentStatistics()
    _loadStatistics()->>ApiService: _loadMonthlyLoanData()
    _loadStatistics()->>ApiService: _loadCategoryDistribution()
    _loadStatistics()->>ApiService: _loadTopItems()
  end
  _loadStatistics()->>StatisticsDashboard: setState() with aggregated stats
  StatisticsDashboard->>StatisticsDashboard: _buildRoleBasedContent()
```

### Statistics Aggregation Methods

Each `_load*Statistics()` method follows a consistent pattern:

1. **Fetch data** from API endpoint with query parameters
2. **Handle response type** (List or Map) - APIs may return raw lists or pre-aggregated statistics
3. **Parse models** - Convert JSON to typed models (InventoryItemModel, LoanModel, etc.)
4. **Aggregate metrics** - Calculate totals, counts, percentages
5. **Return statistics map** - Consistent key-value structure for rendering

Example from [client/lib/presentation/screens/statistics/statistics_dashboard.dart L178-L206](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L178-L206)

:

```javascript
Future<Map<String, dynamic>> _loadInventoryStatistics(Map<String, String> queryParams) async {
  final inventoryData = await _apiService.get(inventoryEndpoint, queryParams: queryParams);
  List<InventoryItemModel> items = [];
  for (var item in inventoryData) {
    items.add(InventoryItemModel.fromJson(item));
  }
  return {
    'total_items': items.length,
    'available_items': items.where((i) => i.status == 'available').length,
    'damaged_items': items.where((i) => i.status == 'damaged').length,
    'missing_items': items.where((i) => i.status == 'missing').length,
    'in_maintenance': items.where((i) => i.status == 'maintenance').length,
    'needs_maintenance': items.where((i) => i.needsMaintenance).length,
  };
}
```

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L77-L176](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L77-L176)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L178-L699](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L178-L699)

---

## Period Filtering

The dashboard supports temporal analysis through a period selector dropdown with four predefined ranges:

| Period | Duration | Start Date Calculation |
| --- | --- | --- |
| Última semana | 7 days | `endDate.subtract(Duration(days: 7))` |
| Último mes | 30 days | `endDate.subtract(Duration(days: 30))` |
| Últimos 3 meses | 90 days | `endDate.subtract(Duration(days: 90))` |
| Último año | 365 days | `endDate.subtract(Duration(days: 365))` |

The selected period is stored in `_selectedPeriod` state variable and triggers a full data reload when changed at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L778-L782](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L778-L782)

```mermaid
flowchart TD

DROPDOWN["DropdownButtonFormField<String><br>_selectedPeriod"]
CHANGE["onChanged callback"]
CALC["Calculate startDate<br>based on period"]
QUERY["Build queryParams:<br>{start_date, end_date}"]
RELOAD["_loadStatistics()"]
FETCH["Fetch all statistics<br>with date filters"]

DROPDOWN --> CHANGE
CHANGE --> CALC
CALC --> QUERY
QUERY --> RELOAD
RELOAD --> FETCH
FETCH --> DROPDOWN
```

The query parameters are formatted as ISO 8601 date strings and passed to all API endpoints at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L102-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L102-L105)

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L23](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L23-L23)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L82-L105](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L82-L105)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L743-L794](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L743-L794)

---

## Metric Display System

The dashboard displays key metrics as card-based widgets using the `_buildStatCard()` method. Each metric card shows:

* **Value** - The numeric statistic (large, bold)
* **Title** - Label describing the metric (small, gray)
* **Icon** - Visual indicator with color coding
* **Change indicator** - Percentage change vs baseline (calculated via `_calculateChange()`)

### Metric Card Structure

```mermaid
flowchart TD

CARD["Card Widget"]
PADDING["Padding: 16px"]
COLUMN["Column<br>mainAxisAlignment: center"]
ICON["Icon<br>size: 32, color: roleColor"]
SPACER1["SizedBox height: 8"]
VALUE["Text: value<br>fontSize: 24, bold"]
TITLE["Text: title<br>fontSize: 12, gray"]

subgraph subGraph0 ["Metric Card Layout"]
    CARD
    PADDING
    COLUMN
    ICON
    SPACER1
    VALUE
    TITLE
    CARD --> PADDING
    PADDING --> COLUMN
    COLUMN --> ICON
    COLUMN --> SPACER1
    COLUMN --> VALUE
    COLUMN --> TITLE
end
```

### Role-Specific Metric Grids

The `_buildMetricsSection()` method at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L918-L1074](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L918-L1074)

 renders different metric sets based on user role:

**Supervisor Metrics:**

* Verificaciones (total_checks) - Primary metric for verification workflow
* Cumplimiento (compliance_rate) - Percentage of completed checks
* Mantenimientos (total_requests) - Maintenance request count
* Ambientes (total_environments) - Environment count

**Admin Metrics:**

* Préstamos Totales (total_loans) - All loan records
* Items Disponibles (available_items) - Available inventory
* Préstamos Activos (active_loans) - Currently active loans
* Items en Mantenimiento (in_maintenance) - Items under repair

**Admin General Metrics:**

* Usuarios Totales (total_users) - System-wide user count
* Préstamos Totales (total_loans) - All loans
* Items Disponibles (available_items) - Available inventory
* Mantenimientos (total_requests) - Maintenance requests

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L918-L1074](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L918-L1074)

---

## Chart Visualizations

The dashboard uses the `fl_chart` package to render interactive bar and pie charts. Charts are generated dynamically based on aggregated data and user role.

### Bar Chart Implementation

Monthly loan/check trends are displayed using `BarChart` widgets from `fl_chart`:

```mermaid
flowchart TD

DATA["monthlyLoans or monthlyChecks<br>List<Map<String, dynamic>>"]
CHART["BarChart Widget"]
CONFIG["BarChartData configuration"]
MAXCALC["Calculate maxY:<br>max(counts) * 1.2"]
GROUPS["Map to BarChartGroupData"]
TITLES["FlTitlesData:<br>bottomTitles shows months"]
BARS["barGroups:<br>one per month"]
TOUCH["BarTouchData:<br>enabled: false"]

CHART --> TITLES
CHART --> BARS
CHART --> TOUCH

subgraph subGraph1 ["Chart Components"]
    TITLES
    BARS
    TOUCH
end

subgraph subGraph0 ["Bar Chart Data Flow"]
    DATA
    CHART
    CONFIG
    MAXCALC
    GROUPS
    DATA --> MAXCALC
    DATA --> GROUPS
    MAXCALC --> CONFIG
    GROUPS --> CONFIG
    CONFIG --> CHART
end
```

The implementation at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1361-L1454](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1361-L1454)

 creates bar charts with:

* **X-axis**: Month abbreviations (Ene, Feb, Mar, etc.)
* **Y-axis**: Count values (auto-scaled to 120% of max)
* **Bar color**: Role-specific (blue for supervisor, AppColors.primary for admin)
* **Touch interaction**: Disabled for performance

### Category Distribution Pie Chart

The `_buildCategoryDistributionChart()` method at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1456-L1554](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1456-L1554)

 renders a pie chart showing inventory distribution by category:

```css
PieChart(
  PieChartData(
    sections: categoryDistribution.map((category) {
      return PieChartSectionData(
        value: category['percentage'].toDouble(),
        title: '${category['category']}\n${category['percentage']}%',
        color: _getCategoryColor(category['category']),
        radius: 80,
      );
    }).toList(),
  ),
)
```

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1076-L1169](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1076-L1169)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1361-L1554](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1361-L1554)

---

## Role-Specific Dashboard Sections

The `_buildRoleBasedContent()` method at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L883-L916](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L883-L916)

 constructs the dashboard layout based on the authenticated user's role.

### Supervisor Dashboard

```mermaid
flowchart TD

ROLE["Role Indicator:<br>Panel de Supervisor"]
PERIOD["Period Filter"]
METRICS["Metrics Section:<br>4 stat cards"]
CHART1["Monthly Checks Chart:<br>Bar chart"]
ENVSTAT["Environment Status Section:<br>4 status cards"]
M1["Verificaciones<br>checkStats.total_checks"]
M2["Cumplimiento<br>checkStats.compliance_rate"]
M3["Mantenimientos<br>maintenanceStats.total_requests"]
M4["Ambientes<br>environmentStats.total_environments"]
E1["Total Environments"]
E2["Active Environments"]
E3["Warehouses"]
E4["Classrooms"]

METRICS --> M1
METRICS --> M2
METRICS --> M3
METRICS --> M4
ENVSTAT --> E1
ENVSTAT --> E2
ENVSTAT --> E3
ENVSTAT --> E4

subgraph subGraph2 ["Environment Cards"]
    E1
    E2
    E3
    E4
end

subgraph subGraph1 ["Metric Cards"]
    M1
    M2
    M3
    M4
end

subgraph subGraph0 ["Supervisor Dashboard Layout"]
    ROLE
    PERIOD
    METRICS
    CHART1
    ENVSTAT
    ROLE --> PERIOD
    PERIOD --> METRICS
    METRICS --> CHART1
    CHART1 --> ENVSTAT
end
```

The environment status section is rendered by `_buildEnvironmentStatusSection()` at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1171-L1233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1171-L1233)

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L895-L898](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L895-L898)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1171-L1233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1171-L1233)

### Admin Dashboard

```mermaid
flowchart TD

ROLE2["Role Indicator:<br>Panel de Administrador"]
PERIOD2["Period Filter"]
METRICS2["Metrics Section:<br>4 stat cards"]
CHART2["Monthly Loans Chart:<br>Bar chart"]
PIE["Category Distribution:<br>Pie chart"]
TOP["Top Items Section:<br>List widget"]
A1["Préstamos Totales<br>loanStats.total_loans"]
A2["Items Disponibles<br>inventoryStats.available_items"]
A3["Préstamos Activos<br>loanStats.active_loans"]
A4["Items en Mantenimiento<br>inventoryStats.in_maintenance"]

METRICS2 --> A1
METRICS2 --> A2
METRICS2 --> A3
METRICS2 --> A4

subgraph subGraph1 ["Metric Cards"]
    A1
    A2
    A3
    A4
end

subgraph subGraph0 ["Admin Dashboard Layout"]
    ROLE2
    PERIOD2
    METRICS2
    CHART2
    PIE
    TOP
    ROLE2 --> PERIOD2
    PERIOD2 --> METRICS2
    METRICS2 --> CHART2
    CHART2 --> PIE
    PIE --> TOP
end
```

The admin role focuses on warehouse operations, showing loan trends and inventory distribution.

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L899-L910](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L899-L910)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L971-L1020](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L971-L1020)

### Admin General Dashboard

```mermaid
flowchart TD

ROLE3["Role Indicator:<br>Panel de Administrador General"]
PERIOD3["Period Filter"]
METRICS3["Metrics Section:<br>4 stat cards"]
CHART3["Monthly Loans Chart"]
PIE2["Category Distribution"]
TOP2["Top Items Section"]
USERMGMT["User Management Section:<br>4 user type cards"]
AG1["Usuarios Totales<br>userStats.total_users"]
AG2["Préstamos Totales<br>loanStats.total_loans"]
AG3["Items Disponibles<br>inventoryStats.available_items"]
AG4["Mantenimientos<br>maintenanceStats.total_requests"]
U1["Estudiantes<br>userStats.students"]
U2["Instructores<br>userStats.instructors"]
U3["Administradores<br>userStats.admins"]
U4["Usuarios Activos<br>userStats.active_users"]

METRICS3 --> AG1
METRICS3 --> AG2
METRICS3 --> AG3
METRICS3 --> AG4
USERMGMT --> U1
USERMGMT --> U2
USERMGMT --> U3
USERMGMT --> U4

subgraph subGraph2 ["User Type Cards"]
    U1
    U2
    U3
    U4
end

subgraph subGraph1 ["Metric Cards"]
    AG1
    AG2
    AG3
    AG4
end

subgraph subGraph0 ["Admin General Dashboard Layout"]
    ROLE3
    PERIOD3
    METRICS3
    CHART3
    PIE2
    TOP2
    USERMGMT
    ROLE3 --> PERIOD3
    PERIOD3 --> METRICS3
    METRICS3 --> CHART3
    CHART3 --> PIE2
    PIE2 --> TOP2
    TOP2 --> USERMGMT
end
```

The admin_general role has the most comprehensive view, including user management statistics rendered by `_buildUserManagementSection()` at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1266-L1328](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1266-L1328)

**Sources:**

* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L905-L913](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L905-L913)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1021-L1070](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1021-L1070)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L1266-L1328](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L1266-L1328)

---

## Dashboard Navigation Access

The statistics dashboard is accessible from the supervisor and admin dashboards via action cards:

**Supervisor Dashboard**: Linked at [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L510-L517](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L510-L517)

 with the label "Estadísticas" and route `/statistics-dashboard`.

**Admin General Dashboard**: Similar navigation available through the dashboard grid.

The dashboard requires authentication and role-based authorization. The `StatisticsDashboard` widget initializes the `ApiService` with the `AuthProvider` at [client/lib/presentation/screens/statistics/statistics_dashboard.dart L71-L75](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L71-L75)

 to ensure all API requests include the bearer token.

**Sources:**

* [client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart L510-L517](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/dashboard/supervisor_dashboard_screen.dart#L510-L517)
* [client/lib/presentation/screens/statistics/statistics_dashboard.dart L64-L75](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/statistics/statistics_dashboard.dart#L64-L75)