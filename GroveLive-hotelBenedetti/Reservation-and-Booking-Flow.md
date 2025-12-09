# Reservation and Booking Flow

> **Relevant source files**
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [controllers/ClienteController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php)

## Purpose and Scope

This document traces the complete booking lifecycle from initial room browsing through receptionist confirmation. The flow begins when a client (guest) views available rooms, selects a room with check-in/check-out dates, and submits a reservation request. The system creates a reservation in `pendiente` (pending) status and notifies the receptionist, who can then confirm or cancel the request. Upon confirmation, the reservation transitions to `confirmada` status and becomes eligible for check-in.

For information about the check-in process that follows reservation confirmation, see [Check-in Process](/GroveLive/hotelBenedetti/4.2-check-in-process). For details about the check-out workflow, see [Check-out and Payment Process](/GroveLive/hotelBenedetti/4.3-check-out-and-payment-process). For information about the client interface itself, see [Client Interface](/GroveLive/hotelBenedetti/3.5-client-interface). For receptionist interface details, see [Receptionist Interface](/GroveLive/hotelBenedetti/3.3-receptionist-interface).

---

## Booking Flow Overview

The reservation and booking process involves three primary actors: the client (guest), the backend system, and the receptionist. The flow is asynchronous, with the client initiating a booking request that requires receptionist approval before becoming active.

### Complete Booking Sequence

```

```

**Sources:** [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

---

## Client-Side Room Browsing

The booking flow begins with the client viewing available rooms. Two distinct functions handle room data presentation: one for display purposes and one for booking form population.

### Room Loading Functions

| Function | Endpoint | Purpose | Target Element |
| --- | --- | --- | --- |
| `loadRooms()` | `HabitacionController.php?action=getAvailableRooms` | Display room cards with details | `#room-grid` |
| `loadAvailableRoomsForBooking()` | `HabitacionController.php?action=getAvailableRooms` | Populate booking form dropdown | `#room-select` |

### Room Display Implementation

The `loadRooms()` function fetches available rooms and renders them as cards in the room grid. Each card displays the room number, type, price per night, and availability status.

**Key Implementation Details:**

* Fetches rooms with `estado='disponible'` from backend
* Creates dynamic `room-card` div elements
* Displays room attributes: `numero_habitacion`, `tipo`, `precio`, `estado`
* Shows fallback message when no rooms are available

**Sources:** [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

### Booking Form Population

The `loadAvailableRoomsForBooking()` function populates the room selection dropdown in the booking form with available rooms.

**Key Implementation Details:**

* Populates `<select id="room-select">` element
* Each option contains: `room.id` as value, display text includes room number, type, and nightly price
* Provides default "Selecciona una habitación" placeholder option

**Sources:** [assets/js/cliente.js L44-L65](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L44-L65)

---

## Client-Side Booking Submission

When the client submits the booking form, the `handleBooking()` function performs client-side validation before sending the request to the backend.

### Booking Form Validation Rules

```

```

### Validation Logic Breakdown

| Validation | Condition | Error Message |
| --- | --- | --- |
| Required Fields | `!roomId \|\| !checkin \|\| !checkout` | "Por favor, completa todos los campos." |
| Check-in Date | `checkin < today` | "La fecha de llegada no puede ser anterior a hoy." |
| Check-out Date | `checkout <= checkin` | "La fecha de salida debe ser posterior a la fecha de llegada." |

### Request Structure

The booking request is sent via `fetch()` as a POST request with URL-encoded form data:

```
POST /controllers/ReservaController.php
Content-Type: application/x-www-form-urlencoded

action=createBooking&roomId={id}&checkin={date}&checkout={date}
```

**Sources:** [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

---

## Backend Reservation Creation

When the `ReservaController.php` receives a `createBooking` action, it performs the following operations:

### Reservation Creation Process

```

```

### Database Operations

The backend performs these database operations in sequence:

1. **Create Reserva Record:** * `usuario_id`: From session (`$_SESSION['usuario_id']`) * `habitacion_id`: From request parameter `roomId` * `fecha_entrada`: From request parameter `checkin` * `fecha_salida`: From request parameter `checkout` * `estado`: Set to `'pendiente'`
2. **Update Room Status:** * Changes `Habitacion2.estado` from `'disponible'` to `'reservada'`
3. **Create Notification:** * Target: All users with `rol='recepcionista'` * Message: Includes client name, room number, and dates * `leida`: Set to `0` (false)

**Sources:** Based on system architecture; specific implementation not provided in files but inferred from [assets/js/cliente.js L98-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L98-L116)

---

## Reservation State Transitions

Reservations progress through a defined set of states during the booking lifecycle.

### State Diagram

```

```

### State Transition Table

| From State | To State | Trigger | Actor | Room Status Change |
| --- | --- | --- | --- | --- |
| (none) | `pendiente` | `createBooking` | Cliente | `disponible` → `reservada` |
| `pendiente` | `confirmada` | `confirmReservation` | Recepcionista | No change |
| `pendiente` | `cancelada` | `cancelReservation` | Recepcionista | `reservada` → `disponible` |

**Sources:** [assets/js/cliente.js L91-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L91-L116)

 [assets/js/script_recepcionista.js L31-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L101)

---

## Receptionist Confirmation Process

Receptionists view pending reservation notifications and can either confirm or cancel them. This is handled through the receptionist interface.

### Notification Structure

Each notification in the receptionist interface contains:

* `data-reserva-id`: The reservation ID
* `data-id`: The notification ID
* Buttons: "Confirm Reservation" and "Cancel Reservation"

### Confirmation Flow

The `confirmReservation()` function handles the confirmation process:

**Process Steps:**

1. Extract `reservaId` and `notificationId` from notification div attributes
2. Build POST request with `action=confirmReservation`
3. Send request to `ReservaController.php`
4. On success: * Remove notification from UI * Call `updateNotificationList()` to check if list is empty
5. On error: Display alert with error message

**Sources:** [assets/js/script_recepcionista.js L31-L58](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L58)

### Cancellation Flow with Reason

Cancellation requires the receptionist to provide a reason through a modal dialog:

```

```

**Key Implementation Details:**

* Dialog ID: `#cancel-reason-dialog`
* Form ID: `#cancel-reason-form`
* Reason input: `#cancel-reason`
* Uses HTML5 `<dialog>` element with `showModal()` and `close()` methods

**Sources:** [assets/js/script_recepcionista.js L60-L108](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L60-L108)

---

## Request and Response Formats

### Client to Backend API Calls

| Action | Endpoint | Method | Parameters | Response |
| --- | --- | --- | --- | --- |
| Load Rooms | `HabitacionController.php?action=getAvailableRooms` | GET | `action=getAvailableRooms` | `{success: bool, rooms: []}` |
| Create Booking | `ReservaController.php` | POST | `action=createBooking, roomId, checkin, checkout` | `{success: bool, message: string}` |
| Confirm Reservation | `ReservaController.php` | POST | `action=confirmReservation, reservaId, notificationId` | `{success: bool, message: string}` |
| Cancel Reservation | `ReservaController.php` | POST | `action=cancelReservation, reservaId, notificationId, reason` | `{success: bool, message: string}` |

### JSON Response Structure

All API endpoints return consistent JSON structure:

```

```

For room listings, the response includes:

```

```

**Sources:** [assets/js/cliente.js L16-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L41)

 [assets/js/cliente.js L98-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L98-L116)

 [assets/js/script_recepcionista.js L31-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L101)

---

## Frontend-Backend Integration

### Component Interaction Map

```

```

**Sources:** [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

---

## Post-Booking Actions

After successful booking submission, the client-side interface performs cleanup and refresh operations:

### Post-Submission Operations

```

```

**Purpose:**

1. **Display Success Message:** Shows green success message to user
2. **Reset Form:** Clears all form inputs for next booking
3. **Refresh Room List:** Updates room cards to reflect new availability
4. **Refresh Dropdown:** Updates booking form dropdown to remove newly reserved room

This ensures the UI remains synchronized with the database state after the reservation is created.

**Sources:** [assets/js/cliente.js L104-L109](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L104-L109)

---

## Error Handling

### Client-Side Error Display

The `showMessage()` function provides user feedback for validation errors and server responses:

| Element ID | Purpose | Color |
| --- | --- | --- |
| `booking-error` | Display validation or server errors | Red |
| `booking-success` | Display successful booking confirmation | Green |

### Error Scenarios

| Error Type | Detection Location | Message |
| --- | --- | --- |
| Empty Fields | Client (handleBooking) | "Por favor, completa todos los campos." |
| Past Check-in Date | Client (handleBooking) | "La fecha de llegada no puede ser anterior a hoy." |
| Invalid Date Range | Client (handleBooking) | "La fecha de salida debe ser posterior a la fecha de llegada." |
| Network Error | Client (fetch catch) | "Error al procesar la reserva: " + error.message |
| Server Error | Server Response | Message from `data.message` field |

**Sources:** [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

---

## Notification System Integration

The booking flow generates notifications at two key points:

### Notification Creation Points

1. **After Reservation Creation:** * Target: Users with `rol='recepcionista'` * Message: Informs about new pending reservation * Contains: Client name, room number, check-in/check-out dates * Used for: Receptionist approval workflow
2. **After Reservation Cancellation:** * Target: Original booking user (cliente) * Message: Informs about cancellation with reason * Contains: Cancellation reason provided by receptionist * Used for: Client awareness of booking status

### Notification Display

Notifications appear in the receptionist interface with action buttons:

* Each notification has `data-reserva-id` and `data-id` attributes
* Contains "Confirm Reservation" and "Cancel Reservation" buttons
* Notifications are removed from UI upon confirmation or cancellation

For detailed notification architecture, see [Notification System](/GroveLive/hotelBenedetti/6.3-notification-system).

**Sources:** [assets/js/script_recepcionista.js L31-L108](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L108)

---

## Summary

The reservation and booking flow implements a two-stage approval process that prevents overbooking and ensures receptionist oversight. The flow's key characteristics:

* **Asynchronous Processing:** Bookings enter `pendiente` state awaiting receptionist action
* **Room Locking:** Rooms transition to `reservada` immediately upon booking to prevent conflicts
* **Notification-Driven:** Uses notification system to alert receptionists of pending bookings
* **Reversible State:** Cancellation returns rooms to `disponible` status
* **Client-Side Validation:** Prevents invalid date submissions before backend processing
* **Consistent API:** All operations follow POST action-based routing pattern

Upon confirmation, reservations become eligible for the check-in process (see [Check-in Process](/GroveLive/hotelBenedetti/4.2-check-in-process)).

**Sources:** [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)