# User Interface Structure

> **Relevant source files**
> * [front end/index.html](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html)
> * [front end/style.css](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css)
> * [graphic_resources/collage5.jpg](https://github.com/axchisan/CoopAgronet/blob/e8818744/graphic_resources/collage5.jpg)
> * [graphic_resources/images.jpeg](https://github.com/axchisan/CoopAgronet/blob/e8818744/graphic_resources/images.jpeg)
> * [graphic_resources/maxresdefault.jpg](https://github.com/axchisan/CoopAgronet/blob/e8818744/graphic_resources/maxresdefault.jpg)

## Purpose and Scope

This document describes the structure of `index.html` as the single-page application (SPA) shell and its associated styling system in `style.css`. It covers the seven main content sections, UI components (forms, tables, navigation), CSS organization, and external dependencies. For information about the JavaScript modules that interact with these UI elements, see [Authentication Frontend](/axchisan/CoopAgronet/3.2-authentication-frontend), [Crop Management Frontend](/axchisan/CoopAgronet/3.3-crop-management-frontend), and [Support Form Frontend](/axchisan/CoopAgronet/3.4-support-form-frontend). For details on visual assets beyond the HTML structure, see [Visual Assets and Styling](/axchisan/CoopAgronet/3.5-visual-assets-and-styling).

**Sources:** [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)

---

## Single-Page Application Architecture

CoopAgroNet uses a single HTML file (`index.html`) that contains all UI sections simultaneously loaded in the browser. Navigation between sections occurs through hash-based routing (`#inicio`, `#gestion-cultivos`, etc.) rather than separate page loads. The document is structured as follows:

```

```

**Sources:** [front L1-L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L13)

 [front L292-L301](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L301)

 [front L288-L290](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L288-L290)

---

## Header Structure

The header contains the primary navigation menu and a banner section with call-to-action:

```

```

The navigation menu uses Font Awesome icons (`i.fas.fa-*`) alongside text labels. Each `<a>` element uses hash fragment identifiers to link to sections within the same page.

**Sources:** [front L15-L43](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L15-L43)

---

## Main Content Sections

The `<main>` element contains seven distinct sections, each with `id` and `class="section"`:

| Section ID | Title | Purpose | Key Elements |
| --- | --- | --- | --- |
| `#inicio` | Bienvenido a CoopAgroNet | Landing/welcome section | Feature cards (`div.key-features`) |
| `#gestion-cultivos` | Gestión de Cultivos | Crop CRUD interface | `form#cropForm`, `table.crop-table`, `tbody#cropTableBody` |
| `#comunidad` | Comunidad | Community feed display | `div.community-feed` with static posts |
| `#suscripciones` | Suscripciones | Subscription plans | `div.subscription-plans` with three plan cards |
| `#login` | Login/Registro | Authentication forms | `form#login-form`, `form#register-form`, `div#user-profile` |
| `#admin` | Administración | Admin panel placeholder | `div#admin-panel` (populated by inline script) |
| `#soporte` | Soporte | Support/help form | `form#support-form`, `div#mensaje-form` |

An eighth section `#social` exists for social media links but is not listed in the main navigation consistently.

**Sources:** [front L45-L286](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L45-L286)

---

## Authentication UI Components

The login section (`#login`) contains three mutually exclusive UI states controlled by JavaScript:

```

```

The password fields use `div.password-container` wrappers with toggle buttons (`button.toggle-password`) that have `data-target` attributes pointing to the input ID to be toggled. Form switching is handled by inline JavaScript that listens for clicks on `#show-register-form` and `#show-login-form`.

**Sources:** [front L177-L246](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L177-L246)

 [front L302-L318](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L302-L318)

---

## Crop Management UI Components

The `#gestion-cultivos` section provides the primary CRUD interface:

```

```

The form uses `name` attributes matching backend field names (`tipo`, `fecha_siembra`, `cantidad`, `dueno`, `edad`, `ubicacion`, `notas`). The submit button's text changes to "Actualizar" during edit operations. The empty `tbody#cropTableBody` is populated by `agregar_cultivo.js` via `fetchCultivos()`.

**Sources:** [front L71-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L71-L107)

---

## Support Form UI

The support section contains a simple contact form:

```

```

The `div#mensaje-form` displays success/error messages managed by `enviar_pregunta.js`. Unlike other forms, this one doesn't toggle between states—it simply submits and displays feedback.

**Sources:** [front L270-L285](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L270-L285)

---

## External Dependencies

The application loads multiple external libraries in specific order:

```

```

**Icon Libraries:**

* **Font Awesome 6.0.0**: Used for icons throughout (`fa-tractor`, `fa-home`, `fa-seedling`, etc.)
* **Boxicons 2.1.4**: Alternative icon set (though primarily Font Awesome is used)

**React Ecosystem:**

* **React 18 (development UMD)**: Core library for `crop-management.js` component
* **ReactDOM 18 (development UMD)**: DOM rendering for React
* **Babel Standalone**: Client-side JSX transpilation for `crop-management.js`

The React libraries are loaded even though the main application uses vanilla JavaScript, suggesting dual frontend approach or migration in progress.

**Sources:** [front L7-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L7-L11)

 [front L292-L300](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L292-L300)

---

## CSS Architecture

The `style.css` file follows a component-based organization with 678 lines of styles:

### Global Styles

```
body (lines 1-7): Base font, margin, padding, background, color
header (lines 9-17): Dark background with gradient border
nav (lines 19-24, 26-30, 32-43, 45-51): Flexbox navigation with hover effects
```

**Sources:** [front L1-L60](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L1-L60)

### Section Styling

All `section` elements share common styling with the `.section` class:

```
.section (lines 110-120): White background, border-radius, box-shadow, subtle border
.section h2::before (lines 122-132): Top gradient border pseudo-element
```

This creates consistent visual separation between content areas. The gradient uses `linear-gradient(to right, #4CAF50, #8BC34A)`.

**Sources:** [front L110-L132](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L110-L132)

### Component-Specific Styles

| Component | CSS Classes/Selectors | Lines | Key Properties |
| --- | --- | --- | --- |
| Banner | `.banner`, `.banner-content`, `.cta-button` | 62-104 | Absolute positioning, overlay, transition effects |
| Features | `.key-features`, `.feature` | 134-159 | Flexbox layout, hover transform, green icons |
| Community Feed | `.community-feed`, `.post` | 161-198 | Flexbox wrap, image cropping, hover effects |
| Subscription Plans | `.subscription-plans`, `.plan` | 200-240 | Flexbox layout, card styling, button transitions |
| Login Container | `.login-container`, password toggle styles | 255-354 | Centered form, password visibility toggle button |
| Crop Form | `.crop-form` | 389-431 | Light background, full-width inputs, green button |
| Crop Table | `.crop-table` | 434-476 | Bordered cells, striped rows, button styling |
| Support Container | `.support-container` | 596-678 | Similar to login container with textarea |

**Sources:** [front L62-L678](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L62-L678)

### Responsive Design

Media queries at `@media (max-width: 768px)` provide mobile adaptations:

* **Navigation (lines 482-495)**: Stacks vertically
* **Feature/Plan Cards (lines 497-513)**: Full width layout
* **Crop Form (lines 517-528)**: Adjusted padding and font sizes
* **Crop Table (lines 531-572)**: Converts to card-based layout with `display: block`, hides thead, adds `data-label` pseudo-elements

**Sources:** [front L480-L572](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L480-L572)

---

## Key UI Element Identifiers

### Frequently Referenced IDs

The following element IDs are extensively used by JavaScript modules:

**Authentication:**

* `#login-form`: Main login form
* `#register-form`: Registration form (initially hidden)
* `#user-profile`: Post-login display (initially hidden)
* `#login-password`, `#register-password`, `#confirm-password`: Password inputs
* `#login-btn`, `#logout-btn`: Action buttons
* `#login-error`: Error message display
* `#user-name`: Span to display logged-in user's name
* `#show-register-form`, `#show-login-form`: Form toggle links
* `#message-box`: Registration feedback container

**Crop Management:**

* `#cropForm`: Main form for adding/editing crops
* `#cropTableBody`: Table body populated with crop rows
* `#type`, `#fechaSiembra`, `#quantity`, `#owner`, `#age`, `#location`, `#notes`: Form input fields
* `#message`: Form feedback display

**Support:**

* `#support-form`: Contact form
* `#mensaje-form`: Feedback message container

**Admin:**

* `#admin-panel`: Container populated by inline script

**Sources:** [front L74-L107](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L74-L107)

 [front L182-L244](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L182-L244)

 [front L275-L281](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L275-L281)

---

## Form Action and Integration Points

### Form Submission Handlers

None of the forms have `action` attributes except `#register-form`, which has:

```

```

All other forms rely on JavaScript event handlers to prevent default submission and use `fetch()` API. This is handled by:

* `login.js`: Intercepts `#login-form` submission
* `agregar_cultivo.js`: Intercepts `#cropForm` submission
* `acciones_cultivos.js`: Handles dynamically created edit/delete buttons
* `enviar_pregunta.js`: Intercepts `#support-form` submission

**Sources:** [front L214-L215](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L214-L215)

---

## Password Visibility Toggle

Password fields use a consistent pattern for show/hide functionality:

```

```

The CSS positions the button absolutely within the container (lines 317-353). JavaScript (presumably in `login.js`) toggles the input type between `password` and `text` and switches the icon between `fa-eye` and `fa-eye-slash`.

**Sources:** [front L186-L191](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L186-L191)

 [front L222-L236](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L222-L236)

 [front L317-L353](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L317-L353)

---

## Inline JavaScript

The document includes inline JavaScript at the end for two purposes:

1. **Form Toggle Logic** (lines 302-318): Adds event listeners to `#show-register-form` and `#show-login-form` links to toggle display between login and registration forms.
2. **Mock Admin Panel** (lines 320-327): Populates `#admin-panel` with static HTML containing two alert-triggering buttons for "Gestionar Usuarios" and "Gestionar Cultivos". This is placeholder functionality not connected to real admin features.

**Sources:** [front L301-L328](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L301-L328)

---

## Summary Diagram: UI Component Relationships

```

```

**Sources:** [front L1-L331](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/index.html#L1-L331)

 [front L1-L678](https://github.com/axchisan/CoopAgronet/blob/e8818744/front end/style.css#L1-L678)