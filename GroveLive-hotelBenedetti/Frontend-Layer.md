# Frontend Layer

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)

## Purpose and Scope

This document provides an overview of the presentation layer in the Hotel Benedetti system, explaining the organization of CSS styling and JavaScript client-side logic across all user interfaces. The frontend implements a role-based interface system with four distinct user experiences: Administrator, Receptionist, Maid, and Client.

For detailed information about specific styling patterns and color systems, see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system). For in-depth JavaScript implementation details, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic). For responsive design strategies, see [Responsive Design Implementation](/GroveLive/hotelBenedetti/5.3-responsive-design-implementation). For visual design principles, see [Visual Design System](/GroveLive/hotelBenedetti/8-visual-design-system).

---

## Frontend Architecture Overview

The presentation layer follows a modular architecture where each user role has dedicated CSS and JavaScript files. This separation enables independent styling and behavior for each interface while maintaining a consistent visual identity.

### Role-Based File Structure

```

```

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

 [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [assets/js/cliente.js L1-L155](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L155)

---

## CSS Styling System

### Dark Theme Implementation

All role-based interfaces implement a consistent dark theme with a layered background approach. Each interface uses a background image overlaid with a semi-transparent dark layer to ensure text readability.

| Component | Implementation | Color Value |
| --- | --- | --- |
| Background Image | `background-image: url('../img/fondo2.jpg')` | N/A |
| Dark Overlay | `body::before` pseudo-element | `rgba(35, 39, 42, 0.8)` |
| Content Container | `background-color` | `#2c2f33` |
| Secondary Container | `background-color` | `#36393f` |
| Text Color | `color` | `#dcddde` |
| Accent Color | Interactive elements | `#9b59b6` |
| Accent Hover | Hover state | `#8e44ad` |

The layered z-index approach ensures proper stacking:

* Background image: `z-index: auto` (default)
* Dark overlay (`body::before`): `z-index: 1` [assets/css/StyleAdmin.css L30](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L30-L30)
* All content elements: `z-index: 2` [assets/css/StyleAdmin.css L36](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L36-L36)

**Sources:** [assets/css/StyleAdmin.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L8-L37)

 [assets/css/StyleCliente.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L9-L37)

 [assets/css/StyleMucama.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L9-L37)

 [assets/css/StyleRecepcionista.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L9-L37)

### Responsive Breakpoints

All CSS files implement a mobile-first responsive strategy with two primary breakpoints:

```

```

**768px Breakpoint Adjustments:**

* Navigation changes from horizontal to vertical [assets/css/StyleAdmin.css L289-L292](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L289-L292)
* Dashboard stat cards switch to single column [assets/css/StyleAdmin.css L303-L310](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L310)
* Font sizes reduce by 10-20% [assets/css/StyleCliente.css L400-L402](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L400-L402)

**480px Breakpoint Adjustments:**

* Logo size reduces from 150px to 120px [assets/css/StyleAdmin.css L337-L339](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L337-L339)
* Heading sizes further reduced [assets/css/StyleCliente.css L441-L442](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L441-L442)
* Floating buttons shrink [assets/css/StyleCliente.css L464-L467](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L464-L467)

**Sources:** [assets/css/StyleAdmin.css L280-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L380)

 [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

 [assets/css/StyleMucama.css L217-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L217-L281)

 [assets/css/StyleRecepcionista.css L327-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L407)

### Component Styling Patterns

#### Form Elements

All interfaces share a consistent form styling approach:

```

```

Focus states apply purple accent color [assets/css/StyleCliente.css L272-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L272-L277)

 [assets/css/StyleRecepcionista.css L284-L288](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L284-L288)

#### Button States

| State | Background Color | Implementation |
| --- | --- | --- |
| Default | `#9b59b6` | Primary action buttons |
| Hover | `#8e44ad` | All purple buttons |
| Edit Action | `#36A2EB` | Blue for edit operations |
| Delete Action | `#FF6384` | Red for destructive actions |
| Confirm | `#9b59b6` | Confirmation actions |
| Cancel | `#f44336` | Cancel/reject actions |

Transition effects: `transition: background-color 0.3s ease` [assets/css/StyleAdmin.css L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L205-L205)

**Sources:** [assets/css/StyleAdmin.css L198-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L266)

 [assets/css/StyleCliente.css L279-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L279-L294)

 [assets/css/StyleRecepcionista.css L130-L157](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L130-L157)

#### Table Styling

Tables use alternating row colors and hover effects:

```

```

**Sources:** [assets/css/StyleAdmin.css L213-L239](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L239)

 [assets/css/StyleMucama.css L168-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L190)

 [assets/css/StyleRecepcionista.css L165-L187](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L187)

---

## JavaScript Module Organization

### Per-Role JavaScript Architecture

Each role-based interface has a dedicated JavaScript module that handles:

1. DOM manipulation
2. AJAX communication with PHP controllers
3. Client-side validation
4. User feedback (SweetAlert2 integration)

```

```

**Sources:** [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)

 [assets/js/cliente.js L1-L12](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L1-L12)

### Common JavaScript Patterns

#### AJAX Request Pattern

All JavaScript modules use the Fetch API with consistent patterns:

```

```

**GET requests:** Query parameters in URL [assets/js/admin.js L39](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L39)

 [assets/js/cliente.js L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L16)

**POST requests:** URLSearchParams in request body [assets/js/admin.js L232-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L232-L235)

 [assets/js/cliente.js L91-L96](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L91-L96)

**Sources:** [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

 [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

#### Form Submission Handler Pattern

All form handlers follow this structure:

1. Prevent default submission: `event.preventDefault()` [assets/js/admin.js L209](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L209-L209)  [assets/js/cliente.js L69](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L69-L69)
2. Extract form data from DOM elements
3. Client-side validation (date checks, required fields)
4. Build URLSearchParams object
5. Send POST request via fetch()
6. Handle response with SweetAlert2
7. Reset form and reload data on success

**Sources:** [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266)

 [assets/js/cliente.js L68-L116](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L68-L116)

#### Dynamic DOM Manipulation

Tables and lists are populated dynamically using createElement and innerHTML:

```

```

This pattern appears in:

* `loadEmployees()` [assets/js/admin.js L151-L183](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L151-L183)
* `loadRooms()` [assets/js/admin.js L363-L391](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L363-L391)
* `loadRooms()` in cliente.js [assets/js/cliente.js L22-L32](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L22-L32)

**Sources:** [assets/js/admin.js L143-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L205)

 [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

### External Library Integration

#### Chart.js Integration

The admin dashboard uses Chart.js for data visualization with instance management:

```

```

Two chart types are implemented:

* **Pie Chart:** Room status distribution (Ocupadas, Disponibles, En Limpieza) [assets/js/admin.js L64-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L64-L90)
* **Bar Chart:** Employee distribution (Recepcionistas, Mucamas) [assets/js/admin.js L93-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L93-L124)

**Sources:** [assets/js/admin.js L5-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L6)

 [assets/js/admin.js L56-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L56-L124)

#### SweetAlert2 Integration

All user feedback uses SweetAlert2 instead of native alerts:

| Use Case | Icon | Implementation |
| --- | --- | --- |
| Success | `success` | CRUD operation success [assets/js/admin.js L239-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L245) |
| Error | `error` | Operation failure [assets/js/admin.js L252-L256](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L252-L256) |
| Warning | `warning` | Confirmation dialog [assets/js/admin.js L305-L312](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L305-L312) |

Configuration options:

* `timer`: Auto-close duration (e.g., 1500ms)
* `showConfirmButton`: Hide confirm button on auto-close
* `showCancelButton`: Two-button dialogs for confirmations

**Sources:** [assets/js/admin.js L126-L139](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L126-L139)

 [assets/js/admin.js L239-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L257)

 [assets/js/admin.js L305-L351](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L305-L351)

---

## Client-Server Communication Flow

### Request-Response Cycle

```

```

### Action-Based Routing

Controllers use the `action` parameter to route requests:

**AdminController.php actions:**

* `getDashboardStats` - Dashboard statistics [assets/js/admin.js L39](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L39)
* `getEmployees` - List all employees [assets/js/admin.js L144](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L144-L144)
* `addEmployee` / `updateEmployee` - CRUD operations [assets/js/admin.js L221-L222](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L221-L222)
* `deleteEmployee` - Remove employee [assets/js/admin.js L315-L316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L315-L316)
* `getRooms` / `addRoom` / `updateRoom` / `deleteRoom` - Room management [assets/js/admin.js L356-L432](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L356-L432)

**HabitacionController.php actions:**

* `getAvailableRooms` - Fetch available rooms [assets/js/cliente.js L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L16)

**ReservaController.php actions:**

* `createBooking` - Create new reservation [assets/js/cliente.js L92](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L92-L92)

**NotificacionController.php actions:**

* `markNotificationAsRead` - Mark notification as read [assets/js/cliente.js L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L124-L124)

**Sources:** [assets/js/admin.js L39](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L39)

 [assets/js/admin.js L144](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L144-L144)

 [assets/js/admin.js L232-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L232-L235)

 [assets/js/cliente.js L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L16-L16)

 [assets/js/cliente.js L91-L96](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L91-L96)

 [assets/js/cliente.js L123-L126](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L123-L126)

---

## Asset Management

### Background Images

All interfaces use background images with overlay effects:

* **Admin Interface:** `url('../img/fondo2.jpg')` [assets/css/StyleAdmin.css L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L11-L11)
* **Client Interface:** `url('../img/fondo2.jpg')` [assets/css/StyleCliente.css L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L11-L11)
* **Receptionist Interface:** `url('../img/fondo.jpg')` [assets/css/StyleRecepcionista.css L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L11-L11)
* **Maid Interface:** `url('../img/fondo.jpg')` [assets/css/StyleMucama.css L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L11-L11)

The dark overlay is implemented using the `::before` pseudo-element with absolute positioning and `rgba(35, 39, 42, 0.8)` background color.

### Logo and Branding

All interfaces display the hotel logo in the header:

```

```

Responsive sizing:

* Desktop: `max-width: 150px` [assets/css/StyleAdmin.css L64](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L64-L64)
* Mobile (< 480px): `max-width: 120px` [assets/css/StyleAdmin.css L337-L339](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L337-L339)

### Social Media Icons

The client interface includes floating buttons with icon assets:

* Instagram icon: `.floating-btn.instagram` [assets/css/StyleCliente.css L373-L379](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L373-L379)
* WhatsApp icon: `.floating-btn.whatsapp` [assets/css/StyleCliente.css L381-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L381-L387)

Icons are positioned fixed at bottom-right with hover scale effects [assets/css/StyleCliente.css L340-L365](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L365)

**Sources:** [assets/css/StyleAdmin.css L58-L66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L58-L66)

 [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

---

## Navigation and Section Management

### Tab-Based Navigation Pattern

The admin interface implements a single-page application pattern using section visibility toggling:

```

```

The `.hidden` class uses `display: none` [assets/css/StyleAdmin.css L109-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L109-L111)

**Navigation triggers data loading:**

* Dashboard button → `loadDashboardStats()` [assets/js/admin.js L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L20-L20)
* Employees button → `loadEmployees()` [assets/js/admin.js L22](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L22-L22)
* Rooms button → `loadRooms()` [assets/js/admin.js L24](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L24-L24)

**Sources:** [assets/js/admin.js L10-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L10-L27)

 [assets/css/StyleAdmin.css L109-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L109-L111)

### Anchor-Based Navigation

Non-admin interfaces use traditional anchor-based navigation:

```

```

CSS smooth scrolling can be implemented, though not explicitly defined in the provided files.

**Sources:** [assets/css/StyleCliente.css L64-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L64-L84)

 [assets/css/StyleMucama.css L64-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L64-L83)

 [assets/css/StyleRecepcionista.css L64-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L64-L83)