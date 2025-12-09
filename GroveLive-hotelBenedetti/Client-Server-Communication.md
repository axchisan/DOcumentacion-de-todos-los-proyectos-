# Client-Server Communication

> **Relevant source files**
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)
> * [assets/js/login.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js)
> * [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js)
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)

## Purpose and Scope

This document details the AJAX-based communication architecture used throughout the Hotel Benedetti system. All client-server interactions use the `fetch()` API to send asynchronous HTTP requests, eliminating full page reloads and creating a single-page application experience. This page covers request formats, response structures, error handling patterns, and the integration of user feedback mechanisms.

For information about the backend controller architecture that processes these requests, see [Backend API Design](/GroveLive/hotelBenedetti/6-backend-api-design). For details on how JavaScript modules organize this communication logic, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic).

---

## Communication Architecture

The system implements a consistent AJAX pattern across all user interfaces. JavaScript event handlers capture user interactions, validate input client-side, construct HTTP requests using `URLSearchParams`, and process JSON responses to update the DOM dynamically.

### Request-Response Flow

```

```

**Sources:** [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

---

## Request Patterns

All HTTP requests use either GET (for data retrieval) or POST (for mutations). The system routes requests to specific controller actions via an `action` parameter.

### GET Requests

GET requests retrieve data without side effects. The `action` parameter is passed as a query string parameter.

```

```

**Example GET Request Structure:**

| JavaScript Function | Endpoint | Query Parameters | Line Reference |
| --- | --- | --- | --- |
| `loadDashboardStats()` | `AdminController.php` | `action=getDashboardStats` | [assets/js/admin.js L39](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L39) |
| `loadEmployees()` | `AdminController.php` | `action=getEmployees` | [assets/js/admin.js L144](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L144-L144) |
| `loadRooms()` | `HabitacionController.php` | `action=getAvailableRooms` | [assets/js/cliente.js L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L16) |
| `editEmployee()` | `AdminController.php` | `action=getEmployeeById&employeeId={id}` | [assets/js/admin.js L271](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L271-L271) |

**Sources:** [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

 [assets/js/admin.js L143-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L205)

 [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

### POST Requests

POST requests perform mutations (create, update, delete operations). Request bodies use `URLSearchParams` encoding with `application/x-www-form-urlencoded` content type.

```

```

**Example POST Request Structure:**

The `URLSearchParams` object constructs form-encoded request bodies:

```

```

**Sources:** [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266)

 [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/script_recepcionista.js L31-L58](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L31-L58)

### Action-Based Routing

Controllers dispatch to specific methods based on the `action` parameter:

| Action Parameter | Controller Method | HTTP Method | Purpose |
| --- | --- | --- | --- |
| `getDashboardStats` | `getDashboardStats` | GET | Retrieve room and employee statistics |
| `getEmployees` | `getEmployees` | GET | Retrieve all employees |
| `addEmployee` | `addEmployee` | POST | Create new employee record |
| `updateEmployee` | `updateEmployee` | POST | Update existing employee |
| `deleteEmployee` | `deleteEmployee` | POST | Delete employee record |
| `getRooms` | `getRooms` | GET | Retrieve all rooms |
| `addRoom` | `addRoom` | POST | Create new room |
| `updateRoom` | `updateRoom` | POST | Update existing room |
| `deleteRoom` | `deleteRoom` | POST | Delete room record |

**Sources:** [controllers/AdminController.php L17-L255](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L255)

 [controllers/AdminController.php L256-L322](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L256-L322)

---

## Response Handling

All controllers return JSON-encoded responses with a consistent structure. The JavaScript layer parses these responses and updates the UI accordingly.

### JSON Response Structure

**Success Response Format:**

```

```

**Error Response Format:**

```

```

**Specific Response Examples:**

```

```

**Sources:** [controllers/AdminController.php L292-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L292-L302)

 [controllers/AdminController.php L303-L305](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L303-L305)

 [controllers/AdminController.php L82-L85](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L82-L85)

### Response Processing Pattern

The client-side follows a consistent pattern for processing responses:

```

```

**Sources:** [assets/js/admin.js L236-L265](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L236-L265)

 [assets/js/cliente.js L102-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L102-L116)

 [assets/js/admin.js L323-L350](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L323-L350)

### Success Path Implementation

When `data.success === true`, the client updates the UI and optionally displays success feedback:

```

```

**DOM Update Operations:**

| Operation | Implementation | Example |
| --- | --- | --- |
| Populate Table | `createElement`, `innerHTML`, `appendChild` | [assets/js/admin.js L151-L185](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L151-L185) |
| Reset Form | `form.reset()`, clear hidden fields | [assets/js/admin.js L246-L248](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L246-L248) |
| Refresh Data | Re-invoke load function | [assets/js/admin.js L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L249-L249) |
| Remove Element | `element.remove()` | [assets/js/cliente.js L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L135-L135) |

**Sources:** [assets/js/admin.js L238-L250](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L238-L250)

 [assets/js/admin.js L151-L185](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L151-L185)

### Error Path Implementation

When `data.success === false` or a network error occurs, the system displays error messages using SweetAlert2:

```

```

**Sources:** [assets/js/admin.js L252-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L252-L257)

 [assets/js/admin.js L259-L265](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L259-L265)

 [assets/js/cliente.js L110-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L110-L115)

---

## Error Management

The system implements three layers of error handling: client-side validation, network error catching, and server-side validation with user-friendly error messages.

### Client-Side Validation

JavaScript validates user input before sending requests to prevent unnecessary network calls:

```

```

**Validation Examples:**

| Validation Type | Check | Error Message | Line Reference |
| --- | --- | --- | --- |
| Required Fields | `!field` or `field.trim() === ""` | "Por favor, completa todos los campos." | [assets/js/cliente.js L75-L78](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L78) |
| Email Format | `/^[^\s@]+@[^\s@]+\.[^\s@]+$/` | "Ingresa un correo electrónico válido." | [assets/js/login.js L18-L22](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L18-L22) |
| Password Length | `password.length < 6` | "Contraseña debe tener al menos 6 caracteres." | [assets/js/login.js L69-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L69-L72) |
| Date Logic | `checkout <= checkin` | "Fecha de salida debe ser posterior a fecha de llegada." | [assets/js/cliente.js L86-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L86-L89) |
| Date Range | `checkin < today` | "Fecha de llegada no puede ser anterior a hoy." | [assets/js/cliente.js L80-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L80-L84) |

**Sources:** [assets/js/cliente.js L75-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L89)

 [assets/js/login.js L13-L22](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L13-L22)

 [assets/js/login.js L58-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L58-L72)

### Network Error Handling

The `.catch()` handler intercepts network failures, JSON parsing errors, and unexpected exceptions:

```

```

**Catch Handler Pattern:**

All fetch calls implement consistent error catching:

```

```

**Sources:** [assets/js/admin.js L198-L204](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L198-L204)

 [assets/js/admin.js L259-L265](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L259-L265)

 [assets/js/cliente.js L113-L115](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L113-L115)

### Server-Side Error Handling

PHP controllers wrap all logic in try-catch blocks and return standardized error responses:

```

```

**Server-Side Validation Examples:**

| Validation | Check | Error Response | Line Reference |
| --- | --- | --- | --- |
| Role Validation | `!in_array($role, ['recepcionista', 'mucama'])` | "Rol no válido. Solo se permiten recepcionistas y mucamas." | [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35) |
| Duplicate Email | `SELECT COUNT(*) WHERE email = :email` | "El email ya está registrado." | [controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48) |
| Duplicate DNI | `SELECT COUNT(*) WHERE dni = :dni` | "El DNI ya está registrado." | [controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61) |
| Duplicate Room Number | `SELECT COUNT(*) WHERE numero_habitacion = :roomNumber` | "El número de habitación ya existe." | [controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207) |

**Sources:** [controllers/AdminController.php L10-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L10-L334)

 [controllers/AdminController.php L29-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L61)

 [controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207)

---

## User Feedback Mechanisms

The system uses SweetAlert2 for all user notifications, providing a consistent visual feedback system across all interfaces.

### SweetAlert2 Integration

```

```

**SweetAlert2 Usage Patterns:**

| Pattern | Configuration | Use Case | Line Reference |
| --- | --- | --- | --- |
| Auto-close Success | `timer: 1500, showConfirmButton: false` | Create/Update operations | [assets/js/admin.js L239-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L245) |
| Error Display | `icon: 'error', title: 'Error'` | Validation failures, server errors | [assets/js/admin.js L252-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L252-L257) |
| Confirmation Dialog | `showCancelButton: true, icon: 'warning'` | Delete operations | [assets/js/admin.js L305-L312](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L305-L312) |

**Sources:** [assets/js/admin.js L239-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L245)

 [assets/js/admin.js L252-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L252-L257)

 [assets/js/admin.js L305-L351](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L305-L351)

### Alert vs. SweetAlert2

Different interfaces use different alert mechanisms:

| Interface | Success Feedback | Error Feedback | Confirmation | Line Reference |
| --- | --- | --- | --- | --- |
| Admin | SweetAlert2 | SweetAlert2 | SweetAlert2 | [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js) |
| Cliente | `showMessage()` custom | `showMessage()` custom | N/A | [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155) |
| Recepcionista | `alert()` | `alert()` | Native dialog | [assets/js/script_recepcionista.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js) |

The `showMessage()` function in Cliente interface provides simpler inline feedback:

```

```

**Sources:** [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

 [assets/js/script_recepcionista.js L52](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L52-L52)

 [assets/js/admin.js L239-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L257)

---

## Communication Flow Examples

### Complete Example: Delete Employee

This example traces the full communication cycle for deleting an employee record:

```

```

**Sources:** [assets/js/admin.js L302-L352](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L302-L352)

 [controllers/AdminController.php L187-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L187-L190)

### Complete Example: Client Booking

This example shows the booking creation flow from the client interface:

```

```

**Sources:** [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

 [assets/js/cliente.js L150-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L150-L155)

---

## Summary of Communication Patterns

| Aspect | Implementation | Key Files |
| --- | --- | --- |
| **HTTP Library** | Native `fetch()` API | All JS files |
| **Request Encoding** | `URLSearchParams` (form-encoded) | [assets/js/admin.js L220-L230](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L220-L230) |
| **Routing** | `action` parameter | [controllers/AdminController.php L18-L254](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L18-L254) |
| **Response Format** | JSON with `{success, message, data}` | [controllers/AdminController.php L8](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L8-L8) |
| **Success Feedback** | SweetAlert2 or custom `showMessage()` | [assets/js/admin.js L239-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L245) |
| **Error Feedback** | SweetAlert2 with error icon | [assets/js/admin.js L252-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L252-L257) |
| **Network Error Handling** | `.catch()` handlers on all fetch calls | [assets/js/admin.js L198-L204](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L198-L204) |
| **Server Error Handling** | Try-catch blocks with JSON responses | [controllers/AdminController.php L329-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L329-L334) |
| **Client Validation** | Pre-flight checks before fetch | [assets/js/cliente.js L75-L89](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L75-L89) |
| **Server Validation** | Business logic checks with error messages | [controllers/AdminController.php L29-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L61) |

**Sources:** [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

 [assets/js/login.js L1-L133](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/login.js#L1-L133)

 [assets/js/script_recepcionista.js L1-L174](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/script_recepcionista.js#L1-L174)

 [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)