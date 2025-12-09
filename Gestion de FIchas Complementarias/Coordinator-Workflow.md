# Coordinator Workflow

> **Relevant source files**
> * [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)
> * [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)
> * [components/solicitudes-pendientes.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx)

## Purpose and Scope

This document describes the workflow for users with the `COORDINADOR` role within the SENA Gestión Complementarias system. Coordinators are responsible for reviewing and approving training solicitudes submitted by instructors within their assigned center (`centroId`), managing instructor accounts, and generating performance reports.

For information about the instructor's perspective on creating solicitudes, see [Instructor Workflow](/axchisan/gestionComplementarias/4.3-instructor-workflow). For administrator system-wide management, see [Administrator Workflow](/axchisan/gestionComplementarias/4.5-administrator-workflow). For the complete solicitud lifecycle state machine, see [Solicitud Lifecycle](/axchisan/gestionComplementarias/4.2-solicitud-lifecycle).

**Sources:** High-level system architecture diagrams (Diagram 2: User Roles and System Access Patterns)

---

## Coordinator Role and Permissions

### Access Control Model

Coordinators have center-scoped permissions enforced at the API level. All data access is filtered by the coordinator's `centroId` to ensure they only see solicitudes and instructors within their assigned training center.

| Permission | Scope | Implementation |
| --- | --- | --- |
| View Solicitudes | Programs in coordinator's center | `where: { programa: { centroId: user.centroId } }` |
| Approve/Reject Solicitudes | Center programs only | Validated in API route handlers |
| Manage Instructors | Center instructors only | Filtered by `centroId` in instructor endpoints |
| Generate Reports | Center-specific data | Report API filters by `centroId` |
| View Dashboard Statistics | Center metrics only | Calculated from center-filtered data |

**Key Routes:**

* `/solicitudes-pendientes` - Primary review interface
* `/solicitudes/:id` - Detailed solicitud view
* `/crear-usuario` - Instructor registration
* `/reportes` - Analytics and reporting
* `/` (Dashboard) - Performance overview

**Sources:** [components/coordinador-dashboard.tsx L76-L116](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L76-L116)

 High-level architecture Diagram 6 (Authentication and Authorization Flow)

---

## Dashboard Overview

The coordinator dashboard (`CoordinadorDashboard` component) provides a comprehensive overview of center operations, statistics, and quick access to common tasks.

### Dashboard Architecture

```

```

**Sources:** [components/coordinador-dashboard.tsx L76-L116](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L76-L116)

 [components/coordinador-dashboard.tsx L307-L381](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L307-L381)

### Key Performance Indicators (KPIs)

The dashboard displays six primary KPI cards calculated from solicitud and instructor data:

| KPI | Calculation | Visual Indicator | Data Source |
| --- | --- | --- | --- |
| Pendientes | `estado === "PENDIENTE"` | Red border, urgent count | [line 130](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/line 130) |
| Aprobadas | `estado === "APROBADA"` | Green border, approval rate | [line 131](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/line 131) |
| Total Solicitudes | All solicitudes count | Blue border | [line 164](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/line 164) |
| Instructores | Active instructor count | Purple border | [line 193](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/line 193) |
| Aprendices | Sum of enrolled learners | Amber border | [line 175-177](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/line 175-177) |
| Horas Formación | Sum of approved hours | Indigo border | [line 172-174](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/line 172-174) |

**Urgent Solicitudes Detection:**
Solicitudes pending for more than 7 days are flagged as urgent with special UI treatment.

```

```

**Sources:** [components/coordinador-dashboard.tsx L142-L146](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L142-L146)

 [components/coordinador-dashboard.tsx L255-L263](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L255-L263)

### Dashboard Tabs

The dashboard uses a tabbed interface (`Tabs` component from Radix UI) with four sections:

1. **Resumen Ejecutivo** - Performance metrics with progress bars for approval rate, response time, and instructor participation
2. **Solicitudes Recientes** - Last 5 solicitudes with quick view links
3. **Mis Instructores** - Summary cards for up to 5 instructors showing active solicitudes and training hours
4. **Mi Perfil** - Personal information and management statistics

**Sources:** [components/coordinador-dashboard.tsx L467-L773](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L467-L773)

---

## Solicitud Review Workflow

### Primary Review Interface

The `SolicitudesPendientes` component provides the main interface for reviewing solicitudes. It displays all solicitudes in `PENDIENTE` or `EN_REVISION` states for the coordinator's center.

```

```

**Sources:** [components/solicitudes-pendientes.tsx L37-L81](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L37-L81)

 [components/solicitud-detalle-modal.tsx L100-L149](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L100-L149)

### Solicitud List Features

**Search and Filtering:**

* Full-text search across: `codigo`, `programa.nombre`, `instructor.name`, `nombreEmpresa`
* State filter toggle: all pending/in-review vs. pending only
* Real-time filtering on client side

**Solicitud Card Information:**
Each solicitud card displays:

* Unique code (e.g., `SOL-2024-001`)
* Status badge (color-coded)
* Number of learners to enroll
* Program name
* Instructor name and email
* Company/organization name
* Course start date
* Duration in hours
* Justification text (truncated)

**Quick Actions:**

* **Ver Detalles** - Opens detailed modal
* **Aprobar** - Quick approve (opens approval modal)
* **Rechazar** - Quick reject (opens approval modal with rejection flow)

**Sources:** [components/solicitudes-pendientes.tsx L150-L157](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L150-L157)

 [components/solicitudes-pendientes.tsx L236-L318](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L236-L318)

### Priority Calculation

The system automatically calculates priority based on how long a solicitud has been pending:

```

```

**Implementation:**

```

```

**Sources:** [components/coordinador-dashboard.tsx L255-L263](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L255-L263)

 [components/solicitudes-pendientes.tsx L219](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L219-L219)

---

## Detailed Solicitud Review

### Solicitud Detail Modal

The `SolicitudDetalleModal` component provides comprehensive information organized into collapsible cards:

| Section | Information Displayed | Purpose |
| --- | --- | --- |
| Header | Code, program name, status, ficha number | Quick identification |
| Instructor Info | Name, cédula, email, specialization | Verify instructor credentials |
| Program Info | Code, duration, modality, cupo, objectives, competencies | Validate program details |
| Company Info | Name, representative, location, address, NIT | Verify organization information |
| Special Programs | Flags for emprendimiento, bilingüismo, posconflicto, etc. | Identify special program types |
| Programming | Enrollment dates, course dates, detailed schedule | Validate timeline |
| Learning Objectives | Ordered list of objectives from program | Review educational goals |
| Justification | Free-text justification from instructor | Understand request rationale |
| Review Comments | Previous coordinator comments (if any) | View revision history |
| Action Panel | Approve/reject buttons with comment field | Make decision |

**Sources:** [components/solicitud-detalle-modal.tsx L15-L98](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L15-L98)

 [components/solicitud-detalle-modal.tsx L248-L565](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L248-L565)

### Special Programs Identification

The system tracks 12 special program types or convenios (agreements). These are displayed as badges in the modal:

```

```

**Sources:** [components/solicitud-detalle-modal.tsx L207-L226](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L207-L226)

 [components/solicitud-detalle-modal.tsx L384-L402](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L384-L402)

### Schedule Review

Coordinators review detailed schedules stored in the `HorarioDetallado` model. Each schedule entry includes:

* Day of week (`diaSemana`)
* Start and end times (`horaInicio`, `horaFin`)
* Optional specific date (`fecha`)
* Flexibility flag (`esFlexible`)
* Optional observations

**Sources:** [components/solicitud-detalle-modal.tsx L440-L468](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L440-L468)

---

## Approval and Rejection Process

### Approval Workflow

When a coordinator approves a solicitud, the following occurs:

```

```

**API Request Format:**

```

```

**Database Updates:**

* `estado`: Changed to `"APROBADA"`
* `numeroFicha`: Set to provided value (validated for uniqueness)
* `comentariosRevision`: Optional coordinator comments
* `fechaAprobacion`: Current timestamp
* `updatedAt`: Current timestamp

**Sources:** [components/solicitudes-pendientes.tsx L93-L108](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L93-L108)

 [components/solicitud-detalle-modal.tsx L151-L162](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L151-L162)

### Rejection Workflow

When a coordinator rejects a solicitud:

```

```

**API Request Format:**

```

```

**Important:** The `comentarios` field is required for rejections to ensure instructors understand why their solicitud was not approved. This enables them to revise and resubmit.

**Sources:** [components/solicitudes-pendientes.tsx L111-L127](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L111-L127)

 [components/solicitud-detalle-modal.tsx L165-L177](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L165-L177)

### State Transitions

After approval or rejection, the solicitud state changes trigger automatic notifications to the instructor through the `NotificationService`:

| Original State | Action | New State | Notification Type |
| --- | --- | --- | --- |
| `PENDIENTE` | Approve | `APROBADA` | `APROBADA` |
| `PENDIENTE` | Reject | `RECHAZADA` | `RECHAZADA` |
| `EN_REVISION` | Approve | `APROBADA` | `APROBADA` |
| `EN_REVISION` | Reject | `RECHAZADA` | `RECHAZADA` |
| `RECHAZADA` | Instructor revises | `PENDIENTE` | `NUEVA_SOLICITUD` |

**Sources:** High-level architecture Diagram 3 (Solicitud Lifecycle)

---

## Performance Metrics and Analytics

### Calculated Metrics

The coordinator dashboard calculates several performance metrics in real-time:

**Approval Rate:**

```

```

**Average Response Time (in days):**

```

```

**Monthly Progress:**

```

```

**Sources:** [components/coordinador-dashboard.tsx L118-L179](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L118-L179)

 [components/coordinador-dashboard.tsx L160-L170](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L160-L170)

### Monthly Goals Tracking

Coordinators have two primary monthly goals tracked on the dashboard:

| Goal | Default Value | Current Progress Calculation |
| --- | --- | --- |
| Solicitudes Processed | 50 | Count of approved + rejected this month |
| Response Time | ≤ 3 days | Average days from creation to decision |

Progress bars visually indicate goal achievement with percentage completion.

**Sources:** [components/coordinador-dashboard.tsx L93-L95](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L93-L95)

 [components/coordinador-dashboard.tsx L265-L268](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L265-L268)

---

## Instructor Management

Coordinators can register and manage instructors within their center through the `/crear-usuario` route. While the instructor management UI is not included in the provided files, the dashboard shows instructor summary information:

### Instructor Summary Display

The dashboard's "Mis Instructores" tab displays summary cards with:

```

```

This data is loaded from `GET /api/instructores` which returns instructors filtered by the coordinator's `centroId`.

**Sources:** [components/coordinador-dashboard.tsx L67-L74](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L67-L74)

 [components/coordinador-dashboard.tsx L229-L253](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L229-L253)

 [components/coordinador-dashboard.tsx L643-L682](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L643-L682)

### Instructor Statistics

The dashboard tracks three instructor-related metrics:

1. **Total Instructores** - All instructors in the center
2. **Instructores Activos** - Instructors with `isActive = true`
3. **Instructores Con Solicitudes** - Instructors who have submitted at least one solicitud

**Sources:** [components/coordinador-dashboard.tsx L182-L196](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L182-L196)

---

## Navigation and Quick Actions

### Primary Navigation Paths

```

```

**Sources:** [components/coordinador-dashboard.tsx L336-L349](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L336-L349)

 [components/coordinador-dashboard.tsx L549-L576](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L549-L576)

### Context-Aware Quick Actions

The dashboard provides quick action buttons that adapt based on current state:

**When Urgent Solicitudes Exist (>7 days pending):**

* Red alert banner displayed in welcome card
* Alert count shown: "X solicitudes urgentes"
* "Atención Requerida" warning box in quick actions panel

**When No Pending Solicitudes:**

* Green success state shown
* Congratulatory message: "¡Excelente trabajo!"
* Focus shifts to instructor management and reporting

**Sources:** [components/coordinador-dashboard.tsx L328-L333](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L328-L333)

 [components/coordinador-dashboard.tsx L578-L588](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L578-L588)

 [components/solicitudes-pendientes.tsx L322-L338](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L322-L338)

---

## Component State Management

### Data Loading Pattern

All coordinator components follow a consistent data loading pattern using React hooks:

```

```

**Pattern Implementation:**

1. Extract `token` and `user` from `useAuth()` context
2. Set up `useEffect` with `[token]` or `[token, user]` dependency
3. Define async `loadData()` function
4. Call API endpoints with `Authorization: Bearer ${token}` header
5. Update local state with `useState` setters
6. Display loading spinner while `loading === true`
7. Render UI when data available

**Sources:** [components/coordinador-dashboard.tsx L101-L116](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L101-L116)

 [components/solicitudes-pendientes.tsx L50-L81](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L50-L81)

 [components/solicitud-detalle-modal.tsx L123-L149](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L123-L149)

### Loading States

All coordinator views implement loading states with consistent UI:

```

```

**Sources:** [components/coordinador-dashboard.tsx L295-L304](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L295-L304)

 [components/solicitudes-pendientes.tsx L164-L173](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L164-L173)

---

## Error Handling and Validation

### Client-Side Validation

Before submitting approval or rejection decisions, the UI enforces:

* **Approval requires:** Valid `numeroFicha` (will be validated for uniqueness on server)
* **Rejection requires:** Non-empty `comentarios` explaining the rejection reason

### Server Response Handling

API calls handle errors gracefully:

```

```

Errors are caught and logged to console, with the UI remaining in a consistent state.

**Sources:** [components/solicitudes-pendientes.tsx L93-L108](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L93-L108)

 [components/solicitudes-pendientes.tsx L111-L127](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L111-L127)

---

## Summary of Key Code Entities

| Entity | Type | Location | Purpose |
| --- | --- | --- | --- |
| `CoordinadorDashboard` | React Component | [components/coordinador-dashboard.tsx L76](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L76-L76) | Main dashboard with statistics and tabs |
| `SolicitudesPendientes` | React Component | [components/solicitudes-pendientes.tsx L37](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L37-L37) | List view for reviewing pending solicitudes |
| `SolicitudDetalleModal` | React Component | [components/solicitud-detalle-modal.tsx L109](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L109-L109) | Detailed modal for viewing and acting on solicitudes |
| `loadEstadisticas()` | Function | [components/coordinador-dashboard.tsx L118](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L118-L118) | Loads and calculates all dashboard statistics |
| `loadSolicitudesRecientes()` | Function | [components/coordinador-dashboard.tsx L202](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L202-L202) | Fetches recent 5 solicitudes |
| `loadInstructoresResumen()` | Function | [components/coordinador-dashboard.tsx L229](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L229-L229) | Fetches instructor summaries |
| `calcularPrioridad()` | Function | [components/coordinador-dashboard.tsx L255](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L255-L255) | Calculates urgency based on age |
| `handleModalApprove()` | Function | [components/solicitudes-pendientes.tsx L93](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L93-L93) | Handles approval API call |
| `handleModalReject()` | Function | [components/solicitudes-pendientes.tsx L111](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L111-L111) | Handles rejection API call |
| `getProgramasEspeciales()` | Function | [components/solicitud-detalle-modal.tsx L207](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L207-L207) | Extracts special program flags |
| `/api/solicitudes` | API Endpoint | Referenced throughout | Fetches solicitudes filtered by center |
| `/api/instructores` | API Endpoint | Referenced throughout | Fetches instructors filtered by center |
| `/api/solicitudes/:id/approve` | API Endpoint | Referenced | Approves a solicitud |
| `/api/solicitudes/:id/reject` | API Endpoint | Referenced | Rejects a solicitud |

**Sources:** All component files listed above