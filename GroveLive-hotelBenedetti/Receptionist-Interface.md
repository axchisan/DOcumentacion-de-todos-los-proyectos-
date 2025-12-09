# Receptionist Interface

> **Relevant source files**
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [controllers/CheckinController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)

## Purpose and Scope

The Receptionist Interface provides front-desk personnel with tools to manage the hotel's daily operational workflows. This interface handles reservation confirmations/cancellations, guest check-ins, guest check-outs, and provides visibility into current guest occupancy and room status. For information about the authentication flow that routes receptionists to this interface, see [Authentication System](/GroveLive/hotelBenedetti/3.1-authentication-system). For details about the backend business process logic, see [Reservation and Booking Flow](/GroveLive/hotelBenedetti/4.1-reservation-and-booking-flow), [Check-in Process](/GroveLive/hotelBenedetti/4.2-check-in-process), and [Check-out and Payment Process](/GroveLive/hotelBenedetti/4.3-check-out-and-payment-process).

**Sources:** [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

---

## Interface Architecture

The receptionist interface follows the system's three-tier architecture with a role-specific presentation layer, dedicated backend controllers, and shared database models.

```

```

**Sources:** [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

---

## Interface Components

The receptionist interface consists of three primary sections, each serving a distinct operational function.

### Notifications Section

Displays pending reservation requests that require receptionist action. Each notification represents a booking created by a cliente that awaits confirmation or cancellation.

**HTML Structure:**

* `#notifications-section` - Container with dark background
* `#notification-list` - Holds individual notification cards
* `.notification` - Individual notification elements with `data-reserva-id` and `data-id` attributes

**Actions:**

* `.confirm-reservation` button - Triggers `confirmReservation()` function
* `.cancel-reservation` button - Opens `#cancel-reason-dialog` modal

**Sources:** [assets/css/StyleRecepcionista.css L112-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L112-L156)

 [assets/js/script_recepcionista.js L3-L9](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L3-L9)

### Guests Section

Shows currently checked-in guests occupying rooms. This table provides a real-time view of hotel occupancy.

**HTML Structure:**

* `#guests-section` - Container for guest information
* `#guest-list table` - Displays guest details with columns for name, room, check-in date, and actions
* `tr[data-reserva-id]` - Each row contains the reservation identifier

**Actions:**

* `.checkout-button` - Opens `#checkout-dialog` modal to process guest departure

**Sources:** [assets/css/StyleRecepcionista.css L159-L202](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L159-L202)

 [assets/js/script_recepcionista.js L21-L23](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L21-L23)

### Rooms Section

Displays all rooms with their current status and details. Includes a refresh button to update room data.

**HTML Structure:**

* `#rooms-section` - Container for room information
* `#refresh-rooms-button` - Manually triggers room list refresh
* `#room-list table` - Displays room number, type, price, and status

**Sources:** [assets/css/StyleRecepcionista.css L205-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L205-L249)

---

## Reservation Management Workflow

The receptionist's primary responsibility is processing incoming reservation requests from clientes. This workflow bridges the booking creation (cliente-initiated) and check-in readiness (receptionist-confirmed).

```

```

### Confirmation Process

The `confirmReservation()` function [assets/js/script_recepcionista.js L31-L58](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L58)

 extracts `data-reserva-id` and `data-id` attributes from the clicked notification element, then sends a POST request to `ReservaController.php` with `action=confirmReservation`. Upon success, the notification is removed from the DOM and `updateNotificationList()` [assets/js/script_recepcionista.js L103-L108](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L103-L108)

 checks if the list is empty to display a "no pending notifications" message.

### Cancellation Process

The cancellation flow requires a two-step interaction:

1. **Dialog Opening:** `openCancelReasonDialog()` [assets/js/script_recepcionista.js L60-L68](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L60-L68)  retrieves reservation and notification IDs, stores them as attributes on `#cancel-reason-form`, and displays the modal dialog using `showModal()`.
2. **Submission:** `cancelReservation()` [assets/js/script_recepcionista.js L70-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L70-L101)  prevents default form submission, extracts the cancellation reason from `#cancel-reason` textarea, and sends the data to `ReservaController.php`. The backend updates the reservation status and creates a notification for the cliente explaining the cancellation.

**Sources:** [assets/js/script_recepcionista.js L31-L108](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L108)

---

## Check-in Process

Check-in transitions a confirmed reservation into active occupancy. The receptionist interface provides check-in buttons for reservations that meet the entry criteria.

```

```

### Client-Side Handler

The `handleCheckin()` function [assets/js/script_recepcionista.js L110-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L110-L135)

 is attached to `.checkin-button` elements during initialization [assets/js/script_recepcionista.js L17-L19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L17-L19)

 It:

1. Locates the parent `<tr>` element containing the reservation data
2. Extracts `data-reserva-id` attribute
3. Sends POST request with `action=checkin` and `reservaId`
4. Displays alert and reloads page on success

### Backend Validation

`CheckinController.php` [controllers/CheckinController.php L18-L54](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L18-L54)

 enforces business rules:

| Validation | Query | Error Message |
| --- | --- | --- |
| Reservation exists and confirmed | `SELECT * FROM reservas WHERE id = :reservaId AND estado = 'confirmada'` | "La reserva no existe o no está confirmada." |
| No duplicate check-in | `SELECT * FROM checkin WHERE reserva_id = :reservaId` | "El check-in ya fue realizado para esta reserva." |

If validations pass, the controller creates a `Checkin` model instance [controllers/CheckinController.php L49-L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L49-L51)

 with:

* `reserva_id` - Links to the reservation
* `fecha_checkin` - Current timestamp
* `observaciones` - Null (no observations)

**Sources:** [assets/js/script_recepcionista.js L110-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L110-L135)

 [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

---

## Check-out and Payment Process

Check-out marks the end of a guest's stay, records payment, transitions the room to cleaning status, and triggers notifications for housekeeping staff.

```

```

### Dialog Management

The checkout process requires payment information input:

1. **Opening Dialog:** `openCheckoutDialog()` [assets/js/script_recepcionista.js L137-L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L143)  extracts `data-reserva-id` from the table row, stores it on the form, and calls `showModal()` on `#checkout-dialog`.
2. **Form Submission:** `handleCheckout()` [assets/js/script_recepcionista.js L145-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L145-L174)  prevents default behavior, retrieves `reservaId` and `totalPagado` value from `#total-pagado` input field, and submits to `CheckoutController.php`.

### Backend Processing

`CheckoutController.php` [controllers/CheckoutController.php L20-L93](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L20-L93)

 performs comprehensive validation and side effects:

**Validation Chain:**

1. Reservation must exist with `estado='confirmada'` [controllers/CheckoutController.php L25-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L37)
2. Check-in record must exist [controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50)
3. No existing checkout record [controllers/CheckoutController.php L53-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L63)

**Side Effects on Success:**

1. Create `Checkout` record with `reserva_id`, `fecha_checkout` (current timestamp), and `total_pagado` [controllers/CheckoutController.php L65-L69](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L69)
2. Update room status to "en limpieza" [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)
3. Create notification for cliente with payment confirmation [controllers/CheckoutController.php L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L75-L78)
4. Query all mucama-role users [controllers/CheckoutController.php L81-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L84)
5. Create cleaning notification for each mucama [controllers/CheckoutController.php L86-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L86-L91)

**Sources:** [assets/js/script_recepcionista.js L137-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L174)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

---

## Dialog Components

The interface uses native HTML `<dialog>` elements for modal interactions requiring user input beyond simple confirmation.

### Cancel Reason Dialog

**Element:** `#cancel-reason-dialog`

**Form:** `#cancel-reason-form`

* Stores `data-reserva-id` and `data-notification-id` attributes
* Contains `#cancel-reason` textarea for explanation text
* Submit button sends cancellation with reason
* Cancel button (`#cancel-cancel-reason`) closes dialog without action

**Styling:** Dark background `#2c2f33`, purple focus borders `#9b59b6`, rounded corners [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

### Checkout Dialog

**Element:** `#checkout-dialog`

**Form:** `#checkout-form`

* Stores `data-reserva-id` attribute
* Contains `#total-pagado` input for payment amount
* Submit button processes checkout
* Cancel button (`#cancel-checkout`) closes dialog without action

**Event Handlers:** Initialized in DOMContentLoaded [assets/js/script_recepcionista.js L11-L28](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L11-L28)

**Sources:** [assets/js/script_recepcionista.js L11-L28](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L11-L28)

 [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

---

## Visual Design

The receptionist interface follows the system's dark theme design language with purple accent colors for interactive elements and role-appropriate visual hierarchy.

### Color Scheme

| Element | Color Code | Usage |
| --- | --- | --- |
| Background overlay | `rgba(35, 39, 42, 0.8)` | Darkens background image |
| Primary background | `#2c2f33` | Header, section containers |
| Secondary background | `#36393f` | Cards, table backgrounds, form inputs |
| Hover background | `#40444b` | Table row hover state |
| Text primary | `#dcddde` | Headers, labels |
| Text secondary | `#b0b3b8` | Notification text |
| Accent purple | `#9b59b6` | Buttons, table headers, links |
| Accent purple dark | `#8e44ad` | Hover states |
| Destructive red | `#f44336` | Cancel buttons |
| Destructive red dark | `#d32f2f` | Cancel hover states |

**Sources:** [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

### Layout Structure

The interface uses a vertical flow layout with centered content:

```

```

**Header Navigation:**

* Flexbox horizontal layout [assets/css/StyleRecepcionista.css L65-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L65-L83)
* Purple links with hover effects
* Responsive: Switches to vertical stack at 768px [assets/css/StyleRecepcionista.css L332-L335](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L332-L335)

**Section Styling:**

* Rounded corners `border-radius: 8px`
* Padding `20px` for breathing room
* Margin bottom `40px` for visual separation

**Sources:** [assets/css/StyleRecepcionista.css L40-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L40-L103)

### Table Design

Both guest and room tables share consistent styling:

```
┌─────────────────────────────────────────┐
│ Header Row (#9b59b6 background)        │
├─────────────────────────────────────────┤
│ Data Row (Hover: #40444b)              │
│ Data Row                                │
│ Data Row                                │
└─────────────────────────────────────────┘
```

**Specifications:**

* Full width `width: 100%`
* Collapsed borders `border-collapse: collapse`
* Cell padding `12px` (desktop), `8px` (tablet), `6px` (mobile)
* Border between rows `1px solid #4a4d52`
* Purple header background `#9b59b6`
* Hover effect darkens row to `#40444b`

**Sources:** [assets/css/StyleRecepcionista.css L165-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L249)

### Button Styling

All action buttons follow a consistent rounded design pattern:

**Primary Actions (Confirm, Checkout):**

* Background: `#9b59b6`
* Hover: `#8e44ad`
* Border radius: `20px` (pill shape)
* Padding: `8px 12px` (compact)

**Destructive Actions (Cancel):**

* Background: `#f44336`
* Hover: `#d32f2f`
* Same border radius and padding

**Transitions:** All buttons include `transition: background-color 0.3s ease` for smooth color changes [assets/css/StyleRecepcionista.css L130-L225](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L130-L225)

**Sources:** [assets/css/StyleRecepcionista.css L130-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L130-L315)

---

## Responsive Breakpoints

The interface adapts to different screen sizes with two primary breakpoints.

### Tablet Breakpoint (768px)

**Changes applied:**

* Header title: `24px` font size (reduced from `28px`)
* Navigation: Flexbox direction changes to `column`
* Main padding: `20px 15px` (reduced from `40px 20px`)
* Table font size: `14px`
* Table cell padding: `8px` (reduced from `12px`)
* Button padding: `8px 10px` with `14px` font size

**Sources:** [assets/css/StyleRecepcionista.css L327-L360](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L360)

### Mobile Breakpoint (480px)

**Additional changes:**

* Logo: `120px` max width (reduced from `150px`)
* Header title: `20px` font size
* Section titles: `22px` (h2), `18px` (h3)
* Notification cards: `10px` padding
* Table font size: `12px`
* Table cell padding: `6px`
* Dialog padding: `15px` (reduced from `20px`)
* Input font size: `14px`

**Sources:** [assets/css/StyleRecepcionista.css L362-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L362-L407)

---

## Background Image and Overlay

The interface uses a layered approach to create visual depth while maintaining text readability:

1. **Base Layer:** Background image `fondo.jpg` with `fixed` attachment creates parallax effect [assets/css/StyleRecepcionista.css L11-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L11-L15)
2. **Overlay Layer:** Pseudo-element `body::before` applies dark semi-transparent overlay `rgba(35, 39, 42, 0.8)` at `z-index: 1` [assets/css/StyleRecepcionista.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L22-L31)
3. **Content Layer:** All body children positioned at `z-index: 2` to appear above overlay [assets/css/StyleRecepcionista.css L34-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L34-L37)

This creates a professional dark interface where the background adds visual interest without compromising legibility.

**Sources:** [assets/css/StyleRecepcionista.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L8-L37)

---

## Error Handling

The JavaScript modules use simple `alert()` dialogs for error feedback. While functional, this represents a potential area for enhancement with the SweetAlert2 library used in other interfaces.

**Current Implementation:**

* Successful operations: Alert + page reload
* Failed operations: Alert with error message from JSON response
* Network errors: Alert with exception message

**Example from check-in:**

```

```

**Sources:** [assets/js/script_recepcionista.js L124-L134](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L124-L134)

 [assets/js/script_recepcionista.js L163-L169](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L163-L169)

---

## Summary

The Receptionist Interface serves as the operational hub for front-desk staff, providing streamlined workflows for reservation management, guest check-in/check-out, and real-time visibility into room occupancy. The interface leverages action-based controller routing, modal dialogs for complex inputs, and maintains the system's dark theme with purple accents for visual consistency across roles.

**Key Technical Characteristics:**

* Event-driven JavaScript with fetch() API for asynchronous operations
* Action-parameter routing to specialized controllers
* Native HTML dialog elements for modal interactions
* Responsive design with 768px and 480px breakpoints
* Backend validation enforcing business rules at database level
* Notification system integration for cross-role communication

**Sources:** [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)