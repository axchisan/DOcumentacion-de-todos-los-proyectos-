# Appointment Management

> **Relevant source files**
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/static/css/empleado_dashboard.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css)
> * [app/static/css/gestion_citas_pendientes.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css)
> * [app/static/css/gestion_promociones.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css)
> * [app/static/css/trabajar_citas.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)

## Purpose and Scope

This document covers the administrative interface for managing appointments in Casa Bella. Specifically, it details how administrators view pending appointments that have been booked by clients and assign employees to fulfill those appointments. This page focuses on the admin-side workflow that bridges client bookings and employee work.

For information about how clients book appointments, see [Appointment Booking](/GroveLive/CasaBella/5.3-appointment-booking). For information about how employees confirm and complete appointments, see [Appointment Workflow](/GroveLive/CasaBella/7.1-appointment-workflow).

## Overview

The appointment management system allows administrators to:

1. View all pending appointments that have not yet been assigned to an employee
2. Visualize appointments in a calendar interface using FullCalendar
3. Assign employees to specific appointments based on their specialization
4. Send automatic notifications to assigned employees
5. Track assignment history through the `Asignacion` model

The system enforces a three-stage appointment lifecycle: `pendiente` → `confirmada` → `completada`. The admin's role is to move appointments from the unassigned pending state to the employee-assigned pending state.

**Sources:** [app/routes/admin.py L495-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L573)

## System Architecture

```

```

**Sources:** [app/routes/admin.py L495-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L573)

## Core Routes

### Route: gestion_citas_pendientes

The primary admin route for viewing pending appointments.

**Endpoint:** `GET /admin/gestion_citas_pendientes`

**Location:** [app/routes/admin.py L495-L541](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L541)

**Authentication:** Requires `@login_required` and `current_user.rol == 'admin'`

**Query Logic:**

```

```

This query retrieves:

* Appointments with `estado='pendiente'` (not yet confirmed or completed)
* Appointments with `id_empleado=None` (not yet assigned to any employee)
* Uses `joinedload` for `asignaciones` relationship to avoid N+1 queries

**Data Preparation:**

The route prepares two JSON structures for FullCalendar integration:

1. **Detailed Event Data** (`citas_pendientes_json`): Contains full appointment details for modal display [app/routes/admin.py L507-L519](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L507-L519)
2. **Aggregated Calendar Data** (`citas_pendientes_json_calendar`): Groups appointments by date for calendar day markers [app/routes/admin.py L522-L535](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L522-L535)

**Sources:** [app/routes/admin.py L495-L541](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L541)

### Route: asignar_empleado

The POST endpoint for assigning an employee to a pending appointment.

**Endpoint:** `POST /admin/asignar_empleado/<int:id_cita>`

**Location:** [app/routes/admin.py L543-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L543-L573)

**Request Parameters:**

* `id_empleado` (form field): The ID of the employee to assign
* `notas` (form field, optional): Assignment notes for the employee

**Validation Logic:**

| Check | Condition | Error Response |
| --- | --- | --- |
| Appointment state | `cita.estado != 'pendiente'` | "Esta cita no puede ser asignada." |
| Already assigned | `cita.id_empleado is not None` | "Esta cita no puede ser asignada." |
| Employee selected | `not id_empleado` | "Debes seleccionar un empleado." |

**Assignment Process:**

1. **Create Assignment Record** [app/routes/admin.py L558](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L558-L558) : ``` ```
2. **Update Appointment** [app/routes/admin.py L560](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L560-L560) : ``` ```
3. **Create Notification** [app/routes/admin.py L565-L569](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L565-L569) : ``` ```

**Sources:** [app/routes/admin.py L543-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L543-L573)

## Data Model Relationships

```

```

**Key Relationships:**

* `Cita.id_empleado` is nullable - `NULL` indicates unassigned pending appointment
* `Asignacion` creates an audit trail of employee assignments
* `Notificacion` records are created automatically when assignments occur
* `Usuario` with `rol='empleado'` may have `especialidad` field for matching services

**Sources:** [app/routes/admin.py L501-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L501-L573)

 [app/models/citas.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/citas.py)

 [app/models/asignaciones.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/asignaciones.py)

 [app/models/notificaciones.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/notificaciones.py)

## FullCalendar Integration

### Calendar Data Format

The system provides appointment data to FullCalendar in two formats:

#### Format 1: Detailed Events for Modals

**Variable:** `citas_pendientes_json`

**Structure:** [app/routes/admin.py L507-L519](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L507-L519)

```

```

This format provides full event details for displaying in modals when administrators click on calendar events.

#### Format 2: Day-Aggregated Events

**Variable:** `citas_pendientes_json_calendar`

**Structure:** [app/routes/admin.py L522-L535](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L522-L535)

```

```

This format aggregates appointments by date, showing the total count per day on the calendar view.

**Color Coding:**

| Condition | Background | Border | Meaning |
| --- | --- | --- | --- |
| `len(citas) > 0` | `#ffeb3b` (yellow) | `#ff9800` (orange) | Day has pending appointments |
| `len(citas) == 0` | `#ffffff` (white) | `#ddd` (light gray) | Day has no pending appointments |

**Sources:** [app/routes/admin.py L506-L541](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L506-L541)

### Calendar Configuration

The FullCalendar library is configured via the template [app/templates/gestion_citas_pendientes.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/gestion_citas_pendientes.html)

 Key configuration points:

* **Event Source:** JSON data injected via template context
* **View Mode:** Month/week/day views available via FullCalendar buttons
* **Interactivity:** Click events open Bootstrap modals with assignment forms
* **Styling:** Custom CSS overrides in [app/static/css/gestion_citas_pendientes.css L113-L159](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L113-L159)

**CSS Customization Examples:**

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L113-L159](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L113-L159)

## Assignment Workflow

```

```

**Workflow Steps:**

1. **Client Booking:** Client creates appointment via `client.agendar_cita` (see [Appointment Booking](/GroveLive/CasaBella/5.3-appointment-booking))
2. **Pending State:** Appointment enters `pendiente` state with no assigned employee
3. **Admin Access:** Admin navigates to `/admin/gestion_citas_pendientes`
4. **Calendar View:** FullCalendar displays all unassigned pending appointments
5. **Assignment:** Admin selects employee and submits assignment form
6. **Validation:** System validates appointment is assignable [app/routes/admin.py L550-L552](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L550-L552)
7. **Record Creation:** Creates `Asignacion` record for audit trail [app/routes/admin.py L558-L560](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L558-L560)
8. **Notification:** Sends notification to assigned employee [app/routes/admin.py L565-L571](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L565-L571)
9. **Employee Access:** Employee can now see appointment in their dashboard

**Sources:** [app/routes/admin.py L543-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L543-L573)

## Employee Retrieval Logic

When displaying the assignment form, the system retrieves all available employees:

**Query:** [app/routes/admin.py L504](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L504-L504)

```

```

This query:

* Filters users by `rol='empleado'`
* Returns all active employees regardless of specialization
* No pagination (assumes manageable employee count)
* Employees displayed in dropdown for admin selection

**Employee Selection Considerations:**

The system does **not** automatically match employees to services based on `especialidad`. The admin must manually select the appropriate employee. This design allows flexibility for:

* Cross-training scenarios
* Workload balancing
* Scheduling around availability

To implement automatic matching, future enhancements could filter employees by `especialidad` field matching service requirements.

**Sources:** [app/routes/admin.py L504](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L504-L504)

## Notification System

### Notification Creation

When an employee is assigned, the system creates a notification record [app/routes/admin.py L562-L571](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L562-L571)

:

```

```

### Notification Fields

| Field | Type | Purpose |
| --- | --- | --- |
| `id_usuario` | Integer (FK) | Target employee's user ID |
| `mensaje` | String | Human-readable notification message |
| `tipo` | Enum | Category: `'cita'`, `'inventario'`, or `'promocion'` |
| `fecha_creacion` | DateTime | Timestamp of notification creation |

### Message Format

The notification message includes:

* Appointment date and time formatted as `YYYY-MM-DD HH:MM`
* Service name from the `Servicio` table
* Phrased in Spanish: "Te han asignado una nueva cita para el..."

### Notification Retrieval

Employees can view their notifications through:

* Employee dashboard (`employee.dashboard_empleado`)
* Notification center (if implemented)
* Integrated with appointment views (`employee.trabajar_citas`)

**Sources:** [app/routes/admin.py L562-L571](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L562-L571)

## UI Components

### Page Structure

The `gestion_citas_pendientes.html` template renders:

1. **Header Section:** Page title "Gestión de Citas Pendientes"
2. **Alert Container:** Flash messages for assignment success/failure
3. **Calendar Container:** FullCalendar widget with appointment data
4. **Modal Forms:** Bootstrap modals for assignment form inputs

### Calendar Display

**Container ID:** `#calendar` [app/static/css/gestion_citas_pendientes.css L114-L122](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L114-L122)

**Styling Features:**

* Dark/light theme support via CSS variables
* Responsive design for mobile/tablet/desktop
* Custom event styling with gradient backgrounds
* Hover effects with shadow and scale transforms

**Event Interaction:**

```

```

### Assignment Modal

**Components:**

| Component | Purpose | HTML Element |
| --- | --- | --- |
| Employee Dropdown | Select employee to assign | `<select name="id_empleado">` |
| Notes Textarea | Optional assignment notes | `<textarea name="notas">` |
| Submit Button | Trigger assignment POST | `<button type="submit">` |
| Cancel Button | Close modal without action | `<button data-bs-dismiss="modal">` |

**Form Action:** `POST /admin/asignar_empleado/<id_cita>`

**Modal Styling:** [app/static/css/gestion_citas_pendientes.css L188-L236](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L188-L236)

### Responsive Design

**Breakpoints:**

* **Desktop (> 768px):** Full calendar view with sidebar
* **Tablet (768px):** Adjusted calendar with stacked controls
* **Mobile (< 576px):** Compact calendar, mobile-optimized buttons [app/static/css/gestion_citas_pendientes.css L313-L343](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L313-L343)

**Mobile Optimizations:**

* Reduced font sizes for headers
* Compressed padding in modals
* Full-width form elements
* Touch-friendly button sizing

**Sources:** [app/static/css/gestion_citas_pendientes.css L1-L355](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L1-L355)

## Dashboard Integration

### Admin Dashboard Link

The admin dashboard (`dashboard_admin.html`) displays a count of pending appointments [app/templates/dashboard_admin.html L52-L59](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L52-L59)

:

```

```

**Count Calculation:** [app/routes/admin.py L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L39-L39)

```

```

This count includes **all** pending appointments, not just unassigned ones. It serves as an alert indicator for admins to check the appointment management page.

### Navigation Access

Admins access appointment management via:

1. Direct URL: `/admin/gestion_citas_pendientes`
2. Admin navigation menu (in `base.html` template)
3. Dashboard link (if implemented)

**Sources:** [app/templates/dashboard_admin.html L52-L59](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html#L52-L59)

 [app/routes/admin.py L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L39-L39)

## Error Handling

### Validation Errors

The assignment route implements three validation checks with corresponding error messages:

| Validation | Code Location | Error Message |
| --- | --- | --- |
| Appointment not pending | [app/routes/admin.py L550](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L550-L550) | "Esta cita no puede ser asignada." |
| Already assigned | [app/routes/admin.py L550](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L550-L550) | "Esta cita no puede ser asignada." |
| No employee selected | [app/routes/admin.py L555-L557](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L555-L557) | "Debes seleccionar un empleado." |

### Database Errors

The route relies on Flask-SQLAlchemy's implicit transaction management:

* `db.session.add()` stages changes
* `db.session.commit()` persists atomically
* No explicit rollback handling (relies on Flask transaction context)

**Potential Improvements:**

* Add try-except blocks for `IntegrityError`
* Implement explicit rollback on failure
* Validate employee exists before assignment

### Flash Message Patterns

**Success:** [app/routes/admin.py L572](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L572-L572)

```

```

**Error:** [app/routes/admin.py L547](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L547-L547)

 [app/routes/admin.py L551](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L551-L551)

 [app/routes/admin.py L556](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L556-L556)

```

```

**Display:** Flash messages appear as Bootstrap alerts in [app/static/css/gestion_citas_pendientes.css L38-L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L38-L74)

**Sources:** [app/routes/admin.py L543-L573](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L543-L573)

 [app/static/css/gestion_citas_pendientes.css L38-L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L38-L74)

## Query Optimization

### Eager Loading with joinedload

The pending appointments query uses `joinedload` to prevent N+1 query problems [app/routes/admin.py L501-L503](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L501-L503)

:

```

```

**Impact:**

* Single SQL query with JOIN instead of N+1 queries
* Loads `asignaciones` relationship data eagerly
* Improves performance when displaying assignment history

### Related Query Patterns

**Employee Retrieval:** [app/routes/admin.py L504](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L504-L504)

```

```

**Service Lookup:** [app/routes/admin.py L509](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L509-L509)

```

```

**User Lookup:** [app/routes/admin.py L515](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L515-L515)

 [app/routes/admin.py L563-L564](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L563-L564)

```

```

### Performance Considerations

**Current Implementation:**

* No pagination on pending appointments (loads all)
* No caching of employee list
* No database indexing specified in code (relies on ORM defaults)

**Recommended Optimizations:**

* Add pagination for high-volume appointment systems
* Cache employee list in session or Redis
* Index `Cita.estado` and `Cita.id_empleado` columns
* Use `select_in_load` for large datasets

**Sources:** [app/routes/admin.py L495-L541](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L495-L541)

## Theme Support

The appointment management UI supports dark/light theme switching via CSS variables:

### Dark Theme app/static/css/gestion_citas_pendientes.css250-254

```

```

### Light Theme app/static/css/gestion_citas_pendientes.css256-260

```

```

### Variable Usage

CSS variables are applied throughout:

* Card backgrounds: `var(--card-bg-dark)`
* Text colors: `var(--text-primary)`, `var(--text-secondary)`
* Borders: `var(--border-color)`
* Shadows: `var(--shadow-elegant)`

**Theme Toggle:** Implemented in base template (see [UI/UX Design System](/GroveLive/CasaBella/8-uiux-design-system))

**Sources:** [app/static/css/gestion_citas_pendientes.css L250-L260](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L250-L260)