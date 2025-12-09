# Room Cleaning and Status Management

> **Relevant source files**
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/js/mucama.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js)
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)

## Purpose and Scope

This document explains how hotel rooms transition through cleaning states after guest checkout, focusing on the workflow that moves rooms from occupied status through the cleaning process and back to availability. This includes the backend status management, notification distribution to housekeeping staff (mucamas), and the maid interface for marking rooms as cleaned.

For information about the initial checkout process that triggers cleaning, see [Check-out and Payment Process](/GroveLive/hotelBenedetti/4.3-check-out-and-payment-process). For details about the maid user interface design and styling, see [Maid Interface](/GroveLive/hotelBenedetti/3.4-maid-interface). For the notification system architecture, see [Notification System](/GroveLive/hotelBenedetti/6.3-notification-system).

---

## Room Status States

The system tracks room availability through three distinct states stored in the `estado` field of the `habitaciones` table:

| Status | Description | Triggered By | Next State |
| --- | --- | --- | --- |
| `disponible` | Room is clean and ready for booking | Maid marks room as cleaned | `ocupada` (via check-in) |
| `ocupada` | Room is currently occupied by a guest | Check-in process | `en limpieza` (via checkout) |
| `en limpieza` | Room requires housekeeping after checkout | Checkout process | `disponible` (via maid action) |

These states are enforced throughout the system to prevent double-bookings and coordinate housekeeping operations. The administrator dashboard displays real-time counts of rooms in each state via the `getDashboardStats` action.

**Sources:** [controllers/AdminController.php L276-L279](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L276-L279)

 [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)

---

## Complete Status Transition Workflow

```

```

**Sources:** [controllers/CheckoutController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L72-L72)

 [controllers/CheckoutController.php L81-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L81-L91)

---

## Checkout-Triggered Cleaning Process

When a receptionist performs checkout, the `CheckoutController.php` initiates a multi-step process that transitions the room into cleaning state and notifies housekeeping staff:

### Status Update Operation

After validating the reservation and creating the checkout record, the controller updates the room status:

```
CheckoutController.php:72
$habitacion->updateStatus($reserva_data['habitacion_id'], "en limpieza");
```

This call uses the `Habitacion` model's `updateStatus` method to atomically change the room's `estado` field in the database.

### Notification Distribution

The controller queries all users with `rol = 'mucama'` and creates individual notification records for each housekeeping staff member:

```

```

The notification message includes the room ID and reservation context:

```
"La habitación {habitacion_id} necesita limpieza después del check-out de la reserva (ID: {reservaId})."
```

**Sources:** [controllers/CheckoutController.php L65-L92](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L65-L92)

---

## Maid Interface and Cleaning Operations

### Frontend Room List Display

The maid interface (`mucama.php`) displays two key sections:

1. **Notifications section** - Unread cleaning notifications
2. **Rooms section** - Table of rooms with `estado = 'en limpieza'`

Each room in the table includes a "Marcar como Limpiada" button that triggers the cleaning workflow.

```

```

**Sources:** [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

 [assets/css/StyleMucama.css L145-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L145-L205)

### Client-Side Cleaning Workflow

The `markRoomAsCleaned` function in [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

 implements the user interaction:

| Step | Line Range | Action |
| --- | --- | --- |
| Event binding | 8-10 | Attach click handlers to all `.mark-as-cleaned` buttons |
| Extract room ID | 56-57 | Retrieve `data-habitacion-id` attribute from table row |
| User confirmation | 59-66 | Display SweetAlert2 dialog asking for confirmation |
| API request | 68-76 | POST to `MucamaController.php` with `action=markAsCleaned` and `habitacionId` |
| Success handling | 79-88 | Show success message, reload page to refresh room list |
| Error handling | 90-103 | Display error messages via SweetAlert2 |

### Backend Status Update

When the backend receives the `markAsCleaned` action, it:

1. Validates the `habitacionId` parameter
2. Updates the room's `estado` field to `'disponible'`
3. Returns JSON response `{success: true, message: "..."}`

The page reload ([assets/js/mucama.js L87](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L87-L87)

) ensures the cleaned room is removed from the "en limpieza" list and no longer displays in the maid's work queue.

**Sources:** [assets/js/mucama.js L1-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L106)

---

## Notification Management

### Notification Marking

Maids can mark individual notifications as read using the "Marcar como leída" button in the notifications section. This triggers the `markNotificationAsRead` function:

```

```

The function removes the notification DOM element immediately upon successful backend confirmation, providing instant visual feedback to the user.

**Sources:** [assets/js/mucama.js L13-L46](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L46)

 [assets/js/mucama.js L48-L53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L48-L53)

---

## UI Styling and Responsive Design

The maid interface uses consistent dark theme styling with purple accents:

| Component | Styling | Purpose |
| --- | --- | --- |
| Room table | `.mark-as-cleaned` button | Purple (#9b59b6) with hover darkening to #8e44ad |
| Table rows | `tr:hover` background #40444b | Visual feedback for row selection |
| Table headers | Background #9b59b6 | Clear section identification |
| Notifications | `.mark-as-read` button | Rounded purple buttons matching table actions |

The interface is fully responsive with breakpoints at 768px and 480px, adapting table font sizes and button padding for mobile devices.

**Sources:** [assets/css/StyleMucama.css L145-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L145-L281)

---

## Integration with Dashboard Statistics

The administrator dashboard reflects room cleaning status in real-time through the `getDashboardStats` action in `AdminController.php`:

```sql
Query: "SELECT COUNT(*) as en_limpieza FROM habitaciones WHERE estado = 'en limpieza'"
```

This count updates automatically as maids mark rooms as cleaned, providing management visibility into housekeeping workflow efficiency. The statistic is displayed in a Chart.js visualization showing the distribution of room states across the hotel inventory.

**Sources:** [controllers/AdminController.php L276-L279](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L276-L279)

 [controllers/AdminController.php L292-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L292-L302)

---

## Error Handling and Validation

The cleaning workflow includes multiple validation checkpoints:

### Checkout-Side Validation

* Ensures check-in occurred before allowing checkout ([controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50) )
* Prevents duplicate checkout operations ([controllers/CheckoutController.php L53-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L53-L63) )
* Validates reservation is in `confirmada` state

### Maid-Side Validation

* Confirms room exists and is in `en limpieza` state before allowing status change
* SweetAlert2 confirmation prevents accidental status changes
* Backend validates `habitacionId` parameter exists and is valid

All error responses follow the standard JSON format:

```json
{
    "success": false,
    "message": "Error description"
}
```

Frontend error handling displays these messages via SweetAlert2 dialogs for clear user feedback.

**Sources:** [assets/js/mucama.js L90-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L90-L103)

 [controllers/CheckoutController.php L25-L63](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L25-L63)