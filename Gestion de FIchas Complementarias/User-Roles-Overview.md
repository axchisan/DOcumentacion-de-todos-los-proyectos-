# User Roles Overview

> **Relevant source files**
> * [app/api/solicitudes/route.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts)
> * [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)
> * [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)
> * [prisma/schema.prisma](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma)

## Purpose and Scope

This document describes the three user roles defined in the SENA Gestión Complementarias system: `INSTRUCTOR`, `COORDINADOR`, and `ADMIN`. It covers their permissions, data access patterns, and how role-based access control (RBAC) is implemented at the database, API, and UI levels.

For information about the authentication and authorization mechanisms that enforce these roles, see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization). For role-specific workflows and operational procedures, see sections [Instructor Workflow](/axchisan/gestionComplementarias/4.3-instructor-workflow), [Coordinator Workflow](/axchisan/gestionComplementarias/4.4-coordinator-workflow), and [Administrator Workflow](/axchisan/gestionComplementarias/4.5-administrator-workflow).

---

## Role Hierarchy and System Architecture

```

```

**Sources:** [prisma/schema.prisma L14-L34](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L34)

 [prisma/schema.prisma L36-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L36-L53)

 [prisma/schema.prisma L55-L77](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L55-L77)

 [prisma/schema.prisma L109-L195](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L109-L195)

 [prisma/schema.prisma L233-L237](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L233-L237)

---

## Role Definitions in Database Schema

The system defines three roles through the `Role` enum in the Prisma schema:

```

```

**Sources:** [prisma/schema.prisma L14-L34](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L34)

 [prisma/schema.prisma L233-L237](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L233-L237)

### User Model Role Field

The `User` model contains a `role` field that determines user permissions:

| Field | Type | Default | Description |
| --- | --- | --- | --- |
| `role` | `Role` | `INSTRUCTOR` | User's system role from the Role enum |
| `centroId` | `String` | Required | Foreign key to Centro - determines data scope |
| `isActive` | `Boolean` | `true` | Soft delete flag for user accounts |

**Sources:** [prisma/schema.prisma L21-L23](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L21-L23)

---

## INSTRUCTOR Role

### Overview

`INSTRUCTOR` is the default role for all newly created users. Instructors create and submit training solicitudes but have limited system access.

### Permissions Table

| Permission | INSTRUCTOR | Description |
| --- | --- | --- |
| Create Solicitud | ✓ | Can create training requests for programs in their centro |
| Edit Own Draft Solicitud | ✓ | Can edit solicitudes in `BORRADOR` state |
| View Own Solicitudes | ✓ | Can view only solicitudes where `instructorId = user.userId` |
| View Other Solicitudes | ✗ | Cannot access solicitudes from other instructors |
| Approve/Reject Solicitudes | ✗ | No review authority |
| Manage Users | ✗ | Cannot create or modify user accounts |
| Access All Centros | ✗ | Data scoped to their own centro |

**Sources:** [app/api/solicitudes/route.ts L16-L17](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L16-L17)

### Data Access Pattern

```

```

**Sources:** [app/api/solicitudes/route.ts L6-L71](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L71)

 [app/api/solicitudes/route.ts L16-L17](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L16-L17)

### API-Level Access Control

The `GET` endpoint in `/api/solicitudes/route.ts` implements instructor filtering:

```

```

**Sources:** [app/api/solicitudes/route.ts L16-L17](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L16-L17)

### Instructor-Specific Features

Instructors have access to the following system features:

| Feature | Route | Description |
| --- | --- | --- |
| Create Solicitud | `/nueva-solicitud` | Form to create new training requests |
| View My Solicitudes | `/solicitudes` | List of solicitudes created by the instructor |
| View Solicitud Details | `/solicitudes/:id` | Detailed view of their own solicitudes |
| View Profile | `/perfil` | Personal profile and statistics |

**Sources:** [app/api/solicitudes/route.ts L73-L242](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L73-L242)

### Solicitud Creation Rules

When an instructor creates a solicitud, the system enforces these rules:

1. **Role Check**: Only users with `role === "INSTRUCTOR"` can create solicitudes
2. **Centro Validation**: The selected programa must belong to the instructor's centro
3. **Program Availability**: Only active programs (`isActive: true`) can be selected
4. **Unique Code Generation**: System auto-generates unique solicitud codes in format `SOL-YYYY-NNN`

**Sources:** [app/api/solicitudes/route.ts L73-L242](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L73-L242)

 [app/api/solicitudes/route.ts L75-L77](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L75-L77)

 [app/api/solicitudes/route.ts L82-L98](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L82-L98)

 [app/api/solicitudes/route.ts L104-L119](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L104-L119)

---

## COORDINADOR Role

### Overview

`COORDINADOR` is the middle-management role with authority to review, approve, and reject solicitudes. Coordinators manage instructors within their centro and have access to reporting features.

### Permissions Table

| Permission | COORDINADOR | Description |
| --- | --- | --- |
| Review Solicitudes | ✓ | Can view all solicitudes for programs in their centro |
| Approve Solicitudes | ✓ | Can change estado to `APROBADA` and assign `numeroFicha` |
| Reject Solicitudes | ✓ | Can change estado to `RECHAZADA` with comentarios |
| Create Instructors | ✓ | Can register new instructor accounts in their centro |
| Manage Instructors | ✓ | Can view and deactivate instructors in their centro |
| Generate Reports | ✓ | Can export solicitud data and statistics |
| View Centro Statistics | ✓ | Can access dashboard with centro-wide metrics |

**Sources:** [app/api/solicitudes/route.ts L18-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L18-L22)

 [components/coordinador-dashboard.tsx L1-L777](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L1-L777)

### Data Access Pattern

```

```

**Sources:** [app/api/solicitudes/route.ts L18-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L18-L22)

### Centro-Scoped Data Access

The coordinator's data access is scoped by their `centroId` through a nested relation:

```

```

This filters solicitudes by checking if the associated `Programa` belongs to the coordinator's `Centro`.

**Sources:** [app/api/solicitudes/route.ts L18-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L18-L22)

### Coordinator Dashboard Features

The `CoordinadorDashboard` component provides comprehensive management tools:

| Feature | Component Location | Description |
| --- | --- | --- |
| Executive Summary | `components/coordinador-dashboard.tsx` | Key metrics and performance indicators |
| Pending Solicitudes | `/solicitudes-pendientes` | Queue of requests awaiting review |
| Instructor Management | `/crear-usuario` | Register and manage instructor accounts |
| Report Generation | `/reportes` | Export data in PDF/Excel formats |
| Statistics Tracking | Dashboard tabs | Approval rates, response times, targets |

**Sources:** [components/coordinador-dashboard.tsx L29-L96](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L29-L96)

 [components/coordinador-dashboard.tsx L306-L777](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L306-L777)

### Coordinator Statistics

The dashboard calculates and displays the following metrics:

```

```

**Sources:** [components/coordinador-dashboard.tsx L29-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L29-L55)

 [components/coordinador-dashboard.tsx L118-L200](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L118-L200)

### Approval/Rejection Authority

Coordinators can approve or reject solicitudes through the detail modal:

```

```

**Sources:** [components/solicitud-detalle-modal.tsx L527-L565](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L527-L565)

 [components/solicitud-detalle-modal.tsx L151-L177](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L151-L177)

---

## ADMIN Role

### Overview

`ADMIN` is the highest privilege role with system-wide access across all centros. Admins have unrestricted data access and can manage all system entities.

### Permissions Table

| Permission | ADMIN | Description |
| --- | --- | --- |
| View All Solicitudes | ✓ | No centro filtering - full system access |
| View All Users | ✓ | Can access users from any centro |
| Manage All Users | ✓ | Can create, edit, deactivate users in any centro |
| Cross-Centro Reports | ✓ | Can generate reports across multiple centros |
| System Configuration | ✓ | Can modify system-wide settings |
| Manage Centros | ✓ | Can create and configure training centers |
| Manage Programas | ✓ | Can create programs for any centro |

**Sources:** [app/api/solicitudes/route.ts L13-L31](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L13-L31)

### Data Access Pattern

```

```

**Sources:** [app/api/solicitudes/route.ts L6-L71](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L71)

### Unfiltered Data Access

For admin users, the API does not apply role-based filtering:

```

```

**Sources:** [app/api/solicitudes/route.ts L13-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L13-L22)

---

## Role-Based Access Control Implementation

### Three-Layer Security Model

```

```

**Sources:** [prisma/schema.prisma L14-L34](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L34)

 [app/api/solicitudes/route.ts L1-L242](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L1-L242)

### Database-Level Role Storage

Roles are stored in the `users` table:

| Column | Type | Constraint | Description |
| --- | --- | --- | --- |
| `role` | `Role` enum | `NOT NULL` | One of: INSTRUCTOR, COORDINADOR, ADMIN |
| `centroId` | `String` | `FOREIGN KEY` | References centros.id |
| `isActive` | `Boolean` | `DEFAULT true` | Soft delete flag |

**Sources:** [prisma/schema.prisma L21-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L21-L25)

### API-Level Filtering Logic

The complete filtering logic in the solicitudes API endpoint:

```

```

**Sources:** [app/api/solicitudes/route.ts L13-L56](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L13-L56)

---

## Data Scoping Comparison

### Role-Based WHERE Clauses

The following table shows how each role's data access is restricted through Prisma WHERE clauses:

| Role | WHERE Clause | Prisma Query | Scope |
| --- | --- | --- | --- |
| `INSTRUCTOR` | `{ instructorId: user.userId }` | Direct match on solicitud | Only their own solicitudes |
| `COORDINADOR` | `{ programa: { centroId: user.centroId } }` | Nested relation through programa | All solicitudes in their centro |
| `ADMIN` | `{}` | Empty where clause | No filtering - all solicitudes |

**Sources:** [app/api/solicitudes/route.ts L13-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L13-L22)

### Data Flow by Role

```

```

**Sources:** [app/api/solicitudes/route.ts L6-L71](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L71)

---

## Role Assignment and Management

### Default Role Assignment

New users are created with the `INSTRUCTOR` role by default:

**Sources:** [prisma/schema.prisma L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L21-L21)

### Role Modification

Only coordinators and admins can create users. Role assignment happens during user creation:

| Who Can Create | Can Assign Roles | Scope |
| --- | --- | --- |
| `COORDINADOR` | `INSTRUCTOR` only | Within their centro |
| `ADMIN` | Any role | Any centro |

**Sources:** [app/api/solicitudes/route.ts L73-L77](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L73-L77)

### Centro Association

All users must be associated with a Centro:

1. The `centroId` field is required and not nullable
2. Users can only create solicitudes for programs in their centro
3. Coordinators can only manage users in their centro
4. Admins can assign users to any centro

**Sources:** [prisma/schema.prisma L22-L23](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L22-L23)

 [app/api/solicitudes/route.ts L82-L98](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L82-L98)

---

## Role-Specific UI Components

### Dashboard Components by Role

```

```

**Sources:** [components/coordinador-dashboard.tsx L1-L777](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L1-L777)

### Conditional Feature Rendering

The coordinator dashboard shows role-specific features:

```

```

**Sources:** [components/solicitud-detalle-modal.tsx L527-L565](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L527-L565)

---

## Summary Table: Role Capabilities

| Capability | INSTRUCTOR | COORDINADOR | ADMIN |
| --- | --- | --- | --- |
| **Data Access** |  |  |  |
| View own solicitudes | ✓ | ✓ | ✓ |
| View centro solicitudes | ✗ | ✓ | ✓ |
| View all solicitudes | ✗ | ✗ | ✓ |
| **Solicitud Management** |  |  |  |
| Create solicitud | ✓ | ✗ | ✗ |
| Edit draft solicitud | ✓ (own) | ✗ | ✗ |
| Approve solicitud | ✗ | ✓ | ✓ |
| Reject solicitud | ✗ | ✓ | ✓ |
| Delete solicitud | ✗ | ✓ | ✓ |
| **User Management** |  |  |  |
| Register instructors | ✗ | ✓ (own centro) | ✓ (any centro) |
| Deactivate users | ✗ | ✓ (own centro) | ✓ (any centro) |
| Change user roles | ✗ | ✗ | ✓ |
| **Reporting** |  |  |  |
| View personal stats | ✓ | ✓ | ✓ |
| Generate centro reports | ✗ | ✓ | ✓ |
| Generate system reports | ✗ | ✗ | ✓ |
| **System Administration** |  |  |  |
| Manage centros | ✗ | ✗ | ✓ |
| Manage programas | ✗ | ✓ (own centro) | ✓ (any centro) |
| System configuration | ✗ | ✗ | ✓ |

**Sources:** [app/api/solicitudes/route.ts L6-L242](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L6-L242)

 [components/coordinador-dashboard.tsx L1-L777](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L1-L777)

 [components/solicitud-detalle-modal.tsx L1-L576](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L1-L576)