# Report Generation

> **Relevant source files**
> * [client/lib/data/models/environment_model.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/environment_model.dart)
> * [client/lib/presentation/screens/reports/report_generator_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart)
> * [server/app/schemas/generated_reports.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/generated_reports.py)
> * [server/requirements.txt](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/requirements.txt)

## Purpose and Scope

This document describes the report generation system, which allows authorized users to create comprehensive PDF and Excel reports from aggregated system data. The system implements role-based report types, configurable parameters, and asynchronous file generation with MinIO storage.

For information about viewing real-time statistics and metrics, see [Statistics Dashboard](/axchisan/GestionInventarioSENA/9.3-statistics-dashboard). For backend report service implementation and APIs, see [Report Service & APIs](/axchisan/GestionInventarioSENA/9.2-report-service-and-apis).

## System Architecture

The report generation system consists of three main layers: the client UI for report configuration, the ReportService for data aggregation and formatting, and backend generators for PDF and Excel output.

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L1-L778](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L1-L778)

 [server/requirements.txt L20-L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/requirements.txt#L20-L40)

 High-Level System Architecture Diagram 5

## Role-Based Report Types

The system provides different report types based on user role, implementing hierarchical access control where higher privilege roles have access to more comprehensive reporting capabilities.

### Report Type Mappings

The `roleBasedReportTypes` map defines available reports per role:

| Role | Report Types | Count |
| --- | --- | --- |
| **supervisor** | inventory_checks, maintenance, environment_status | 3 |
| **admin** | loans, inventory, alerts, maintenance | 4 |
| **admin_general** | users, loans, environments, inventory, maintenance, audit, statistics | 7 |

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L33-L124](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L33-L124)

### Report Type Details

Each report type is defined with an ID, title, description, and icon:

**Supervisor Reports**:

* `inventory_checks`: Verification status by environment
* `maintenance`: Maintenance request status and history
* `environment_status`: List and status of all environments

**Admin Reports**:

* `loans`: Loan requests and borrowing history
* `inventory`: Warehouse equipment status
* `alerts`: Environment alert monitoring
* `maintenance`: Maintenance request tracking

**Admin General Reports**:

* `users`: System user management data
* `loans`: Complete loan history
* `environments`: Environment configuration and status
* `inventory`: System-wide inventory status
* `maintenance`: All maintenance requests
* `audit`: Complete activity audit log
* `statistics`: Comprehensive KPIs and metrics

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L33-L124](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L33-L124)

## Report Configuration

The report generation screen provides multiple configuration options to customize report output.

### Configuration Parameters

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L18-L543](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L18-L543)

### Format Selection

Two output formats are supported:

| Format | Description | Generator |
| --- | --- | --- |
| `pdf` | Portable Document Format | ReportLab library |
| `excel` | Microsoft Excel spreadsheet | Pandas library |

The format is selected via a dropdown at [client/lib/presentation/screens/reports/report_generator_screen.dart L455-L467](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L455-L467)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L20-L467](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L20-L467)

 [server/requirements.txt L24-L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/requirements.txt#L24-L40)

### Date Range Filtering

The `selectedDateRange` parameter allows filtering report data by time period:

* Optional parameter (can be null for all-time data)
* Selected via Material date range picker
* Stored as `DateTimeRange` with start and end dates
* Formatted for display as `dd/MM/yyyy - dd/MM/yyyy`

The date selection logic is implemented in `_selectDateRange()` at [client/lib/presentation/screens/reports/report_generator_screen.dart L699-L709](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L699-L709)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L21-L713](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L21-L713)

### Environment Filtering

Reports can be filtered by environment with role-specific logic:

```

```

The `availableEnvironments` getter implements role-based filtering at [client/lib/presentation/screens/reports/report_generator_screen.dart L181-L188](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L181-L188)

:

* **Admin role**: Only warehouse environments (`env.isWarehouse == true`)
* **Other roles**: All environments

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L22-L538](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L22-L538)

 [client/lib/data/models/environment_model.dart L16-L17](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/data/models/environment_model.dart#L16-L17)

### Additional Options

Two boolean flags control report content:

* `includeImages`: Whether to include images in the report (default: false)
* `includeStatistics`: Whether to include statistical summaries (default: true)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L23-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L23-L24)

## User Interface Components

The `ReportGeneratorScreen` is organized into multiple widget sections for configuration and management.

### Screen Component Hierarchy

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L11-L228](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L11-L228)

### Role Indicator

The `_buildRoleIndicator()` method displays the current user's role with contextual information:

* **Role icon**: Different icons per role (supervisor_account, admin_panel_settings, manage_accounts)
* **Role color**: Visual distinction (blue for supervisor, green for admin, purple for admin_general)
* **Role description**: Summary of accessible report types

Implementation at [client/lib/presentation/screens/reports/report_generator_screen.dart L230-L277](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L230-L277)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L230-L329](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L230-L329)

### Report Type Selection

Each available report type is rendered as an interactive card with:

* **Icon**: Visual identifier for report type
* **Title**: Display name in Spanish
* **Description**: Brief explanation of report contents
* **Selection state**: Visual highlight when selected
* **Check mark**: Indicator for currently selected type

The `_buildReportTypeCard()` method creates selectable cards at [client/lib/presentation/screens/reports/report_generator_screen.dart L370-L428](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L370-L428)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L331-L428](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L331-L428)

### Configuration Section

The configuration card provides dropdowns and pickers for:

1. **Format dropdown**: PDF or Excel selection
2. **Date range picker**: Period selection with formatted display
3. **Environment dropdown**: Environment filtering with role-aware options

Implemented in `_buildConfigurationSection()` at [client/lib/presentation/screens/reports/report_generator_screen.dart L430-L543](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L430-L543)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L430-L543](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L430-L543)

### Generate Button

The `_buildGenerateButton()` method renders a full-width action button with:

* **Loading state**: Circular progress indicator during generation
* **Disabled state**: Prevents multiple simultaneous generations
* **Success/error feedback**: SnackBar messages post-generation

Implementation at [client/lib/presentation/screens/reports/report_generator_screen.dart L545-L584](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L545-L584)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L545-L584](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L545-L584)

### Recent Reports

The recent reports section displays previously generated reports with:

* **Report metadata**: Name, type, date, size
* **Status badge**: Generation status indicator
* **Download button**: Action to retrieve report file
* **Refresh button**: Reload recent reports list

Components implemented in `_buildRecentReports()` and `_buildRecentReportItem()` at [client/lib/presentation/screens/reports/report_generator_screen.dart L586-L697](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L586-L697)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L586-L697](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L586-L697)

## Report Generation Flow

The report generation process involves client-side parameter collection, service layer orchestration, and backend processing with file storage.

### Generation Sequence

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L715-L767](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L715-L767)

 High-Level System Architecture Diagram 5

### Generation Method

The `_generateReport()` method orchestrates the client-side flow:

1. **Set loading state**: `setState(() => isGenerating = true)`
2. **Convert date range**: Create `DateTimeRange` from selected dates
3. **Call service**: `_reportService.generateReport()` with all parameters
4. **Handle response**: Display success/error message
5. **Update UI**: Refresh recent reports list
6. **Reset state**: `setState(() => isGenerating = false)`

Implementation at [client/lib/presentation/screens/reports/report_generator_screen.dart L715-L767](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L715-L767)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L715-L767](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L715-L767)

### Parameter Passing

Report generation parameters are passed to the service:

```yaml
reportType: selectedReportType (String)
format: selectedFormat ('pdf' or 'excel')
dateRange: DateTimeRange? (optional)
environmentId: String? (UUID or null if 'all')
includeImages: bool
includeStatistics: bool
```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L727-L734](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L727-L734)

### Platform-Specific Behavior

The system handles platform differences:

* **Web**: Automatic download trigger, success message
* **Non-Web**: File saved to device, path displayed in dialog

Conditional logic at [client/lib/presentation/screens/reports/report_generator_screen.dart L739-L751](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L739-L751)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L739-L751](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L739-L751)

## Data Sources

Reports aggregate data from multiple database tables based on report type:

| Report Type | Primary Data Sources |
| --- | --- |
| inventory | InventoryItem |
| inventory_checks | InventoryCheck, InventoryCheckItem |
| maintenance | MaintenanceRequest, MaintenanceHistory |
| loans | Loan |
| users | User |
| environments | Environment |
| alerts | Environment (alert conditions) |
| audit | AuditLog |
| statistics | All tables (aggregated) |

**Sources**: High-Level System Architecture Diagram 5

## File Storage and Retrieval

Generated reports are stored in MinIO object storage with metadata tracked in the database.

### Storage Schema

The `GeneratedReport` model tracks report metadata:

```yaml
id: UUID (primary key)
user_id: UUID (foreign key to User)
report_type: str
title: str
file_format: str (pdf/excel)
file_path: str? (MinIO path)
file_size: int? (bytes)
status: str (pending/completed/failed)
parameters: Dict[str, Any]? (JSON)
generated_at: datetime?
expires_at: datetime?
download_count: int
created_at: datetime
```

**Sources**: [server/app/schemas/generated_reports.py L1-L28](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/generated_reports.py#L1-L28)

### Recent Reports

The `getRecentReports()` method retrieves user's recent reports:

* Query backend for user's generated reports
* Sort by creation date (most recent first)
* Display with name, type, date, size, status
* Enable download action for completed reports

Recent reports are loaded on screen initialization and after successful generation at [client/lib/presentation/screens/reports/report_generator_screen.dart L147-L172](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L147-L172)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L27-L697](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L27-L697)

## Service Initialization

The screen initializes services and loads data on startup.

### Initialization Sequence

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L126-L172](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L126-L172)

### Service Dependencies

The screen depends on:

* **AuthProvider**: User authentication and role context
* **ApiService**: HTTP client for backend communication
* **ReportService**: Report-specific API wrapper

Created at [client/lib/presentation/screens/reports/report_generator_screen.dart L133-L145](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L133-L145)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L126-L145](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L126-L145)

### Data Loading

Initial data loading fetches:

1. **Environments**: List of all environments for filtering
2. **Recent reports**: User's previously generated reports

Loading uses `Future.wait()` for parallel requests at [client/lib/presentation/screens/reports/report_generator_screen.dart L151-L154](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L151-L154)

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L147-L172](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L147-L172)

## Error Handling

The screen implements error handling for both data loading and report generation.

### Loading Errors

Data loading errors are caught and displayed:

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L161-L171](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L161-L171)

### Generation Errors

Report generation errors reset state and display feedback:

```

```

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L755-L765](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L755-L765)

## Related Components

The report generation system integrates with:

* **ReportService**: Client-side service for API communication (see [Report Service & APIs](/axchisan/GestionInventarioSENA/9.2-report-service-and-apis))
* **Statistics Dashboard**: Real-time data visualization alternative (see [Statistics Dashboard](/axchisan/GestionInventarioSENA/9.3-statistics-dashboard))
* **MinIO Storage**: Object storage for generated files
* **ReportLab**: PDF generation library
* **Pandas**: Excel generation library

**Sources**: [client/lib/presentation/screens/reports/report_generator_screen.dart L7-L136](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/reports/report_generator_screen.dart#L7-L136)

 [server/requirements.txt L20-L40](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/requirements.txt#L20-L40)