# JavaScript Client-Side Logic

> **Relevant source files**
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)
> * [assets/js/config.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/config.js)
> * [assets/js/login.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js)
> * [assets/js/mucama.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [assets/js/utils.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/utils.js)

## Purpose and Scope

This document details the client-side JavaScript implementation across all user interfaces in the Hotel Benedetti system. It covers the JavaScript modules that handle AJAX communication with PHP controllers, form validation, DOM manipulation, and integration with third-party libraries such as SweetAlert2 and Chart.js. For information about the visual styling applied to these interfaces, see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system). For details on server-side API endpoints that these JavaScript modules communicate with, see [Backend API Design](/GroveLive/hotelBenedetti/6-backend-api-design).

## Module Organization

The JavaScript codebase is organized into role-specific modules, each responsible for a particular user interface. Each module follows the same architectural pattern: event-driven interaction handling, asynchronous data fetching, and dynamic DOM updates.

### JavaScript Module Structure

```

```

**Module Responsibilities:**

| Module | File Path | Primary Responsibilities |
| --- | --- | --- |
| Configuration | `assets/js/config.js` | API base URL definition |
| Utilities | `assets/js/utils.js` | Logging and helper functions |
| Authentication | `assets/js/login.js` | Login/registration form handling |
| Administrator | `assets/js/admin.js` | Dashboard stats, employee CRUD, room CRUD |
| Client | `assets/js/cliente.js` | Room browsing, booking creation, notifications |
| Receptionist | `assets/js/script_recepcionista.js` | Reservation management, check-in/out processing |
| Maid | `assets/js/mucama.js` | Room cleaning notifications and status updates |

**Sources:** [assets/js/config.js L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/config.js#L1-L1)

 [assets/js/utils.js L1-L3](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/utils.js#L1-L3)

 [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [assets/js/mucama.js L1-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L106)

 [assets/js/login.js L1-L133](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L1-L133)

## Common JavaScript Patterns

All JavaScript modules follow consistent patterns for initialization, event handling, and server communication.

### Initialization Pattern

Every module uses the `DOMContentLoaded` event listener to ensure the DOM is fully loaded before attaching event handlers:

```

```

**Examples:**

* [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)  - Admin module initialization with navigation and form handlers
* [assets/js/cliente.js L1-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L12)  - Client module initialization for rooms and bookings
* [assets/js/script_recepcionista.js L1-L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L29)  - Receptionist module initialization for reservations and check-in/out
* [assets/js/mucama.js L1-L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L11)  - Maid module initialization for cleaning tasks

**Sources:** [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)

 [assets/js/cliente.js L1-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L12)

 [assets/js/script_recepcionista.js L1-L29](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L29)

 [assets/js/mucama.js L1-L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L11)

### AJAX Request Pattern

All modules use the `fetch()` API with consistent request/response handling:

```

```

**Standard Fetch Pattern Structure:**

| Step | Code Pattern | Purpose |
| --- | --- | --- |
| 1. Data preparation | `new URLSearchParams({...})` | Encode form data for POST requests |
| 2. HTTP request | `fetch(url, {method, body})` | Send AJAX request to PHP controller |
| 3. Response parsing | `.then(response => response.json())` | Parse JSON response from server |
| 4. Success handling | `if (data.success) {...}` | Update UI on successful operation |
| 5. Error handling | `.catch(error => {...})` | Display error messages to user |

**Sources:** [assets/js/admin.js L39-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L140)

 [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/login.js L8-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L8-L48)

## Administrator Module (admin.js)

The administrator module manages the three-section interface: Dashboard, Employees, and Rooms. It includes Chart.js integration for data visualization.

### Admin Module Function Map

```

```

### Dashboard Statistics Loading

The `loadDashboardStats()` function [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

 fetches aggregated statistics and renders two Chart.js visualizations:

1. **Room Status Pie Chart** [assets/js/admin.js L64-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L64-L90)  - Displays distribution of room states (ocupadas, disponibles, en limpieza)
2. **Employee Bar Chart** [assets/js/admin.js L93-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L93-L124)  - Shows count of recepcionistas and mucamas

**Chart Instance Management:**

* Global variables `roomStatusChartInstance` and `employeeChartInstance` [assets/js/admin.js L5-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L6)  prevent memory leaks
* Existing chart instances are destroyed before creating new ones [assets/js/admin.js L56-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L56-L61)

**Sources:** [assets/js/admin.js L5-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L6)

 [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

### Employee CRUD Operations

| Operation | Function | Controller Action | HTTP Method |
| --- | --- | --- | --- |
| List | `loadEmployees()` [assets/js/admin.js L143-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L205) | `getEmployees` | GET |
| Add | `handleEmployeeSubmit()` [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266) | `addEmployee` | POST |
| Update | `handleEmployeeSubmit()` [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266) | `updateEmployee` | POST |
| Delete | `deleteEmployee()` [assets/js/302-352](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/302-352) | `deleteEmployee` | POST |
| Get by ID | `editEmployee()` [assets/js/admin.js L268-L299](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L268-L299) | `getEmployeeById` | GET |

**Form Handling Logic:**

* The same form handles both add and update operations [assets/js/admin.js L33-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L33-L34)
* Presence of `employee-id` value determines whether to add or update [assets/js/admin.js L211-L230](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L211-L230)
* Button text changes to "Actualizar Empleado" during edit mode [assets/js/admin.js L283](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L283-L283)

**Sources:** [assets/js/admin.js L143-L352](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L352)

### Room CRUD Operations

Room management follows the same pattern as employee management with parallel functions:

* `loadRooms()` [assets/js/admin.js L355-L413](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L355-L413)
* `handleRoomSubmit()` [assets/js/admin.js L416-L468](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L416-L468)
* `editRoom()` [assets/js/admin.js L471-L499](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L471-L499)
* `deleteRoom()` [assets/js/admin.js L502-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L502-L552)

**Sources:** [assets/js/admin.js L355-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L355-L552)

## Client Module (cliente.js)

The client module handles room browsing and booking functionality for guest users.

### Client Module Data Flow

```

```

### Room Loading Functions

**`loadRooms()`** [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

* Fetches available rooms from `HabitacionController.php`
* Dynamically creates room cards with room details
* Displays in `room-grid` element

**`loadAvailableRoomsForBooking()`** [assets/js/cliente.js L44-L65](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L44-L65)

* Populates dropdown select element for booking form
* Same endpoint but different presentation (select options vs. cards)

**Sources:** [assets/js/cliente.js L15-L65](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L65)

### Booking Validation and Submission

The `handleBooking()` function [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 implements multi-step validation:

| Validation Step | Code Location | Error Message |
| --- | --- | --- |
| Required fields | [assets/js/cliente.js L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L78) | "Por favor, completa todos los campos." |
| Past date check | [assets/js/cliente.js L80-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L80-L84) | "La fecha de llegada no puede ser anterior a hoy." |
| Date logic | [assets/js/cliente.js L86-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L86-L89) | "La fecha de salida debe ser posterior a la fecha de llegada." |

**POST Request Payload:**

* `action: "createBooking"`
* `roomId`: Selected room ID
* `checkin`: Check-in date
* `checkout`: Check-out date

**Sources:** [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

### Notification Handling

The `markNotificationAsRead()` function [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

 handles notification dismissal:

1. Extracts `notificationId` from `data-id` attribute [assets/js/cliente.js L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L121-L121)
2. Sends POST request to `NotificacionController.php`
3. Removes notification div from DOM on success [assets/js/cliente.js L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L135-L135)
4. Updates UI if no notifications remain [assets/js/cliente.js L137-L139](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L137-L139)

**Sources:** [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

## Receptionist Module (script_recepcionista.js)

The receptionist module manages reservation workflows, check-in, and check-out processes.

### Receptionist Function Overview

```

```

### Reservation Confirmation and Cancellation

**Confirmation Flow:**

1. User clicks confirm button on notification [assets/js/script_recepcionista.js L3-L5](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L3-L5)
2. `confirmReservation()` extracts `reservaId` and `notificationId` [assets/js/script_recepcionista.js L32-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L32-L34)
3. POST request updates reservation status
4. Notification removed from DOM [assets/js/script_recepcionista.js L49](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L49-L49)

**Cancellation Flow:**

1. User clicks cancel button [assets/js/script_recepcionista.js L7-L9](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L7-L9)
2. `openCancelReasonDialog()` opens modal dialog [assets/js/script_recepcionista.js L67](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L67-L67)
3. User enters cancellation reason
4. `cancelReservation()` sends reason to backend [assets/js/script_recepcionista.js L77-L82](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L77-L82)
5. Dialog closes and notification removed [assets/js/script_recepcionista.js L91-L93](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L91-L93)

**Sources:** [assets/js/script_recepcionista.js L31-L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L101)

### Check-in and Check-out Processing

**Check-in:** [assets/js/script_recepcionista.js L110-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L110-L135)

* Single-step process requiring only `reservaId`
* Creates `Checkin` record via `CheckinController.php`
* Page reload to reflect updated status

**Check-out:** [assets/js/script_recepcionista.js L137-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L137-L174)

* Two-step process requiring `reservaId` and `totalPagado`
* Opens dialog for payment amount input [assets/js/script_recepcionista.js L142](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L142-L142)
* Creates `Checkout` record and triggers room cleaning workflow
* Page reload to reflect updates

**Dialog Element Management:**

* Uses HTML5 `<dialog>` element with `.showModal()` and `.close()` methods
* Cancel buttons close dialogs without submitting [assets/js/script_recepcionista.js L12-L28](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L12-L28)

**Sources:** [assets/js/script_recepcionista.js L110-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L110-L174)

## Maid Module (mucama.js)

The maid module provides housekeeping staff with tools to manage room cleaning notifications.

### Maid Module Function Map

```

```

### Room Cleaning Workflow

The `markRoomAsCleaned()` function [assets/js/mucama.js L55-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L55-L106)

 implements a confirmation-before-action pattern:

1. Extract `habitacionId` from table row's `data-habitacion-id` attribute [assets/js/mucama.js L57](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L57-L57)
2. Display SweetAlert2 confirmation dialog [assets/js/mucama.js L59-L66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L59-L66)
3. If confirmed, POST to `MucamaController.php` with `action=markAsCleaned`
4. Backend updates room `estado` from "en limpieza" to "disponible"
5. Display success message with auto-dismiss timer [assets/js/mucama.js L80-L86](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L80-L86)
6. Reload page to reflect changes [assets/js/mucama.js L87](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L87-L87)

**Sources:** [assets/js/mucama.js L1-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L1-L106)

## Authentication Module (login.js)

The authentication module handles login and registration forms with client-side validation.

### Authentication Flow

```

```

### Login Function

The `login()` function [assets/js/login.js L8-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L8-L48)

 implements the following validation steps:

| Validation | Code Location | Requirement |
| --- | --- | --- |
| Empty fields | [assets/js/login.js L13-L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L13-L16) | Email and password required |
| Email format | [assets/js/login.js L18-L22](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L18-L22) | Regex: `/^[^\s@]+@[^\s@]+\.[^\s@]+$/` |

**Request Parameters:**

* `action: "login"`
* `email`: User email address
* `password`: User password (plain text, hashed server-side)
* `rol`: Selected role (cliente, administrador, recepcionista, mucama)

**Response Handling:**

* On success: Redirect to role-specific interface via `window.location.href` [assets/js/login.js L40](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L40-L40)
* On failure: Display error message using `showError()` [assets/js/login.js L42](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L42-L42)

**Sources:** [assets/js/login.js L8-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L8-L48)

### Registration Function

The `register()` function [assets/js/login.js L50-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L50-L115)

 includes additional validation:

| Field | Validation | Code Location |
| --- | --- | --- |
| All fields | Required | [assets/js/login.js L58-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L58-L61) |
| Email | Regex format | [assets/js/login.js L63-L67](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L63-L67) |
| Password | Minimum 6 characters | [assets/js/login.js L69-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L69-L72) |

**Request Parameters:**

* `action: "register"`
* `nombre`, `apellido`, `dni`, `telefono`, `email`, `password`

**Post-Registration Flow:**

1. Display success alert [assets/js/login.js L100](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L100-L100)
2. Switch to login form via `showLogin()` [assets/js/login.js L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L101-L101)
3. Clear all form fields [assets/js/login.js L102-L107](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L102-L107)

**Sources:** [assets/js/login.js L50-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L50-L115)

### Form Toggle Functions

Two helper functions manage form visibility:

* `showRegister()` [assets/js/login.js L117-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L117-L121)  - Hides login form, shows registration form
* `showLogin()` [assets/js/login.js L123-L127](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L123-L127)  - Hides registration form, shows login form

**Sources:** [assets/js/login.js L117-L127](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L117-L127)

## External Library Integration

The system integrates two major JavaScript libraries for enhanced user experience.

### SweetAlert2 Integration

SweetAlert2 is used across all modules (except cliente.js which uses native alerts) for consistent notification styling.

**Usage Patterns:**

| Use Case | Implementation | Modules Using |
| --- | --- | --- |
| Success notifications | `Swal.fire({icon: 'success', timer: 1500})` | admin.js, mucama.js |
| Error notifications | `Swal.fire({icon: 'error', text: errorMsg})` | admin.js, mucama.js |
| Confirmation dialogs | `Swal.fire({showCancelButton: true})` | admin.js (delete operations), mucama.js |

**Example: Delete Confirmation** [assets/js/admin.js L305-L351](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L305-L351)

```

```

**Sources:** [assets/js/admin.js L126-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L126-L552)

 [assets/js/mucama.js L32-L105](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L32-L105)

### Chart.js Integration

Chart.js is used exclusively in the admin module for dashboard visualizations.

```

```

**Chart Configurations:**

**Room Status Pie Chart** [assets/js/admin.js L64-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L64-L90)

* Type: `pie`
* Labels: ['Ocupadas', 'Disponibles', 'En Limpieza']
* Colors: ['#FF6384', '#36A2EB', '#FFCE56']

**Employee Distribution Bar Chart** [assets/js/admin.js L93-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L93-L124)

* Type: `bar`
* Labels: ['Recepcionistas', 'Mucamas']
* Y-axis: Begins at zero with step size of 1

**Instance Management:**

* Global variables prevent memory leaks [assets/js/admin.js L5-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L6)
* `.destroy()` called before recreating charts [assets/js/admin.js L56-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L56-L61)

**Sources:** [assets/js/admin.js L5-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L124)

## DOM Manipulation Strategies

All modules follow consistent patterns for dynamic content generation and updates.

### Table Generation Pattern

Multiple modules dynamically generate HTML tables from JSON data:

```

```

**Examples:**

* Employee table: [assets/js/admin.js L151-L186](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L151-L186)
* Room table: [assets/js/admin.js L363-L393](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L363-L393)

**Common Elements:**

* Table structure: `<thead>` with column headers, `<tbody>` for data rows
* Action buttons with `data-id` attributes for entity identification
* Event listener attachment after DOM insertion

**Sources:** [assets/js/admin.js L143-L413](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L413)

### Form Manipulation

Forms are reused for both create and update operations:

**State Management:**

1. Hidden `id` field determines mode (empty = add, populated = update)
2. Button text changes based on mode: "Agregar" vs. "Actualizar"
3. Form reset clears all fields and returns to add mode

**Edit Flow:**

1. User clicks "Editar" button with `data-id`
2. `editEmployee()` or `editRoom()` fetches entity data
3. Form fields populated with current values [assets/js/admin.js L275-L282](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L275-L282)
4. Submit button text changes [assets/js/admin.js L283](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L283-L283)

**Sources:** [assets/js/admin.js L208-L499](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L499)

### Notification Removal Pattern

All modules that handle notifications follow the same removal pattern:

1. Extract `notificationId` from closest `.notification` div
2. Send POST request to mark as read
3. Remove div from DOM on success: `notificationDiv.remove()`
4. Check if container is empty and update UI accordingly

**Sources:** [assets/js/cliente.js L119-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L119-L147)

 [assets/js/mucama.js L13-L53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L53)

## Error Handling and User Feedback

Error handling follows a consistent pattern across all modules with slight variations in feedback mechanisms.

### Error Handling Strategy

```

```

### Feedback Mechanisms by Module

| Module | Success Feedback | Error Feedback |
| --- | --- | --- |
| admin.js | SweetAlert2 with auto-dismiss timer | SweetAlert2 error dialog |
| cliente.js | `showMessage()` with green text | `showMessage()` with red text |
| script_recepcionista.js | `alert()` + page reload | `alert()` with error message |
| mucama.js | SweetAlert2 with auto-dismiss | SweetAlert2 error dialog |
| login.js | `alert()` + redirect/form switch | `showError()` inline message |

**SweetAlert2 Success Example** [assets/js/admin.js L239-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L245)

```

```

**Inline Error Example** [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

```

```

**Sources:** [assets/js/admin.js L126-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L126-L552)

 [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

 [assets/js/login.js L129-L133](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L129-L133)

 [assets/js/mucama.js L32-L105](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L32-L105)

## Request/Response Data Structures

All AJAX requests follow consistent data formatting patterns.

### Common Request Format

All POST requests use `URLSearchParams` for form-encoded data:

```

```

### Standard Response Format

All PHP controllers return JSON responses with this structure:

```

```

### Action-Based Routing

All controllers use the `action` parameter to determine which operation to perform:

| Controller | Common Actions |
| --- | --- |
| AdminController.php | getDashboardStats, getEmployees, addEmployee, updateEmployee, deleteEmployee, getRooms, addRoom, updateRoom, deleteRoom |
| HabitacionController.php | getAvailableRooms |
| ReservaController.php | createBooking, confirmReservation, cancelReservation |
| CheckinController.php | checkin |
| CheckoutController.php | checkout |
| NotificacionController.php | markNotificationAsRead |
| MucamaController.php | markAsCleaned |
| AuthController.php | login, register |

**Sources:** [assets/js/admin.js L39-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L552)

 [assets/js/cliente.js L16-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L147)

 [assets/js/script_recepcionista.js L31-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L174)

 [assets/js/mucama.js L13-L106](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/mucama.js#L13-L106)

 [assets/js/login.js L8-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L8-L115)