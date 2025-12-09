# Dashboard Components

> **Relevant source files**
> * [app/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx)
> * [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)
> * [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)

## Purpose and Scope

This document describes the role-specific dashboard components that serve as the primary interface for authenticated users in the SENA Gestión Complementarias system. Each dashboard is tailored to a specific user role (INSTRUCTOR, COORDINADOR, ADMIN) and provides role-appropriate statistics, actions, and navigation.

For information about the overall page layout and navigation structure, see [Layout and Navigation](/axchisan/gestionComplementarias/5.1-layout-and-navigation). For documentation of the solicitud management interface components, see [Solicitud Management Components](/axchisan/gestionComplementarias/5.4-solicitud-management-components).

**Sources:** [app/page.tsx L1-L74](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L1-L74)

---

## Dashboard Selection and Rendering

The application conditionally renders different dashboard components based on the authenticated user's role. The main application entry point (`page.tsx`) checks the user role from the authentication context and displays the appropriate dashboard.

### Dashboard Routing Architecture

```

```

The conditional rendering logic in [app/page.tsx L26-L73](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L26-L73)

 follows this pattern:

| User State | Component Rendered | File Path |
| --- | --- | --- |
| `isLoading === true` | Loading spinner | [app/page.tsx L15-L23](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L15-L23) |
| `user === null` | Landing page (Hero, Features, Footer) | [app/page.tsx L27-L35](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L27-L35) |
| `user.role === "ADMIN"` | `<AdminDashboard />` | [app/page.tsx L44-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L44-L53) |
| `user.role === "COORDINADOR"` | `<CoordinadorDashboard />` | [app/page.tsx L56-L65](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L56-L65) |
| `user.role === "INSTRUCTOR"` | `<InstructorDashboard />` | [app/page.tsx L68](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L68-L68) |

**Sources:** [app/page.tsx L12-L74](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L12-L74)

---

## Coordinator Dashboard Component

The `CoordinadorDashboard` component is the most comprehensive dashboard implementation in the system, providing executive-level oversight of training solicitudes, instructor management, and performance metrics.

### Component Architecture

```

```

**Sources:** [components/coordinador-dashboard.tsx L76-L116](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L76-L116)

### Data Structures

The dashboard uses three primary TypeScript interfaces to manage data:

#### EstadisticasCoordinador Interface

```

```

Defined in [components/coordinador-dashboard.tsx L29-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L29-L55)

#### SolicitudReciente Interface

```

```

Defined in [components/coordinador-dashboard.tsx L57-L65](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L57-L65)

#### InstructorResumen Interface

```

```

Defined in [components/coordinador-dashboard.tsx L67-L74](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L67-L74)

**Sources:** [components/coordinador-dashboard.tsx L29-L74](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L29-L74)

---

## Data Loading and Statistics Calculation

The dashboard loads data from multiple API endpoints and calculates derived statistics client-side.

### Data Loading Sequence

```

```

**Sources:** [components/coordinador-dashboard.tsx L101-L116](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L101-L116)

 [components/coordinador-dashboard.tsx L118-L200](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L118-L200)

### Statistics Calculation Logic

The `loadEstadisticas` function performs several calculations on raw solicitud data:

| Statistic | Calculation Method | Code Reference |
| --- | --- | --- |
| Pending Count | Filter `estado === "PENDIENTE"` | [components/coordinador-dashboard.tsx L130](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L130-L130) |
| Approved Count | Filter `estado === "APROBADA"` | [components/coordinador-dashboard.tsx L131](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L131-L131) |
| Rejected Count | Filter `estado === "RECHAZADA"` | [components/coordinador-dashboard.tsx L132](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L132-L132) |
| Monthly Processed | Filter by `updatedAt >= firstDayOfMonth` and final states | [components/coordinador-dashboard.tsx L135-L139](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L135-L139) |
| Urgent Requests | Filter pending > 7 days old | [components/coordinador-dashboard.tsx L142-L146](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L142-L146) |
| Average Response Time | Calculate mean days between `createdAt` and `updatedAt` | [components/coordinador-dashboard.tsx L149-L158](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L149-L158) |
| Approval Rate | `(approved / total) * 100` | [components/coordinador-dashboard.tsx L160](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L160-L160) |
| Total Training Hours | Sum `duracionHoras` from approved solicitudes | [components/coordinador-dashboard.tsx L172-L174](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L172-L174) |
| Total Apprentices | Sum `numeroAprendicesInscribir` from approved solicitudes | [components/coordinador-dashboard.tsx L175-L177](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L175-L177) |

**Sources:** [components/coordinador-dashboard.tsx L118-L200](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L118-L200)

---

## Dashboard UI Structure

The coordinator dashboard uses a tab-based layout with four main sections.

### Tab Navigation Structure

```

```

**Sources:** [components/coordinador-dashboard.tsx L467-L773](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L467-L773)

### Welcome Header Section

The welcome header displays user context and provides immediate access to critical actions:

* **User Greeting**: Displays coordinator name from `user?.name`
* **Centro Information**: Shows `user?.centro?.nombre` with Building icon
* **Monthly Progress**: Displays completion percentage of `metaMensualSolicitudes`
* **Urgent Alerts**: Conditionally shows alert if `solicitudesUrgentes > 0`
* **Primary Actions**: * "Gestionar Solicitudes" button → `/solicitudes-pendientes` * "Registrar Instructor" button → `/crear-usuario`

Implementation in [components/coordinador-dashboard.tsx L308-L381](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L308-L381)

**Sources:** [components/coordinador-dashboard.tsx L308-L381](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L308-L381)

### Statistics Cards Grid

Six color-coded statistic cards display key metrics:

| Card | Metric | Border Color | Icon | Additional Info |
| --- | --- | --- | --- | --- |
| Pendientes | `solicitudesPendientes` | Red (`border-l-red-500`) | Clock | Urgent count if > 0 |
| Aprobadas | `solicitudesAprobadas` | Green (`border-l-green-500`) | CheckCircle | Approval rate % |
| Total | `totalSolicitudes` | Blue (`border-l-blue-500`) | FileText | "Solicitudes" label |
| Instructores | `totalInstructores` | Purple (`border-l-purple-500`) | Users | Active count |
| Aprendices | `totalAprendices` | Amber (`border-l-amber-500`) | GraduationCap | "Matriculados" |
| Horas | `horasFormacionAprobadas` | Indigo (`border-l-indigo-500`) | BookOpen | "Formación" |

Each card uses hover shadow transition: `hover:shadow-lg transition-shadow`.

Implementation in [components/coordinador-dashboard.tsx L383-L464](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L383-L464)

**Sources:** [components/coordinador-dashboard.tsx L383-L464](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L383-L464)

---

## Tab Content Sections

### Resumen Ejecutivo Tab

The executive summary tab displays two main cards:

#### Performance Metrics Card

Displays three progress bars tracking key coordinator KPIs:

1. **Approval Rate**: `estadisticas.tasaAprobacion` with 85% goal
2. **Response Time**: `estadisticas.tiempoPromedioRespuesta` days vs `metaTiempoRespuesta`
3. **Active Instructors**: Ratio of `instructoresActivos / totalInstructores`

Each metric uses the `Progress` component from [components/ui/progress](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/progress)

 Implementation in [components/coordinador-dashboard.tsx L490-L541](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L490-L541)

#### Quick Actions Card

Provides navigation buttons to key coordinator functions:

* "Revisar Solicitudes Pendientes" → `/solicitudes-pendientes` (shows count badge)
* "Registrar Nuevo Instructor" → `/crear-usuario`
* "Generar Reportes" → `/reportes`
* "Gestionar Programas" → `/cursos`

Conditionally displays urgent alert if `solicitudesUrgentes > 0` with red background and AlertTriangle icon.

Implementation in [components/coordinador-dashboard.tsx L543-L591](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L543-L591)

**Sources:** [components/coordinador-dashboard.tsx L487-L592](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L487-L592)

### Solicitudes Recientes Tab

Displays the 5 most recent solicitudes loaded from `/api/solicitudes?limit=5&orderBy=createdAt&order=desc`.

Each solicitud card shows:

* `codigo` as title
* `programa.nombre` as description
* Status badge using `getStatusBadge(estado, prioridad)`
* Instructor name
* Creation date formatted with `toLocaleDateString("es-CO")`
* "Ver" button linking to `/solicitudes/${id}`

Priority calculation in `calcularPrioridad` function:

* `ALTA`: Pending > 7 days
* `MEDIA`: Pending 3-7 days
* `BAJA`: Pending < 3 days or not pending

Implementation in [components/coordinador-dashboard.tsx L594-L641](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L594-L641)

 priority logic in [components/coordinador-dashboard.tsx L255-L263](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L255-L263)

**Sources:** [components/coordinador-dashboard.tsx L594-L641](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L594-L641)

 [components/coordinador-dashboard.tsx L202-L263](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L202-L263)

### Mis Instructores Tab

Displays a grid of instructor summary cards, showing the top 5 instructors from `/api/instructores?limit=5`.

Each instructor card displays:

* Instructor name (`name`) and email
* Active solicitudes count: `solicitudesActivas`
* Training hours: `horasFormacion`
* Last activity date: `ultimaActividad` formatted as date

The section includes a "Registrar Instructor" button linking to `/crear-usuario`.

Grid layout: `grid md:grid-cols-2 lg:grid-cols-3 gap-6`

Implementation in [components/coordinador-dashboard.tsx L643-L683](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L643-L683)

**Sources:** [components/coordinador-dashboard.tsx L643-L683](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L643-L683)

 [components/coordinador-dashboard.tsx L229-L253](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L229-L253)

### Mi Perfil Tab

Displays two cards with coordinator profile and performance data:

#### Personal Information Card

Shows user data from `useAuth()` context:

* Full name: `user?.name`
* ID number: `user?.cedula`
* Centro: `user?.centro?.nombre`
* Email: `user?.email`
* Role badge: "Coordinador"
* Specialty: `user?.especialidad` or "No especificada"

#### Management Statistics Card

Displays:

* Monthly goal progress bar with percentage
* Grid of 4 colored stat boxes: * Approved solicitudes (green) * Total instructors (blue) * Training hours (purple) * Average response time (amber)
* "Ver Reportes Detallados" button → `/reportes`

Implementation in [components/coordinador-dashboard.tsx L685-L772](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L685-L772)

**Sources:** [components/coordinador-dashboard.tsx L685-L772](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L685-L772)

---

## Status Badge Rendering

The `getStatusBadge` function generates status badges with contextual styling:

```

```

Implementation in [components/coordinador-dashboard.tsx L270-L293](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L270-L293)

Badge class mappings:

* **PENDIENTE (High Priority)**: `bg-red-100 text-red-800` with AlertTriangle
* **PENDIENTE (Normal)**: `bg-yellow-100 text-yellow-800` with Clock
* **APROBADA**: `bg-green-100 text-green-800` with CheckCircle
* **RECHAZADA**: `bg-red-100 text-red-800` no icon
* **Default**: `variant="outline"` with estado text

**Sources:** [components/coordinador-dashboard.tsx L270-L293](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L270-L293)

---

## Utility Functions

### Progress Calculation

The `getProgressPercentage` function calculates monthly goal completion:

```

```

Ensures percentage never exceeds 100%. Used in multiple locations for progress bars.

Implementation in [components/coordinador-dashboard.tsx L265-L268](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L265-L268)

**Sources:** [components/coordinador-dashboard.tsx L265-L268](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L265-L268)

---

## Instructor and Admin Dashboards

While the provided code focuses on the coordinator dashboard, the system also includes:

* **`AdminDashboard`**: Referenced in [app/page.tsx L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L52-L52)  provides system-wide administration interface
* **`InstructorDashboard`**: Referenced in [app/page.tsx L68](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L68-L68)  provides instructor-focused solicitud creation and tracking

These components follow similar patterns to `CoordinadorDashboard` but with role-appropriate data filtering and actions. For specific details on these components, refer to their respective implementation files.

**Sources:** [app/page.tsx L44-L68](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/page.tsx#L44-L68)

---

## Loading States

All dashboards implement loading states while fetching data:

```

```

The loading state is controlled by the `loading` state variable, set to `true` during data fetching and `false` when complete. Implementation in [components/coordinador-dashboard.tsx L295-L304](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L295-L304)

**Sources:** [components/coordinador-dashboard.tsx L78](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L78-L78)

 [components/coordinador-dashboard.tsx L295-L304](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L295-L304)

---

## UI Component Dependencies

The dashboard components rely on several UI primitives from the component library:

| Component | Purpose | Used For |
| --- | --- | --- |
| `Card`, `CardContent`, `CardHeader`, `CardTitle`, `CardDescription` | Container components | All card layouts |
| `Button` | Action triggers | Navigation and quick actions |
| `Badge` | Status indicators | Estado display, counts |
| `Progress` | Progress bars | KPI tracking, goal completion |
| `Tabs`, `TabsContent`, `TabsList`, `TabsTrigger` | Tab navigation | Main content organization |

All components imported from `@/components/ui/*` directories, built on Radix UI primitives with Tailwind CSS styling.

Import statement in [components/coordinador-dashboard.tsx L4-L8](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L4-L8)

**Sources:** [components/coordinador-dashboard.tsx L4-L26](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L4-L26)

---

## Icon Usage

The dashboard uses Lucide React icons extensively for visual hierarchy:

* **Users**: Instructor count
* **FileText**: Solicitudes count
* **TrendingUp**: Progress indicators
* **Clock**: Pending status
* **CheckCircle**: Approved status
* **BookOpen**: Training hours
* **GraduationCap**: Apprentices
* **Building**: Centro information
* **Target**: Goals
* **Activity**: Performance metrics
* **BarChart3**: Reports
* **AlertTriangle**: Urgent alerts
* **Plus**: Create actions
* **Eye**: View actions

All icons imported from `lucide-react` package.

Import statement in [components/coordinador-dashboard.tsx L9-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L9-L25)

**Sources:** [components/coordinador-dashboard.tsx L9-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L9-L25)