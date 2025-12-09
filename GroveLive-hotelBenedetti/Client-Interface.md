# Client Interface

> **Relevant source files**
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/img/fondohotelcliente2.webp](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp)
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)
> * [controllers/ClienteController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php)

## Purpose and Scope

The Client Interface provides the guest-facing web interface for Hotel Benedetti, allowing visitors to browse available rooms, make reservations, view notifications, and access hotel information. This document details the presentation layer components, client-side logic, and backend communication patterns specific to the guest experience.

For backend user management operations, see [Authentication System](/GroveLive/hotelBenedetti/3.1-authentication-system). For server-side booking processing logic, see [Reservation and Booking Flow](/GroveLive/hotelBenedetti/4.1-reservation-and-booking-flow). For overall CSS styling patterns, see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system).

---

## Interface Architecture

The Client Interface consists of three primary layers: the HTML structure (not shown in provided files but referenced by JavaScript), the styling system, and the client-side interaction logic.

```

```

**Sources:** [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

---

## Visual Design System

The interface implements a cohesive dark theme with purple accent colors consistent with the overall Hotel Benedetti brand identity.

### Color Palette

| Element | Color | CSS Variable |
| --- | --- | --- |
| Primary Background | `#23272a` (dark gray) | Body overlay |
| Secondary Background | `#2c2f33` (medium gray) | Header, sections |
| Tertiary Background | `#36393f` (light gray) | Cards, inputs |
| Primary Text | `#dcddde` (light gray) | All text content |
| Secondary Text | `#b0b3b8` (medium gray) | Descriptions |
| Accent Color | `#9b59b6` (purple) | Links, buttons, borders |
| Accent Hover | `#8e44ad` (darker purple) | Interactive states |
| WhatsApp Green | `#25D366` | WhatsApp button |
| Input Border | `#7289da` (blue-purple) | Form inputs |

**Sources:** [assets/css/StyleCliente.css L8-L32](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L32)

 [assets/css/StyleCliente.css L40-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L40-L84)

### Background Treatment

The interface uses a layered background approach:

1. **Base Layer:** Background image (`fondo2.jpg`) with fixed attachment [assets/css/StyleCliente.css L11-L18](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L11-L18)
2. **Overlay Layer:** Semi-transparent dark overlay (`rgba(35, 39, 42, 0.8)`) to ensure text readability [assets/css/StyleCliente.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L22-L31)
3. **Content Layer:** All content positioned with `z-index: 2` above the overlay [assets/css/StyleCliente.css L34-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L34-L37)

**Sources:** [assets/css/StyleCliente.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L37)

 [assets/img/fondohotelcliente2.webp L1-L173](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L173)

---

## Page Structure and Sections

The Client Interface comprises multiple sections organized vertically on a single scrollable page.

```

```

### Header Component

The header contains the hotel logo and navigation menu implemented with flexbox for responsive layout [assets/css/StyleCliente.css L40-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L40-L84)

**Navigation Links:**

* Each link styled with purple color (`#9b59b6`)
* Hover effect: darker purple background (`#36393f`) with rounded corners
* Responsive: switches to column layout on mobile devices [assets/css/StyleCliente.css L404-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L404-L407)

**Sources:** [assets/css/StyleCliente.css L40-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L40-L84)

 [assets/css/StyleCliente.css L399-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L407)

### Notification System

Displays unread notifications for the logged-in guest.

**Component Structure:**

```html
#notifications (section)
  └── .notification (individual notification div)
      ├── <p> (message text)
      └── <button.mark-as-read> (action button)
```

**Styling:**

* Container background: `#2c2f33` [assets/css/StyleCliente.css L114-L119](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L114-L119)
* Individual notification: `#36393f` background with flexbox layout [assets/css/StyleCliente.css L121-L129](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L129)
* Action button: purple with hover effect [assets/css/StyleCliente.css L136-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L136-L149)

**Sources:** [assets/css/StyleCliente.css L113-L150](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L113-L150)

 [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

---

## Room Browsing System

The room browsing functionality dynamically loads available rooms from the backend.

```

```

### Load Rooms Function

The `loadRooms()` function fetches and displays available rooms [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

**Flow:**

1. Fetch from `HabitacionController.php?action=getAvailableRooms`
2. Parse JSON response
3. For each room, create a card element with: * Room number (`numero_habitacion`) * Room type (`tipo`) * Price per night (`precio`) * Current status (`estado`)
4. Append cards to `#room-grid` container
5. Handle errors with fallback message

**Grid Layout:**

* CSS Grid with auto-fit columns (minimum 300px) [assets/css/StyleCliente.css L152-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L156)
* Responsive: adjusts column count based on viewport width

**Sources:** [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

 [assets/css/StyleCliente.css L151-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L151-L156)

---

## Booking System

The booking system allows guests to create reservations with date validation and room selection.

### Booking Form Components

| Field | ID | Type | Validation |
| --- | --- | --- | --- |
| Room Selection | `room-select` | `<select>` | Required, must be valid room ID |
| Check-in Date | `checkin` | `<input type="date">` | Required, cannot be before today |
| Check-out Date | `checkout` | `<input type="date">` | Required, must be after check-in |
| Submit Button | - | `<button>` | Triggers `handleBooking()` |

### Form Styling

**Form Container:** [assets/css/StyleCliente.css L246-L252](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L252)

* Background: `#2c2f33`
* Max-width: 500px, centered
* Border-radius: 8px

**Input Fields:** [assets/css/StyleCliente.css L260-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L277)

* Background: `#36393f`
* Border: `#7289da`
* Focus state: purple glow (`#9b59b6`)

**Submit Button:** [assets/css/StyleCliente.css L279-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L279-L294)

* Background: `#9b59b6`
* Full width, rounded corners (20px)
* Hover: darker purple with shadow effect

**Sources:** [assets/css/StyleCliente.css L245-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L245-L294)

### Booking Workflow

```

```

### Booking Handler Function

The `handleBooking()` function implements the booking logic [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

**Validation Rules:**

1. All fields must be filled [assets/js/cliente.js L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L78)
2. Check-in date cannot be before today [assets/js/cliente.js L80-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L80-L84)
3. Check-out date must be after check-in [assets/js/cliente.js L86-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L86-L89)

**Request Format:**

```

```

**Response Handling:**

* Success: Display success message, reset form, reload room lists
* Error: Display error message with details from server

**Sources:** [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

### Load Available Rooms for Booking

The `loadAvailableRoomsForBooking()` function populates the room selection dropdown [assets/js/cliente.js L44-L65](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L44-L65)

**Dropdown Format:**

```
Habitación [numero] ([tipo]) - $[precio]/noche
```

Example: `Habitación 101 (individual) - $50/noche`

**Sources:** [assets/js/cliente.js L44-L65](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L44-L65)

---

## Notification Management

Guests can view and mark notifications as read through an interactive notification panel.

### Mark Notification as Read

The `markNotificationAsRead()` function handles notification dismissal [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

**Process:**

1. User clicks "mark as read" button on notification
2. Extract notification ID from `data-id` attribute
3. Send POST request to `NotificacionController.php` with: * `action: "markNotificationAsRead"` * `notificationId: <id>`
4. On success: Remove notification DOM element
5. If no notifications remain: Display "No tienes notificaciones nuevas"

**DOM Structure Required:**

```

```

**Sources:** [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

 [assets/css/StyleCliente.css L113-L150](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L113-L150)

---

## Room Type Showcase

The room types section displays hotel room categories with imagery and descriptions.

### Room Type Card Structure

**CSS Grid Layout:** [assets/css/StyleCliente.css L175-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L175-L179)

* Auto-fit columns (minimum 300px)
* 20px gap between cards

**Card Components:**

* Image: 100% width, 200px height, object-fit cover [assets/css/StyleCliente.css L194-L200](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L194-L200)
* Title: 20px font size [assets/css/StyleCliente.css L202-L206](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L202-L206)
* Description: 14px, secondary text color [assets/css/StyleCliente.css L208-L212](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L208-L212)
* Details button: Purple with hover effect [assets/css/StyleCliente.css L214-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L214-L228)

**Interaction:**

* Card hover: translates up 5px [assets/css/StyleCliente.css L190-L192](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L190-L192)
* Button hover: darker purple + shadow [assets/css/StyleCliente.css L225-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L225-L228)

**Sources:** [assets/css/StyleCliente.css L174-L229](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L174-L229)

---

## Additional Interface Sections

### Amenities Section

Displays hotel services as a centered list [assets/css/StyleCliente.css L159-L173](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L159-L173)

**Styling:**

* List items: `#36393f` background, 10px padding
* Max-width: 300px, centered
* No bullet points (`list-style: none`)

### Testimonials Section

Grid layout for guest reviews [assets/css/StyleCliente.css L231-L243](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L231-L243)

**Layout:**

* CSS Grid with auto-fit (300px minimum)
* Cards: `#36393f` background, rounded corners
* Centered text alignment

### Contact Section

Displays contact information and embedded map [assets/css/StyleCliente.css L296-L318](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L296-L318)

**Features:**

* Purple links with hover effect
* Responsive iframe for map
* Centered layout

### FAQ Section

Grid layout for frequently asked questions [assets/css/StyleCliente.css L320-L338](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L320-L338)

**Structure:**

* Auto-fit grid (300px minimum)
* Individual FAQ items in cards
* Question heading + answer text

**Sources:** [assets/css/StyleCliente.css L159-L338](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L159-L338)

---

## Floating Social Media Buttons

Fixed-position buttons for external communication channels.

```

```

**Button Specifications:**

* Size: 50px × 50px circular buttons [assets/css/StyleCliente.css L350-L360](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L350-L360)
* Logo size: 30px × 30px [assets/css/StyleCliente.css L367-L371](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L367-L371)
* Z-index: 1000 (always on top) [assets/css/StyleCliente.css L347](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L347-L347)
* Hover: scale up 1.1× with shadow [assets/css/StyleCliente.css L362-L365](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L362-L365)

**Colors:**

* Instagram: `#9b59b6` (brand purple) [assets/css/StyleCliente.css L373-L379](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L373-L379)
* WhatsApp: `#25D366` (official green) [assets/css/StyleCliente.css L381-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L381-L387)

**Sources:** [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

---

## Client-Side Architecture

The JavaScript module follows an event-driven initialization pattern.

```

```

### Event Listener Registration

All event listeners are registered in the `DOMContentLoaded` callback [assets/js/cliente.js L1-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L12)

:

1. **Room data loading:** Calls `loadRooms()` immediately [assets/js/cliente.js L3](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L3-L3)
2. **Booking dropdown:** Calls `loadAvailableRoomsForBooking()` [assets/js/cliente.js L5](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L5-L5)
3. **Form submission:** Attaches `handleBooking` to `#booking-form` submit event [assets/js/cliente.js L7](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L7-L7)
4. **Notification buttons:** Attaches `markNotificationAsRead` to all `.mark-as-read` buttons [assets/js/cliente.js L9-L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L9-L11)

**Sources:** [assets/js/cliente.js L1-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L12)

### Message Display Function

The `showMessage()` utility function handles user feedback [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

**Parameters:**

* `elementId`: Target DOM element ID
* `message`: Text to display
* `type`: "error" (red) or "success" (green)

**Styling:**

* Error messages: red text
* Success messages: green text
* Display: block (makes element visible)

**Sources:** [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

---

## Backend Communication Patterns

All backend communication uses the Fetch API with JSON response format.

### API Endpoints

| Endpoint | Method | Action Parameter | Purpose | Handler Function |
| --- | --- | --- | --- | --- |
| `HabitacionController.php` | GET | `getAvailableRooms` | Fetch available rooms | `loadRooms()` |
| `HabitacionController.php` | GET | `getAvailableRooms` | Populate booking dropdown | `loadAvailableRoomsForBooking()` |
| `ReservaController.php` | POST | `createBooking` | Create reservation | `handleBooking()` |
| `NotificacionController.php` | POST | `markNotificationAsRead` | Mark notification read | `markNotificationAsRead()` |

### Request Format

**GET Requests:**

```

```

**POST Requests:**

```

```

### Response Format

All endpoints return JSON with a consistent structure:

```

```

**Example - Available Rooms:**

```

```

**Sources:** [assets/js/cliente.js L16-L40](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L40)

 [assets/js/cliente.js L45-L64](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L45-L64)

 [assets/js/cliente.js L98-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L98-L115)

 [assets/js/cliente.js L128-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L128-L146)

---

## Error Handling

The interface implements comprehensive error handling at multiple levels.

### Network Error Handling

All `fetch()` calls include `.catch()` blocks to handle network failures:

**Room Loading Error:** [assets/js/cliente.js L37-L40](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L37-L40)

```

```

**Booking Error:** [assets/js/cliente.js L113-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L113-L115)

```

```

**Notification Error:** [assets/js/cliente.js L144-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L144-L146)

```

```

### Server Response Error Handling

The interface checks the `success` field in JSON responses:

**Booking Response:**

```

```

**Sources:** [assets/js/cliente.js L37-L40](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L37-L40)

 [assets/js/cliente.js L104-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L104-L115)

 [assets/js/cliente.js L134-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L134-L146)

---

## Responsive Design Implementation

The interface adapts to three viewport categories with specific breakpoints.

### Breakpoint Strategy

| Breakpoint | Target Devices | Key Adjustments |
| --- | --- | --- |
| Default | Desktop (>768px) | Full layout, multi-column grids |
| `@media (max-width: 768px)` | Tablets | Reduced font sizes, stacked navigation |
| `@media (max-width: 480px)` | Mobile phones | Further size reductions, compact buttons |

### Tablet Breakpoint (≤768px)

**Header Adjustments:** [assets/css/StyleCliente.css L400-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L400-L407)

* Font size: 28px → 24px
* Navigation: column layout instead of row
* Reduced gap between nav items

**Section Padding:** [assets/css/StyleCliente.css L409-L411](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L409-L411)

* Padding: 40px 20px → 20px 15px

**Floating Buttons:** [assets/css/StyleCliente.css L418-L432](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L418-L432)

* Size: 50px → 45px
* Logo: 30px → 25px
* Adjusted positioning

### Mobile Breakpoint (≤480px)

**Logo Size:** [assets/css/StyleCliente.css L436-L438](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L436-L438)

* Max-width: 150px → 120px

**Typography:** [assets/css/StyleCliente.css L440-L446](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L440-L446)

* H1: 24px → 20px
* H2: 26px → 22px

**Form Elements:** [assets/css/StyleCliente.css L448-L457](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L448-L457)

* Input/button font: 16px → 14px
* Input padding: 10px → 8px
* Form padding: 20px → 15px

**Room Type Cards:** [assets/css/StyleCliente.css L474-L485](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L474-L485)

* Title: 20px → 18px
* Description: 14px → 13px
* Button padding: 10px 15px → 8px 12px

**Floating Buttons:** [assets/css/StyleCliente.css L459-L472](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L459-L472)

* Size: 45px → 40px
* Logo: 25px → 20px
* Closer to edge (10px positioning)

**Sources:** [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

---

## Security Considerations

The Client Interface implements client-side validation as a first line of defense, but relies on server-side validation for security.

### Client-Side Validation

**Date Validation:**

1. Check-in cannot be in the past [assets/js/cliente.js L80-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L80-L84)
2. Check-out must be after check-in [assets/js/cliente.js L86-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L86-L89)
3. All fields must be filled [assets/js/cliente.js L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L78)

**Important Note:** Client-side validation is for user experience only. The backend controllers (ReservaController.php, HabitacionController.php, NotificacionController.php) must implement their own validation to prevent malicious requests.

### Session Management

The ClienteController.php (used for administrative client record management, not the guest interface) implements session-based authentication [controllers/ClienteController.php L16-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php#L16-L20)

 [controllers/ClienteController.php L26-L30](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php#L26-L30)

:

```

```

**Note:** The actual client-facing interface likely has separate authentication handling not shown in the provided files.

**Sources:** [assets/js/cliente.js L75-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L89)

 [controllers/ClienteController.php L16-L30](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php#L16-L30)

---

## Integration Points

The Client Interface integrates with multiple backend systems and external services.

```

```

### Backend Controller Integration

**Room Management:**

* Endpoint: `HabitacionController.php`
* Actions: `getAvailableRooms`
* Usage: Initial load and post-booking refresh

**Reservation Management:**

* Endpoint: `ReservaController.php`
* Actions: `createBooking`
* Usage: Form submission handler

**Notification Management:**

* Endpoint: `NotificacionController.php`
* Actions: `markNotificationAsRead`
* Usage: Notification dismissal

### External Service Integration

**Social Media:**

* Instagram button links to hotel's Instagram profile
* WhatsApp button opens direct message to hotel

**Map Integration:**

* Contact section embeds iframe for location display
* Responsive sizing for mobile devices [assets/css/StyleCliente.css L413-L416](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L413-L416)

**Sources:** [assets/js/cliente.js L16-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L147)

 [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

 [assets/css/StyleCliente.css L312-L317](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L312-L317)