# Notification System

> **Relevant source files**
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)
> * [assets/js/mucama.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)

## Purpose and Scope

This document details the notification system that enables asynchronous communication between different user roles in the Hotel Benedetti management system. The notification system alerts users about key operational events such as new bookings, completed checkouts, and room cleaning requests.

For information about the overall role-based access control architecture, see [User Roles and Access Control](/GroveLive/hotelBenedetti/3-user-roles-and-access-control). For details about the complete reservation workflow that triggers notifications, see [Reservation and Booking Flow](/GroveLive/hotelBenedetti/4.1-reservation-and-booking-flow). For checkout processes that generate notifications, see [Check-out and Payment Process](/GroveLive/hotelBenedetti/4.3-check-out-and-payment-process).

---

## System Architecture

The notification system operates as a message queue mechanism that creates, stores, and delivers messages to specific users based on their roles. Notifications are triggered during critical business events and persist in the database until marked as read by the recipient.

### Core Components

| Component | File Path | Responsibility |
| --- | --- | --- |
| Notification Model | `models/Notificacion.php` | Database operations for notification creation and updates |
| Notification Controller | `controllers/NotificacionController.php` | Handles marking notifications as read |
| Checkout Controller | `controllers/CheckoutController.php` | Creates notifications during checkout process |
| Reservation Controller | `controllers/ReservaController.php` | Creates notifications during booking and confirmation |
| Client JavaScript | `assets/js/cliente.js` | Client-side notification management |
| Maid JavaScript | `assets/js/mucama.js` | Maid-side notification management |
| Receptionist JavaScript | `assets/js/script_recepcionista.js` | Receptionist notification handling |

Sources: [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/mucama.js L1-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L106)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

---

## Notification Data Model

The notification system relies on a database entity with the following structure:

```

```

**Field Descriptions:**

* `id`: Primary key identifier
* `usuario_id`: Foreign key linking to the recipient user
* `reserva_id`: Foreign key linking to the associated reservation (if applicable)
* `mensaje`: Text content of the notification message
* `leida`: Boolean flag indicating whether the notification has been read (`false` by default)
* `created_at`: Timestamp of notification creation

Sources: [controllers/CheckoutController.php L75-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L91)

---

## Notification Creation Events

The system generates notifications at specific trigger points during the reservation lifecycle. Each event creates targeted notifications for relevant user roles.

### Event Trigger Diagram

```

```

Sources: [controllers/CheckoutController.php L74-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L74-L91)

### Checkout Notification Creation Details

The checkout process demonstrates the multi-recipient notification pattern. When a checkout occurs, the system creates two distinct notification streams:

**Client Notification** [controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78)

:

```yaml
usuario_id: reserva_data['cliente_id']
reserva_id: reservaId
mensaje: "Se ha realizado el check-out de su reserva (ID: $reservaId). Total pagado: $$totalPagado."
```

**Maid Notifications** [controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91)

:
The system queries all users with `rol = 'mucama'` and creates individual notifications for each:

```yaml
usuario_id: mucama['id'] (iterated)
reserva_id: reservaId
mensaje: "La habitación {habitacion_id} necesita limpieza después del check-out de la reserva (ID: $reservaId)."
```

Sources: [controllers/CheckoutController.php L74-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L74-L91)

---

## Notification Delivery and Display

Notifications are rendered server-side in role-specific PHP view files and displayed as HTML elements with embedded data attributes.

### Display Structure

Each notification in the HTML interface follows this pattern:

```

```

The `data-id` and `data-reserva-id` attributes enable JavaScript event handlers to identify and process specific notifications.

### Role-Specific Display Logic

| Role | View File | Notification Display Context |
| --- | --- | --- |
| Cliente | `views/cliente.php` | Embedded in notifications section with booking confirmations |
| Recepcionista | `views/recepcionista.php` | Displayed as pending reservation requests requiring action |
| Mucama | `views/mucama.php` | Listed as room cleaning assignments |

Sources: [assets/js/cliente.js L9-L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L9-L11)

 [assets/js/mucama.js L3-L5](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L3-L5)

 [assets/js/script_recepcionista.js L3-L9](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L3-L9)

---

## Notification Reading Mechanism

The system implements a client-initiated notification dismissal pattern where users explicitly mark notifications as read.

### Read Flow Sequence

```

```

Sources: [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

 [assets/js/mucama.js L13-L46](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L46)

### Client Implementation

The client-side notification reading implementation [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

 follows this pattern:

1. **Event Binding**: Attaches click handlers to `.mark-as-read` buttons
2. **ID Extraction**: Retrieves `notificationId` from parent `.notification` element's `data-id` attribute
3. **API Request**: Sends POST to `NotificacionController.php` with `action=markNotificationAsRead`
4. **DOM Update**: Removes notification element on success
5. **Empty State**: Displays "No tienes notificaciones nuevas" message when list is empty

**Request Format**:

```

```

**Response Handling**:

* Success: `notificationDiv.remove()`
* Failure: Display error message via `alert()` (cliente) or SweetAlert2 (mucama)

Sources: [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

 [assets/js/mucama.js L13-L46](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L46)

### Maid Implementation

The maid interface [assets/js/mucama.js L13-L46](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L46)

 implements the same notification reading pattern with enhanced error handling using SweetAlert2:

* On success: Calls `updateNotificationList()` to check for empty state
* Empty state message: "No tienes notificaciones de habitaciones para limpiar."

Sources: [assets/js/mucama.js L13-L46](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L46)

 [assets/js/mucama.js L48-L53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L48-L53)

---

## Controller-Level Notification Management

### NotificacionController.php Action Routing

The notification controller handles the `markNotificationAsRead` action:

**Expected Input**:

* `action`: "markNotificationAsRead"
* `notificationId`: Integer notification ID

**Process**:

1. Validates action parameter
2. Calls `Notificacion` model's `markAsRead()` method
3. Updates `leida` field to `true` in database
4. Returns JSON response with success status

**Response Format**:

```

```

Sources: [assets/js/cliente.js L128-L133](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L128-L133)

 [assets/js/mucama.js L22-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L22-L27)

### Notification Creation in Controllers

Controllers create notifications using the `Notificacion` model instance:

**Pattern** [controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78)

:

```

```

**Multi-User Pattern** [controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91)

:

```

```

Sources: [controllers/CheckoutController.php L74-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L74-L91)

---

## Notification Integration Points

### Receptionist Workflow Integration

The receptionist interface integrates notifications with reservation management [assets/js/script_recepcionista.js L31-L58](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L58)

:

**Confirm Reservation Flow**:

1. User clicks `.confirm-reservation` button on notification
2. Extracts `reservaId` and `notificationId` from data attributes
3. POSTs to `ReservaController.php` with `action=confirmReservation`
4. Controller confirms reservation AND marks notification as read
5. Notification removed from DOM

**Cancel Reservation Flow** [assets/js/script_recepcionista.js L70-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L70-L101)

:

1. Opens modal dialog for cancellation reason
2. Submits reason with `action=cancelReservation`
3. Controller cancels reservation, creates client notification with reason
4. Removes receptionist's notification from UI

This dual-action pattern (business operation + notification management) ensures notifications stay synchronized with reservation states.

Sources: [assets/js/script_recepcionista.js L31-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L101)

---

## Summary Table: Notification Types

| Event | Recipient Role | Message Template | Trigger Location |
| --- | --- | --- | --- |
| Booking Created | Recepcionista | "Nueva reserva pendiente (ID: X)" | `ReservaController.php` |
| Reservation Confirmed | Cliente | "Su reserva (ID: X) ha sido confirmada" | `ReservaController.php` |
| Reservation Cancelled | Cliente | "Su reserva (ID: X) ha sido cancelada. Motivo: [reason]" | `ReservaController.php` |
| Checkout Completed | Cliente | "Se ha realizado el check-out de su reserva (ID: X). Total pagado: $Y" | `CheckoutController.php:75-78` |
| Room Needs Cleaning | Mucama (all) | "La habitación X necesita limpieza después del check-out de la reserva (ID: Y)" | `CheckoutController.php:86-91` |

Sources: [controllers/CheckoutController.php L75-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L91)