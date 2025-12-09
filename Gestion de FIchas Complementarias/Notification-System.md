# Notification System

> **Relevant source files**
> * [app/api/solicitudes/route.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts)
> * [components/navigation.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx)
> * [prisma/schema.prisma](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma)

## Purpose and Scope

This document describes the notification system used to communicate solicitud lifecycle events to users. The system provides event-driven notifications that inform instructors and coordinators about status changes, approvals, rejections, and other important updates related to training solicitudes.

The notification system consists of three primary components: the database model for persisting notifications, the `NotificationService` for creating and managing notifications, and the API endpoints and UI components for delivering notifications to users. For information about the solicitud workflow that triggers these notifications, see [Solicitud Lifecycle](/axchisan/gestionComplementarias/4.2-solicitud-lifecycle). For details on the API implementation, see [API Architecture](/axchisan/gestionComplementarias/6.1-api-architecture).

---

## Notification Data Model

The notification system is built around the `Notificacion` entity in the database schema, which stores all notification records with metadata about their type, content, and read status.

### Schema Structure

| Field | Type | Description |
| --- | --- | --- |
| `id` | String (CUID) | Primary key |
| `tipo` | TipoNotificacion (enum) | Notification category |
| `titulo` | String | Notification title |
| `mensaje` | String | Notification body text |
| `leida` | Boolean | Read status (default: false) |
| `fechaCreada` | DateTime | Creation timestamp |
| `usuarioId` | String (FK) | Recipient user ID |
| `solicitudId` | String (FK, optional) | Related solicitud ID |

**Relations:**

* Each notification belongs to one `User` (recipient)
* Each notification may reference one `Solicitud` (optional, for solicitud-related notifications)

**Sources:** [prisma/schema.prisma L213-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L213-L230)

### Notification Types Enum

The `TipoNotificacion` enum defines seven notification categories:

```

```

**Sources:** [prisma/schema.prisma L273-L281](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L273-L281)

---

## NotificationService Architecture

The `NotificationService` class provides a centralized service layer for creating and managing notifications. This service is invoked by API route handlers when solicitud state changes occur.

### Service Methods

```mermaid
flowchart TD

notificarNuevaSolicitud["notificarNuevaSolicitud()<br>Creates NUEVA_SOLICITUD"]
notificarAprobacion["notificarAprobacion()<br>Creates SOLICITUD_APROBADA"]
notificarRechazo["notificarRechazo()<br>Creates SOLICITUD_RECHAZADA"]
notificarRevision["notificarRevision()<br>Creates SOLICITUD_REVISION"]
prismaCreate["prisma.notificacion.create()"]

notificarNuevaSolicitud --> prismaCreate
notificarAprobacion --> prismaCreate
notificarRechazo --> prismaCreate
notificarRevision --> prismaCreate

subgraph subGraph1 ["Database Operations"]
    prismaCreate
end

subgraph subGraph0 ["NotificationService Class"]
    notificarNuevaSolicitud
    notificarAprobacion
    notificarRechazo
    notificarRevision
end
```

Each notification method follows a consistent pattern:

1. Accepts parameters for recipient ID, solicitud ID, and relevant metadata
2. Constructs the notification title and message
3. Creates a database record via Prisma ORM
4. Returns the created notification object

**Sources:** [app/api/solicitudes/route.ts L4](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L4-L4)

 [app/api/solicitudes/route.ts L213-L228](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L213-L228)

---

## Notification Lifecycle and Events

The notification system follows an event-driven architecture where solicitud state transitions trigger notification creation. The following diagram maps solicitud events to notification types and recipients.

### Event-to-Notification Flow

```mermaid
sequenceDiagram
  participant Instructor
  participant API Route Handler
  participant NotificationService
  participant Prisma/Database
  participant Coordinador

  note over Instructor,Coordinador: Solicitud Submission Event
  Instructor->>API Route Handler: POST /api/solicitudes
  API Route Handler->>Prisma/Database: {estado: PENDIENTE}
  Prisma/Database-->>API Route Handler: prisma.solicitud.create()
  API Route Handler->>API Route Handler: Solicitud created
  loop [For each coordinador]
    API Route Handler->>NotificationService: Query coordinadores
    NotificationService->>Prisma/Database: in same centro
    Prisma/Database-->>NotificationService: NotificationService.notificarNuevaSolicitud()
  end
  API Route Handler-->>Instructor: (solicitudId, coordinadorId, instructorName, codigo)
  note over Coordinador: Notification appears in panel
  note over Instructor,Coordinador: Approval Event
  Coordinador->>API Route Handler: prisma.notificacion.create()
  API Route Handler->>NotificationService: {tipo: NUEVA_SOLICITUD, usuarioId: coordinadorId}
  NotificationService->>Prisma/Database: Notification created
  Prisma/Database-->>NotificationService: 201 Created
  API Route Handler-->>Coordinador: PATCH /api/solicitudes/[id]
  note over Instructor: Notification appears in panel
```

**Sources:** [app/api/solicitudes/route.ts L213-L229](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L213-L229)

### Notification Trigger Points

| Solicitud Event | Estado Transition | Notification Type | Recipients |
| --- | --- | --- | --- |
| Submit solicitud | BORRADOR → PENDIENTE | `NUEVA_SOLICITUD` | All coordinators in same centro |
| Approve solicitud | EN_REVISION → APROBADA | `SOLICITUD_APROBADA` | Solicitud instructor |
| Reject solicitud | EN_REVISION → RECHAZADA | `SOLICITUD_RECHAZADA` | Solicitud instructor |
| Request revision | EN_REVISION → PENDIENTE | `SOLICITUD_REVISION` | Solicitud instructor |
| Assign ficha | APROBADA (add numeroFicha) | `ASIGNACION_FICHA` | Solicitud instructor |

**Sources:** [prisma/schema.prisma L254-L261](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L254-L261)

 [prisma/schema.prisma L273-L281](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L273-L281)

---

## API Endpoints for Notifications

The notification system exposes REST endpoints for retrieving and managing user notifications. The primary endpoint is `/api/notificaciones`.

### GET /api/notificaciones

Retrieves notifications for the authenticated user with pagination and filtering support.

**Request Parameters:**

* `limit` (optional, default: 50): Maximum number of notifications to return
* `skip` (optional, default: 0): Number of notifications to skip for pagination
* `leida` (optional): Filter by read status (boolean)

**Response Format:**

```

```

**Authorization:** Requires JWT authentication via `withAuth` middleware. Users can only access their own notifications (filtered by `usuarioId = user.userId`).

**Sources:** [components/navigation.tsx L27-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L27-L42)

### PATCH /api/notificaciones/[id]

Marks a specific notification as read.

**Request Body:**

```

```

**Response:** Updated notification object

**Authorization:** Requires JWT authentication. Users can only update their own notifications.

---

## Client-Side Integration

The notification system integrates with the React frontend through polling, state management, and UI components.

### Notification Polling Architecture

```mermaid
flowchart TD

Navigation["Navigation Component<br>components/navigation.tsx"]
AuthContext["useAuth() Hook<br>Authentication Context"]
Timer["setInterval(30000)<br>30-second polling"]
API["GET /api/notificaciones?limit=1"]
State["useState(notificationCount)"]
Badge["Badge Component<br>Unread count display"]
Panel["NotificationPanel Component"]

Navigation --> AuthContext
AuthContext --> Navigation
Navigation --> Timer
Timer --> API
API --> State
State --> Badge
Navigation --> Panel
Panel --> API
```

**Sources:** [components/navigation.tsx L18-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L18-L25)

 [components/navigation.tsx L27-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L27-L42)

### Notification Count Loading

The navigation component loads the notification count on mount and sets up periodic polling:

1. **Initial Load:** When user authenticates, `loadNotificationCount()` is called
2. **Polling:** `setInterval` executes every 30 seconds (30000ms)
3. **API Call:** Fetches `/api/notificaciones?limit=1` with JWT token in Authorization header
4. **State Update:** Updates `notificationCount` state with `data.noLeidas`
5. **Badge Display:** Shows count in red badge if count > 0

**Sources:** [components/navigation.tsx L18-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L18-L25)

 [components/navigation.tsx L27-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L27-L42)

### Notification Badge Display

The badge component displays the unread notification count with the following logic:

| Condition | Display |
| --- | --- |
| `notificationCount === 0` | Badge hidden |
| `1 <= notificationCount <= 99` | Shows exact count |
| `notificationCount > 99` | Shows "99+" |

**Desktop Navigation:**

```

```

**Sources:** [components/navigation.tsx L139-L143](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L139-L143)

 [components/navigation.tsx L180-L184](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L180-L184)

### NotificationPanel Component

The `NotificationPanel` component provides a slide-out panel for viewing and managing notifications:

**Component Props:**

* `isOpen`: Boolean to control panel visibility
* `onClose`: Callback function when panel closes

**Features:**

* Displays full list of notifications with titles and messages
* Shows notification type and timestamp
* Allows marking individual notifications as read
* Provides "Mark all as read" action
* Links to related solicitudes
* Auto-refreshes count when closed

**Sources:** [components/navigation.tsx L241-L247](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L241-L247)

 [components/navigation.tsx L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L10-L10)

---

## Error Handling and Resilience

The notification system implements defensive error handling to prevent notification failures from blocking critical solicitud operations.

### Error Handling Pattern

```mermaid
flowchart TD

CreateSolicitud["Create Solicitud in Database"]
CheckCoordinators["Check for coordinators<br>in same centro"]
HasCoordinators["Has coordinators?"]
CreateNotifications["Create notifications<br>Promise.all()"]
NotificationError["Notification<br>creation fails?"]
LogError["console.error()<br>Log notification error"]
ReturnSuccess["Return 201 Created<br>with solicitud data"]

CreateSolicitud --> CheckCoordinators
CheckCoordinators --> HasCoordinators
HasCoordinators --> CreateNotifications
HasCoordinators --> ReturnSuccess
CreateNotifications --> NotificationError
NotificationError --> LogError
NotificationError --> ReturnSuccess
LogError --> ReturnSuccess
```

**Key Design Decisions:**

1. **Try-Catch Isolation:** Notification creation is wrapped in a separate try-catch block
2. **Non-Blocking Errors:** If notification creation fails, the solicitud creation still succeeds
3. **Error Logging:** Notification errors are logged to console but not returned to client
4. **Graceful Degradation:** Users receive their solicitud confirmation even if coordinators don't receive notifications

**Sources:** [app/api/solicitudes/route.ts L213-L228](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L213-L228)

---

## Notification Recipient Logic

The system applies role-based logic to determine notification recipients for each event type.

### Recipient Selection Rules

```mermaid
flowchart TD

Event["Solicitud Event"]
NUEVA["Event Type"]
QueryCoordinadores["Query all coordinadores<br>where centroId = solicitud.programa.centroId<br>AND isActive = true"]
GetInstructor["Get solicitud.instructorId"]
GetInstructor2["Get solicitud.instructorId"]
GetInstructor3["Get solicitud.instructorId"]
CreateMultiple["Create notification for each<br>coordinador in loop"]
CreateSingle1["Create single notification<br>for instructor"]
CreateSingle2["Create single notification<br>for instructor"]
CreateSingle3["Create single notification<br>for instructor"]

Event --> NUEVA
NUEVA --> QueryCoordinadores
NUEVA --> GetInstructor
NUEVA --> GetInstructor2
NUEVA --> GetInstructor3
QueryCoordinadores --> CreateMultiple
GetInstructor --> CreateSingle1
GetInstructor2 --> CreateSingle2
GetInstructor3 --> CreateSingle3
```

**Recipient Logic Summary:**

| Event Type | Recipients | Selection Logic |
| --- | --- | --- |
| `NUEVA_SOLICITUD` | All active coordinators in centro | `programa.centro.usuarios` where `role = COORDINADOR AND isActive = true` |
| `SOLICITUD_APROBADA` | Solicitud instructor | `solicitud.instructorId` |
| `SOLICITUD_RECHAZADA` | Solicitud instructor | `solicitud.instructorId` |
| `SOLICITUD_REVISION` | Solicitud instructor | `solicitud.instructorId` |
| `ASIGNACION_FICHA` | Solicitud instructor | `solicitud.instructorId` |

**Sources:** [app/api/solicitudes/route.ts L82-L98](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L82-L98)

 [app/api/solicitudes/route.ts L213-L224](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/api/solicitudes/route.ts#L213-L224)

---

## Database Queries and Performance

The notification system uses efficient database queries with proper indexing and relationship loading.

### Key Query Patterns

**Fetching User Notifications:**

```

```

**Counting Unread Notifications:**

```

```

**Sources:** [components/navigation.tsx L27-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L27-L42)

### Performance Considerations

1. **Polling Interval:** 30-second polling strikes a balance between responsiveness and server load
2. **Lightweight Counts:** The count endpoint uses `limit=1` and only returns aggregate data
3. **Indexed Queries:** Database queries filter by `usuarioId` which has a foreign key index
4. **Pagination:** Notification lists support `skip` and `take` parameters to limit result sets
5. **Selective Includes:** Only loads related `solicitud` data when needed (codigo and estado fields)

**Sources:** [components/navigation.tsx L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L22-L22)

 [components/navigation.tsx L29](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L29-L29)

 [prisma/schema.prisma L222-L228](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L222-L228)