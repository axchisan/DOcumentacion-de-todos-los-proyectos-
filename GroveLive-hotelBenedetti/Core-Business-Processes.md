# Core Business Processes

> **Relevant source files**
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)
> * [assets/js/mucama.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [controllers/CheckinController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)

## Purpose and Scope

This document provides an overview of the four primary operational workflows that drive hotel operations in the Hotel Benedetti system. These workflows encompass the complete guest lifecycle from initial room browsing through final room cleaning and room availability restoration.

For details on the user interfaces through which these processes are executed, see:

* Client reservation interface: [Client Interface](/GroveLive/hotelBenedetti/3.5-client-interface)
* Front desk operations: [Receptionist Interface](/GroveLive/hotelBenedetti/3.3-receptionist-interface)
* Housekeeping operations: [Maid Interface](/GroveLive/hotelBenedetti/3.4-maid-interface)

For information about the notification system that coordinates these workflows, see [Notification System](/GroveLive/hotelBenedetti/6.3-notification-system).

## Overview

The hotel management system implements four interconnected business processes:

1. **Reservation and Booking Flow**: Guest browses rooms, creates booking request, receptionist approves/denies
2. **Check-in Process**: Front desk validates reservation and records guest arrival
3. **Check-out and Payment Process**: Front desk records departure, collects payment, triggers room cleaning
4. **Room Cleaning and Status Management**: Housekeeping receives notification, cleans room, marks as available

Each process involves coordination between multiple user roles through an asynchronous notification system. State transitions are enforced through database-level validation in controllers.

Sources: [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [assets/js/mucama.js L1-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L106)

 [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

## Process State Model

The following diagram illustrates the state transitions for a reservation and its associated room throughout the complete business process lifecycle:

**Reservation and Room State Transitions**

```

```

Sources: [controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34)

 [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)

 [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/script_recepcionista.js L31-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L101)

 [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

## Data Flow Across Processes

The following table summarizes the key entities and their state changes across the four business processes:

| Process | Entity Modified | State Transition | Notification Recipients |
| --- | --- | --- | --- |
| **Reservation** | `reservas.estado` | `NULL` → `pendiente` → `confirmada` | Recepcionista (on creation), Cliente (on confirm/cancel) |
| **Check-in** | `checkin` table | New record created | None |
| **Check-out** | `checkout` table, `habitacion2.estado` | New record + room → `en limpieza` | Cliente (payment receipt), All Mucamas (cleaning request) |
| **Cleaning** | `habitacion2.estado` | `en limpieza` → `disponible` | None |

Sources: [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

## Process Interaction Diagram

This sequence diagram shows how the four core processes interact across user roles and system components:

**End-to-End Business Process Flow**

```

```

Sources: [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/script_recepcionista.js L31-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L174)

 [controllers/CheckinController.php L15-L60](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L15-L60)

 [controllers/CheckoutController.php L17-L99](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L17-L99)

 [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

## Key Controller Actions

The following table maps user actions to backend controller endpoints:

| User Role | Frontend Function | Controller Endpoint | Action Parameter |
| --- | --- | --- | --- |
| Cliente | `handleBooking()` | `ReservaController.php` | `createBooking` |
| Recepcionista | `confirmReservation()` | `ReservaController.php` | `confirmReservation` |
| Recepcionista | `cancelReservation()` | `ReservaController.php` | `cancelReservation` |
| Recepcionista | `handleCheckin()` | `CheckinController.php` | `checkin` |
| Recepcionista | `handleCheckout()` | `CheckoutController.php` | `checkout` |
| Mucama | `markRoomAsCleaned()` | `MucamaController.php` | `markAsCleaned` |

Sources: [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/script_recepcionista.js L31-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L174)

 [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

## Validation Rules

Each business process enforces specific validation rules at the controller level:

### Reservation Creation Validation

* Check-in date must not be in the past (client-side: [assets/js/cliente.js L80-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L80-L84) )
* Check-out date must be after check-in date (client-side: [assets/js/cliente.js L86-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L86-L89) )
* Room must be available (controller validation)
* All fields must be populated ([assets/js/cliente.js L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L78) )

### Check-in Validation

* Reservation must exist and have `estado = 'confirmada'` ([controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34) )
* Check-in record must not already exist for this reservation ([controllers/CheckinController.php L37-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L37-L47) )

### Check-out Validation

* Reservation must exist and have `estado = 'confirmada'` ([controllers/CheckoutController.php L25-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L37) )
* Check-in record must exist for this reservation ([controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50) )
* Check-out record must not already exist for this reservation ([controllers/CheckoutController.php L53-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L63) )

### Room Cleaning Validation

* Room must be in `estado = 'en limpieza'` state (enforced by MucamaController)

Sources: [assets/js/cliente.js L75-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L89)

 [controllers/CheckinController.php L22-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L47)

 [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)

## Notification Triggers

The table below lists automatic notification generation during business processes:

| Process | Trigger Point | Recipient Role | Notification Content |
| --- | --- | --- | --- |
| **Booking Created** | After `createBooking` | `recepcionista` | New reservation pending approval |
| **Reservation Confirmed** | After `confirmReservation` | `cliente` | Reservation confirmed |
| **Reservation Cancelled** | After `cancelReservation` | `cliente` | Reservation cancelled with reason |
| **Check-out Completed** | After `checkout` creation | `cliente` | Payment receipt with total amount ([controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78) <br> ) |
| **Check-out Completed** | After room status → `en limpieza` | All `mucama` users | Room needs cleaning ([controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91) <br> ) |

Sources: [controllers/CheckoutController.php L74-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L74-L91)

## Critical Code Paths

### Booking Submission Flow

**Cliente Booking Process**

```

```

Sources: [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

### Check-in Execution Flow

**Receptionist Check-in Process**

```

```

Sources: [assets/js/script_recepcionista.js L110-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L110-L135)

 [controllers/CheckinController.php L18-L54](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L18-L54)

### Check-out and Room Transition Flow

**Receptionist Check-out and Room Status Update**

```

```

Sources: [assets/js/script_recepcionista.js L145-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L145-L174)

 [controllers/CheckoutController.php L20-L93](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L20-L93)

### Room Cleaning Completion Flow

**Mucama Room Cleaning Process**

```

```

Sources: [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

## Process Dependencies

The business processes have strict ordering requirements:

**Process Dependency Chain**

```

```

These dependencies are enforced through:

* State validation in controllers ([controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34)  [controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50) )
* Database constraints on `reservas.estado` field
* Foreign key relationships between `reservas`, `checkin`, and `checkout` tables

Sources: [controllers/CheckinController.php L22-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L47)

 [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)

## Error Handling

All business process endpoints return JSON responses with the following structure:

```

```

Common error scenarios:

| Error Condition | HTTP Status | JSON Response | User Impact |
| --- | --- | --- | --- |
| Invalid reservation state | 200 (with error flag) | `{"success": false, "message": "La reserva no existe o no está confirmada."}` | Alert dialog displayed |
| Duplicate check-in | 200 (with error flag) | `{"success": false, "message": "El check-in ya fue realizado..."}` | Alert dialog displayed |
| Missing check-in before checkout | 200 (with error flag) | `{"success": false, "message": "No se ha realizado el check-in..."}` | Alert dialog displayed |
| Server exception | 200 (with error flag) | `{"success": false, "message": "Error en el servidor: ..."}` | Alert dialog with technical details |

Error responses are handled uniformly in frontend JavaScript using `alert()` or SweetAlert2 dialogs ([assets/js/script_recepcionista.js L129](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L129-L129)

 [assets/js/mucama.js L90-L95](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L90-L95)

).

Sources: [controllers/CheckinController.php L28-L46](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L28-L46)

 [controllers/CheckoutController.php L31-L62](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L31-L62)

 [assets/js/script_recepcionista.js L126-L134](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L126-L134)

 [assets/js/mucama.js L78-L95](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L78-L95)

## Room Status Lifecycle

The `habitacion2.estado` field transitions through the following states during business processes:

**Room Estado State Diagram**

```

```

The room status is updated to `en limpieza` at checkout ([controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)

) and returns to `disponible` after cleaning completion.

Sources: [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)

## Process Timing

The system records timestamps at key events for audit and reporting:

| Timestamp Field | Table | Set During | Value |
| --- | --- | --- | --- |
| `reservas.fecha_entrada` | `reservas` | Booking creation | Guest's intended check-in date |
| `reservas.fecha_salida` | `reservas` | Booking creation | Guest's intended check-out date |
| `checkin.fecha_checkin` | `checkin` | Check-in process | `date('Y-m-d H:i:s')` ([controllers/CheckinController.php L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L50-L50) <br> ) |
| `checkout.fecha_checkout` | `checkout` | Check-out process | `date('Y-m-d H:i:s')` ([controllers/CheckoutController.php L66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L66-L66) <br> ) |

These timestamps enable reporting on early/late arrivals, extended stays, and housekeeping turnaround times.

Sources: [controllers/CheckinController.php L49-L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L49-L51)

 [controllers/CheckoutController.php L65-L67](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L67)

## Summary

The four core business processes form a tightly-coupled workflow where each step depends on the successful completion of previous steps. State validation is enforced at the controller level through SQL queries that check reservation and check-in status before allowing state transitions. The asynchronous notification system coordinates activities between different user roles, ensuring that receptionists are notified of new bookings, clients are notified of confirmations/cancellations, and housekeeping staff are notified of rooms requiring cleaning.

The system maintains a complete audit trail through timestamped records in the `reservas`, `checkin`, and `checkout` tables, while room availability is managed through the `habitacion2.estado` field. All frontend-backend communication follows a consistent pattern of AJAX POST requests with action parameters and JSON responses containing success flags and messages.

For implementation details of individual workflows, see the child pages:

* [Reservation and Booking Flow](/GroveLive/hotelBenedetti/4.1-reservation-and-booking-flow)
* [Check-in Process](/GroveLive/hotelBenedetti/4.2-check-in-process)
* [Check-out and Payment Process](/GroveLive/hotelBenedetti/4.3-check-out-and-payment-process)
* [Room Cleaning and Status Management](/GroveLive/hotelBenedetti/4.4-room-cleaning-and-status-management)