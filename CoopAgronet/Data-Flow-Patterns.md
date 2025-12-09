# Data Flow Patterns

> **Relevant source files**
> * [backennd/db_interaction/agregar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php)
> * [front end/acciones_cultivos.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js)
> * [front end/agregar_cultivo.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js)
> * [front end/enviar_pregunta.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js)
> * [front end/login.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js)

## Purpose and Scope

This document describes the common data flow patterns used throughout the CoopAgroNet application to move data between the client (browser) and server (PHP backend). These patterns define how user actions trigger API requests, how responses are processed, and how the UI is updated.

For information about the specific API endpoints and their implementation details, see [Backend System](/axchisan/CoopAgronet/2-backend-system). For frontend component structure, see [Frontend System](/axchisan/CoopAgronet/3-frontend-system). For security implications of these patterns, see [Security Considerations](/axchisan/CoopAgronet/4-security-considerations).

---

## Overview of Data Flow Patterns

The CoopAgroNet application employs several recurring data flow patterns across all user interactions:

| Pattern | Description | Used In | Characteristics |
| --- | --- | --- | --- |
| **Form-to-API Submission** | User fills form → FormData created → POST to endpoint → Response processed | Login, Registration, Crop Creation, Support | Synchronous, blocking UI during request |
| **Fetch-Display-Refresh** | Page loads → GET request → Parse JSON → Populate DOM | Crop listing | Executed on DOMContentLoaded and after mutations |
| **Edit-in-Place** | Click edit → Fetch single record → Populate form → Submit updates → Reload | Crop editing | Repurposes create form for updates |
| **Confirmation-Delete** | Click delete → Confirm dialog → DELETE request → Alert response → Reload | Crop deletion | Destructive action with user confirmation |
| **Temporary Feedback** | Submit action → Show message → Auto-dismiss after N seconds | All forms | Non-blocking notifications |

All patterns use the modern `fetch()` API with Promise-based `.then()` chains for asynchronous request handling.

**Sources:** [front end/agregar_cultivo.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js)

 [front end/acciones_cultivos.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js)

 [front end/enviar_pregunta.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js)

 [front end/login.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js)

---

## Form Submission Pattern

### Standard Form-to-API Flow

The most common pattern in the application is form submission to backend endpoints. This pattern is implemented consistently across authentication, crop creation, and support forms.

```

```

**Diagram: Form Submission Pattern with fetch() API**

### Implementation Examples

**Crop Creation Flow**

[front L9-L40](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L9-L40)

 demonstrates the standard pattern:

1. **Event listener setup:** Form submit event captured in `DOMContentLoaded` wrapper
2. **Prevent default:** `e.preventDefault()` stops page reload
3. **FormData creation:** `new FormData(form)` serializes all form fields
4. **Fetch request:** POST to `/backennd/db_interaction/agregar_cultivo.php`
5. **Response handling:** `.then(response => response.text())` parses server response
6. **UI update:** Success message displayed via `#message` element
7. **Form reset:** `form.reset()` clears all fields
8. **Data refresh:** `fetchCultivos()` called to reload table
9. **Auto-dismiss:** `setTimeout()` hides message after 3 seconds

**Backend Processing**

[backennd/db_interaction/agregar_cultivo.php L7-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L26)

 shows server-side handling:

1. **Method check:** `if ($_SERVER["REQUEST_METHOD"] == "POST")`
2. **Parameter extraction:** Each field accessed via `$_POST["field_name"]`
3. **SQL execution:** Direct string interpolation (⚠️ SQL injection vulnerability)
4. **Response:** Text string echoed back to client

**Support Form Flow**

[front L2-L36](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L2-L36)

 follows similar pattern but with JSON response:

1. **FormData creation:** `new FormData(this)` from form element
2. **Fetch to endpoint:** POST to `/backennd/db_interaction/enviar_pregunta.php`
3. **JSON parsing:** `.then(response => response.json())`
4. **Conditional UI:** Message styled based on `data.success` boolean
5. **Longer timeout:** 5-second auto-dismiss instead of 3 seconds

**Sources:** [front L9-L40](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L9-L40)

 [front L2-L36](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L2-L36)

 [backennd/db_interaction/agregar_cultivo.php L7-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L26)

---

## Data Fetching and Display Pattern

### Fetch-Display-Refresh Cycle

The application loads data from the server and populates the DOM dynamically. This pattern is used primarily for displaying the crop list.

```

```

**Diagram: Data Fetching and DOM Population Flow**

### Implementation Details

**fetchCultivos() Function**

[front L41-L76](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L41-L76)

 implements the complete fetch-display cycle:

1. **GET request:** `fetch("/proyecto_pagina_asociacones/CoopAgroNet/backennd/db_interaction/obtener_cultivos.php")`
2. **JSON parsing:** `.then(response => response.json())`
3. **Table clearing:** `tableBody.innerHTML = ""` removes old rows
4. **Iteration:** `data.forEach(cultivo => ...)` processes each record
5. **Row creation:** Template literal constructs HTML with `${cultivo.campo}` interpolation
6. **Button data attributes:** `data-id="${cultivo.id}"` stores ID for edit/delete operations
7. **DOM insertion:** `tableBody.appendChild(row)` adds row to table
8. **Event binding:** `querySelectorAll` finds buttons and attaches `editCultivo`/`deleteCultivo` handlers

**Backend Endpoint**

The backend endpoint `obtener_cultivos.php` (not provided in files but referenced in code) returns a JSON array:

```go
[
  {
    "id": "1",
    "tipo": "Maíz",
    "fecha_siembra": "2024-01-15",
    "cantidad": "100",
    "dueno": "Juan Pérez",
    "edad": "45",
    "ubicacion": "Parcela A",
    "notas": "Riego diario"
  },
  ...
]
```

**Timing Considerations**

The `fetchCultivos()` function is called:

* On page load via `DOMContentLoaded` event
* After successful crop creation ([front L29](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L29-L29) )
* Implicitly after update/delete via `location.reload()` which triggers `DOMContentLoaded` again

**Sources:** [front L41-L76](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L41-L76)

---

## Update and Delete Patterns

### Edit-in-Place Pattern

The edit workflow repurposes the crop creation form for updates using a multi-step data flow.

```

```

**Diagram: Crop Edit Flow with Form State Management**

### Implementation Analysis

**Edit Initialization**

[front L31-L53](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L31-L53)

 orchestrates the edit flow:

1. **ID extraction:** `event.target.dataset.id` retrieves crop ID from button
2. **Fetch single record:** GET request to `obtener_cultivo.php?id=${cultivoId}`
3. **JSON parsing:** `.then(response => response.json())`
4. **Form population:** Each field set via `document.getElementById(...).value = cultivo.campo`
5. **Button mutation:** Submit button text changed to "Actualizar" and ID stored in `dataset.id`
6. **Event handler swap:** Old `addCultivo` listener removed, new `updateCultivo` listener added

**Update Submission**

[front L56-L72](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L56-L72)

 handles the update:

1. **ID retrieval:** `event.target.dataset.id` gets stored crop ID
2. **FormData creation:** `new FormData(document.getElementById("cropForm"))`
3. **ID appending:** `formData.append("id", cultivoId)` adds ID to POST data
4. **Fetch POST:** Request to `editar_cultivo.php` with form data
5. **Alert response:** Text response shown in browser alert
6. **Page reload:** `location.reload()` refreshes entire page

### Delete Pattern with Confirmation

```

```

**Diagram: Delete Confirmation and Page Reload Flow**

[front L14-L28](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L14-L28)

 implements deletion:

1. **ID extraction:** `event.target.dataset.id` from delete button
2. **Confirmation:** `confirm()` blocks with Spanish message
3. **Conditional fetch:** Only proceeds if user confirms
4. **DELETE method:** `fetch(..., {method: "DELETE"})` with ID in query string
5. **Text response:** `.then(response => response.text())`
6. **Alert feedback:** Server message displayed in alert dialog
7. **Full reload:** `location.reload()` refreshes page to show updated table

**Sources:** [front L14-L72](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L14-L72)

---

## Message Feedback Pattern

### Temporary Message Display

The application uses ephemeral messages to provide user feedback without modal dialogs. Two variations exist with different timeout durations.

| Module | Element ID | Success Class | Error Class | Timeout | Clear Method |
| --- | --- | --- | --- | --- | --- |
| agregar_cultivo.js | `#message` | `color: green` | `color: red` | 3000ms | `style.display = "none"` |
| enviar_pregunta.js | `#mensaje-form` | `.success` | `.error` | 5000ms | `innerHTML = ""` |
| login.js | `#login-error` | N/A | `display: block` | Manual | Manual |

### Implementation Patterns

**Crop Creation Messages**

[front L21-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L21-L33)

 uses inline styling:

```

```

**Support Form Messages**

[front L14-L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L14-L20)

 uses CSS classes and HTML injection:

```

```

The backend `enviar_pregunta.php` returns structured JSON:

```

```

or

```

```

**Login Error Messages**

[front L19](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L19-L19)

 uses simpler show/hide pattern without auto-dismiss:

```

```

The error message persists until the user successfully logs in or the page is refreshed.

**Sources:** [front L21-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L21-L33)

 [front L14-L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L14-L20)

 [front L19](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L19-L19)

---

## State Management Approach

### DOM as Source of Truth

The application uses no centralized state management system. Instead, application state is stored in three locations:

1. **Server-side PHP sessions** - User authentication state (`$_SESSION["user"]`)
2. **DOM element values** - Form inputs hold editable data during updates
3. **Backend database** - Persistent data storage

```

```

**Diagram: State Distribution Across Application Layers**

### State During Edit Operations

When editing a crop, state is temporarily held in the form:

1. **Fetch phase:** [front L34-L36](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L34-L36)  retrieves crop data via GET
2. **Population phase:** [front L37-L43](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L37-L43)  writes data to form input values
3. **Storage phase:** Crop ID stored in submit button's `dataset.id` attribute [front L48](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L48-L48)
4. **Modification phase:** User edits form fields (state held in DOM)
5. **Submission phase:** [front L59-L60](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L59-L60)  reads FormData and appends ID

This approach means:

* **No undo capability** - Once form is populated, original values are lost
* **Page reload required** - Updates don't modify existing DOM elements, full reload is used
* **Race conditions possible** - Multiple concurrent edits would conflict

**Sources:** [front L34-L60](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L34-L60)

---

## Event Listener Attachment Patterns

### Race Condition Workaround

The application uses two different strategies for attaching event listeners to dynamically created buttons:

**Strategy 1: setTimeout Delay**

[front L1-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L11)

 uses a 1-second delay:

```

```

This pattern assumes:

* The `fetchCultivos()` call from `agregar_cultivo.js` will complete within 1 second
* The table will be populated before the timeout fires
* Network latency won't exceed 1 second

**Strategy 2: Inline Attachment**

[front L65-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L65-L71)

 attaches listeners immediately after creating rows:

```

```

This creates **duplicate event listeners** because:

1. Listeners attached by `agregar_cultivo.js` after table population
2. Listeners also attached by `acciones_cultivos.js` after 1-second delay
3. Each button ends up with two listeners for the same event

**Proper Solution (Not Implemented)**

Event delegation would eliminate these issues:

```

```

**Sources:** [front L1-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L11)

 [front L65-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L65-L71)

---

## Page Reload Strategy

### Full Page Reload After Mutations

The application uses `location.reload()` as its primary UI update mechanism after data changes:

| Operation | Location | Reload Call |
| --- | --- | --- |
| Crop Update | [front L69](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L69-L69) | `location.reload()` after alert |
| Crop Delete | [front L24](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L24-L24) | `location.reload()` after alert |
| Crop Create | N/A | Calls `fetchCultivos()` instead |

**Flow After Update/Delete:**

```

```

**Diagram: Full Page Reload Cycle**

**Advantages:**

* Simple implementation - no need to manually update DOM elements
* Guaranteed consistency - always shows latest server state
* No state synchronization bugs

**Disadvantages:**

* **Loss of scroll position** - User returns to top of page
* **Loss of form state** - Any data in other forms is cleared
* **Network overhead** - Re-downloads HTML, CSS, JS on every mutation
* **User disruption** - Visible page flash and loading delay
* **Loss of client state** - Any in-memory JavaScript state is destroyed

**Alternative Used for Creation:**

[front L29](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L29-L29)

 uses `fetchCultivos()` to refresh data without full reload, providing better UX.

**Sources:** [front L24](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L24-L24)

 [front L69](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L69-L69)

 [front L29](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L29-L29)

---

## Authentication State Flow

### Login State Management

Authentication state flows through PHP sessions and DOM manipulation:

```

```

**Diagram: Authentication State Flow Through Sessions**

### Implementation Details

**Login Success Path**

[front L14-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L14-L17)

 handles successful authentication:

1. Hide login form via `style.display = "none"`
2. Show user profile section via `style.display = "block"`
3. Populate user name from `data.user` response field

The server ([backennd/db_interaction/login.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/login.php)

 - not in provided files) stores user data in `$_SESSION["user"]` which persists across requests via PHP session cookies.

**Logout Flow**

[front L26-L32](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L26-L32)

 implements logout:

1. POST to `backend/db_interaction/logout.php` (⚠️ note path inconsistency)
2. Server destroys session via `session_destroy()`
3. Client shows login form and hides profile

**State Persistence:**

PHP sessions persist across page navigations because:

* `session_start()` is called in backend endpoints
* Session cookie sent with every request
* No explicit timeout configured (uses PHP default of 1440 seconds)

**Sources:** [front L14-L32](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L14-L32)

---

## Error Handling Patterns

### Catch Block Implementation

All `fetch()` calls include `.catch()` handlers, but with minimal error processing:

**Crop Operations**

```

```

[front L73](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L73-L73)

 [front L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L26-L26)

 [front L52](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L52-L52)

 [front L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L71-L71)

**Support Form**

```

```

[front L27-L34](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L27-L34)

**Login**

```

```

[front L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L22-L22)

### Error Handling Gaps

1. **No user notification** - Most errors only logged to console, invisible to users
2. **No retry mechanism** - Network failures require manual page refresh
3. **No offline detection** - Application doesn't detect or handle offline state
4. **Generic messages** - No differentiation between network errors, server errors, or validation errors
5. **No error boundaries** - Unhandled exceptions crash the JavaScript execution

**Sources:** [front L73](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L73-L73)

 [front L27-L34](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L27-L34)

 [front L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/login.js#L22-L22)

---

## Summary of Data Flow Characteristics

| Characteristic | Pattern Used | Impact |
| --- | --- | --- |
| **Request Format** | FormData for POST, query params for GET | Standard web form encoding |
| **Response Format** | Mixed JSON and text responses | Inconsistent parsing requirements |
| **State Management** | DOM-based, no framework | Simple but limited scalability |
| **UI Updates** | Full page reload or manual DOM manipulation | Inefficient but predictable |
| **Error Handling** | Console logging with minimal user feedback | Poor user experience on errors |
| **Race Conditions** | setTimeout workarounds and duplicate listeners | Fragile timing-dependent code |
| **Authentication** | PHP sessions with cookie-based persistence | Stateful, requires session store |
| **Feedback Mechanism** | Temporary messages with auto-dismiss | Non-intrusive but easy to miss |

These patterns reflect a functional but basic implementation suitable for a prototype or small-scale deployment. For production use, the application would benefit from:

* Centralized state management
* Consistent JSON API responses
* Optimistic UI updates without full page reloads
* Event delegation for dynamic content
* Robust error handling with user-visible feedback
* Request debouncing and loading states

**Sources:** All files referenced throughout this document