# Check-out and Payment Process

> **Relevant source files**
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)

## Purpose and Scope

This document details the guest departure workflow, including the initiation of checkout through the receptionist interface, payment amount recording, checkout record creation, automatic room status transitions to "en limpieza" (cleaning), and notification generation for both clients and housekeeping staff. This process follows the check-in procedure (see [4.2](/GroveLive/hotelBenedetti/4.2-check-in-process)) and triggers the room cleaning workflow (see [4.4](/GroveLive/hotelBenedetti/4.4-room-cleaning-and-status-management)).

---

## Checkout Process Overview

The checkout process is initiated exclusively by receptionists through their interface. When a guest departs, the receptionist records the total amount paid, the system creates a checkout record, transitions the room to cleaning status, and generates notifications for relevant parties.

### High-Level Checkout Workflow

```

```

**Sources:** [assets/js/script_recepcionista.js L137-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L174)

 [controllers/CheckoutController.php L20-L93](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L20-L93)

---

## Frontend Implementation

### Receptionist Interface Elements

The receptionist interface displays a table of currently checked-in guests with a checkout button for each entry. The interface styling uses the standard dark theme with purple accents consistent across the system.

| UI Element | Purpose | CSS Class |
| --- | --- | --- |
| Checkout Button | Triggers checkout modal | `.checkout-button` |
| Checkout Dialog | Modal form for payment entry | `#checkout-dialog` |
| Total Paid Input | Numeric input for payment amount | `#total-pagado` |
| Submit Button | Confirms checkout action | Form submit button |
| Cancel Button | Closes modal without action | `#cancel-checkout` |

**Sources:** [assets/css/StyleRecepcionista.css L189-L202](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L189-L202)

 [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

### JavaScript Event Handling

The checkout process is handled through a series of event listeners and functions that manage the modal workflow and AJAX communication.

```

```

**Sources:** [assets/js/script_recepcionista.js L21-L28](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L21-L28)

 [assets/js/script_recepcionista.js L137-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L174)

#### Key JavaScript Functions

**`openCheckoutDialog(event)`** [assets/js/script_recepcionista.js L137-L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L143)

* Extracts `reservaId` from the table row's `data-reserva-id` attribute
* Stores the reservation ID on the form element using `setAttribute()`
* Opens the modal using `showModal()` on the `#checkout-dialog` element

**`handleCheckout(event)`** [assets/js/script_recepcionista.js L145-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L145-L174)

* Prevents default form submission behavior
* Retrieves `reservaId` from form attribute and `totalPagado` from input field
* Constructs URLSearchParams with action "checkout", reservaId, and totalPagado
* Sends POST request to `CheckoutController.php`
* Handles JSON response, displaying success/error alerts
* Reloads the page on successful checkout

**Sources:** [assets/js/script_recepcionista.js L137-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L174)

---

## Backend Processing

### CheckoutController Architecture

The `CheckoutController.php` handles all server-side checkout logic including validation, database operations, and notification generation.

```

```

**Sources:** [controllers/CheckoutController.php L17-L99](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L17-L99)

### Validation Logic

The controller performs three critical validation checks before proceeding with checkout:

1. **Reservation Validation** [controllers/CheckoutController.php L25-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L37) * Queries `reservas` table for matching reservation ID * Verifies reservation exists and has `estado = 'confirmada'` * Returns error if reservation not found or not confirmed
2. **Check-in Verification** [controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50) * Queries `checkin` table for existing check-in record * Ensures guest has actually checked in before allowing checkout * Returns error if no check-in record exists
3. **Duplicate Checkout Prevention** [controllers/CheckoutController.php L53-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L63) * Queries `checkout` table for existing checkout record * Prevents multiple checkouts for the same reservation * Returns error if checkout already performed

**Sources:** [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)

---

## Payment Recording

### Checkout Record Creation

The system creates a checkout record with three key properties stored in the `checkout` database table:

| Property | Source | Description |
| --- | --- | --- |
| `reserva_id` | POST parameter | Links checkout to reservation |
| `fecha_checkout` | `date('Y-m-d H:i:s')` | Timestamp of checkout |
| `total_pagado` | POST parameter | Payment amount entered by receptionist |

**Sources:** [controllers/CheckoutController.php L65-L69](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L69)

### Checkout Model Interaction

```

```

**Sources:** [controllers/CheckoutController.php L13](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L13-L13)

 [controllers/CheckoutController.php L65-L69](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L69)

---

## Room Status Transitions

### Status Update to "en limpieza"

After successful checkout record creation, the system automatically updates the room status to indicate it requires cleaning. This triggers the housekeeping workflow.

```

```

**Sources:** [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)

The `habitacion->updateStatus()` method is called with two parameters:

* `$reserva_data['habitacion_id']` - The room ID from the reservation
* `"en limpieza"` - The new status value

This status change makes the room unavailable for new bookings until a maid marks it as cleaned, transitioning it back to "disponible" status (see [4.4](/GroveLive/hotelBenedetti/4.4-room-cleaning-and-status-management) for room cleaning process).

**Sources:** [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)

---

## Notification System

### Client Notification

The system creates a notification for the guest who checked out, confirming the transaction and recording the amount paid.

**Notification Properties:**

* `usuario_id`: Set to `reserva_data['cliente_id']`
* `reserva_id`: The checkout reservation ID
* `mensaje`: "Se ha realizado el check-out de su reserva (ID: $reservaId). Total pagado: $$totalPagado."

**Sources:** [controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78)

### Housekeeping Notifications

The system queries all users with `rol = 'mucama'` and creates individual cleaning notifications for each housekeeping staff member.

```

```

**Mucama Notification Content:**

* `usuario_id`: Each mucama's user ID
* `reserva_id`: The checkout reservation ID
* `mensaje`: "La habitación {habitacion_id} necesita limpieza después del check-out de la reserva (ID: $reservaId)."

**Sources:** [controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91)

---

## Complete Checkout Sequence

### End-to-End Process Flow

```

```

**Sources:** [assets/js/script_recepcionista.js L145-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L145-L174)

 [controllers/CheckoutController.php L17-L99](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L17-L99)

---

## Error Handling

### Frontend Error Management

The JavaScript implementation handles errors through two mechanisms:

1. **Response Validation** [assets/js/script_recepcionista.js L163-L168](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L163-L168) * Checks `data.success` flag in JSON response * Displays error message via `alert()` if checkout fails * Prevents page reload when error occurs
2. **Network Error Catching** [assets/js/script_recepcionista.js L171-L173](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L171-L173) * `.catch()` block handles fetch failures * Displays connection error messages * Logs error details for debugging

**Sources:** [assets/js/script_recepcionista.js L163-L173](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L163-L173)

### Backend Error Responses

The controller returns structured JSON error responses for various failure conditions:

| Error Condition | HTTP Status | Message |
| --- | --- | --- |
| Invalid reservation | 200 | "La reserva no existe o no está confirmada." |
| Missing check-in | 200 | "No se ha realizado el check-in para esta reserva." |
| Duplicate checkout | 200 | "El check-out ya fue realizado para esta reserva." |
| Invalid action | 200 | "Acción no válida." |
| Wrong HTTP method | 200 | "Método no permitido." |
| Server exception | 200 | "Error en el servidor: {exception message}" |

All responses include `success: false` flag for frontend parsing.

**Sources:** [controllers/CheckoutController.php L32-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L32-L35)

 [controllers/CheckoutController.php L45-L49](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L45-L49)

 [controllers/CheckoutController.php L58-L62](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L58-L62)

 [controllers/CheckoutController.php L95-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L95-L111)

---

## Database Interactions

### Tables Modified During Checkout

```

```

**Sources:** [controllers/CheckoutController.php L65-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L91)

### SQL Operations Performed

1. **Reservation Validation Query** [controllers/CheckoutController.php L25-L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L29) ``` ```
2. **Check-in Verification Query** [controllers/CheckoutController.php L40-L43](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L43) ``` ```
3. **Duplicate Checkout Check** [controllers/CheckoutController.php L53-L56](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L56) ``` ```
4. **Mucama Query** [controllers/CheckoutController.php L81-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L84) ``` ```

**Sources:** [controllers/CheckoutController.php L25-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L84)

---

## Integration with Related Processes

The checkout process integrates with multiple system workflows:

* **Requires:** Valid check-in (see [4.2](/GroveLive/hotelBenedetti/4.2-check-in-process)) must exist before checkout can proceed
* **Triggers:** Room cleaning workflow (see [4.4](/GroveLive/hotelBenedetti/4.4-room-cleaning-and-status-management)) when status changes to "en limpieza"
* **Uses:** Notification system (see [6.3](/GroveLive/hotelBenedetti/6.3-notification-system)) to inform clients and housekeeping staff
* **Updates:** Room status in administrative system (see [3.2](/GroveLive/hotelBenedetti/3.2-administrator-interface)) making it unavailable for booking

The `total_pagado` field captures payment information but does not integrate with external payment gateways or financial systems in the current implementation.

**Sources:** [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)