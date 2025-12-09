# Frontend Architecture

> **Relevant source files**
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

This document provides a comprehensive overview of the CSS-based presentation layer architecture in the American Food restaurant management system. It covers the three-layer CSS architecture (global styles → authentication → role-specific interfaces), the component-based design system, CSS custom properties for theming, and responsive design patterns.

For detailed documentation of specific interface implementations, see:

* Design system foundation and global components: [Design System and Global Styles](/axchisan/AmericanFood/5.1-design-system-and-global-styles)
* Login and registration UI: [Authentication Interface](/axchisan/AmericanFood/5.2-authentication-interface)
* Individual role-specific interfaces: [Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system)
* Reusable UI patterns: [Component Patterns](/axchisan/AmericanFood/5.4-component-patterns)

For backend integration and business logic, see [Backend Services](/axchisan/AmericanFood/4-backend-services).

---

## Architecture Overview

The frontend architecture implements a **component-based design system** using pure CSS without JavaScript frameworks. The system is organized into three distinct layers that build upon each other, with each role-specific interface inheriting from the global foundation.

### Three-Layer CSS Architecture

```

```

**Sources:** [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 [css/login.css L1-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L1-L181)

 [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

 [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)

 [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

### CSS File Organization

| File | Lines | Primary Purpose | Key Components |
| --- | --- | --- | --- |
| `style.css` | 704 | Global foundation, design system | CSS variables, navbar, carousel, footer, cart-sidebar |
| `login.css` | 181 | Authentication UI | login-card, role-options, password-strength |
| `cliente.css` | 371 | Customer interface | welcome-banner, menu-item-card, cart-sidebar, reservation-list |
| `cajera.css` | 402 | Cashier operations | cajera-header, sales-summary, order-card, payment-modal |
| `cocina.css` | 29 | Kitchen display | order-card with state classes (.new, .preparing, .ready) |
| `mesera.css` | 705 | Server operations | mesera-header, tables-grid, table-item, order-card |

**Sources:** All CSS files in [css/](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/)

 directory

---

## Design System Foundation

### CSS Custom Properties

The design system defines 18 CSS custom properties in `style.css` that establish the color palette, spacing, and semantic color meanings. All role-specific stylesheets reference these variables for consistency.

```

```

**Sources:** [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

 [css/cliente.css L5-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L5-L6)

 [css/mesera.css L4-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L4-L15)

### Typography System

The global typography system uses `'Poppins'` as the primary font family with fallbacks to system fonts. Heading weights are set to 700 (bold) across the application.

```

```

**Sources:** [css/style.css L21-L31](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L21-L31)

### Global Component Library

The foundation layer provides reusable components used across all interfaces:

| Component | CSS Class | Location | Purpose |
| --- | --- | --- | --- |
| Navigation Bar | `.navbar` | [style.css L44-L83](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L44-L83) | Top navigation with brand and links |
| Primary Button | `.btn-primary` | [style.css L85-L93](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L85-L93) | Primary action buttons |
| Outline Button | `.btn-outline-primary` | [style.css L95-L103](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L95-L103) | Secondary action buttons |
| Carousel | `.carousel-item` | [style.css L106-L136](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L106-L136) | Hero image slider |
| Info Card | `.info-card` | [style.css L159-L196](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L159-L196) | Content cards with icon headers |
| Menu Item | `.menu-item` | [style.css L199-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L199-L238) | Product display cards |
| Footer | `.footer` | [style.css L313-L383](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L313-L383) | Site footer with links and social icons |

**Sources:** [css/style.css L44-L383](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L44-L383)

---

## Role-Based Interface Strategy

Each user role receives a dedicated CSS file that defines a tailored interface optimized for their specific job function. The system implements **principle of least privilege** at the UI level—users only see components relevant to their role.

### Role-to-CSS Mapping

```

```

**Sources:** [css/login.css L1-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L1-L181)

 [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

 [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)

 [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

 Database: `usuarios.rol` field

### Interface Complexity by Role

The complexity of each role's interface correlates with their job responsibilities:

| Role | CSS Lines | Primary Sections | Complexity Rationale |
| --- | --- | --- | --- |
| Kitchen (cocinero) | 29 | Order cards with status | Minimal UI for quick status updates |
| Customer (cliente) | 371 | Menu browsing, cart, orders, reservations | Self-service requires detailed product browsing |
| Cashier (cajera) | 402 | Orders, payments, invoices, sales analytics | Financial operations need multiple views |
| Server (mesera) | 705 | Tables, orders, menu, modals | Most complex—coordinates tables, orders, and kitchen |

**Sources:** Line counts from respective CSS files

### Common Interface Patterns Across Roles

Despite role-specific optimizations, several patterns repeat across interfaces with consistent naming conventions:

**Header Pattern**: Each role has a branded header component

* Customer: `.welcome-banner` [cliente.css L4-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L4-L18)
* Cashier: `.cajera-header` [cajera.css L39-L70](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cajera.css#L39-L70)
* Server: `.mesera-header` [mesera.css L18-L53](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L18-L53)

**Card Pattern**: Information grouped in card components

* `.client-card` [cliente.css L37-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L37-L52)
* `.summary-card` [cajera.css L72-L96](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cajera.css#L72-L96)
* `.mesera-card` [mesera.css L108-L123](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L108-L123)
* `.order-card` (used by all roles) [cocina.css L1-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cocina.css#L1-L6)

**Section Pattern**: Major UI areas wrapped in section containers

* `.client-section` [cliente.css L21-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L21-L34)
* `.orders-section` [cajera.css L99-L104](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cajera.css#L99-L104)  [mesera.css L55-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L55-L59)
* `.tables-section` [mesera.css L55-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L55-L59)

**Sources:** [css/cliente.css L4-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L4-L52)

 [css/cajera.css L39-L104](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L39-L104)

 [css/mesera.css L18-L123](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L18-L123)

 [css/cocina.css L1-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L6)

---

## Component Architecture

### Card-Based Component System

The application uses a card-based UI pattern extensively. Cards provide visual grouping and consistent spacing across all interfaces.

```

```

**Order Card Component** - Shared across multiple roles with role-specific styling:

| Usage | File | Lines | State Classes |
| --- | --- | --- | --- |
| Kitchen Display | `cocina.css` | [1-27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/1-27) | `.new`, `.preparing`, `.ready` with colored borders |
| Cashier Orders | `cajera.css` | [127-191](https://github.com/axchisan/AmericanFood/blob/67fea4d3/127-191) | Standard layout with payment actions |
| Server Orders | `mesera.css` | [270-357](https://github.com/axchisan/AmericanFood/blob/67fea4d3/270-357) | Includes table assignment info |

**Sources:** [css/style.css L159-L196](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L159-L196)

 [css/cliente.css L37-L158](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L37-L158)

 [css/cajera.css L72-L191](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L72-L191)

 [css/cocina.css L1-L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L27)

 [css/mesera.css L163-L357](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L357)

### Modal Overlay Pattern

Modals handle complex operations that require user focus. All modals follow Bootstrap modal structure with custom styling.

**Modal Components Across Roles:**

```

```

**Modal Header Styling Pattern:**

```

```

**Sources:** [css/cajera.css L222-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L222-L250)

 [css/mesera.css L402-L555](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L402-L555)

 [css/cliente.css L309-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L309-L322)

### Sidebar Panel Pattern

Sidebars provide contextual information without leaving the current page. Two sidebar implementations exist:

**1. Cart Sidebar** (fixed-position, slide-in from right)

* Component: `.cart-sidebar` [style.css L576-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L576-L693)
* Positioning: `position: fixed; right: -400px` (hidden), transitions to `right: 0` (visible)
* Overlay: `.cart-overlay` with `rgba(0, 0, 0, 0.5)` background
* Structure: header → scrollable items → footer with totals

**2. Notifications Sidebar** (used by cashier and server)

* Component: `.notifications-sidebar` [cajera.css L264-L360](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cajera.css#L264-L360)  [mesera.css L558-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L558-L624)
* Similar positioning strategy: `right: -350px` (cashier) or `right: -400px` (server)
* Includes: notification-item list, notification-badge count
* State management: `.active` class toggles visibility

**Sidebar Activation Pattern:**

```

```

**Sources:** [css/style.css L576-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L693)

 [css/cajera.css L264-L360](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L264-L360)

 [css/mesera.css L558-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L558-L624)

### State Visualization System

Visual state indicators use color-coded borders and status dots for quick recognition.

**Kitchen Order States** (cocina.css):

```

```

**Server Table States** (mesera.css):

```

```

**Status Legend Component** (mesera.css):

```

```

**Status Dot Implementation:**

```

```

**Sources:** [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

 [css/mesera.css L126-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L126-L190)

---

## Grid and Layout Systems

### CSS Grid Implementation

The application uses CSS Grid for flexible, responsive layouts of card-based content.

**Tables Grid** (Server Interface):

```

```

**Menu Items Grid** (Server Interface):

```

```

**Role Selection Grid** (Login Interface):

```

```

**Grid Characteristics:**

* Uses `repeat(auto-fill, minmax(min, 1fr))` for responsive column creation
* Minimum column widths: 150-200px depending on content type
* Consistent gap spacing: 1rem - 1.5rem
* Automatic reflow at breakpoints without media queries

**Sources:** [css/mesera.css L156-L363](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L156-L363)

 [css/login.css L114-L118](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L114-L118)

### Flexbox for Component Internals

Flexbox handles internal component layout for alignment and spacing.

**Order Header Layout:**

```

```

**Table Item Layout:**

```

```

**Common Flexbox Patterns:**

* `.display: flex; justify-content: space-between` - for headers with actions on both ends
* `.display: flex; align-items: center; gap: X` - for horizontal component groups
* `.display: flex; flex-direction: column` - for vertical stacking (sidebars, lists)

**Sources:** [css/cajera.css L135-L191](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L135-L191)

 [css/mesera.css L163-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L262)

 [css/cliente.css L179-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L224)

---

## Responsive Design Strategy

### Breakpoint System

The application defines three primary responsive breakpoints, applied consistently across all CSS files:

| Breakpoint | Width | Target Devices | Layout Changes |
| --- | --- | --- | --- |
| Large | 992px | Tablets (landscape), small desktops | Reduce carousel height, adjust section padding |
| Medium | 768px | Tablets (portrait) | Stack columns, simplify navigation, reduce font sizes |
| Small | 576px | Mobile phones | Single column layout, full-width buttons, minimal padding |

**Breakpoint Implementation Example (style.css):**

```

```

**Sources:** [css/style.css L386-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L386-L460)

 [css/cliente.css L325-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L325-L371)

 [css/mesera.css L627-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L627-L705)

 [css/login.css L165-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L165-L181)

### Mobile-Specific Adaptations

**Server Interface Mobile Adaptations:**

```

```

**Mobile Table Item Transformation:**

```

```

**Mobile Action Buttons:**

```

```

**Sources:** [css/mesera.css L627-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L627-L705)

### Customer Interface Mobile Optimization

The customer interface includes special mobile-specific features for cart management:

**Desktop**: Sticky sidebar cart at `.cart-sidebar` [cliente.css L161-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L161-L164)

```

```

**Mobile**: Offcanvas drawer with separate mobile styles [cliente.css L309-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L309-L322)

```

```

**Mobile Responsive Adjustments:**

```

```

**Sources:** [css/cliente.css L161-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L371)

### Login Interface Responsive Behavior

The authentication interface adapts forms and role selection for mobile:

```

```

**Sources:** [css/login.css L165-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L165-L181)

---

## Component Interaction Patterns

### Cart System Architecture

The cart system demonstrates cross-interface component reuse. It appears in both global (`style.css`) and customer-specific (`cliente.css`) contexts with different implementations.

```

```

**Cart Toggle Mechanism:**

```

```

**Sources:** [css/style.css L576-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L693)

 [css/cliente.css L161-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L322)

### Notification System Pattern

Both cashier and server interfaces implement notification sidebars with identical structure but different widths.

**Notification Component Structure:**

| Element | Cashier CSS | Server CSS | Purpose |
| --- | --- | --- | --- |
| Container | `.notifications-sidebar` [264-294](https://github.com/axchisan/AmericanFood/blob/67fea4d3/264-294) | `.notifications-sidebar` [558-624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/558-624) | Fixed position panel |
| Width | 350px | 400px | Role-specific sizing |
| Header | `.notifications-header` [295-314](https://github.com/axchisan/AmericanFood/blob/67fea4d3/295-314) | `.notifications-header` [574-594](https://github.com/axchisan/AmericanFood/blob/67fea4d3/574-594) | Branded header with close button |
| List | `.notifications-list` [316-320](https://github.com/axchisan/AmericanFood/blob/67fea4d3/316-320) | `.notifications-list` [596-600](https://github.com/axchisan/AmericanFood/blob/67fea4d3/596-600) | Scrollable notification items |
| Item | `.notification-item` [322-354](https://github.com/axchisan/AmericanFood/blob/67fea4d3/322-354) | Not separately styled | Individual notification card |
| Footer | `.notifications-footer` [356-360](https://github.com/axchisan/AmericanFood/blob/67fea4d3/356-360) | `.notifications-footer` [602-609](https://github.com/axchisan/AmericanFood/blob/67fea4d3/602-609) | Action buttons |
| Overlay | `.notifications-overlay` [280-293](https://github.com/axchisan/AmericanFood/blob/67fea4d3/280-293) | `.notifications-overlay` [611-624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/611-624) | Backdrop dimming |

**Notification Badge:**

```

```

**Sources:** [css/cajera.css L253-L360](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L253-L360)

 [css/mesera.css L558-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L558-L624)

---

## Performance Considerations

### CSS Optimization Patterns

**1. Efficient Selectors:**

* Direct class selectors avoid deep nesting: `.order-card`, `.table-item`
* State classes use single modifiers: `.order-card.new`, `.table-item.available`
* No ID selectors used (except for specific modals like `#newOrderModal`)

**2. Minimal Specificity:**

```

```

**3. Transition Performance:**
All transitions use GPU-accelerated properties:

* `transform: translateY()` for hover effects [style.css L209](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L209-L209)  [mesera.css L176](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L176-L176)
* `right` property for sidebar slides [style.css L584](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L584-L584)  [cajera.css L272](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cajera.css#L272-L272)
* `opacity` for fade effects [style.css L601](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L601-L601)  [cajera.css L291](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cajera.css#L291-L291)

**4. Reflow Minimization:**
Fixed heights avoid layout shifts:

* `.carousel-item { height: 600px; }` [style.css L107](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L107-L107)
* `.menu-item img { height: 200px; }` [style.css L214](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L214-L214)
* `.cart-items { max-height: 300px; }` [cliente.css L167](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L167-L167)

**Sources:** All CSS files, performance patterns throughout

### File Size Analysis

| File | Size (lines) | Optimization Status |
| --- | --- | --- |
| `style.css` | 704 | Could be split into modules (global, carousel, footer) |
| `mesera.css` | 705 | Largest file, could extract modal styles |
| `cajera.css` | 402 | Well-sized, focused scope |
| `cliente.css` | 371 | Well-sized, focused scope |
| `login.css` | 181 | Optimal, single-purpose |
| `cocina.css` | 29 | Minimal, optimal |

**Potential Optimizations:**

1. Extract shared modal styles into `modals.css`
2. Create `notifications.css` for shared notification sidebar
3. Split `style.css` into `base.css`, `components.css`, `layout.css`
4. Consider CSS minification for production

**Sources:** Line counts from file headers

---

## Browser Compatibility

### CSS Features Used

The codebase uses modern CSS features with broad browser support:

**CSS Grid**: [mesera.css L158](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L158-L158)

 [login.css L116](https://github.com/axchisan/AmericanFood/blob/67fea4d3/login.css#L116-L116)

* Browser support: Chrome 57+, Firefox 52+, Safari 10.1+, Edge 16+

**CSS Custom Properties**: [style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/style.css#L3-L18)

* Browser support: Chrome 49+, Firefox 31+, Safari 9.1+, Edge 15+

**Flexbox**: Extensively used throughout all files

* Browser support: All modern browsers, IE 11 with prefixes

**Sticky Positioning**: [cliente.css L162](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L162-L162)

* Browser support: Chrome 56+, Firefox 59+, Safari 13+, Edge 16+

**Calc() Function**: [cliente.css L319](https://github.com/axchisan/AmericanFood/blob/67fea4d3/cliente.css#L319-L319)

 [mesera.css L598](https://github.com/axchisan/AmericanFood/blob/67fea4d3/mesera.css#L598-L598)

* Browser support: All modern browsers, IE 9+

**No Vendor Prefixes**: The code assumes autoprefixer or modern browser usage, as no vendor prefixes are present.

**Recommendation**: For production, add autoprefixer to build process to ensure compatibility with older browsers in the support matrix.

**Sources:** All CSS files, feature usage analysis

---

## Design Patterns Summary

### Pattern Catalog

| Pattern | Files | Classes | Purpose |
| --- | --- | --- | --- |
| **Header Banner** | All role CSS files | `.welcome-banner`, `.cajera-header`, `.mesera-header` | Role-specific branded headers |
| **Card Container** | All files | `.info-card`, `.client-card`, `.order-card`, `.menu-item-card` | Content grouping with shadows |
| **Grid Layout** | mesera, login | `.tables-grid`, `.menu-items-grid`, `.role-options` | Responsive multi-column layouts |
| **Fixed Sidebar** | style, cliente, cajera, mesera | `.cart-sidebar`, `.notifications-sidebar` | Off-canvas panels that slide in |
| **Modal Overlay** | cajera, mesera, cliente | Various modal classes | Focused task completion |
| **State Classes** | cocina, mesera | `.new`, `.preparing`, `.ready`, `.available`, `.occupied` | Visual status indicators |
| **Filter Buttons** | mesera | `.btn-filter`, `.btn-filter.active` | Toggle-able category filters |
| **Status Legend** | mesera | `.table-status-legend`, `.status-dot` | Color key for status meanings |
| **Responsive Grid** | All grids | `repeat(auto-fill, minmax())` | Automatic column adjustment |
| **Icon Badges** | mesera, cajera | `.table-number`, `.notification-badge` | Numeric indicators on icons |

**Sources:** Cross-referenced from all CSS files

### Component Composition Example

The server's table management system demonstrates how multiple patterns compose:

```

```

**Sources:** [css/mesera.css L55-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L55-L262)

---

## Key Architectural Decisions

### Why CSS-Only (No JavaScript Frameworks)?

The frontend architecture uses pure CSS without React, Vue, or Angular for several reasons evident in the codebase:

1. **Server-Side Rendering**: PHP generates HTML; CSS provides styling
2. **Simplicity**: No build process, no bundling, direct file inclusion
3. **Progressive Enhancement**: Works without JavaScript for basic functionality
4. **Maintainability**: CSS expertise is more widely available than framework knowledge
5. **Performance**: No framework overhead, minimal initial payload

**Trade-offs:**

* **Manual State Management**: No reactive data binding; state managed server-side
* **Code Duplication**: Cart logic duplicated between `style.css` and `cliente.css`
* **Limited Interactivity**: Complex interactions require custom JavaScript (not visible in CSS files)

### Why Role-Specific CSS Files?

Instead of a single CSS file with role-based classes, each role gets its own file:

**Benefits:**

* **Parallel Development**: Teams can work on different roles independently
* **Smaller Payload**: Users only load CSS for their role
* **Clear Ownership**: Each file has a single responsibility
* **Easier Testing**: Role interfaces can be tested in isolation

**Trade-offs:**

* **Code Duplication**: Similar components (`.order-card`) redefined per role
* **Maintenance Overhead**: Changes to shared patterns require updates to multiple files
* **Inconsistency Risk**: Divergent implementations of similar components

**Recommendation**: Extract shared component styles into `components.css` loaded by all roles, with role-specific files only containing unique styles.

### Why CSS Custom Properties Over Sass Variables?

The codebase uses CSS custom properties (`:root { --primary-color: ... }`) instead of preprocessor variables:

**Benefits:**

* **Runtime Changes**: Values can be modified via JavaScript if needed
* **Cascade Awareness**: Variables participate in CSS cascade
* **No Build Step**: Direct browser interpretation
* **Scoping**: Can be redefined in specific contexts

**Trade-offs:**

* **No Compilation Features**: No mixins, functions, or nesting
* **Browser Support**: Requires modern browsers (IE11 needs fallbacks)

The choice aligns with the "no build process" architecture decision.

**Sources:** Architectural analysis of codebase structure and patterns

---

## Future Improvements

### Recommended Refactoring

1. **Extract Shared Components** ```python css/ ├── base/ │   ├── variables.css (from style.css:3-18) │   ├── typography.css (from style.css:20-42) │   └── utilities.css ├── components/ │   ├── cards.css (shared card patterns) │   ├── modals.css (shared modal styles) │   ├── sidebars.css (cart + notifications) │   └── buttons.css ├── layout/ │   ├── navbar.css │   ├── footer.css │   └── grid.css └── roles/     ├── cliente.css     ├── cajera.css     ├── cocina.css     └── mesera.css ```
2. **Standardize Component Naming** * Adopt BEM methodology: `.order-card__header`, `.order-card__body`, `.order-card__footer` * Current: Mix of nested selectors and flat classes
3. **Add CSS Documentation** * Document component APIs (expected HTML structure) * Add usage examples for complex components * Document state transitions (e.g., how `.active` is applied)
4. **Performance Optimization** * Implement critical CSS extraction for above-the-fold content * Add CSS minification to build process * Consider lazy-loading role-specific CSS
5. **Accessibility Enhancements** * Add focus-visible styles for keyboard navigation * Ensure sufficient color contrast (some gray combinations may fail WCAG) * Add skip links and ARIA landmarks

**Sources:** Analysis of current architecture and industry best practices

---

## Related Documentation

For deeper dives into specific aspects of the frontend architecture:

* **[Design System and Global Styles](/axchisan/AmericanFood/5.1-design-system-and-global-styles)** - Detailed documentation of CSS custom properties, typography, and global components
* **[Authentication Interface](/axchisan/AmericanFood/5.2-authentication-interface)** - Login form, registration flow, and role selection UI
* **[Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system)** - Individual role interface documentation * **[Customer Interface](/axchisan/AmericanFood/5.3.1-customer-interface-(cliente.css))** - Menu browsing, cart, orders, reservations * **[Cashier Interface](/axchisan/AmericanFood/5.3.2-cashier-interface-(cajera.css))** - Payment processing, sales analytics, invoicing * **[Kitchen Interface](/axchisan/AmericanFood/5.3.3-kitchen-interface-(cocina.css))** - Order status tracking and cooking workflow * **[Server Interface](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css))** - Table management and order coordination
* **[Component Patterns](/axchisan/AmericanFood/5.4-component-patterns)** - Reusable UI patterns and component library

For backend integration:

* **[Backend Services](/axchisan/AmericanFood/4-backend-services)** - PHP application layer that generates HTML
* **[User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control)** - How roles are authenticated and authorized

**Sources:** Wiki structure from table of contents