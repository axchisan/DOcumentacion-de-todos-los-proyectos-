# Appointment Workflow

> **Relevant source files**
> * [app/routes/employee.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py)
> * [app/static/css/empleado_dashboard.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css)
> * [app/static/css/gestion_citas_pendientes.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css)
> * [app/static/css/gestion_promociones.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css)
> * [app/static/css/trabajar_citas.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css)
> * [app/templates/trabajar_citas.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html)

## Purpose and Scope

This document describes the employee appointment workflow in Casa Bella, covering how employees view, confirm, and complete appointments assigned to them. The workflow implements a three-state lifecycle (pendiente → confirmada → completada) that culminates in automatic invoice generation.

For information about how clients book appointments, see [Appointment Booking](/GroveLive/CasaBella/5.3-appointment-booking). For information about how admins assign employees to appointments, see [Appointment Management](/GroveLive/CasaBella/6.4-appointment-management). For details on the invoice generation process, see [Invoice Generation](/GroveLive/CasaBella/7.2-invoice-generation).

**Sources:** [app/routes/employee.py L1-L451](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L1-L451)

---

## Appointment State Lifecycle

Appointments in Casa Bella follow a strict state machine progression managed by the `Cita` model. Employees can only transition appointments through sequential states:

| State | Description | Accessible Actions | Next State |
| --- | --- | --- | --- |
| `pendiente` | Admin has assigned employee, awaiting confirmation | Confirm Appointment | `confirmada` |
| `confirmada` | Employee acknowledged, service pending delivery | Complete Appointment | `completada` |
| `completada` | Service rendered, invoice generated | Download Invoice | Terminal state |

### State Transition Rules

The employee blueprint enforces state validation at each transition:

1. **Confirmation validation** [app/routes/employee.py L108-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L108-L110) : * Only appointments with `estado='pendiente'` can be confirmed * Only the assigned employee (`id_empleado=current_user.id_usuario`) can confirm
2. **Completion validation** [app/routes/employee.py L126-L128](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L126-L128) : * Only appointments with `estado='confirmada'` can be completed * Completion triggers automatic invoice generation and client notification

**Sources:** [app/routes/employee.py L98-L144](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L98-L144)

---

## Employee Dashboard Overview

The employee dashboard (`/employee/dashboard`) provides a summary view of appointment workload:

```

```

**Displayed Metrics:**

* **My Pending Appointments** - Count of appointments specifically assigned to current employee with `estado='pendiente'`
* **All Pending Appointments** - System-wide count of unconfirmed appointments

**Sources:** [app/routes/employee.py L36-L44](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L36-L44)

 [app/static/css/empleado_dashboard.css L1-L275](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L1-L275)

---

## Viewing Assigned Appointments in FullCalendar

The `/employee/trabajar_citas` route implements the primary appointment management interface using FullCalendar for date-based visualization.

### Route Implementation and Data Loading

```

```

**Sources:** [app/routes/employee.py L46-L96](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L46-L96)

### Data Preprocessing for FullCalendar

The route prepares two JSON structures for FullCalendar [app/routes/employee.py L59-L91](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L59-L91)

:

1. **`citas_json`** - Detailed appointment data for modal display:

```

```

1. **`citas_json_calendar`** - Day-level summaries for calendar grid:

```

```

**Performance Optimization:** The query uses `joinedload(Cita.cliente, Cita.empleado)` [app/routes/employee.py L54-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L54-L57)

 to prevent N+1 queries when rendering client and employee names.

**Sources:** [app/routes/employee.py L59-L96](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L59-L96)

### Frontend Calendar Implementation

The FullCalendar instance is configured with a `dateClick` handler [app/templates/trabajar_citas.html L52-L111](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L52-L111)

 that:

1. Filters `citas_json` for appointments matching clicked date
2. Dynamically generates Bootstrap cards for each appointment
3. Renders state-appropriate action buttons based on `extendedProps.estado`
4. Displays results in a Bootstrap modal (`citasDiaModal`)

**Action Button Rendering Logic:**

| Estado | Rendered Action | Form Action URL |
| --- | --- | --- |
| `pendiente` | Confirm button | `/employee/confirmar_cita/{cita.id}` |
| `confirmada` | Complete & Invoice button | `/employee/completar_cita/{cita.id}` |
| `completada` | Download Invoice link | `/employee/generar_factura/{cita.id}` |

**Sources:** [app/templates/trabajar_citas.html L1-L117](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L1-L117)

 [app/static/css/trabajar_citas.css L1-L379](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L1-L379)

---

## Confirming Appointments

### Route: confirmar_cita(id_cita)

```

```

**Sources:** [app/routes/employee.py L98-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L98-L114)

### Validation Logic

The route implements three layers of validation:

1. **Role check** [app/routes/employee.py L101-L103](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L101-L103) : Ensures `current_user.rol == 'empleado'`
2. **Permission check** [app/routes/employee.py L105-L107](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L105-L107) : Verifies assignment ownership via `cita.id_empleado`
3. **State check** [app/routes/employee.py L108-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L108-L110) : Enforces `estado='pendiente'` requirement

**Database Transaction:** The state transition is committed immediately via `db.session.commit()` [app/routes/employee.py L112](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L112-L112)

 There is no rollback handling for database errors.

**Sources:** [app/routes/employee.py L98-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L98-L114)

---

## Completing Appointments and Triggering Invoices

### Route: completar_cita(id_cita)

Completing an appointment executes three operations atomically:

```

```

**Sources:** [app/routes/employee.py L116-L144](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L116-L144)

### Client Notification Generation

When an appointment is completed, the system automatically creates a `Notificacion` record [app/routes/employee.py L132-L142](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L132-L142)

:

```

```

**Notification Attributes:**

* `id_usuario`: Foreign key to client who booked the appointment
* `tipo`: Set to `'cita'` to categorize as appointment-related
* `mensaje`: Formatted string with date, service name, and employee name

**Sources:** [app/routes/employee.py L132-L142](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L132-L142)

### Automatic Invoice Redirect

Upon successful completion, the route immediately redirects to `generar_factura(id_cita)` [app/routes/employee.py L144](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L144-L144)

 which creates a sale record and generates a PDF invoice. This ensures invoicing happens atomically with appointment completion.

**Sources:** [app/routes/employee.py L116-L144](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L116-L144)

---

## Data Model Interactions

The appointment workflow involves interactions across multiple SQLAlchemy models:

### Core Models and Relationships

```

```

**Sources:** [app/routes/employee.py L4-L12](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L4-L12)

### Query Patterns

**Fetching assigned appointments with eager loading:**

```

```

**Servicio lookup pattern:**
The `Cita` model does not define a direct relationship to `Servicio`, requiring manual lookup [app/routes/employee.py L63](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L63-L63)

:

```

```

**Sources:** [app/routes/employee.py L54-L63](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L54-L63)

---

## Sale and Invoice Generation Integration

When an appointment is completed, the `generar_factura` route creates database records before generating the PDF:

### Database Record Creation

```

```

**Sources:** [app/routes/employee.py L146-L278](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L146-L278)

### IVA Calculation

The system applies a hardcoded 16% IVA rate [app/routes/employee.py L34](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L34-L34)

:

```

```

**Sources:** [app/routes/employee.py L34](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L34-L34)

 [app/routes/employee.py L177-L179](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L177-L179)

### Transaction Flow

1. **Create Venta** [app/routes/employee.py L181-L183](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L181-L183) : Primary sale record linked to client
2. **Flush for ID** [app/routes/employee.py L183](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L183-L183) : Obtain `venta.id_venta` without committing
3. **Create DetalleVenta** [app/routes/employee.py L185-L191](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L185-L191) : Line item for service with quantity=1
4. **Create Pago** [app/routes/employee.py L193-L194](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L193-L194) : Payment record with `metodo_pago='efectivo'`
5. **Commit transaction** [app/routes/employee.py L196](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L196-L196) : Persist all records atomically

**Sources:** [app/routes/employee.py L181-L196](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L181-L196)

---

## UI/UX Implementation Details

### FullCalendar Configuration

The calendar is initialized with minimal configuration [app/templates/trabajar_citas.html L46-L113](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L46-L113)

:

```

```

**Key Features:**

* **Day Grid Month View**: Default display mode showing full month
* **Event Aggregation**: Days display count of appointments (e.g., "3 citas")
* **Date Click Handler**: Opens Bootstrap modal with detailed appointment cards

**Sources:** [app/templates/trabajar_citas.html L44-L113](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L44-L113)

### Modal Dynamic Content Generation

When a date is clicked, the JavaScript handler [app/templates/trabajar_citas.html L52-L111](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L52-L111)

:

1. Filters `citas_json` array for matching date using ISO string comparison
2. Creates Bootstrap card elements dynamically via `document.createElement()`
3. Renders action forms/links based on appointment state
4. Displays modal using Bootstrap 5 API: `new bootstrap.Modal()`

**Card Structure:**

```

```

**Sources:** [app/templates/trabajar_citas.html L62-L105](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/trabajar_citas.html#L62-L105)

### Theme-Aware Styling

The CSS implements theme-specific variables [app/static/css/trabajar_citas.css L274-L284](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L274-L284)

:

```

```

**Styled Elements:**

* FullCalendar container with backdrop blur effect
* Modal cards with gradient borders
* State-specific button colors (success, primary, info)
* Fixed-position alerts in top-right corner

**Sources:** [app/static/css/trabajar_citas.css L1-L379](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L1-L379)

 [app/static/css/gestion_citas_pendientes.css L1-L355](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L1-L355)

---

## Security and Authorization

### Role-Based Access Control

All employee routes enforce role validation [app/routes/employee.py L39-L41](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L39-L41)

:

```

```

### Appointment Assignment Verification

Routes that modify appointments verify the employee owns the assignment [app/routes/employee.py L105-L107](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L105-L107)

:

```

```

**Vulnerability Note:** The code uses `get_or_404()` which returns 404 for non-existent appointments, but doesn't differentiate between "appointment doesn't exist" and "not assigned to you", potentially leaking information about appointment IDs.

**Sources:** [app/routes/employee.py L98-L114](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L98-L114)

 [app/routes/employee.py L116-L128](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L116-L128)

---

## Error Handling and Edge Cases

### Missing Data Handling

The code includes defensive checks for missing relationships:

**Missing Client:**

```

```

**Missing Service:**

```

```

### PDF Generation Failure

The system verifies PDF creation [app/routes/employee.py L271-L272](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L271-L272)

:

```

```

However, this check happens after attempting to send the file, making it ineffective for error recovery.

**Sources:** [app/routes/employee.py L161-L175](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L161-L175)

 [app/routes/employee.py L271-L272](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L271-L272)

---

## Performance Considerations

### Database Query Optimization

The main appointment view uses eager loading to prevent N+1 queries [app/routes/employee.py L54-L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L54-L57)

:

```

```

**Issue:** The `Servicio` lookup is performed in a loop [app/routes/employee.py L63](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L63-L63)

 creating N additional queries. This could be optimized with a bulk fetch or relationship definition.

### File System Cleanup

PDF invoices are immediately deleted after serving [app/routes/employee.py L276-L277](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L276-L277)

:

```

```

This prevents accumulation of temporary files but relies on the response being sent successfully. If the file send fails, the exception occurs after deletion, potentially losing the PDF.

**Sources:** [app/routes/employee.py L54-L63](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L54-L63)

 [app/routes/employee.py L276-L277](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/employee.py#L276-L277)