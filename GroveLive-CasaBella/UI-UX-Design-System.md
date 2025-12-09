# UI/UX Design System

> **Relevant source files**
> * [app/static/css/carrito.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css)
> * [app/static/css/empleado_dashboard.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css)
> * [app/static/css/gestion_citas_pendientes.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css)
> * [app/static/css/gestion_promociones.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css)
> * [app/static/css/trabajar_citas.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css)
> * [app/static/js/carrito.js](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js)
> * [app/templates/base.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html)
> * [app/templates/carrito.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html)
> * [app/templates/productos.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html)
> * [app/templates/servicios.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html)

## Purpose and Scope

This document describes Casa Bella's visual design system, including the CSS architecture, theming mechanism, typography, component library, animation patterns, and responsive design strategies. It covers how the frontend implements a cohesive dark/light mode system, reusable UI components, and progressive enhancement patterns. For information about template inheritance and Jinja2 templating structure, see [Frontend Architecture](/GroveLive/CasaBella/3.3-frontend-architecture). For Bootstrap integration with FullCalendar in appointment management, see [Appointment Management](/GroveLive/CasaBella/6.4-appointment-management) and [Appointment Workflow](/GroveLive/CasaBella/7.1-appointment-workflow).

**Sources:** [app/templates/base.html L1-L270](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L270)

---

## Design Principles & Theme Architecture

Casa Bella implements a **dual-theme design system** that supports dark and light modes through CSS custom properties and the `data-theme` attribute on the `<body>` element. The theme state is controlled by a toggle button in the navigation bar and persisted via JavaScript.

### Theme Implementation Architecture

```

```

**Sources:** [app/templates/base.html L24](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L24-L24)

 [app/templates/base.html L106-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L106-L108)

 [app/templates/base.html L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L266)

### Core CSS Variables

The design system uses CSS custom properties that change based on the `data-theme` attribute:

| Variable Category | Dark Mode Example | Light Mode Example | Usage |
| --- | --- | --- | --- |
| Background | `--card-bg-dark: rgba(26, 33, 48, 0.9)` | `--card-bg-dark: rgba(255, 255, 255, 0.9)` | Card backgrounds |
| Text Primary | `--text-primary: #f0f0f0` | `--text-primary: #333333` | Headings, body text |
| Text Secondary | `--text-secondary: #b0b0b0` | `--text-secondary: #666666` | Labels, captions |
| Primary Gradient | `--gradient-primary: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%)` | Same | Buttons, accents |
| Borders | `--border-color` | `--border-color` | Card borders, dividers |
| Shadows | `--shadow-elegant` | `--shadow-elegant` | Depth, elevation |

**Sources:** [app/static/css/gestion_citas_pendientes.css L250-L260](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L250-L260)

 [app/static/css/trabajar_citas.css L274-L284](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L274-L284)

 [app/static/css/empleado_dashboard.css L163-L177](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L163-L177)

---

## Typography System

Casa Bella uses a **two-font system** combining serif and sans-serif typefaces for visual hierarchy and readability.

### Font Stack Architecture

```

```

**Typography Specifications:**

| Element | Font Family | Size | Weight | Transform | Line Height |
| --- | --- | --- | --- | --- | --- |
| `h1` | Playfair Display | 2.5rem | 700 | - | - |
| `h5.card-title` | Playfair Display | 1.5rem | 600 | - | - |
| `.modal-title` | Playfair Display | 1.5rem | - | - | - |
| Body text | Inter | 0.9-1rem | 400 | - | 1.5 |
| `.btn` | Inter | 0.85rem | 600 | uppercase | - |
| `.lead` | Inter | 1.25rem | 400 | - | - |

**Text Shadow Effects:**

Dark mode applies subtle glow effects to headings for enhanced readability:

* `h1` dark mode: `text-shadow: 0 0 10px rgba(59, 130, 246, 0.3)`
* Light mode removes text shadows

**Sources:** [app/templates/base.html L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L18-L18)

 [app/static/css/gestion_citas_pendientes.css L10-L27](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L10-L27)

 [app/static/css/carrito.css L10-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L10-L21)

 [app/static/css/trabajar_citas.css L10-L35](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L10-L35)

---

## Color System & Brand Identity

### Color Palette

| Color Name | CSS Variable | Hex/RGB Value | Usage |
| --- | --- | --- | --- |
| Primary Blue | `--primary-color` | #3b82f6 | Primary actions, links |
| Secondary Blue | `--secondary-color` | #2563eb | Headings, emphasis |
| Success Green | `--success-color` | #10b981 | Success states, confirmations |
| Danger Red | `--danger-color` | #dc2626 | Errors, deletions |
| Warning Orange | `--warning-color` | #f59e0b | Warnings, promotions |
| Info Cyan | N/A | #0ea5e9 | Informational elements |

### Gradient Definitions

```

```

**Gradient Usage:**

* `.btn-primary`: `background: var(--gradient-primary)`
* `.btn-success`: `background: linear-gradient(135deg, #10b981 0%, #059669 100%)`
* `.btn-danger`: `background: linear-gradient(135deg, #dc2626 0%, #f87171 100%)`
* Modal headers: `background: var(--gradient-primary)`

**Sources:** [app/static/css/trabajar_citas.css L89-L107](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L89-L107)

 [app/static/css/gestion_promociones.css L100-L113](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L100-L113)

 [app/static/css/carrito.css L206-L222](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L206-L222)

---

## Component Library

### Navigation Bar Component

The navigation bar implements role-based rendering and dynamic state management:

```

```

**Navigation Features:**

* **Logo:** Circular brand image with responsive text display (`d-none d-md-inline`)
* **Active State:** `.active.fw-medium` applied based on `request.endpoint`
* **Badge Counts:** Dynamic display for favorites and cart items
* **Sticky Position:** `sticky-top` class keeps navigation visible

**Sources:** [app/templates/base.html L26-L181](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L26-L181)

### Card Component Patterns

Cards follow a consistent structure across the application:

```

```

**Card Variants:**

| Variant | Background | Border | Shadow | Usage |
| --- | --- | --- | --- | --- |
| Standard | `var(--card-bg-dark)` | `1px solid var(--border-color)` | `var(--shadow-elegant)` | Products, Services |
| Hover State | Same | `var(--primary-color)` | `var(--shadow-hover)` | Interactive cards |
| Cart Item | Same | Top gradient bar | Enhanced | Shopping cart |
| Table Card (mobile) | Same | Standard | Standard | Mobile data display |

**Sources:** [app/templates/productos.html L25-L61](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L25-L61)

 [app/templates/servicios.html L25-L61](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L25-L61)

 [app/static/css/gestion_citas_pendientes.css L162-L186](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L162-L186)

### Button System

```

```

**Button Specifications:**

| Class | Background | Padding | Font Size | Transform | Border Radius |
| --- | --- | --- | --- | --- | --- |
| `.btn-primary` | `var(--gradient-primary)` | 0.5rem 1.25rem | 0.85rem | uppercase | 10px |
| `.btn-sm` | Inherited | 0.4rem 0.8rem | 0.85rem | uppercase | 10px |
| `.btn-icon` | Transparent | Minimal | - | - | 8px |
| `.btn-agregar-carrito` | Gradient | Custom | - | - | 8px |

**Interactive States:**

* **Hover:** `transform: translateY(-2px) scale(1.02)` + enhanced shadow
* **Active:** `transform: translateY(0)` (pressed state)
* **Focus:** `box-shadow: 0 0 10px rgba(59, 130, 246, 0.3)`

**Sources:** [app/static/css/gestion_citas_pendientes.css L76-L112](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L76-L112)

 [app/static/css/trabajar_citas.css L77-L126](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L77-L126)

 [app/static/css/carrito.css L243-L283](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L243-L283)

### Alert Component System

Alerts use a fixed positioning system with backdrop blur and left-border color coding:

```

```

**Alert Styling:**

* Position: `fixed; top: 20px; right: 20px; z-index: 1050`
* Border: `border-radius: 12px; border-left: 4px solid [semantic-color]`
* Background: Semi-transparent with semantic color tint
* Max Width: `400px` (90% on mobile)
* Animation: `opacity 0.5s ease, transform 0.5s ease`

**Semantic Color Mapping:**

* Success: `rgba(16, 185, 129, 0.15)` background, `#10b981` border
* Danger: `rgba(220, 38, 38, 0.15)` background, `#dc2626` border
* Warning: `rgba(245, 158, 11, 0.15)` background, `#f59e0b` border
* Info: `rgba(59, 130, 246, 0.15)` background, `#3b82f6` border

**Sources:** [app/static/css/gestion_citas_pendientes.css L38-L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L38-L74)

 [app/static/css/carrito.css L40-L82](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L40-L82)

### Modal Component Structure

```

```

**Modal Styling:**

* Background: `var(--card-bg-dark)` with `backdrop-filter: blur(10px)`
* Header: `background: var(--gradient-primary)` with rounded top corners
* Border Radius: `15px` for content, `10px` for top corners
* Form Controls: Custom background matching theme, 2px borders
* Focus State: `border-color: var(--secondary-color)` with glow effect

**Sources:** [app/static/css/gestion_citas_pendientes.css L188-L235](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L188-L235)

 [app/static/css/gestion_promociones.css L268-L317](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L268-L317)

### Form Component Patterns

Form elements inherit theme-specific styling:

| Element | Dark Mode Background | Light Mode Background | Border | Focus Effect |
| --- | --- | --- | --- | --- |
| `.form-control` | `var(--card-bg-dark)` | `rgba(0, 0, 0, 0.05)` | `2px solid var(--border-color)` | Blue glow |
| `.form-select` | `var(--card-bg-dark)` | `rgba(0, 0, 0, 0.05)` | `2px solid var(--border-color)` | Blue glow |
| `.form-label` | N/A | N/A | N/A | N/A |

**Focus States:**

* Border: `border-color: var(--secondary-color)`
* Shadow: `box-shadow: 0 0 10px rgba(59, 130, 246, 0.3)`

**Sources:** [app/static/css/gestion_citas_pendientes.css L220-L231](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L220-L231)

 [app/static/css/gestion_promociones.css L298-L313](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L298-L313)

---

## Layout & Responsive Design

### Container System

```

```

**Breakpoint Strategy:**

| Breakpoint | Width | Layout Changes | Typical Adjustments |
| --- | --- | --- | --- |
| Desktop | ≥992px | Multi-column grids | Full tables, 4-column grids |
| Tablet | 768-991px | 2-column grids | Reduced padding, smaller fonts |
| Mobile | ≤767px | Single column | Card-based tables, stacked layout |
| Small Mobile | ≤576px | Single column | Minimal padding, compact buttons |

**Responsive Pattern: Table to Card Transformation**

The application implements a **table-to-card transformation** pattern for mobile devices:

```

```

**Sources:** [app/static/css/gestion_promociones.css L139-L187](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L139-L187)

 [app/static/css/gestion_promociones.css L190-L234](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L190-L234)

 [app/static/css/gestion_promociones.css L372-L451](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L372-L451)

### Grid Systems

**Product/Service Card Grid:**

```

```

**Cart Item Layout:**

```

```

**Sources:** [app/static/css/carrito.css L126-L347](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L126-L347)

---

## Animation & Effects System

### Animation Catalog

```

```

**Animation Definitions:**

| Animation Name | Keyframes | Duration | Easing | Usage |
| --- | --- | --- | --- | --- |
| `fadeIn` | opacity: 0→1 | 1s | ease-in | Container entrance |
| `glideIn` | opacity: 0→1, translateY: 50px→0, scale: 0.9→1 | 0.8s | ease-out | Card entrance |
| `rowFadeIn` | opacity: 0→1, translateY: 20px→0 | 0.5s | ease-out | Staggered list items |
| `slideIn` | opacity: 0→1, translateY: -20px→0 | 0.3s | ease | Alert entrance |

**Staggered Animation Pattern:**

```

```

**Sources:** [app/static/css/carrito.css L303-L329](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L303-L329)

 [app/static/css/gestion_citas_pendientes.css L263-L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L263-L266)

 [app/static/css/empleado_dashboard.css L180-L198](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L180-L198)

### Hover & Interaction Effects

**Card Hover Pattern:**

```

```

**Button Hover Pattern:**

```

```

**Gradient Border on Hover:**

```

```

**Ripple Effect on Primary Button:**

```

```

**Sources:** [app/static/css/carrito.css L101-L123](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L101-L123)

 [app/static/css/carrito.css L262-L283](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L262-L283)

 [app/static/css/gestion_citas_pendientes.css L99-L106](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L99-L106)

---

## Bootstrap Integration

Casa Bella extends Bootstrap 5.3.0 with custom styling while maintaining compatibility with Bootstrap's utility classes and JavaScript components.

### Bootstrap Dependencies

```

```

**Bootstrap Component Usage:**

| Component | Bootstrap Class | Custom Enhancement | Files |
| --- | --- | --- | --- |
| Navbar | `.navbar.navbar-expand-lg` | Custom colors, sticky behavior | base.html:26-181 |
| Dropdown | `.dropdown` | Theme-aware styling | base.html:142-150, 155-175 |
| Modal | `.modal` | Gradient headers, blur backdrop | Various page templates |
| Alert | `.alert.alert-dismissible` | Fixed positioning, custom colors | Multiple CSS files |
| Cards | `.card` | Enhanced shadows, animations | productos.html, servicios.html |
| Badges | `.badge` | Custom color schemes | base.html:115-128 |
| Buttons | `.btn` | Gradient backgrounds, transitions | All templates |

**Bootstrap Icon Integration:**

Icons are used throughout the interface with the `bi-*` class prefix:

* Cart: `bi-cart` (line 122)
* Heart: `bi-heart` / `bi-heart-fill` (lines 113, 54)
* Bell: `bi-bell` (line 135)
* Calendar: `bi-calendar-plus` (servicios.html:51)
* Person: `bi-person-circle` (lines 144, 157)
* Box: `bi-box-seam` (productos.html:46)
* Clock: `bi-clock` (servicios.html:46)

**Sources:** [app/templates/base.html L14-L16](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L14-L16)

 [app/templates/base.html L264](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L264-L264)

 [app/templates/productos.html L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L46-L46)

 [app/templates/servicios.html L46-L51](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L46-L51)

### Bootstrap Grid Customization

The application uses Bootstrap's grid system but extends it with custom CSS Grid implementations for specific layouts:

**Bootstrap Grid Usage:**

```

```

**Custom Grid Overlay:**

```

```

---

## JavaScript & Interactivity Patterns

### Theme Toggle Implementation

```

```

The theme toggle button is implemented at [app/templates/base.html L106-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L106-L108)

 and controlled by `theme-toggle.js` loaded at [app/templates/base.html L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L266)

**Expected Theme Toggle Logic:**

```

```

**Sources:** [app/templates/base.html L106-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L106-L108)

 [app/templates/base.html L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L266)

### AJAX Interaction Patterns

#### Favorite Toggle System

```

```

**AJAX Implementation Pattern:**

From [app/templates/productos.html L94-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L94-L119)

:

```

```

**Sources:** [app/templates/productos.html L82-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L82-L119)

 [app/templates/servicios.html L82-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L82-L119)

#### Cart Quantity Updates

```

```

**Implementation:**

From [app/templates/carrito.html L77-L98](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L77-L98)

:

```

```

**Cart Total Calculation:**

From [app/static/js/carrito.js L2-L8](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L2-L8)

:

```

```

**Sources:** [app/templates/carrito.html L74-L131](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L74-L131)

 [app/static/js/carrito.js L1-L29](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L1-L29)

### jQuery Integration

The application uses jQuery 3.6.0 loaded from CDN for AJAX operations and DOM manipulation:

**jQuery Usage Patterns:**

* **Selector Pattern:** `$('.favorite-btn')`, `$('#favorite-btn-' + itemId)`
* **Event Binding:** `$(document).ready(function() { ... })`
* **AJAX Calls:** `$.ajax({ ... })`, `$.get(url, callback)`
* **DOM Updates:** `button.addClass('active')`, `button.find('i').removeClass('bi-heart')`

**Sources:** [app/templates/productos.html L74-L127](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L74-L127)

 [app/templates/servicios.html L74-L127](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L74-L127)

---

## Footer Component

The footer implements a comprehensive information architecture with social media integration and responsive column layout:

```

```

**Footer Features:**

* **Brand Section:** Logo image, business description, social media icons
* **Navigation Columns:** Quick links to services and products
* **Contact Information:** Email, physical address, business hours, Instagram handle
* **Social Integration:** Direct links to Facebook, Instagram, WhatsApp, and email
* **Legal Links:** Privacy policy and terms of service placeholders
* **Responsive Layout:** 4 columns on desktop, stacks on mobile

**Icon Usage:**

* `bi-facebook`, `bi-instagram`, `bi-whatsapp`, `bi-envelope` (social links)
* `bi-envelope`, `bi-geo-alt`, `bi-clock`, `bi-instagram` (contact info)

**Sources:** [app/templates/base.html L187-L261](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L187-L261)

---

## Print Styles

The design system includes print-specific styles to optimize printed output:

```

```

**Print Optimizations:**

* Hide interactive elements (buttons, alerts, modals)
* Remove shadows and complex backgrounds
* Simplify borders for better print clarity
* Maintain readable typography

**Sources:** [app/static/css/gestion_citas_pendientes.css L346-L355](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L346-L355)

 [app/static/css/trabajar_citas.css L370-L379](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L370-L379)

 [app/static/css/gestion_promociones.css L454-L463](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L454-L463)

---

## CSS Architecture & File Organization

```

```

**File Organization Pattern:**

1. **base.css** - Core theme variables, global resets, shared component styles
2. **Page-Specific CSS** - Extends base styles with page-specific layouts and components
3. **Shared Patterns** - Common components (cards, buttons, alerts) defined once, inherited everywhere

**CSS Loading Strategy:**

```

```

**Sources:** [app/templates/base.html L20-L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L20-L22)

 [app/templates/productos.html L3-L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L3-L5)

 [app/templates/carrito.html L3-L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L3-L5)

---

## Accessibility Considerations

The design system implements several accessibility features:

| Feature | Implementation | Location |
| --- | --- | --- |
| **Semantic HTML** | Proper heading hierarchy (h1, h5) | All templates |
| **ARIA Labels** | `aria-label="Close"` on buttons | Alert dismissals |
| **Visual Hidden Text** | `.visually-hidden` for badge counts | base.html:117, 126 |
| **Focus States** | Custom focus indicators on forms | All CSS files |
| **Color Contrast** | High contrast text/background ratios | Theme variables |
| **Keyboard Navigation** | Bootstrap dropdown keyboard support | base.html |
| **Alt Text** | Image fallback with onerror | productos.html:27, servicios.html:27 |

**Sources:** [app/templates/base.html L117](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L117-L117)

 [app/templates/base.html L126](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L126-L126)

 [app/templates/productos.html L27](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L27-L27)

---

This design system provides a cohesive, themeable, and responsive UI framework that supports Casa Bella's multi-role user interface with consistent visual language across all pages.