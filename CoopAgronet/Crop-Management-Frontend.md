# Crop Management Frontend

> **Relevant source files**
> * [front end/acciones_cultivos.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js)
> * [front end/agregar_cultivo.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js)
> * [front end/crop-management.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/crop-management.js)
> * [front end/index.html](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html)

## Purpose and Scope

This page documents the client-side implementation of crop management functionality in CoopAgroNet. It covers the three JavaScript modules that handle CRUD operations for crop records: `agregar_cultivo.js`, `acciones_cultivos.js`, and `crop-management.js`. These modules interact with the `#gestion-cultivos` section of the single-page application to provide create, read, update, and delete capabilities for crop data.

For backend API endpoints that these modules communicate with, see [2.3](/axchisan/CoopAgronet/2.3-crop-management-api). For the overall frontend architecture and page structure, see [3.1](/axchisan/CoopAgronet/3.1-user-interface-structure). Individual module implementations are detailed in pages [3.3.1](/axchisan/CoopAgronet/3.3.1-crop-creation-and-listing), [3.3.2](/axchisan/CoopAgronet/3.3.2-crop-edit-and-delete-actions), and [3.3.3](/axchisan/CoopAgronet/3.3.3-react-crop-component).

**Sources:** [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107)

 [front L1-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L77)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L74)

 [front L1-L165](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/crop-management.js#L1-L165)

## System Architecture

The crop management frontend consists of three JavaScript modules working with shared DOM elements. The system follows a **dual implementation pattern**: vanilla JavaScript handles the primary CRUD operations via direct DOM manipulation, while a React component provides an alternative modern UI approach.

### Module Responsibilities

| Module | Primary Responsibility | Key Functions | Backend Endpoints |
| --- | --- | --- | --- |
| `agregar_cultivo.js` | Crop listing and creation | `fetchCultivos()`, form submit handler | `obtener_cultivos.php`, `agregar_cultivo.php` |
| `acciones_cultivos.js` | Crop editing and deletion | `editCultivo()`, `deleteCultivo()`, `updateCultivo()` | `obtener_cultivo.php`, `editar_cultivo.php`, `eliminar_cultivo.php` |
| `crop-management.js` | React-based alternative UI | `CropManagementApp`, `CropDetailsModal` | None (client-side only) |

### Module Architecture Diagram

```

```

**Sources:** [front L1-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L77)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L74)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/crop-management.js#L1-L74)

 [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107)

## DOM Element Structure

The crop management UI is built around three primary DOM elements within the `#gestion-cultivos` section:

### Form Element: #cropForm

Form inputs map to backend field names using the `name` attribute:

| Input ID | Name Attribute | Backend Field | Type | Required |
| --- | --- | --- | --- | --- |
| `type` | `tipo` | `tipo` | text | Yes |
| `fechaSiembra` | `fecha_siembra` | `fecha_siembra` | date | No |
| `quantity` | `cantidad` | `cantidad` | number | Yes |
| `owner` | `dueno` | `dueno` | text | Yes |
| `age` | `edad` | `edad` | number | Yes |
| `location` | `ubicacion` | `ubicacion` | text | Yes |
| `notes` | `notas` | `notas` | text | No |

The form submit button dynamically changes behavior between "Añadir" (create) and "Actualizar" (update) modes.

**Sources:** [front L74-L85](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L74-L85)

### Table Element: #cropTableBody

The table body is populated dynamically by `fetchCultivos()` with rows containing:

* Seven data columns (tipo, fecha_siembra, cantidad, dueno, edad, ubicacion, notas)
* One actions column with edit and delete buttons using `data-id` attributes

Each button stores the crop ID in its `data-id` attribute for event handlers:

```

```

**Sources:** [front L46-L62](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L46-L62)

 [front L89-L105](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L89-L105)

### Message Element: #message

Hidden by default (`display: none`), this div displays feedback after form submissions. The `agregar_cultivo.js` module controls its visibility and styling:

* Success: green text, 3-second display timeout
* Error: red text, persistent until next action

**Sources:** [front L21-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L21-L33)

 [front L84](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L84-L84)

## Data Flow Patterns

### CRUD Operation Sequence

```

```

**Sources:** [front L7-L40](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L7-L40)

 [front L14-L72](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L14-L72)

## Module Interaction Patterns

### Event Listener Attachment Strategy

The system uses a **delayed attachment pattern** to handle race conditions between table population and event listener registration:

1. **Initial Attachment** (agregar_cultivo.js): Event listeners attached immediately after `fetchCultivos()` populates the table [front L65-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L65-L71)
2. **Delayed Attachment** (acciones_cultivos.js): Additional listeners attached after 1-second delay to ensure table is fully rendered [front L2-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L2-L10)

This dual attachment approach suggests coordination issues between the two modules.

### Form State Management

The form operates in two modes:

**Create Mode (Default)**

* Submit button text: "Añadir"
* Form action: POST to `agregar_cultivo.php`
* Handler: `agregar_cultivo.js` submit listener
* After success: Form resets, table refreshes via `fetchCultivos()`

**Update Mode (Triggered by Edit)**

* Submit button text: "Actualizar"
* Button `dataset.id`: Contains crop ID
* Handler: `updateCultivo()` function from `acciones_cultivos.js`
* After success: Page reload via `location.reload()`

The mode transition occurs in `editCultivo()`:

```

```

**Sources:** [front L31-L52](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L31-L52)

### State Machine: Form Mode Transitions

```

```

**Sources:** [front L31-L52](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L31-L52)

 [front L56-L72](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L56-L72)

 [front L9-L40](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L9-L40)

## Dual Implementation: Vanilla JavaScript vs React

The codebase contains two parallel implementations of crop management UI:

### Vanilla JavaScript Implementation (Active)

**Files:** `agregar_cultivo.js`, `acciones_cultivos.js`

**Characteristics:**

* Direct DOM manipulation using `document.getElementById()` and `querySelector()`
* Imperative event handling with `addEventListener()`
* State stored in DOM (form values, button states, table rows)
* Backend integration via Fetch API
* Page reloads for UI updates after mutations
* Shared global scope (functions accessible between modules)

**Backend Communication:**

| Operation | Endpoint | Method | Response Handling |
| --- | --- | --- | --- |
| Fetch all | `/obtener_cultivos.php` | GET | JSON array, populate table |
| Fetch one | `/obtener_cultivo.php?id=X` | GET | JSON object, populate form |
| Create | `/agregar_cultivo.php` | POST | Text, display message |
| Update | `/editar_cultivo.php` | POST | Text, alert + reload |
| Delete | `/eliminar_cultivo.php?id=X` | DELETE | Text, alert + reload |

**Sources:** [front L1-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L77)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L74)

### React Implementation (Alternative)

**File:** `crop-management.js`

**Characteristics:**

* Component-based architecture with `CropManagementApp` and `CropDetailsModal`
* Declarative UI using React state (`useState` hooks)
* State management via React's virtual DOM
* No backend integration (operates on client-side state only)
* Renders to `#crop-management-app` element (not visible in current HTML)

**Component Structure:**

```

```

**Key Limitation:** The React implementation does not connect to backend APIs, making it a UI prototype rather than a functional alternative.

**Sources:** [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/crop-management.js#L1-L74)

 [front L297-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L297-L300)

### Implementation Comparison

```

```

**Sources:** [front L1-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L77)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L74)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/crop-management.js#L1-L74)

## Code Entity Mapping

### Function-to-Backend Endpoint Mapping

| JavaScript Function | File | Lines | Backend Endpoint | HTTP Method |
| --- | --- | --- | --- | --- |
| `fetchCultivos()` | agregar_cultivo.js | 41-74 | `/obtener_cultivos.php` | GET |
| Form submit handler | agregar_cultivo.js | 9-40 | `/agregar_cultivo.php` | POST |
| `editCultivo()` | acciones_cultivos.js | 31-53 | `/obtener_cultivo.php` | GET |
| `updateCultivo()` | acciones_cultivos.js | 56-72 | `/editar_cultivo.php` | POST |
| `deleteCultivo()` | acciones_cultivos.js | 14-28 | `/eliminar_cultivo.php` | DELETE |

### DOM Selector References

| Selector | Purpose | Module | Lines |
| --- | --- | --- | --- |
| `#cropForm` | Form element reference | agregar_cultivo.js, acciones_cultivos.js | [agregar_cultivo.js L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/agregar_cultivo.js#L2-L2) <br>  [acciones_cultivos.js L59](https://github.com/axchisan/CoopAgronet/blob/e8818744/acciones_cultivos.js#L59-L59) |
| `#cropTableBody` | Table body for crop list | agregar_cultivo.js | [agregar_cultivo.js L3](https://github.com/axchisan/CoopAgronet/blob/e8818744/agregar_cultivo.js#L3-L3) |
| `#message` | Success/error message div | agregar_cultivo.js | [agregar_cultivo.js L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/agregar_cultivo.js#L4-L4) |
| `.edit-btn` | Edit button class selector | agregar_cultivo.js, acciones_cultivos.js | [agregar_cultivo.js L65](https://github.com/axchisan/CoopAgronet/blob/e8818744/agregar_cultivo.js#L65-L65) <br>  [acciones_cultivos.js L3](https://github.com/axchisan/CoopAgronet/blob/e8818744/acciones_cultivos.js#L3-L3) |
| `.delete-btn` | Delete button class selector | agregar_cultivo.js, acciones_cultivos.js | [agregar_cultivo.js L69](https://github.com/axchisan/CoopAgronet/blob/e8818744/agregar_cultivo.js#L69-L69) <br>  [acciones_cultivos.js L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/acciones_cultivos.js#L7-L7) |
| `#type`, `#fechaSiembra`, `#quantity`, etc. | Form input fields | acciones_cultivos.js | [acciones_cultivos.js L37-L43](https://github.com/axchisan/CoopAgronet/blob/e8818744/acciones_cultivos.js#L37-L43) |

**Sources:** [front L1-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L77)

 [front L1-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L1-L74)

## Request/Response Patterns

### FormData Construction

Create and update operations use the `FormData` API to collect form inputs:

```

```

**Sources:** [front L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L12-L12)

 [front L59-L60](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L59-L60)

### Response Handling Patterns

| Operation | Response Type | Success Action | Error Action |
| --- | --- | --- | --- |
| Fetch all | JSON array | Populate table, attach listeners | Log to console |
| Fetch one | JSON object | Populate form, change mode | Log to console |
| Create | Plain text | Display message (3s), reset form, refresh table | Display error message |
| Update | Plain text | Alert user, reload page | Log to console |
| Delete | Plain text | Alert user, reload page | Log to console |

**Key Pattern:** Mutations (create, update, delete) use **optimistic full page reload** strategy instead of partial DOM updates:

```

```

This approach simplifies state management but sacrifices performance and user experience (form data loss, scroll position reset).

**Sources:** [front L18-L39](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L18-L39)

 [front L66-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L66-L71)

 [front L22-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L22-L26)

## Known Issues and Limitations

### Race Condition Workarounds

The 1-second delay in `acciones_cultivos.js` indicates coordination issues:

```

```

This suggests `agregar_cultivo.js` table population and `acciones_cultivos.js` listener attachment happen asynchronously without proper coordination.

**Sources:** [front L2-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L2-L10)

### Missing Mode Reset

After update or delete operations, the form remains in "Update Mode" until page reload. There is no explicit function to reset the form to "Create Mode" without reloading.

### Incomplete React Integration

The React component in `crop-management.js` lacks:

* Backend API integration
* Proper mounting point in DOM (no `#crop-management-app` element exists)
* Coordination with vanilla JS modules

Additionally, the file contains unrelated code (password toggle, registration form handling) suggesting poor module organization.

**Sources:** [front L96-L159](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/crop-management.js#L96-L159)

### Inconsistent Response Handling

Different operations use different feedback mechanisms:

* Create: Custom message div with auto-dismiss
* Update/Delete: Browser `alert()` dialogs
* Read: Silent (console.error only)

This creates an inconsistent user experience across operations.

**Sources:** [front L21-L38](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L21-L38)

 [front L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L23-L23)

 [front L68](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/acciones_cultivos.js#L68-L68)