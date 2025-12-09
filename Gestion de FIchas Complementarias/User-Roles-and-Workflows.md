# User Roles and Workflows

> **Relevant source files**
> * [app/api/solicitudes/route.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts)
> * [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)
> * [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)
> * [components/solicitudes-pendientes.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx)
> * [prisma/schema.prisma](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma)

## Purpose and Scope

This document provides an overview of the three user roles in the SENA Gestión Complementarias system and how they interact with the solicitud (training request) approval workflow. It describes the role-based access control (RBAC) system, the lifecycle states of solicitudes, and the primary workflows for each user type.

For detailed information about the data model and entity relationships, see [Data Model](/axchisan/gestionComplementarias/3.3-data-model). For authentication and authorization implementation details, see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization). For frontend component implementations, see [Dashboard Components](/axchisan/gestionComplementarias/5.3-dashboard-components) and [Solicitud Management Components](/axchisan/gestionComplementarias/5.4-solicitud-management-components).

---

## System Roles Overview

The system implements a three-tier role hierarchy defined in the `Role` enum. Each role has specific permissions and data access patterns enforced at both the API and UI levels.

### Role Hierarchy

```

```

**Sources:** [prisma/schema.prisma L233-L237](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L233-L237)

 [app/api/solicitudes/route.ts L16-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L16-L22)

### Role Permissions Matrix

| Permission | INSTRUCTOR | COORDINADOR | ADMIN |
| --- | --- | --- | --- |
| Create solicitudes | ✓ | ✗ | ✗ |
| View own solicitudes | ✓ | ✗ | ✗ |
| View center solicitudes | ✗ | ✓ | ✓ |
| View all solicitudes | ✗ | ✗ | ✓ |
| Approve/reject solicitudes | ✗ | ✓ | ✓ |
| Create instructors | ✗ | ✓ | ✓ |
| Manage users | ✗ | ✗ | ✓ |
| Generate reports | ✗ | ✓ | ✓ |
| Export documents | ✓ | ✓ | ✓ |

**Sources:** [prisma/schema.prisma L14-L34](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L34)

 [app/api/solicitudes/route.ts L6-L71](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L71)

---

## Data Access Patterns by Role

The system enforces role-based data scoping at the API level using Prisma where clauses. Each role has a specific filter applied to queries.

### API-Level Access Control

```

```

**Implementation in `GET /api/solicitudes`:**

* **INSTRUCTOR**: `where: { instructorId: user.userId }` - only solicitudes created by the logged-in instructor
* **COORDINADOR**: `where: { programa: { centroId: user.centroId } }` - all solicitudes for programs in the coordinator's center
* **ADMIN**: No filter applied - all solicitudes across all centers

**Sources:** [app/api/solicitudes/route.ts L6-L71](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L71)

 [lib/middleware.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/lib/middleware.ts)

### User Model and Role Assignment

Each user record in the database has a `role` field that determines their permissions:

```

```

The `centroId` field is critical for coordinators, as it defines the scope of solicitudes they can review.

**Sources:** [prisma/schema.prisma L14-L34](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L34)

 [prisma/schema.prisma L233-L237](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L233-L237)

---

## Solicitud Lifecycle State Machine

All training requests progress through a defined set of states. State transitions are controlled by user actions and role permissions.

### State Diagram

```

```

**Sources:** [prisma/schema.prisma L254-L261](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L254-L261)

 app/api/solicitudes/[id]/approve/route.ts, app/api/solicitudes/[id]/reject/route.ts

### Estado Enum Definition

The `EstadoSolicitud` enum defines all possible states:

```

```

**Sources:** [prisma/schema.prisma L254-L261](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L254-L261)

---

## State Transition Actions

Each state transition is triggered by a specific API endpoint and generates notifications.

### State Transition API Endpoints

| Transition | HTTP Method | Endpoint | Required Role | Generates Notification |
| --- | --- | --- | --- | --- |
| Create → BORRADOR | POST | `/api/solicitudes` | INSTRUCTOR | None |
| BORRADOR → PENDIENTE | POST | `/api/solicitudes` | INSTRUCTOR | `NUEVA_SOLICITUD` |
| PENDIENTE → EN_REVISION | PATCH | `/api/solicitudes/[id]` | COORDINADOR | `SOLICITUD_REVISION` |
| EN_REVISION → APROBADA | POST | `/api/solicitudes/[id]/approve` | COORDINADOR | `SOLICITUD_APROBADA` |
| EN_REVISION → RECHAZADA | POST | `/api/solicitudes/[id]/reject` | COORDINADOR | `SOLICITUD_RECHAZADA` |

**Sources:** [app/api/solicitudes/route.ts L73-L242](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L73-L242)

 [lib/notification-service.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/lib/notification-service.ts)

### Notification Flow

```

```

**Sources:** [app/api/solicitudes/route.ts L213-L229](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L213-L229)

 [lib/notification-service.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/lib/notification-service.ts)

---

## Role-Specific Workflows

Each role has distinct workflows and UI components designed for their responsibilities.

### INSTRUCTOR Workflow

```

```

**Key Components:**

* `SolicitudForm` - Multi-step form for creating solicitudes
* `InstructorDashboard` - Statistics and solicitudes list
* `SolicitudDetalleModal` - View solicitud details

**Sources:** [components/solicitud-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-form.tsx)

 [components/instructor-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/instructor-dashboard.tsx)

 [app/nueva-solicitud/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/nueva-solicitud/page.tsx)

### COORDINADOR Workflow

```

```

**Key Components:**

* `CoordinadorDashboard` - Comprehensive statistics and metrics
* `SolicitudesPendientes` - List of solicitudes requiring review
* `ApprovalModal` - Quick approve/reject interface
* `SolicitudDetalleModal` - Full solicitud review interface

**Sources:** [components/coordinador-dashboard.tsx L1-L777](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L1-L777)

 [components/solicitudes-pendientes.tsx L1-L367](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-pendientes.tsx#L1-L367)

 [components/solicitud-detalle-modal.tsx L1-L576](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L1-L576)

---

## Notification System Integration

The notification system keeps users informed of solicitud status changes. Each role receives notifications relevant to their responsibilities.

### Notification Types by Role

```

```

**Notification Model:**

```

```

**Sources:** [prisma/schema.prisma L213-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L213-L230)

 [prisma/schema.prisma L273-L281](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L273-L281)

 [lib/notification-service.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/lib/notification-service.ts)

### Notification Creation Events

| Event | Trigger | Notification Type | Recipients |
| --- | --- | --- | --- |
| Solicitud submitted | `POST /api/solicitudes` with `isDraft=false` | `NUEVA_SOLICITUD` | All coordinadores in centro |
| Solicitud approved | `POST /api/solicitudes/[id]/approve` | `SOLICITUD_APROBADA` | Solicitud instructor |
| Solicitud rejected | `POST /api/solicitudes/[id]/reject` | `SOLICITUD_RECHAZADA` | Solicitud instructor |
| Ficha assigned | Approval with `numeroFicha` | `ASIGNACION_FICHA` | Solicitud instructor |
| Pending > 7 days | Cron job (if implemented) | `RECORDATORIO` | Assigned coordinador |

**Sources:** [app/api/solicitudes/route.ts L213-L229](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L213-L229)

 app/api/solicitudes/[id]/approve/route.ts, app/api/solicitudes/[id]/reject/route.ts

---

## Dashboard Statistics Calculation

Each role has a dashboard that displays role-specific metrics. The coordinator dashboard is the most comprehensive.

### Coordinator Dashboard Metrics

The `CoordinadorDashboard` component calculates various statistics:

**Key Statistics:**

| Metric | Calculation | Source |
| --- | --- | --- |
| `solicitudesPendientes` | `count(estado = PENDIENTE)` | [components/coordinador-dashboard.tsx L130](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L130-L130) |
| `solicitudesAprobadas` | `count(estado = APROBADA)` | [components/coordinador-dashboard.tsx L131](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L131-L131) |
| `solicitudesUrgentes` | `count(PENDIENTE && createdAt > 7 days ago)` | [components/coordinador-dashboard.tsx L142-L146](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L142-L146) |
| `tasaAprobacion` | `(aprobadas / total) * 100` | [components/coordinador-dashboard.tsx L160](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L160-L160) |
| `tiempoPromedioRespuesta` | `avg(updatedAt - createdAt)` for processed requests | [components/coordinador-dashboard.tsx L149-L158](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L149-L158) |
| `solicitudesProcesadasMes` | `count((APROBADA \|\| RECHAZADA) && updatedAt >= firstDayOfMonth)` | [components/coordinador-dashboard.tsx L137-L139](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L137-L139) |

**Urgent Solicitudes Logic:**

A solicitud is marked as urgent if:

1. Current state is `PENDIENTE`
2. Days since creation > 7

```

```

**Sources:** [components/coordinador-dashboard.tsx L76-L200](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L76-L200)

---

## Key API Endpoints by Role

### Instructor Endpoints

| Endpoint | Method | Purpose | Access Control |
| --- | --- | --- | --- |
| `/api/solicitudes` | GET | List own solicitudes | `instructorId = user.userId` |
| `/api/solicitudes` | POST | Create new solicitud | Only INSTRUCTOR role |
| `/api/solicitudes/[id]` | GET | View solicitud details | Owner or centro coordinador |
| `/api/solicitudes/[id]` | PATCH | Update draft solicitud | Owner + estado = BORRADOR |
| `/api/solicitudes/[id]/export` | GET | Export PDF/Excel | Owner or coordinador |

**Sources:** [app/api/solicitudes/route.ts L6-L242](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L242)

### Coordinator Endpoints

| Endpoint | Method | Purpose | Access Control |
| --- | --- | --- | --- |
| `/api/solicitudes` | GET | List centro solicitudes | `programa.centroId = user.centroId` |
| `/api/solicitudes/[id]/approve` | POST | Approve solicitud | COORDINADOR or ADMIN |
| `/api/solicitudes/[id]/reject` | POST | Reject solicitud | COORDINADOR or ADMIN |
| `/api/instructores` | GET | List centro instructors | COORDINADOR or ADMIN |
| `/api/instructores` | POST | Create new instructor | COORDINADOR or ADMIN |

**Sources:** [app/api/solicitudes/route.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts)

 app/api/solicitudes/[id]/approve/route.ts, app/api/solicitudes/[id]/reject/route.ts

### Admin Endpoints

Admin users have access to all endpoints without centro-based filtering. They can manage users across all centers and view all solicitudes system-wide.

**Sources:** [app/api/solicitudes/route.ts L16-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L16-L22)

---

## Summary

The SENA Gestión Complementarias system implements a robust three-tier RBAC model:

1. **INSTRUCTOR** - Creates and submits solicitudes, views own requests
2. **COORDINADOR** - Reviews and approves/rejects solicitudes for their centro, manages instructors
3. **ADMIN** - Full system access, cross-centro visibility, user management

The solicitud lifecycle progresses through clearly defined states (`BORRADOR` → `PENDIENTE` → `EN_REVISION` → `APROBADA`/`RECHAZADA`) with role-based transition controls. Each state transition triggers appropriate notifications to keep users informed.

Data access is strictly controlled at the API level using Prisma where clauses based on the authenticated user's role and `centroId`. This ensures coordinators only see solicitudes for programs in their center, and instructors only see their own requests.

For detailed information about specific workflows, see the subsections:

* [User Roles Overview](/axchisan/gestionComplementarias/4.1-user-roles-overview)
* [Solicitud Lifecycle](/axchisan/gestionComplementarias/4.2-solicitud-lifecycle)
* [Instructor Workflow](/axchisan/gestionComplementarias/4.3-instructor-workflow)
* [Coordinator Workflow](/axchisan/gestionComplementarias/4.4-coordinator-workflow)
* [Administrator Workflow](/axchisan/gestionComplementarias/4.5-administrator-workflow)