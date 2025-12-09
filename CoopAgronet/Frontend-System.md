# Frontend System

> **Relevant source files**
> * [front end/agregar_cultivo.js](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js)
> * [front end/index.html](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html)
> * [front end/style.css](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css)

## Purpose and Scope

This document describes the client-side architecture of CoopAgroNet, covering the single-page application structure, JavaScript module organization, UI component hierarchy, and styling system. The frontend communicates with backend PHP endpoints documented in [Backend System](/axchisan/CoopAgronet/2-backend-system) to provide crop management, user authentication, and support features.

For backend API endpoints and database interactions, see [Backend System](/axchisan/CoopAgronet/2-backend-system). For security vulnerabilities in the system, see [Security Considerations](/axchisan/CoopAgronet/4-security-considerations).

---

## Application Architecture Overview

CoopAgroNet implements a single-page application (SPA) pattern using hash-based navigation, where all content resides in a single HTML document with sections toggled via anchor links.

### Core Technologies

| Technology | Version/Source | Purpose |
| --- | --- | --- |
| HTML5 | Standard | Application shell and semantic markup |
| CSS3 | Custom ([front end/style.css](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css) <br> ) | Visual presentation and responsive design |
| Vanilla JavaScript | ES6+ | Core application logic and DOM manipulation |
| React | 18.0 (CDN) | Alternative UI for crop management |
| Babel Standalone | Latest (CDN) | JSX transpilation for React components |
| Font Awesome | 6.0.0 (CDN) | Icon library |
| Boxicons | 2.1.4 (CDN) | Additional icon set |

**Sources:** [front L8-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L8-L11)

 [front L297-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L297-L300)

---

## Single Page Application Structure

### HTML Document Organization

The application shell ([front end/index.html](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html)

) defines a hierarchical structure with seven main sections, each accessible via hash navigation:

```

```

**Sources:** [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)

### Navigation System

The navigation bar uses hash-based routing with icon-labeled links:

| Hash Link | Icon Class | Target Section | Purpose |
| --- | --- | --- | --- |
| `#inicio` | `fas fa-home` | Welcome | Platform introduction and features |
| `#gestion-cultivos` | `fas fa-seedling` | Crop Management | CRUD operations for crops |
| `#comunidad` | `fas fa-users` | Community | Social feed (static) |
| `#suscripciones` | `fas fa-money-bill-wave` | Subscriptions | Three-tier pricing |
| `#login` | `fas fa-sign-in-alt` | Authentication | Login/register forms |
| `#admin` | `fas fa-user-shield` | Admin Panel | Administrative interface |
| `#soporte` | `fas fa-question-circle` | Support | Help and questions |

**Sources:** [front L22-L31](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L22-L31)

---

## JavaScript Module Architecture

The frontend employs a modular JavaScript architecture where each `.js` file manages a specific feature domain. Modules communicate through DOM manipulation and AJAX requests.

### Module Dependency Map

```

```

**Sources:** [front L292-L296](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L296)

 [front L1-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L77)

### Module Loading Order

Modules are loaded synchronously at the end of `<body>`:

1. `login.js` - Authentication state management
2. `enviar_pregunta.js` - Support form handler
3. `agregar_cultivo.js` - Crop creation and list fetching
4. `acciones_cultivos.js` - Crop edit/delete operations
5. React/ReactDOM/Babel (CDN)
6. `crop-management.js` (JSX, transpiled by Babel)
7. Inline script for form toggle logic

**Sources:** [front L292-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L300)

---

## User Authentication Interface

### Form Structure and State Management

The authentication section (`#login`) contains two forms that toggle visibility via JavaScript:

```

```

**Sources:** [front L177-L246](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L177-L246)

 [front L302-L318](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L302-L318)

### Login Form DOM Elements

| Element ID | Type | Purpose | Parent Module |
| --- | --- | --- | --- |
| `#login-form` | `<form>` | Container for login inputs | `login.js` |
| `input[name="correo"]` | Email input | User email identifier | - |
| `#login-password` | Password input | User password (masked) | - |
| `.toggle-password` | Button | Show/hide password toggle | Inline script |
| `#login-btn` | Submit button | Trigger authentication | - |
| `#login-error` | Paragraph | Error message display | - |
| `#show-register-form` | Link | Switch to registration | Inline handler |

**Sources:** [front L182-L205](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L182-L205)

### Registration Form DOM Elements

| Element ID | Type | Purpose | Validation |
| --- | --- | --- | --- |
| `#register-form` | `<form>` | Container for registration | POST to `create_acount.php` |
| `input[name="nombre"]` | Text input | Full name | Required |
| `input[name="correo"]` | Email input | Email address | Required, unique |
| `#register-password` | Password input | Password | Required |
| `#confirm-password` | Password input | Password confirmation | Must match |
| `#message-box` | Div | Success/error display | - |
| `#show-login-form` | Link | Switch to login | Inline handler |

**Sources:** [front L214-L244](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L214-L244)

### Password Visibility Toggle

Each password field is wrapped in a `.password-container` with a toggle button:

```html
<div class="password-container">
  <input type="password" id="login-password" class="pass">
  <button type="button" class="toggle-password" data-target="login-password">
    <i class="fas fa-eye"></i>
  </button>
</div>
```

The toggle button uses `data-target` attribute to reference its input field. Styling positions the button absolutely within the container ([front L317-L343](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L317-L343)

).

**Sources:** [front L186-L191](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L186-L191)

 [front L317-L353](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L317-L353)

---

## Crop Management Interface

### Form and Table Architecture

The crop management section (`#gestion-cultivos`) combines a creation form and a live-updating table:

```

```

**Sources:** [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107)

 [front L41-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L41-L74)

### Crop Form Field Mapping

The form uses mixed English/Spanish naming conventions:

| Input ID | Name Attribute | Placeholder | Type | Backend Field |
| --- | --- | --- | --- | --- |
| `#type` | `tipo` | "Tipo de Cultivo" | Text | `tipo` |
| `#fechaSiembra` | `fecha_siembra` | "Fecha de siembra" | Date picker | `fecha_siembra` |
| `#quantity` | `cantidad` | "Cantidad Producida (kg)" | Number | `cantidad` |
| `#owner` | `dueno` | "Dueño del Cultivo" | Text | `dueno` |
| `#age` | `edad` | "Edad del Cultivo (días)" | Number | `edad` |
| `#location` | `ubicacion` | "Ubicación del Cultivo" | Text | `ubicacion` |
| `#notes` | `notas` | "Notas Adicionales" | Text | `notas` |

**Note:** The date input uses `onfocus` and `onblur` handlers to toggle between `type="text"` and `type="date"` for better UX ([front L76-L77](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L76-L77)

).

**Sources:** [front L75-L82](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L75-L82)

### Crop List Fetching (agregar_cultivo.js)

The `fetchCultivos()` function retrieves and renders all crops:

**Function Flow:**

1. **Fetch Request:** GET to `/proyecto_pagina_asociacones/CoopAgroNet/backennd/db_interaction/obtener_cultivos.php`
2. **Response Parsing:** Convert JSON array to JavaScript objects
3. **Table Clearing:** `tableBody.innerHTML = ""`
4. **Row Generation:** For each crop, create `<tr>` with 8 `<td>` elements
5. **Button Attachment:** Append edit and delete buttons with `data-id` attributes
6. **Event Binding:** Attach `editCultivo` and `deleteCultivo` handlers

**Row Template:**

```

```

**Sources:** [front L41-L74](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L41-L74)

### Form Submission Handler (agregar_cultivo.js)

The form submission process:

1. **Event Prevention:** `e.preventDefault()` stops page reload
2. **Data Extraction:** `new FormData(form)` captures all input values
3. **POST Request:** Sends FormData to `agregar_cultivo.php`
4. **Success Handling:** * Display success message in `#message` (green text) * Reset form with `form.reset()` * Call `fetchCultivos()` to refresh table * Auto-hide message after 3 seconds
5. **Error Handling:** Display red error message

**Sources:** [front L9-L40](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L9-L40)

### Module Initialization Timing

The entire module wraps in `DOMContentLoaded` event listener:

```

```

This ensures DOM elements exist before manipulation.

**Sources:** [front L1-L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L1-L7)

---

## Crop Edit and Delete Operations

While the `acciones_cultivos.js` file is not provided in the source files, its functionality is referenced in [front L66-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L66-L71)

 where event listeners are attached to dynamically generated buttons.

### Expected Functionality (from Context)

Based on the high-level architecture diagrams and button structure:

**Edit Flow:**

1. User clicks `.edit-btn` with `data-id` attribute
2. `editCultivo()` handler: * Fetches crop data via GET to `obtener_cultivo.php?id=X` * Populates `#cropForm` fields with existing values * Changes submit button text to "Actualizar" * Modifies form action to update mode
3. On submit, POST to `editar_cultivo.php` with updated data
4. Success triggers `fetchCultivos()` to refresh table

**Delete Flow:**

1. User clicks `.delete-btn` with `data-id` attribute
2. `deleteCultivo()` handler: * Shows browser confirmation dialog * If confirmed, sends DELETE request to `eliminar_cultivo.php?id=X` * Success triggers `fetchCultivos()` to refresh table

**Timing Workaround:**
The event listeners are attached inside `fetchCultivos()` after table population to ensure buttons exist in the DOM. This suggests potential race condition handling ([front L64-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L64-L71)

).

**Sources:** [front L57-L71](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L57-L71)

---

## React Crop Management Component

The application includes a React-based alternative UI via `crop-management.js`, loaded as a Babel-transpiled JSX module.

### React Integration Setup

```

```

**Sources:** [front L297-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L297-L300)

### Script Loading Configuration

The React stack is loaded in specific order:

```

```

**Key Points:**

* **Development Build:** Uses `react.development.js` (not production build)
* **CORS Enabled:** `crossorigin` attribute for CDN resources
* **JSX Transpilation:** `type="text/babel"` triggers client-side transpilation
* **Render Target:** Expected to mount in `#admin-panel` or alternative container

**Sources:** [front L297-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L297-L300)

### Dual UI Strategy

The application maintains both vanilla JavaScript and React implementations:

| Approach | Files | Rendering | State Management |
| --- | --- | --- | --- |
| Vanilla JS | `agregar_cultivo.js`, `acciones_cultivos.js` | Direct DOM manipulation | URL parameters, page reloads |
| React | `crop-management.js` | Component-based | React state hooks |

This dual approach suggests either:

* A/B testing different UI paradigms
* Gradual migration from vanilla JS to React
* Demonstration of framework flexibility

**Sources:** Context from high-level diagrams

---

## Support Form Interface

The support section (`#soporte`) provides a contact form for user questions and issues.

### Form Structure

```

```

**Sources:** [front L270-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L270-L285)

### Support Form Fields

| Element | Attributes | Purpose |
| --- | --- | --- |
| `input[name="nombre"]` | `type="text"`, `required`, `placeholder="Nombre Completo"` | User identification |
| `input[name="correo"]` | `type="email"`, `required`, `placeholder="Correo Electrónico"` | User contact (must be registered) |
| `textarea[name="pregunta"]` | `rows="4"`, `required`, `placeholder="Describe tu problema o pregunta"` | Question content |
| `button[type="submit"]` | Icon: `fas fa-paper-plane` | Submission trigger |
| `#mensaje-form` | Hidden by default | Feedback display |

**Sources:** [front L276-L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L276-L281)

### Message Display System

The support form uses a different message element than crop management:

| Section | Message Element | CSS Classes | Auto-Dismiss |
| --- | --- | --- | --- |
| Crop Form | `#message` | Color via `style.color` property | 3 seconds |
| Support Form | `#mensaje-form` | `.success` or `.error` | 5 seconds (expected) |
| Registration | `#message-box` | `.success-message` or `.error-message` | Variable |

**Sources:** [front L84](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L84-L84)

 [front L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L281-L281)

 [front L217](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L217-L217)

### Backend Integration

The form is handled by `enviar_pregunta.js`, which:

1. Prevents default form submission
2. Creates `FormData` from form inputs
3. POSTs to `/backend/db_interaction/enviar_pregunta.php`
4. Validates user email exists in `usuarios` table
5. Inserts question into `preguntas` table with `usuario_id` foreign key
6. Displays success/error in `#mensaje-form`
7. Clears form on success

**Sources:** [front L293](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L293-L293)

 Context from high-level diagrams

---

## Visual Styling System

The application's visual presentation is controlled by a comprehensive CSS file with responsive design patterns.

### CSS Architecture

```

```

**Sources:** [front L1-L678](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L1-L678)

### Color Palette and Branding

| Element | Color | Hex/RGB | Usage |
| --- | --- | --- | --- |
| Primary Green | Light Green | `#5cb85c` | Buttons, accents, icons |
| Primary Green Hover | Dark Green | `#449d44` | Interactive states |
| Secondary Green | Lime | `#4CAF50` | Borders, gradients |
| Background | Light Gray | `#f4f4f4` | Page background |
| Card Background | White | `#fff` | Section containers |
| Text Primary | Dark Gray | `#333` | Main text |
| Text Secondary | Medium Gray | `#666` | Supporting text |
| Header/Footer | Dark Gray | `#333` | Navigation bars |

**Sources:** [front L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L1-L17)

 [front L86-L104](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L86-L104)

### Responsive Design Breakpoints

The CSS includes mobile-first responsive patterns:

**Desktop (Default):**

* Navigation: Horizontal flex layout
* Features: Three-column grid
* Forms: Centered containers with max-width
* Tables: Full table structure

**Mobile (<768px):**

* Navigation: Vertical stack ([front L483-L495](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L483-L495) )
* Features: Single column ([front L497-L504](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L497-L504) )
* Tables: Convert to card layout with labels ([front L531-L572](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L531-L572) )
* Forms: Full-width with adjusted padding ([front L517-L528](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L517-L528) )

**Sources:** [front L481-L572](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L481-L572)

### Component-Specific Styling Classes

**Crop Form:**

* `.crop-form` - Form container with shadow and border radius
* Input styling: 100% width, consistent padding, border-radius
* Button: Full-width green with hover effects

**Crop Table:**

* `.crop-table` - Full-width border-collapse table
* Alternating row colors: `tbody tr:nth-child(even)`
* Button styling: Blue for edit/delete with hover transitions

**Login Container:**

* `.login-container` - Centered card with 500px max-width
* `.password-container` - Relative positioning for toggle button
* `.toggle-password` - Absolutely positioned eye icon button

**Support Container:**

* `.support-container` - Similar to login container (600px max-width)
* `.faq-link` - Icon-labeled link with hover underline
* `.success` / `.error` - Message feedback classes

**Sources:** [front L389-L431](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L389-L431)

 [front L434-L477](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L434-L477)

 [front L256-L353](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L256-L353)

 [front L596-L678](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L596-L678)

### Transition and Animation Effects

The CSS includes hover effects on interactive elements:

| Element | Transition Properties | Effect |
| --- | --- | --- |
| `nav ul li` | `color 0.3s ease` | Color change + `scale(1.1)` |
| `.cta-button` | `background-color 0.3s ease` | Background + `scale(1.05)` + border color |
| `.feature` | `transform 0.3s ease, box-shadow 0.3s ease` | Lift up (`translateY(-5px)`) + shadow |
| `.post` | `transform 0.3s ease, box-shadow 0.3s ease` | Lift up + shadow |
| `.plan` | `transform 0.3s ease, box-shadow 0.3s ease` | Lift up + shadow |
| Buttons | `background-color 0.3s ease, transform 0.3s ease` | Background + `scale(1.05)` |

**Sources:** [front L34-L43](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L34-L43)

 [front L93-L104](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L93-L104)

 [front L147-L153](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L147-L153)

---

## Static Assets

The application includes three graphic resources referenced in the community section:

| File Path | Usage | Display Location |
| --- | --- | --- |
| `/graphic_resources/images.jpeg` | Community post thumbnail | First `.post` in `#comunidad` |
| `/graphic_resources/maxresdefault.jpg` | Community post thumbnail | Second `.post` in `#comunidad` |
| `/graphic_resources/collage5.jpg` | Community post thumbnail | Third `.post` in `#comunidad` |

Additionally, the banner section uses an external Unsplash image URL:

```yaml
https://images.unsplash.com/photo-1567095761054-7a02e69e5c43?...
```

**Sources:** [front L113](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L113-L113)

 [front L119](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L119-L119)

 [front L125](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L125-L125)

 [front L34-L36](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L34-L36)

---

## Message and Feedback System

The application uses three separate message display mechanisms:

### Message Element Mapping

```

```

**Sources:** [front L84](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L84-L84)

 [front L197](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L197-L197)

 [front L217](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L217-L217)

 [front L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L281-L281)

### Message Display Patterns

**Crop Operations (`#message`):**

* Initially hidden with `style="display: none"`
* Set text via `innerText`
* Color via `style.color = "green"` or `"red"`
* Display via `style.display = "block"`
* Auto-hide after 3 seconds with `setTimeout`

**Registration (`#message-box`):**

* Initially hidden with `style="display: none"`
* Uses CSS classes `.success-message` or `.error-message`
* Includes styled background, border, and text color
* Dismiss timing controlled by JavaScript

**Login Error (`#login-error`):**

* Initially hidden with `style="display: none"`
* Static error message: "Usuario o contraseña incorrectos"
* Inline `style="color: red"`
* Manual dismiss (no auto-hide)

**Support Form (`#mensaje-form`):**

* No inline styles initially
* Uses CSS classes `.success` or `.error`
* Expected 5-second auto-dismiss based on system patterns

**Sources:** [front L21-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/agregar_cultivo.js#L21-L33)

 [front L358-L377](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L358-L377)

 [front L664-L673](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L664-L673)

---

## Form Toggle and UI State Management

The application uses inline JavaScript for form switching logic rather than external module:

### Login/Register Toggle Implementation

```

```

**State Transitions:**

1. **Initial State:** Login form visible, register form hidden
2. **Click "Regístrate" Link:** Hide login, show register
3. **Click "Inicia Sesión" Link:** Hide register, show login

This pattern avoids page navigation while providing clear user flow.

**Sources:** [front L302-L318](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L302-L318)

### Admin Panel Mock Implementation

The admin section includes inline mock functionality:

```

```

This demonstrates placeholder administrative functionality without actual backend integration.

**Sources:** [front L320-L327](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L320-L327)

---

## Icon Integration

The application uses two icon libraries loaded via CDN:

### Font Awesome Usage

**Navigation Icons:**

* `fas fa-home` - Home/Inicio
* `fas fa-seedling` - Crop Management
* `fas fa-users` - Community
* `fas fa-money-bill-wave` - Subscriptions
* `fas fa-sign-in-alt` - Login
* `fas fa-user-shield` - Admin
* `fas fa-share-alt` - Social Media
* `fas fa-question-circle` - Support

**Button/Action Icons:**

* `fas fa-arrow-right` - CTA buttons
* `fas fa-check` - Subscribe buttons
* `fas fa-envelope` - Contact buttons
* `fas fa-user-plus` - Registration
* `fas fa-paper-plane` - Send message
* `fas fa-eye` - Password visibility toggle

**Sources:** [front L8-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L8-L10)

 [front L23-L30](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L23-L30)

### Boxicons Usage

Boxicons are loaded but specific usage is not explicitly shown in the provided HTML:

```

```

This provides an alternative icon set with class prefix `bx-` or `bxs-` for solid variants.

**Sources:** [front L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L11-L11)

### Social Media Icon Grid

The social media section uses Font Awesome brand icons:

```

```

Styling provides green color with scale animation on hover ([front L576-L593](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L576-L593)

).

**Sources:** [front L261-L267](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L261-L267)

 [front L576-L593](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L576-L593)