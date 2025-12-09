# Support Form Frontend

> **Relevant source files**
> * [backennd/db_interaction/enviar_pregunta.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php)
> * [front end/enviar_pregunta.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js)
> * [front end/index.html](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html)

## Purpose and Scope

This document describes the client-side implementation of the support form system, specifically the [`front end/enviar_pregunta.js`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js`)

() JavaScript module that handles user question submissions. This module manages form submission, communicates with the backend endpoint, displays feedback messages with automatic dismissal, and clears form data upon successful submission.

For information about the backend endpoint that processes these questions, see [Support Question System](/axchisan/CoopAgronet/2.4-support-question-system). For the overall user interface structure, see [User Interface Structure](/axchisan/CoopAgronet/3.1-user-interface-structure).

**Sources:** [front L1-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L1-L37)

---

## System Overview

The support form frontend is a self-contained JavaScript module that attaches to the `#support-form` element in the main HTML document. It intercepts form submissions, sends data to the backend via the Fetch API, and provides visual feedback through the `#mensaje-form` div. The system implements a user-friendly pattern with automatic message dismissal after 5 seconds and form clearing on successful submission.

**Key Components:**

| Component | DOM Element ID | Purpose |
| --- | --- | --- |
| Support Form | `support-form` | HTML form containing three input fields (nombre, correo, pregunta) |
| Message Display | `mensaje-form` | Feedback container for success/error messages |
| Submit Handler | `enviar_pregunta.js` | JavaScript module managing the submission workflow |

**Sources:** [front L1-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L1-L37)

 [front L270-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L270-L285)

---

## DOM Structure and Element Relationships

```

```

**DOM Element Details:**

* **Form Element** [index.html L275-L280](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/index.html#L275-L280) : Contains three required fields with appropriate input types and placeholders
* **Message Container** [index.html L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/index.html#L281-L281) : Empty div that receives dynamically injected feedback messages
* **Input Fields**: Use `name` attributes that match backend POST parameter expectations (nombre, correo, pregunta)

**Sources:** [front L270-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L270-L285)

 [front L1-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L1-L37)

---

## Event Handling Flow

```

```

**Event Handler Registration:**

The module uses the `DOMContentLoaded` event [enviar_pregunta.js L1](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L1-L1)

 to ensure the DOM is fully loaded before attaching the submit event listener [enviar_pregunta.js L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L2-L2)

 This prevents race conditions where the script might execute before the form element exists.

**Sources:** [front L1-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L1-L37)

---

## FormData Construction and Submission

### FormData Object

The system uses the native `FormData` Web API to automatically extract all form input values:

```

```

At [enviar_pregunta.js L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L5-L5)

 the handler constructs a FormData object from the form element (`this` refers to `#support-form`). This automatically captures:

| Field Name | Input Element | Data Type |
| --- | --- | --- |
| `nombre` | `input[name="nombre"]` | String (required) |
| `correo` | `input[name="correo"]` | Email (required) |
| `pregunta` | `textarea[name="pregunta"]` | Text (required) |

### Fetch API Request

The submission uses the Fetch API with the following configuration:

**Endpoint URL** [enviar_pregunta.js L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L8-L8)

:

```
/proyecto_pagina_asociacones/CoopAgroNet/backennd/db_interaction/enviar_pregunta.php
```

**Request Parameters:**

* **Method**: `POST` [enviar_pregunta.js L9](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L9-L9)
* **Body**: FormData object [enviar_pregunta.js L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L10-L10)
* **Content-Type**: Automatically set to `multipart/form-data` by the browser when using FormData

The fetch operation is promise-based, with `.then()` chains for response parsing [enviar_pregunta.js L12-L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L12-L13)

 and data handling [enviar_pregunta.js L13-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L13-L26)

**Sources:** [front L5-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L5-L11)

 [front L275-L280](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L275-L280)

---

## Response Handling and User Feedback

```

```

### Response Structure

The backend returns JSON with the following structure (see [`backennd/db_interaction/enviar_pregunta.php`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php`)

):

| Field | Type | Description |
| --- | --- | --- |
| `success` | Boolean | Indicates operation success |
| `message` | String | Feedback text with emoji prefix (✅, ❌, ⚠️) |

### Message Display Logic

The message display logic [enviar_pregunta.js L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L15-L15)

 uses conditional class assignment:

```

```

This creates a `<p>` element with:

* **Class `success`**: Green styling when `data.success === true`
* **Class `error`**: Red styling when `data.success === false`

### CSS Classes

The CSS classes applied to messages are expected to be defined in [`front end/style.css`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/style.css`)

 and provide visual distinction for different message types.

**Sources:** [front L12-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L12-L26)

 [backennd/db_interaction/enviar_pregunta.php L14-L47](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L14-L47)

---

## Auto-Dismiss Mechanism

```

```

### Timeout Implementation

The module implements a consistent 5-second auto-dismiss pattern for all message types:

**Success/Error Messages** [enviar_pregunta.js L18-L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L18-L20)

:

```

```

**Network Error Messages** [enviar_pregunta.js L31-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L31-L33)

:

```

```

### Behavior Characteristics

* **Non-Blocking**: The timeout runs asynchronously; users can submit a new question before the previous message clears
* **No Cancellation**: If a user submits multiple times rapidly, multiple timeouts will be active simultaneously
* **Complete Removal**: Sets `innerHTML = ""` rather than hiding with CSS, completely removing the message from the DOM

**Sources:** [front L17-L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L17-L20)

 [front L31-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L31-L33)

---

## Form State Management

### Conditional Form Clearing

The system implements smart form state management based on submission outcome:

| Condition | Action | Code Reference |
| --- | --- | --- |
| Success response | Clear all fields | [enviar_pregunta.js L23-L24](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L23-L24) |
| Error response | Preserve form data | No reset call |
| Network error | Preserve form data | No reset call |

**Success Path** [enviar_pregunta.js L23-L24](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L23-L24)

:

```

```

This ensures that:

* On successful submission, the user gets a clean form for new questions
* On errors (unregistered email, validation failure), the user can correct issues without re-entering all data

### Form Reset Behavior

The native `.reset()` method:

* Clears all input and textarea values
* Resets to default/placeholder state
* Does not trigger `change` or `input` events
* Does not affect the submit button state

**Sources:** [front L22-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L22-L25)

---

## Error Handling

### Error Categories

The system handles three distinct error scenarios:

```

```

### 1. Backend Validation Errors

Handled in the `.then()` chain [enviar_pregunta.js L13-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L13-L26)

 when `data.success === false`. Backend can return:

* Empty field validation error [enviar_pregunta.php L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L14-L14)
* Unregistered email error with HTML link [enviar_pregunta.php L30-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L30-L33)
* Database insertion error [enviar_pregunta.php L47](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L47-L47)

### 2. Network/Fetch Errors

Caught in the `.catch()` block [enviar_pregunta.js L27-L34](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L27-L34)

 when:

* Network connection fails
* Server is unreachable
* Request times out
* Non-200 HTTP status codes (before JSON parsing)

**Error Message** [enviar_pregunta.js L29](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L29-L29)

:

```

```

### 3. Console Logging

Network errors are logged to the console [enviar_pregunta.js L28](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L28-L28)

 for debugging:

```

```

This provides developers with detailed error information while showing users a user-friendly message.

**Sources:** [front L27-L34](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L27-L34)

 [backennd/db_interaction/enviar_pregunta.php L13-L47](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L13-L47)

---

## Backend Integration

### Request Flow

```

```

### POST Parameter Mapping

The FormData keys map directly to PHP `$_POST` variables:

| Frontend Input | FormData Key | Backend Variable |
| --- | --- | --- |
| `name="nombre"` | `nombre` | `$_POST['nombre']` (line 9) |
| `name="correo"` | `correo` | `$_POST['correo']` (line 10) |
| `name="pregunta"` | `pregunta` | `$_POST['pregunta']` (line 11) |

### Response Handling Contract

The frontend expects JSON responses with this structure:

```

```

All backend paths return this format:

* Validation error [enviar_pregunta.php L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L14-L14)
* Unregistered user [enviar_pregunta.php L30-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L30-L33)
* Success [enviar_pregunta.php L45](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L45-L45)
* Database error [enviar_pregunta.php L47](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L47-L47)

### Special Case: Registration Link

When a user's email is not registered, the backend returns HTML within the message [enviar_pregunta.php L32](https://github.com/axchisan/CoopAgronet/blob/e8818744/`backennd/db_interaction/enviar_pregunta.php#L32-L32)

:

```

```

The `#activate-register` ID is intended to be handled by other JavaScript modules (likely [`front end/login.js`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/login.js`)

) to trigger the registration modal, though this specific handler is not implemented in `enviar_pregunta.js`.

**Sources:** [front L8-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L8-L26)

 [backennd/db_interaction/enviar_pregunta.php L1-L54](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L1-L54)

---

## Module Characteristics

### Initialization Pattern

* **Event**: `DOMContentLoaded` [enviar_pregunta.js L1](https://github.com/axchisan/CoopAgronet/blob/e8818744/`front end/enviar_pregunta.js#L1-L1)
* **Scope**: Self-contained IIFE (Immediately Invoked Function Expression) via the event listener
* **Dependencies**: No external dependencies; uses native Web APIs only

### Web APIs Used

| API | Purpose | Lines |
| --- | --- | --- |
| `addEventListener` | DOM event handling | 1-2 |
| `preventDefault` | Stop form submission | 3 |
| `FormData` | Extract form values | 5 |
| `fetch` | HTTP request | 8-11 |
| `setTimeout` | Auto-dismiss timer | 18-20, 31-33 |
| `getElementById` | DOM element access | 2, 6, 24 |

### No External Libraries

Unlike other frontend modules that use React or jQuery, this module uses only native JavaScript APIs, making it:

* Lightweight (no bundle size overhead)
* Fast to initialize (no library parsing)
* Self-contained (no version conflicts)
* Browser-compatible (uses widely supported APIs)

**Sources:** [front L1-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L1-L37)

---

## Code Structure Summary

The complete module follows this logical flow:

1. **Wait for DOM** [`line 1`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`line 1`)  → Ensures all elements exist
2. **Attach submit listener** [`line 2`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`line 2`)  → Intercept form submission
3. **Prevent default** [`line 3`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`line 3`)  → Stop browser navigation
4. **Collect form data** [`line 5`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`line 5`)  → Build FormData object
5. **Get message container** [`line 6`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`line 6`)  → Reference for updates
6. **Send request** [`lines 8-11`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`lines 8-11`)  → POST to backend
7. **Parse response** [`line 12`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`line 12`)  → Convert to JSON
8. **Handle data** [`lines 13-26`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`lines 13-26`)  → Process success/error
9. **Catch errors** [`lines 27-34`](https://github.com/axchisan/CoopAgronet/blob/e8818744/`lines 27-34`)  → Handle network failures

The module is **36 lines** of well-structured, single-purpose code that handles one specific task: support form submission and feedback.

**Sources:** [front L1-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/enviar_pregunta.js#L1-L37)