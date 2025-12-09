# Customer Interface (cliente.css)

> **Relevant source files**
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

The Customer Interface stylesheet defines the presentation layer for the customer-facing portal where authenticated users with the `rol='cliente'` can browse the menu, manage a shopping cart, view reservations, and track order history. This interface implements a card-based, mobile-responsive design optimized for self-service food ordering.

For authentication UI, see [Authentication Interface](/axchisan/AmericanFood/5.2-authentication-interface). For global styles and shared components, see [Design System and Global Styles](/axchisan/AmericanFood/5.1-design-system-and-global-styles). For the dual-track order processing that customers initiate, see [Online vs In-Person Orders](/axchisan/AmericanFood/8.2-online-vs-in-person-orders).

**Sources**: [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

---

## Component Architecture

The customer interface is organized into a hierarchical component system with five primary functional areas:

```

```

**Sources**: [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

---

## Layout Components

### Welcome Banner

The `.welcome-banner` provides a prominent header section with brand color background to greet authenticated customers.

**CSS Class**: `.welcome-banner` [css/cliente.css L4-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L4-L18)

**Properties**:

* Background color: `var(--primary-color)` (orange #FF6B35)
* Text color: white
* Padding: `2rem` vertical
* Margin bottom: `2rem`

**Child elements**:

* `h1`: Main heading with `0.5rem` bottom margin
* `p`: Subtitle text with `0.9` opacity

**Usage**: Displays customer name and welcome message at top of customer dashboard.

### Client Section

The `.client-section` wrapper provides consistent spacing for major functional areas.

**CSS Class**: `.client-section` [css/cliente.css L21-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L21-L34)

**Properties**:

* Padding: `2rem` top, `4rem` bottom
* Heading color: `var(--primary-color)`
* Icon spacing: `10px` right margin

**Responsive Behavior**:

| Breakpoint | Padding |
| --- | --- |
| Desktop (>992px) | `2rem 0 4rem` |
| Tablet (≤768px) | `1.5rem 0 3rem` |

### Client Card

The `.client-card` component creates elevated white containers for grouping related content.

**CSS Class**: `.client-card` [css/cliente.css L37-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L37-L52)

**Properties**:

* Background: white
* Border radius: `10px`
* Box shadow: `0 4px 15px rgba(0,0,0,0.1)`
* Padding: `1.5rem`
* Height: `100%` (flex container)

**Child heading** (`.client-card h3`):

* Color: `var(--secondary-color)` (red #C0392B)
* Font size: `1.3rem`
* Bottom border: `1px solid var(--gray-medium)`
* Padding bottom: `0.8rem`

**Sources**: [css/cliente.css L4-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L4-L52)

---

## Menu System

### Component Hierarchy

```

```

### Category Navigation

The menu uses Bootstrap `.nav-pills` for horizontal category filtering.

**CSS Classes**: `.menu-categories`, `.nav-pills` [css/cliente.css L93-L107](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L93-L107)

**Pill styling** (`.nav-pills .nav-link`):

* Color: `var(--dark-color)` (inactive state)
* Border radius: `30px` (pill shape)
* Padding: `0.5rem 1.2rem`
* Horizontal margin: `0.5rem` between pills

**Active state** (`.nav-pills .nav-link.active`):

* Background: `var(--primary-color)` (orange)
* Text: white (inherited from Bootstrap)

### Menu Item Card

The `.menu-item-card` presents individual menu products in a vertical card layout.

**CSS Class**: `.menu-item-card` [css/cliente.css L109-L158](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L109-L158)

**Structure**:

1. **Image section**: Fixed 180px height, `object-fit: cover`
2. **Info section** (`.menu-item-info`): Flexible grow area with padding
3. **Footer section** (`.menu-item-footer`): Price and action button

**Key properties**:

```yaml
display: flex
flex-direction: column
height: 100%
background-color: white
border-radius: 10px
box-shadow: 0 4px 10px rgba(0,0,0,0.1)
```

**Image sizing** [css/cliente.css L120-L124](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L120-L124)

:

* Desktop: `180px` height
* Tablet (≤768px): `150px` height

**Price display** (`.menu-item-footer .price`):

* Font weight: `700`
* Color: `var(--secondary-color)` (red)
* Font size: `1.1rem`

**Sources**: [css/cliente.css L93-L158](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L93-L158)

---

## Shopping Cart System

### Architecture Overview

The cart system implements a dual-interface approach: a sticky sidebar for desktop and an offcanvas drawer for mobile.

```

```

### Desktop Cart Sidebar

The `.cart-sidebar` uses sticky positioning to remain visible during page scroll.

**CSS Class**: `.cart-sidebar` [css/cliente.css L161-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L164)

**Properties**:

```

```

**Note**: This differs from the global `.cart-sidebar` in [css/style.css L576-L588](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L588)

 which uses `position: fixed` for the slide-in cart panel. The cliente.css version creates an always-visible column in a grid layout.

### Cart Items Container

The `.cart-items` area provides a scrollable list of products added to cart.

**CSS Class**: `.cart-items` [css/cliente.css L166-L170](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L166-L170)

**Properties**:

* Max height: `300px`
* Overflow: `auto` (vertical scroll)
* Margin bottom: `1rem`

**Empty state** (`.empty-cart-message`) [css/cliente.css L172-L177](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L172-L177)

:

* Text alignment: center
* Color: `var(--gray-dark)`
* Font style: italic
* Padding: `2rem 0`

### Cart Item Component

Each `.cart-item` displays a single product with quantity controls.

**CSS Class**: `.cart-item` [css/cliente.css L179-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L224)

**Layout structure**:

```yaml
display: flex
justify-content: space-between
align-items: center
```

**Sub-components**:

| Class | Purpose | Properties |
| --- | --- | --- |
| `.cart-item-info` | Product name and unit price | `flex-grow: 1` |
| `.cart-item-name` | Product name | `font-weight: 500`, `margin-bottom: 0.2rem` |
| `.cart-item-price` | Unit price display | `font-size: 0.9rem`, color: gray |
| `.cart-item-quantity` | Quantity controls | `display: flex`, `margin-right: 1rem` |
| `.cart-item-remove` | Delete button | `color: var(--danger-color)`, `cursor: pointer` |

**Quantity buttons** [css/cliente.css L207-L214](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L207-L214)

:

* Width/height: `25px`
* Padding: `0`
* Display: flex with centered content
* Span between buttons shows current quantity

### Cart Summary

The `.cart-summary` section displays totals and checkout action.

**CSS Class**: `.cart-summary` [css/cliente.css L226-L237](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L226-L237)

**Properties**:

* Border top: `2px solid var(--gray-medium)`
* Padding top: `1rem`

**Total display** (`.cart-total`):

```

```

### Mobile Offcanvas Cart

The mobile implementation uses Bootstrap's offcanvas component with custom styling.

**CSS Classes**: `.offcanvas-header`, `.cart-items-mobile` [css/cliente.css L309-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L309-L322)

**Header styling** (`.offcanvas-header`):

* Background: `var(--primary-color)`
* Text color: white
* Title font weight: `600`

**Items container** (`.cart-items-mobile`):

* Max height: `calc(100vh - 300px)` (viewport height minus header/footer)
* Overflow: `auto`
* Margin bottom: `1rem`

**Sources**: [css/cliente.css L161-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L322)

 [css/style.css L575-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L575-L693)

---

## Reservation Management

The reservation system displays upcoming table bookings with modification options.

### Reservation List

**CSS Class**: `.reservation-list` [css/cliente.css L55-L58](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L55-L58)

**Properties**:

* Max height: `400px`
* Overflow: `auto` (scrollable for many reservations)

### Reservation Item

Each `.reservation-item` card displays booking details in a flexible layout.

**CSS Class**: `.reservation-item` [css/cliente.css L60-L90](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L60-L90)

**Structure**:

```

```

**Information fields**:

| Class | Icon | Purpose |
| --- | --- | --- |
| `.reservation-date` | Calendar icon | Booking date |
| `.reservation-time` | Clock icon | Time slot |
| `.reservation-people` | Users icon | Party size |

All information fields share common styling [css/cliente.css L71-L85](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L71-L85)

:

```

```

Icons have:

* Right margin: `8px`
* Color: `var(--primary-color)`

### Reservation Actions

**CSS Class**: `.reservation-actions` [css/cliente.css L87-L90](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L87-L90)

**Properties**:

```

```

Typically contains buttons for "Modify" and "Cancel" operations.

**Sources**: [css/cliente.css L55-L90](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L55-L90)

---

## Order History

The order history section displays past orders with status tracking and order details.

### Order History Container

**CSS Class**: `.order-history` [css/cliente.css L240-L243](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L240-L243)

**Properties**:

* Max height: `500px`
* Overflow: `auto` (scrollable list)

### Order Item Card

Each `.order-item` represents a completed or in-progress order.

**CSS Class**: `.order-item` [css/cliente.css L245-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L245-L250)

**Base properties**:

```

```

### Order Header

The `.order-header` section displays metadata about the order.

**CSS Class**: `.order-header` [css/cliente.css L252-L260](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L252-L260)

**Layout**:

```

```

**Data fields** (`.order-date`, `.order-number`, `.order-status`) [css/cliente.css L262-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L262-L266)

:

* Margin bottom: `0.5rem`
* Icons with `5px` right margin

### Order Status Indicators

Status classes apply semantic colors to communicate order state:

**CSS Classes** [css/cliente.css L273-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L273-L279)

:

| Class | Color Variable | Meaning |
| --- | --- | --- |
| `.order-status.delivered` | `var(--success-color)` (green) | Order completed |
| `.order-status.processing` | `var(--warning-color)` (yellow) | Order in progress |

### Order Details

The `.order-details` section shows product line items and total.

**CSS Class**: `.order-details` [css/cliente.css L281-L306](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L281-L306)

**Layout**:

```

```

**Sub-components**:

| Class | Purpose | Properties |
| --- | --- | --- |
| `.order-products` | List of items ordered | `flex-grow: 1`, `margin-right: 1rem` |
| `.order-total` | Total amount | `font-weight: 700`, color: secondary, `margin-right: 1rem` |
| `.order-actions` | Action buttons | `display: flex`, `gap: 0.5rem` |

**Products list** (`.order-products p`):

* Margin bottom: `0.3rem` between items

### Order Actions

**CSS Class**: `.order-actions` [css/cliente.css L303-L306](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L303-L306)

**Properties**:

```

```

Typically contains buttons for "View Details" or "Reorder".

**Sources**: [css/cliente.css L240-L306](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L240-L306)

---

## Responsive Design Strategy

The customer interface implements a three-tier responsive breakpoint system to optimize for desktop, tablet, and mobile viewports.

### Breakpoint Configuration

```

```

### Responsive Rules

**992px Breakpoint** [css/cliente.css L325-L329](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L325-L329)

:

```

```

Changes at this breakpoint:

* Cart switches from sticky sidebar to offcanvas drawer
* Client card spacing reduced

**768px Breakpoint** [css/cliente.css L331-L348](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L331-L348)

:

| Element | Property | Value |
| --- | --- | --- |
| `.welcome-banner` | padding | `1.5rem 0` |
| `.welcome-banner` | margin-bottom | `1.5rem` |
| `.client-section` | padding | `1.5rem 0 3rem` |
| `.client-section h2` | margin-bottom | `1.5rem` |
| `.menu-item-card img` | height | `150px` (from 180px) |

**576px Breakpoint (Mobile)** [css/cliente.css L350-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L350-L371)

:

| Element | Property | Value |
| --- | --- | --- |
| `.welcome-banner h1` | font-size | `1.5rem` |
| `.client-section h2` | font-size | `1.3rem` |
| `.client-card` | padding | `1rem` |
| `.client-card h3` | font-size | `1.1rem` |
| `.reservation-item` | padding | `0.8rem` |
| `.order-item` | padding | `0.8rem` |

### Mobile-Specific Features

The mobile implementation includes specialized components not used on desktop:

1. **Offcanvas Cart** [css/cliente.css L309-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L309-L322) : * Slide-in drawer from right edge * Full viewport height * Primary color header
2. **Compact Item Display**: * Reduced padding throughout * Smaller font sizes * Optimized touch targets

**Sources**: [css/cliente.css L325-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L325-L371)

---

## CSS Class Reference

### Complete Class Inventory

| Class Name | Purpose | File Location |
| --- | --- | --- |
| `.welcome-banner` | Top banner with greeting | [css/cliente.css L4-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L4-L18) |
| `.client-section` | Section wrapper | [css/cliente.css L21-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L21-L34) |
| `.client-card` | Content card container | [css/cliente.css L37-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L37-L52) |
| `.reservation-list` | Reservations scrollable container | [css/cliente.css L55-L58](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L55-L58) |
| `.reservation-item` | Individual reservation card | [css/cliente.css L60-L69](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L60-L69) |
| `.reservation-date` | Date display with icon | [css/cliente.css L71-L78](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L71-L78) |
| `.reservation-time` | Time display with icon | [css/cliente.css L71-L78](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L71-L78) |
| `.reservation-people` | Party size display | [css/cliente.css L71-L78](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L71-L78) |
| `.reservation-actions` | Button container | [css/cliente.css L87-L90](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L87-L90) |
| `.menu-categories` | Category filter wrapper | [css/cliente.css L93-L95](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L93-L95) |
| `.nav-pills` | Bootstrap pill navigation | [css/cliente.css L97-L107](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L97-L107) |
| `.menu-item-card` | Menu product card | [css/cliente.css L109-L118](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L109-L118) |
| `.menu-item-info` | Product details section | [css/cliente.css L126-L146](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L126-L146) |
| `.menu-item-footer` | Price and actions | [css/cliente.css L148-L158](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L148-L158) |
| `.cart-sidebar` | Sticky cart column | [css/cliente.css L161-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L164) |
| `.cart-items` | Scrollable cart list | [css/cliente.css L166-L170](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L166-L170) |
| `.empty-cart-message` | Empty state text | [css/cliente.css L172-L177](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L172-L177) |
| `.cart-item` | Individual cart product | [css/cliente.css L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L185) |
| `.cart-item-info` | Product name/price | [css/cliente.css L187-L189](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L187-L189) |
| `.cart-item-name` | Product name text | [css/cliente.css L191-L194](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L191-L194) |
| `.cart-item-price` | Unit price text | [css/cliente.css L196-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L196-L199) |
| `.cart-item-quantity` | Quantity controls | [css/cliente.css L201-L219](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L201-L219) |
| `.cart-item-remove` | Delete button | [css/cliente.css L221-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L221-L224) |
| `.cart-summary` | Totals section | [css/cliente.css L226-L229](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L226-L229) |
| `.cart-total` | Total amount display | [css/cliente.css L231-L237](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L231-L237) |
| `.order-history` | Past orders container | [css/cliente.css L240-L243](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L240-L243) |
| `.order-item` | Individual order card | [css/cliente.css L245-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L245-L250) |
| `.order-header` | Order metadata section | [css/cliente.css L252-L260](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L252-L260) |
| `.order-date` | Order date display | [css/cliente.css L262-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L262-L266) |
| `.order-number` | Order ID display | [css/cliente.css L262-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L262-L266) |
| `.order-status` | Status badge | [css/cliente.css L262-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L262-L279) |
| `.order-details` | Order contents section | [css/cliente.css L281-L286](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L281-L286) |
| `.order-products` | Product list | [css/cliente.css L288-L295](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L288-L295) |
| `.order-total` | Order total amount | [css/cliente.css L297-L301](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L297-L301) |
| `.order-actions` | Order action buttons | [css/cliente.css L303-L306](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L303-L306) |
| `.offcanvas-header` | Mobile cart header | [css/cliente.css L309-L312](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L309-L312) |
| `.offcanvas-title` | Mobile cart title | [css/cliente.css L314-L316](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L314-L316) |
| `.cart-items-mobile` | Mobile cart items list | [css/cliente.css L318-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L318-L322) |

### CSS Custom Property Dependencies

The customer interface relies on the following CSS custom properties defined in [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

:

| Variable | Value | Usage in cliente.css |
| --- | --- | --- |
| `--primary-color` | #FF6B35 (orange) | Welcome banner background, category active state, icons |
| `--secondary-color` | #C0392B (red) | Card headings, prices, order totals |
| `--dark-color` | #2C3E50 | Inactive navigation items, product names |
| `--gray-light` | #F5F5F5 | Reservation/order item backgrounds |
| `--gray-medium` | #E0E0E0 | Borders, dividers |
| `--gray-dark` | #9E9E9E | Secondary text, descriptions |
| `--success-color` | #27AE60 (green) | Delivered order status |
| `--warning-color` | #F39C12 (orange) | Processing order status |
| `--danger-color` | #E74C3C (red) | Remove cart item icon |

**Sources**: [css/cliente.css L1-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L371)

 [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

---

## Integration with Backend

While this stylesheet defines only presentation logic, the HTML structure it styles connects to backend systems:

### Database Integration Points

The customer interface components map to database tables as follows:

| Component | Database Table | Purpose |
| --- | --- | --- |
| Menu item cards | `menu` | Product catalog with `imagen` field |
| Cart items | Session/`detalles_pedidos` | Shopping cart state |
| Reservation items | `reservas` (implied) | Table booking records |
| Order history | `pedidos` | Order records with `cliente_id` |
| Order details | `detalles_pedidos` | Line items for each order |

### Order Status Mapping

The `.order-status` CSS classes correspond to the `estado` field in the `pedidos` table:

| CSS Class | Database Value | User Display |
| --- | --- | --- |
| `.order-status.processing` | `nuevo`, `en_preparacion` | "Processing" (yellow) |
| `.order-status.delivered` | `entregado` | "Delivered" (green) |

For complete order lifecycle, see [Order Processing Flow](/axchisan/AmericanFood/8.1-order-processing-flow).

**Sources**: [css/cliente.css L273-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L273-L279)

 database schema in [Database Layer](/axchisan/AmericanFood/2-database-layer)