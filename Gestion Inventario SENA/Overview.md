# Overview

> **Relevant source files**
> * [client/lib/core/constants/api_constants.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart)
> * [client/lib/core/services/session_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart)
> * [client/lib/presentation/screens/environment/environment_overview_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart)
> * [client/lib/presentation/screens/environment/manage_schedules_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/manage_schedules_screen.dart)
> * [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart)
> * [client/lib/presentation/screens/inventory/inventory_check_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart)
> * [client/lib/presentation/screens/qr/qr_code_generator_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart)
> * [server/.env](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.env)
> * [server/.gitignore](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.gitignore)
> * [server/app/config.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/config.py)
> * [server/app/database.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/database.py)
> * [server/app/main.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py)
> * [server/app/routers/inventory_checks.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py)
> * [server/docker-compose.yml](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/docker-compose.yml)

## Purpose and Scope

The SENA Inventory Management System is a comprehensive web and mobile application designed to manage educational equipment and physical environments (classrooms, laboratories, warehouses) at SENA (Servicio Nacional de Aprendizaje) institutions. The system provides inventory tracking, verification workflows, maintenance request management, equipment loans, QR code scanning, and compliance auditing across multiple locations.

This page provides a high-level overview of the system architecture, technology stack, key capabilities, and deployment configuration. For detailed information about specific subsystems:

* Client application architecture and services → See [Client Application (Flutter)](/axchisan/GestionInventarioSENA/2.1-client-application-(flutter))
* Backend API structure and routers → See [Backend API (FastAPI)](/axchisan/GestionInventarioSENA/2.2-backend-api-(fastapi))
* Authentication flows and JWT management → See [Authentication & Authorization](/axchisan/GestionInventarioSENA/3-authentication-and-authorization)
* Role-based permissions and access control → See [Role-Based Access Control](/axchisan/GestionInventarioSENA/3.3-role-based-access-control)
* Inventory verification workflows → See [Inventory Verification System](/axchisan/GestionInventarioSENA/5-inventory-verification-system)

Sources: Derived from repository structure analysis and high-level system diagrams.

---

## System Architecture

The SENA Inventory Management System implements a **three-tier client-server architecture** with clear separation between presentation, business logic, and data layers.

```mermaid
flowchart TD

FlutterApp["Flutter Application<br>(client/lib/)"]
AuthProvider["AuthProvider<br>(client/lib/presentation/providers/auth_provider.dart)"]
ApiService["ApiService<br>(client/lib/core/services/api_service.dart)"]
SessionService["SessionService<br>(client/lib/core/services/session_service.dart)"]
FastAPIApp["FastAPI Application<br>(server/app/main.py)"]
AuditMiddleware["AuditMiddleware<br>(server/app/middleware/audit_middleware.py)"]
AuthRouter["auth.router<br>(/api/auth)"]
InventoryRouter["inventory.router<br>(/api/inventory)"]
ChecksRouter["inventory_checks.router<br>(/api/inventory-checks)"]
MaintenanceRouter["maintenance_requests.router<br>(/api/maintenance-requests)"]
LoansRouter["loans.router<br>(/api/loans)"]
ReportsRouter["reports.router<br>(/api/reports)"]
OtherRouters["environments, users, qr,<br>notifications, audit_logs,<br>feedback, schedules, stats"]
PostgreSQL["PostgreSQL Database<br>(SQLAlchemy ORM)"]
MinIO["MinIO Object Storage<br>(Reports & Files)"]

ApiService --> FastAPIApp
AuthRouter --> PostgreSQL
InventoryRouter --> PostgreSQL
ChecksRouter --> PostgreSQL
MaintenanceRouter --> PostgreSQL
LoansRouter --> PostgreSQL
ReportsRouter --> PostgreSQL
ReportsRouter --> MinIO
OtherRouters --> PostgreSQL
AuditMiddleware --> PostgreSQL

subgraph subGraph2 ["Data Tier"]
    PostgreSQL
    MinIO
end

subgraph subGraph1 ["Application Tier"]
    FastAPIApp
    AuditMiddleware
    AuthRouter
    InventoryRouter
    ChecksRouter
    MaintenanceRouter
    LoansRouter
    ReportsRouter
    OtherRouters
    FastAPIApp --> AuditMiddleware
    FastAPIApp --> AuthRouter
    FastAPIApp --> InventoryRouter
    FastAPIApp --> ChecksRouter
    FastAPIApp --> MaintenanceRouter
    FastAPIApp --> LoansRouter
    FastAPIApp --> ReportsRouter
    FastAPIApp --> OtherRouters
end

subgraph subGraph0 ["Client Tier"]
    FlutterApp
    AuthProvider
    ApiService
    SessionService
    FlutterApp --> AuthProvider
    FlutterApp --> ApiService
    ApiService --> SessionService
end
```

**Architecture Description:**

* **Client Tier**: Flutter-based mobile/web application providing cross-platform UI * `AuthProvider` manages authentication state using Provider pattern * `ApiService` handles all HTTP communication with backend * `SessionService` persists tokens and user data in SharedPreferences
* **Application Tier**: FastAPI backend with router-based modular organization * `main.py` configures CORS, middleware, and mounts 14+ routers * `AuditMiddleware` automatically logs all requests for compliance * Routers handle business logic for specific domains (inventory, checks, loans, etc.)
* **Data Tier**: PostgreSQL for relational data, MinIO for file storage * SQLAlchemy ORM provides database abstraction * MinIO stores generated reports (PDF/Excel)

Sources: [server/app/main.py L1-L44](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L1-L44)

 [client/lib/core/services/api_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/api_service.dart)

 [client/lib/core/services/session_service.dart L1-L65](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart#L1-L65)

 [client/lib/core/constants/api_constants.dart L1-L19](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart#L1-L19)

---

## Technology Stack

### Backend Technologies

| Component | Technology | Purpose |
| --- | --- | --- |
| **Framework** | FastAPI | High-performance async REST API framework |
| **Language** | Python 3.x | Backend application logic |
| **ORM** | SQLAlchemy | Database abstraction and migrations |
| **Database** | PostgreSQL | Primary relational data store |
| **Object Storage** | MinIO | Report and file storage |
| **Authentication** | JWT (HS256) | Token-based authentication |
| **Password Hashing** | Bcrypt/Argon2 | Secure password storage |
| **PDF Generation** | ReportLab | Programmatic PDF creation |
| **Excel Processing** | Pandas | Excel report generation |
| **Containerization** | Docker | Application deployment |
| **Timezone** | pytz (America/Bogota) | Colombian timezone handling |

### Frontend Technologies

| Component | Technology | Purpose |
| --- | --- | --- |
| **Framework** | Flutter | Cross-platform UI framework |
| **Language** | Dart | Application development |
| **State Management** | Provider | Reactive state management |
| **Routing** | go_router | Declarative routing |
| **HTTP Client** | http package | API communication |
| **Local Storage** | SharedPreferences | Session persistence |
| **QR Scanning** | qr_code_scanner | QR code reading |
| **QR Generation** | qr_flutter | QR code generation |
| **JWT Handling** | jwt_decoder | Token validation |
| **Date Formatting** | intl | Internationalization |

### Configuration & Deployment

```mermaid
flowchart TD

EnvFile[".env File"]
Settings["Settings<br>(server/app/config.py)"]
Database["PostgreSQL<br>(port 5433)"]
Backend["FastAPI Backend<br>(port 8001)"]
Docker["docker-compose.yml"]

EnvFile --> Settings
Settings --> Database
Docker --> Backend
Backend --> Database
```

**Configuration Details:**

* **Database**: PostgreSQL hosted at `147.93.178.204:5433`
* **Backend Port**: 8001 (configurable via `APP_PORT`)
* **JWT Secret**: Configured via `SECRET_KEY` environment variable
* **Token Expiration**: 30 minutes (`ACCESS_TOKEN_EXPIRE`)
* **Algorithm**: HS256 for JWT signing
* **Client Base URL**: `https://senainventario.axchisan.com`

Sources: [server/app/config.py L1-L27](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/config.py#L1-L27)

 [server/.env L1-L7](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.env#L1-L7)

 [server/docker-compose.yml L1-L20](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/docker-compose.yml#L1-L20)

 [client/lib/core/constants/api_constants.dart L1](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart#L1-L1)

---

## API Endpoint Map

The backend exposes 14 primary routers with 50+ endpoints organized by domain.

```mermaid
flowchart TD

BaseURL["/api"]
Auth["/auth<br>login, register, me"]
Environments["/environments<br>CRUD operations"]
Inventory["/inventory<br>items CRUD"]
Checks["/inventory-checks<br>verification workflow"]
CheckItems["/inventory-check-items<br>item-level checks"]
Maintenance["/maintenance-requests<br>repair tracking"]
MaintenanceHistory["/maintenance-history<br>audit trail"]
Loans["/loans<br>equipment borrowing"]
QR["/qr<br>generate QR codes"]
Schedules["/schedules<br>class schedules"]
Users["/users<br>user management"]
Notifications["/notifications<br>alerts & events"]
Reports["/reports<br>PDF/Excel generation"]
AuditLogs["/audit-logs<br>compliance tracking"]
Feedback["/feedback<br>user feedback"]
Stats["/stats<br>analytics & metrics"]
SystemAlerts["/system-alerts<br>critical notifications"]
SupervisorReviews["/supervisor-reviews<br>approval workflow"]
AlertSettings["/alert-settings<br>notification config"]

BaseURL --> Auth
BaseURL --> Environments
BaseURL --> Inventory
BaseURL --> Checks
BaseURL --> CheckItems
BaseURL --> Maintenance
BaseURL --> MaintenanceHistory
BaseURL --> Loans
BaseURL --> QR
BaseURL --> Schedules
BaseURL --> Users
BaseURL --> Notifications
BaseURL --> Reports
BaseURL --> AuditLogs
BaseURL --> Feedback
BaseURL --> Stats
BaseURL --> SystemAlerts
BaseURL --> SupervisorReviews
BaseURL --> AlertSettings
```

**Key Endpoint Constants** (defined in `api_constants.dart`):

* `baseUrl`: `'https://senainventario.axchisan.com'`
* `loginEndpoint`: `'/api/auth/login'`
* `registerEndpoint`: `'/api/auth/register'`
* `inventoryEndpoint`: `'/api/inventory/'`
* `inventoryChecksEndpoint`: `'/api/inventory-checks/'`
* `maintenanceRequestsEndpoint`: `'/api/maintenance-requests/'`
* `loansEndpoint`: `'/api/loans/'`
* `reportsEndpoint`: `'/api/reports/'`
* `auditLogsEndpoint`: `'/api/audit-logs/'`

Sources: [server/app/main.py L20-L39](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L20-L39)

 [client/lib/core/constants/api_constants.dart L1-L19](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart#L1-L19)

---

## Key System Capabilities

### 1. Inventory Management

* Full CRUD operations for inventory items
* Support for individual items and item groups
* Category-based organization (computers, projectors, keyboards, etc.)
* Status tracking (available, in_use, maintenance, damaged, lost)
* Quantity management with damaged/missing counts
* Serial number and purchase information tracking

### 2. Inventory Verification Workflow

* **Three-stage approval chain**: Student → Instructor → Supervisor
* Scheduled verification by class period
* Item-by-item verification with status updates
* Cleanliness and organization assessment
* Automatic verification total calculations
* Status transitions: `pending` → `instructor_review` → `supervisor_review` → `complete`
* Issue flagging for damaged/missing items

### 3. Maintenance Request System

* User-initiated repair requests for damaged equipment
* Priority levels (low, medium, high, urgent)
* Status workflow: `pending` → `assigned` → `in_progress` → `completed`/`cancelled`
* Assignment to technicians by supervisors
* History tracking for all status changes
* Notification generation for status updates

### 4. QR Code System

* **Generation**: Create QR codes for items and environments
* **Scanning**: Dual-mode scanner (identify vs. link environment)
* **Payload structure**: Version, type, ID, code, timestamp, SHA256 signature
* Export options: PNG save, print to PDF, share

### 5. Equipment Loans

* Loan request submission by users
* Admin approval workflow
* Status tracking: `pending` → `approved`/`rejected` → `active` → `completed`
* Date range management (start_date, end_date)
* Warehouse integration for bulk equipment

### 6. Reporting & Analytics

* **9 Report Types**: Inventory, Verification, Maintenance, Loans, Audit, Statistics, Alerts, Users, Environments
* **Dual Formats**: PDF (ReportLab) and Excel (Pandas)
* **Statistics Dashboard**: Role-specific metrics with charts
* **Period Filtering**: 7-day, 30-day, 90-day, custom ranges
* Asynchronous report generation with MinIO storage

### 7. Audit & Compliance

* Automatic request logging via `AuditMiddleware`
* Comprehensive audit trails (user, action, resource, timestamp)
* Sensitive data filtering (passwords, tokens)
* Audit log viewing with pagination
* Export functionality for compliance reporting

### 8. Notification System

* Event-driven alerts for workflow changes
* Notification types: `verification_pending`, `verification_update`, `maintenance_request`, etc.
* Priority levels (low, medium, high)
* Read/unread tracking
* In-app notification badge display

Sources: [client/lib/presentation/screens/inventory/inventory_check_screen.dart L1-L1000](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart#L1-L1000)

 [server/app/routers/inventory_checks.py L1-L656](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L1-L656)

 [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L1-L601](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L1-L601)

 [client/lib/presentation/screens/environment/environment_overview_screen.dart L1-L1000](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart#L1-L1000)

---

## User Roles and Access Control

The system implements **hierarchical role-based access control (RBAC)** with five distinct user roles, each with progressively expanding permissions.

```mermaid
flowchart TD

Student["Student (role: 'student')<br>11 accessible routes"]
Instructor["Instructor (role: 'instructor')<br>15 accessible routes"]
Supervisor["Supervisor (role: 'supervisor')<br>17 accessible routes"]
Admin["Admin (role: 'admin')<br>22 accessible routes"]
AdminGeneral["Admin General (role: 'admin_general')<br>24 accessible routes"]
StudentOps["• QR Scanning<br>• Inventory Verification (initiate)<br>• View Own Checks<br>• Submit Maintenance Requests<br>• Request Equipment Loans"]
InstructorOps["• Confirm Student Verifications<br>• Manage Class Schedules<br>• View Environment Stats<br>• Access Loan Management"]
SupervisorOps["• Final Verification Approval<br>• Assign Maintenance Tasks<br>• User Management (limited)<br>• View/Generate Reports<br>• Access Audit Logs"]
AdminOps["• Warehouse Management<br>• Full User CRUD<br>• Loan Approvals<br>• System Alerts Config<br>• Environment Management"]
AdminGeneralOps["• Multi-Environment Access<br>• System-Wide Oversight<br>• All Reports & Analytics<br>• Feedback Management<br>• Complete System Control"]

Student --> Instructor
Instructor --> Supervisor
Supervisor --> Admin
Admin --> AdminGeneral
Student --> StudentOps
Instructor --> InstructorOps
Supervisor --> SupervisorOps
Admin --> AdminOps
AdminGeneral --> AdminGeneralOps
```

### Role Permission Summary

| Role | Environment Scope | Key Permissions | Primary Dashboard |
| --- | --- | --- | --- |
| **student** | Single assigned environment | Verify inventory, submit requests | Student dashboard with pending tasks |
| **instructor** | Single assigned environment | Approve student verifications, manage schedules | Instructor dashboard with verification queue |
| **supervisor** | Single assigned environment | Final approvals, assign maintenance, reporting | Supervisor dashboard with oversight metrics |
| **admin** | Single warehouse environment | User management, loan approvals, warehouse ops | Admin dashboard with warehouse stats |
| **admin_general** | All environments (system-wide) | Complete system access, multi-environment oversight | Admin general dashboard with system-wide analytics |

### Navigation & Route Guards

The `NavigationService` (referenced in authentication flow) enforces client-side route protection based on user role. After successful authentication, users are directed to role-appropriate dashboards:

* `/student-dashboard` for students
* `/instructor-dashboard` for instructors
* `/supervisor-dashboard` for supervisors
* `/admin-dashboard` for admin/admin_general

Backend endpoints enforce permissions via the `get_current_user` dependency, checking user role against allowed roles per endpoint.

Sources: System architecture diagrams, role-based access control analysis from provided high-level diagrams

---

## Core Data Models

The system's domain model centers around **Environments** as multi-tenant scoping mechanisms, with **Users** connecting to operational entities through foreign key relationships.

### Entity Relationship Overview

```css
#mermaid-8c0lj14sbtp{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;font-size:16px;fill:#333;}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#mermaid-8c0lj14sbtp .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#mermaid-8c0lj14sbtp .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#mermaid-8c0lj14sbtp .error-icon{fill:#dddddd;}#mermaid-8c0lj14sbtp .error-text{fill:#222222;stroke:#222222;}#mermaid-8c0lj14sbtp .edge-thickness-normal{stroke-width:1px;}#mermaid-8c0lj14sbtp .edge-thickness-thick{stroke-width:3.5px;}#mermaid-8c0lj14sbtp .edge-pattern-solid{stroke-dasharray:0;}#mermaid-8c0lj14sbtp .edge-thickness-invisible{stroke-width:0;fill:none;}#mermaid-8c0lj14sbtp .edge-pattern-dashed{stroke-dasharray:3;}#mermaid-8c0lj14sbtp .edge-pattern-dotted{stroke-dasharray:2;}#mermaid-8c0lj14sbtp .marker{fill:#999;stroke:#999;}#mermaid-8c0lj14sbtp .marker.cross{stroke:#999;}#mermaid-8c0lj14sbtp svg{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;font-size:16px;}#mermaid-8c0lj14sbtp p{margin:0;}#mermaid-8c0lj14sbtp .entityBox{fill:#ffffff;stroke:#dddddd;}#mermaid-8c0lj14sbtp .relationshipLabelBox{fill:#dddddd;opacity:0.7;background-color:#dddddd;}#mermaid-8c0lj14sbtp .relationshipLabelBox rect{opacity:0.5;}#mermaid-8c0lj14sbtp .labelBkg{background-color:rgba(221, 221, 221, 0.5);}#mermaid-8c0lj14sbtp .edgeLabel .label{fill:#dddddd;font-size:14px;}#mermaid-8c0lj14sbtp .label{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;color:#333;}#mermaid-8c0lj14sbtp .edge-pattern-dashed{stroke-dasharray:8,8;}#mermaid-8c0lj14sbtp .node rect,#mermaid-8c0lj14sbtp .node circle,#mermaid-8c0lj14sbtp .node ellipse,#mermaid-8c0lj14sbtp .node polygon{fill:#ffffff;stroke:#dddddd;stroke-width:1px;}#mermaid-8c0lj14sbtp .relationshipLine{stroke:#999;stroke-width:1;fill:none;}#mermaid-8c0lj14sbtp .marker{fill:none!important;stroke:#999!important;stroke-width:1;}#mermaid-8c0lj14sbtp :root{--mermaid-font-family:"trebuchet ms",verdana,arial,sans-serif;}creates/reviewssubmitsrequestsreceivesgeneratesassigned_tocontainsscopeshasrequiresverified_inborrowed_incontainsassociated_withreviewed_bytracked_inUserUUIDidPKstringemailstringroleUUIDenvironment_idFKInventoryCheckUUIDidPKUUIDenvironment_idFKUUIDstudent_idFKUUIDinstructor_idFKUUIDsupervisor_idFKUUIDschedule_idFKstringstatusinttotal_itemsintitems_goodintitems_damagedintitems_missingdatetimecheck_datetimecheck_timeMaintenanceRequestUUIDidPKUUIDitem_idFKUUIDenvironment_idFKUUIDuser_idFKstringprioritystringstatusstringcategoryLoanUUIDidPKUUIDitem_idFKUUIDuser_idFKstringstatusdatetimestart_datedatetimeend_dateNotificationAuditLogEnvironmentUUIDidPKstringnamestringlocationboolis_warehouseboolis_activeInventoryItemUUIDidPKUUIDenvironment_idFKstringnamestringcategorystringstatusintquantityintquantity_damagedintquantity_missingScheduleInventoryCheckItemSupervisorReviewMaintenanceHistory
```

**Key Model Characteristics:**

* **UUIDs**: All primary keys use UUID for distributed system compatibility
* **Multi-tenancy**: Environment ID scopes most operational data
* **Workflow Tracking**: Multiple user ID fields (student_id, instructor_id, supervisor_id) in InventoryCheck support approval chains
* **Quantity Management**: InventoryItem tracks total, damaged, and missing quantities separately
* **Audit Trail**: Timestamps (created_at, updated_at, confirmed_at fields) on all major entities

For complete model definitions and relationships, see [Data Models Reference](/axchisan/GestionInventarioSENA/16-data-models-reference).

Sources: Entity relationship diagram from high-level system analysis

---

## Deployment Architecture

### Docker-Based Deployment

The backend deploys as a containerized application using Docker Compose.

```mermaid
flowchart TD

DockerCompose["docker-compose.yml"]
Dockerfile["Dockerfile"]
Backend["Backend Container<br>(port 8001)"]
Network["app-network<br>(bridge driver)"]
PostgreSQL["External PostgreSQL<br>(147.93.178.204:5433)"]
EnvVars["• APP_PORT=8001<br>• DATABASE_HOST<br>• DATABASE_PORT<br>• DATABASE_USER<br>• DATABASE_PASSWORD<br>• DATABASE_NAME<br>• SECRET_KEY"]

DockerCompose --> Backend
DockerCompose --> Network
Dockerfile --> Backend
Backend --> Network
Backend --> PostgreSQL
Backend --> EnvVars
```

**Container Configuration:**

* **Service Name**: `backend`
* **Build Context**: Current directory (server/)
* **Port Mapping**: `8001:8001` (host:container)
* **Volumes**: `.:/app` (bind mount for development)
* **Network**: `app-network` (custom bridge)
* **Restart Policy**: `on-failure`

**Environment Variables** (loaded from `.env`):

```
DATABASE_HOST=147.93.178.204
DATABASE_PORT=5433
DATABASE_USER=postgres
DATABASE_PASSWORD=<secure_password>
DATABASE_NAME=gestionInventario
SECRET_KEY=<jwt_secret>
APP_PORT=8001
```

### Database Connection

The `database.py` module establishes the SQLAlchemy connection:

```python
# Constructed from settings
SQLALCHEMY_DATABASE_URL = "postgresql+psycopg2://postgres:***@147.93.178.204:5433/gestionInventario"

engine = create_engine(SQLALCHEMY_DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```

The `get_db()` dependency provides database sessions to all routers via FastAPI's dependency injection.

### Client Deployment

The Flutter client can be deployed as:

* **Web App**: Compiled to JavaScript (Flutter web)
* **Mobile App**: APK/IPA for Android/iOS
* **Desktop App**: Native binaries for Windows/macOS/Linux

The base URL is configured via `baseUrl` constant in `api_constants.dart`, currently pointing to production: `https://senainventario.axchisan.com`.

For detailed deployment procedures and configuration options, see [Deployment & Configuration](/axchisan/GestionInventarioSENA/2.3-deployment-and-configuration).

Sources: [server/docker-compose.yml L1-L20](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/docker-compose.yml#L1-L20)

 [server/app/database.py L1-L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/database.py#L1-L18)

 [server/app/config.py L1-L27](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/config.py#L1-L27)

 [server/.env L1-L7](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.env#L1-L7)

---

## Request Flow Example

### Inventory Verification Creation Flow

The following diagram illustrates a complete request flow from client to database, showing the actual code components involved:

```mermaid
sequenceDiagram
  participant InventoryCheckScreen
  participant (client)
  participant ApiService
  participant (api_service.dart)
  participant FastAPI App
  participant (main.py)
  participant AuditMiddleware
  participant (audit_middleware.py)
  participant inventory_checks.router
  participant (inventory_checks.py)
  participant PostgreSQL
  participant (get_db dependency)

  InventoryCheckScreen->>ApiService: _saveCheck()
  ApiService->>ApiService: POST /api/inventory-checks/by-schedule
  ApiService->>FastAPI App: Add Authorization header
  FastAPI App->>AuditMiddleware: (Bearer token)
  AuditMiddleware->>AuditMiddleware: HTTP POST with JSON body
  AuditMiddleware->>inventory_checks.router: Request intercepted
  inventory_checks.router->>inventory_checks.router: Log request to audit_logs table
  inventory_checks.router->>PostgreSQL: Forward to create_verification_by_schedule()
  inventory_checks.router->>inventory_checks.router: get_current_user dependency
  inventory_checks.router->>PostgreSQL: (validates JWT)
  inventory_checks.router->>PostgreSQL: Query Environment, Schedule
  inventory_checks.router->>PostgreSQL: calculate_verification_totals()
  inventory_checks.router->>inventory_checks.router: Query InventoryItem for environment
  inventory_checks.router->>FastAPI App: Create InventoryCheck record
  FastAPI App->>ApiService: Create Notification for instructor
  ApiService->>InventoryCheckScreen: Commit transaction
  InventoryCheckScreen->>InventoryCheckScreen: Return {"status": "success", "check_id": UUID}
```

**Key Components in Flow:**

1. **Client**: `_saveCheck()` method [client/lib/presentation/screens/inventory/inventory_check_screen.dart L812-L880](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart#L812-L880)
2. **API Service**: `ApiService.post()` with authentication
3. **Middleware**: `AuditMiddleware` logs request automatically [server/app/main.py L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L18-L18)
4. **Router Endpoint**: `create_verification_by_schedule()` [server/app/routers/inventory_checks.py L153-L327](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L153-L327)
5. **Database Dependency**: `get_db()` provides SQLAlchemy session [server/app/database.py L13-L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/database.py#L13-L18)
6. **Business Logic**: `calculate_verification_totals()` [server/app/routers/inventory_checks.py L49-L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L49-L79)

Sources: [client/lib/presentation/screens/inventory/inventory_check_screen.dart L812-L880](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart#L812-L880)

 [server/app/routers/inventory_checks.py L153-L327](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L153-L327)

 [server/app/main.py L1-L44](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L1-L44)

---

## Summary

The SENA Inventory Management System is a production-ready, enterprise-grade application that combines modern web technologies (FastAPI, Flutter) with robust architectural patterns (three-tier architecture, RBAC, audit logging) to provide comprehensive inventory management for educational institutions. The system's modular design, with 14+ specialized routers and role-based access control, enables scalable multi-location operations while maintaining strong compliance and security standards.

**Next Steps:**

* For system architecture details → See [System Architecture](/axchisan/GestionInventarioSENA/2-system-architecture)
* For authentication implementation → See [Authentication & Authorization](/axchisan/GestionInventarioSENA/3-authentication-and-authorization)
* For feature-specific documentation → See sections 5-14

Sources: Comprehensive analysis of repository structure and provided system diagrams