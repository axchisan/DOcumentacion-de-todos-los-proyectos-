# Appointment Booking

> **Relevant source files**
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/templates/citas.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/citas.html)
> * [app/templates/index.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html)

## Purpose and Scope

This document describes the client-facing appointment booking system in Casa Bella. It covers how clients select services, choose dates and times, and create appointments that enter the system as "pendiente" (pending) status. For information about how administrators assign employees to appointments, see [Appointment Management](/GroveLive/CasaBella/6.4-appointment-management). For details on how employees confirm and complete appointments, see [Appointment Workflow](/GroveLive/CasaBella/7.1-appointment-workflow).

The appointment booking system enforces business hours validation, time slot restrictions, and creates notifications for both the client and administrators when new appointments are created.

---

## Appointment Booking Flow

The appointment booking process spans multiple user roles and follows a defined lifecycle. Clients initiate the process by creating appointments that require administrative assignment and employee fulfillment.

### End-to-End Appointment Lifecycle

```

```

**Sources:** [app/routes/client.py L96-L104](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L96-L104)

 [app/routes/client.py L782-L855](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L782-L855)

---

## Business Hours Configuration

The appointment system enforces a hardcoded schedule that defines when the salon is open. This schedule is used for both validation logic and display to clients.

### Weekly Schedule Table

| Day of Week | Weekday Index | Open Time | Close Time | Status |
| --- | --- | --- | --- | --- |
| Monday (Lunes) | 0 | 09:00 | 19:00 | Open |
| Tuesday (Martes) | 1 | 08:00 | 19:00 | Open |
| Wednesday (Miércoles) | 2 | 09:00 | 19:00 | Open |
| **Thursday (Jueves)** | **3** | - | - | **CLOSED** |
| Friday (Viernes) | 4 | 09:00 | 19:00 | Open |
| Saturday (Sábado) | 5 | 09:00 | 19:00 | Open |
| Sunday (Domingo) | 6 | 09:00 | 18:00 | Open |

### Business Hours Validation Logic

```

```

**Sources:** [app/routes/client.py L796-L820](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L796-L820)

 [app/routes/client.py L740-L761](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L740-L761)

 [app/templates/index.html L43-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html#L43-L110)

---

## Appointment Creation Route

The `reservar_cita` route in the client blueprint handles the creation of new appointments. It performs comprehensive validation before persisting the appointment to the database.

### Route Implementation Details

| Attribute | Value |
| --- | --- |
| Route | `/client/reservar_cita` |
| Methods | `POST` |
| Function | `reservar_cita()` |
| Auth Required | Yes (`@login_required`) |
| Role Required | `cliente` |
| File Location | [app/routes/client.py L782-L855](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L782-L855) |

### Validation Rules

The route enforces four distinct validation rules in sequence:

1. **Business Hours Validation**: Checks if the requested day/time falls within salon operating hours using the hardcoded `horarios` dictionary [app/routes/client.py L796-L813](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L796-L813)
2. **30-Minute Increment Validation**: Ensures appointments start at :00 or :30 minutes past the hour using modulo arithmetic: `minutos % 30 != 0` [app/routes/client.py L814-L817](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L814-L817)
3. **Past Date Validation**: Prevents booking appointments in the past by comparing against `datetime.now()` [app/routes/client.py L818-L820](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L818-L820)
4. **User Authentication**: Verifies `current_user.is_authenticated` and `current_user.id_usuario` exists [app/routes/client.py L790-L792](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L790-L792)

### Database Entities Created

```

```

**Sources:** [app/routes/client.py L821-L845](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L821-L845)

### Notification Messages

When an appointment is successfully created, two notifications are generated:

**Client Notification:**

```
Cita reservada con éxito para el {fecha_hora} con el servicio {servicio.nombre}.
```

**Admin Notification:**

```
Nueva cita pendiente reservada por {current_user.nombre} para el {fecha_hora} con el servicio {servicio.nombre}. Por favor, asigna un empleado.
```

The admin notification explicitly prompts for employee assignment, linking the client booking process to the admin workflow described in [Appointment Management](/GroveLive/CasaBella/6.4-appointment-management).

**Sources:** [app/routes/client.py L829-L844](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L829-L844)

---

## Appointment Management Operations

Clients can view, edit, and delete their own appointments. These operations respect ownership validation to ensure clients can only modify their own data.

### Edit Appointment Flow

```

```

**Key Implementation Detail:** When editing an appointment, the system resets `estado='pendiente'` even if the appointment was previously confirmed or assigned. This forces the admin to re-review the modified appointment.

**Sources:** [app/routes/client.py L724-L780](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L724-L780)

### Delete Appointment Flow

The delete operation is simpler than edit, performing only ownership validation before removal.

| Operation | Route | Method | Authorization Check |
| --- | --- | --- | --- |
| Delete | `/client/borrar_cita/<int:cita_id>` | `POST` | `cita.id_usuario == current_user.id_usuario` |

**Implementation:** [app/routes/client.py L700-L722](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L700-L722)

The deletion route removes the `Cita` record from the database. If the appointment was already assigned to an employee (via the admin interface), the foreign key relationship may trigger an integrity error. The route handles this with a try-except block that rolls back the transaction and displays an error message.

**Sources:** [app/routes/client.py L710-L717](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L710-L717)

---

## Appointment Booking UI

The appointment booking interface consists of two primary templates that work together to present services and collect booking information.

### Template Structure

```

```

**Sources:** [app/templates/citas.html L1-L89](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/citas.html#L1-L89)

 [app/templates/index.html L43-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html#L43-L110)

### Citas Route Implementation

The `citas` route serves the appointment booking form:

```

```

**Optional Pre-selection:** The route accepts an optional `servicio_id` query parameter to pre-select a service in the booking form. This enables deep-linking from the services catalog page.

**Sources:** [app/routes/client.py L96-L104](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L96-L104)

### Form Fields

The booking form (`citas.html`) contains two primary input fields:

| Field Name | HTML Type | Validation | Purpose |
| --- | --- | --- | --- |
| `servicio_id` | `<select>` dropdown | `required` | Service selection from all available `Servicio` records |
| `fecha_hora` | `datetime-local` | `required` | Date and time picker in format `YYYY-MM-DDTHH:MM` |

The service dropdown displays each service with its price and duration:

```
{{ servicio.nombre }} - ${{ '%0.2f'|format(servicio.precio) }} ({{ servicio.duracion }} min)
```

**Sources:** [app/templates/citas.html L20-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/citas.html#L20-L39)

### Business Hours Display

Both `index.html` and `citas.html` display the same business hours schedule to inform clients when they can book appointments. The schedule is hardcoded in the template, separate from the validation logic in `reservar_cita`.

**Note:** The business hours are duplicated between the backend validation dictionary and the frontend display templates. Changes to the schedule require updating both locations:

* Backend validation: [app/routes/client.py L796-L804](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L796-L804)
* Landing page display: [app/templates/index.html L58-L99](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html#L58-L99)
* Booking page display: [app/templates/citas.html L54-L82](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/citas.html#L54-L82)

**Sources:** [app/templates/citas.html L44-L85](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/citas.html#L44-L85)

 [app/templates/index.html L43-L110](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/index.html#L43-L110)

---

## Appointment State Lifecycle

Appointments progress through a state machine with three distinct states. Client booking only creates appointments in the initial "pendiente" state. Subsequent state transitions are managed by other user roles.

### State Transition Diagram

```

```

### State Management Details

| State | Spanish Name | Created By | Managed By | Key Attributes |
| --- | --- | --- | --- | --- |
| Pending | `'pendiente'` | Client (`reservar_cita`) | Admin assigns employee | `id_empleado` is NULL |
| Confirmed | `'confirmada'` | Employee (`confirmar_cita`) | Employee workflow | `id_empleado` is set |
| Completed | `'completada'` | Employee (`completar_cita`) | Employee workflow | Triggers auto-invoice |

**Important:** When a client edits an appointment (via `editar_cita`), the system resets `estado='pendiente'` regardless of the current state. This ensures modified appointments are re-reviewed by administrators.

**Sources:** [app/routes/client.py L767](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L767-L767)

 [app/routes/client.py L825](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L825-L825)

**Related Pages:**

* For admin assignment workflow, see [Appointment Management](/GroveLive/CasaBella/6.4-appointment-management)
* For employee confirmation and completion, see [Appointment Workflow](/GroveLive/CasaBella/7.1-appointment-workflow)
* For invoice generation upon completion, see [Invoice Generation](/GroveLive/CasaBella/7.2-invoice-generation)

---

## Integration with Services Catalog

The appointment booking system is tightly integrated with the services catalog. Clients can navigate from service browsing to appointment booking with pre-selected services.

### Service-to-Booking Flow

```

```

### Data Flow Between Components

| Component | Provides | Consumes |
| --- | --- | --- |
| `Servicio` model | Service metadata (nombre, precio, duracion) | - |
| `client.servicios` route | List of all services | Servicio.query.all() |
| `client.citas` route | Booking form | servicio_id (optional query param) |
| `client.reservar_cita` route | Appointment creation | servicio_id (form POST data) |
| `Cita` model | Appointment record | id_servicio (foreign key) |

**Sources:** [app/routes/client.py L52-L73](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L73)

 [app/routes/client.py L96-L104](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L96-L104)

 [app/routes/client.py L788](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L788-L788)

---

## Error Handling and Validation Messages

The appointment booking system provides specific error messages for each validation failure, helping clients understand and correct booking issues.

### Validation Error Messages

| Validation Type | Condition | Flash Message | Category |
| --- | --- | --- | --- |
| Closed Day | `dia_semana == 3` (Thursday) | "Hora o día no disponible. El salón está cerrado este día (Jueves)." | `danger` |
| Outside Hours | Time not in range | "Hora no disponible. Por favor, selecciona una hora dentro del rango permitido." | `danger` |
| Invalid Increment | `minutos % 30 != 0` | "Hora no disponible. Las citas solo están disponibles en incrementos de 30 minutos." | `danger` |
| Past Date | `fecha_hora < datetime.now()` | "No puedes reservar citas en el pasado." | `danger` |
| Date Format | ValueError in strptime | "Error en el formato de la fecha. Usa YYYY-MM-DDTHH:MM." | `danger` |
| Success | All validations pass | "Cita reservada con éxito." | `success` |

### Exception Handling

The booking routes implement try-except blocks to handle various error scenarios:

```

```

**Sources:** [app/routes/client.py L847-L854](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L847-L854)

 [app/routes/client.py L771-L778](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L771-L778)

---

## Code Entity Reference

### Key Routes

| Route Path | Function Name | Line Numbers | Purpose |
| --- | --- | --- | --- |
| `/client/citas` | `citas()` | [app/routes/client.py L96-L104](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L96-L104) | Render booking form |
| `/client/reservar_cita` | `reservar_cita()` | [app/routes/client.py L782-L855](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L782-L855) | Create new appointment |
| `/client/editar_cita/<cita_id>` | `editar_cita()` | [app/routes/client.py L724-L780](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L724-L780) | Edit existing appointment |
| `/client/borrar_cita/<cita_id>` | `borrar_cita()` | [app/routes/client.py L700-L722](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L700-L722) | Delete appointment |

### Database Models Referenced

| Model | Import | Key Attributes Used |
| --- | --- | --- |
| `Cita` | `from app.models.citas import Cita` | `id_usuario`, `id_servicio`, `fecha_hora`, `estado` |
| `Servicio` | `from app.models.servicios import Servicio` | `id_servicio`, `nombre`, `precio`, `duracion` |
| `Notificacion` | `from app.models.notificaciones import Notificacion` | `id_usuario`, `mensaje`, `tipo` |
| `Usuario` | `from app.models.users import Usuario` | `id_usuario`, `rol`, `nombre` |

### Constants

| Constant | Value | Location | Purpose |
| --- | --- | --- | --- |
| Business Hours Dictionary | `horarios` dict | [app/routes/client.py L796-L804](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L796-L804) | Validates appointment times |
| Thursday Index | `3` | [app/routes/client.py L806](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L806-L806) | Identifies closed day |
| Time Increment | `30` (minutes) | [app/routes/client.py L815](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L815-L815) | Validates time slots |

**Sources:** [app/routes/client.py L1-L39](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L1-L39)

 [app/routes/client.py L796-L820](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L796-L820)