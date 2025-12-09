# Component Patterns

> **Relevant source files**
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

This document catalogs the reusable UI component patterns implemented across the American Food restaurant management system's CSS architecture. These patterns establish consistency across role-specific interfaces while maintaining flexibility for specialized functionality.

For information about the foundational design system and global styles, see [Design System and Global Styles](/axchisan/AmericanFood/5.1-design-system-and-global-styles). For details about specific role interfaces that implement these patterns, see [Role-Based Interface System](/axchisan/AmericanFood/5.3-role-based-interface-system).

---

## Overview of Component Architecture

The frontend implements a component-based CSS architecture where visual patterns are consistently applied across different user interfaces. Each pattern is defined through CSS classes that can be combined and extended for role-specific needs.

### Component Pattern Hierarchy

```

```

**Sources**: css/style.css, css/cajera.css, css/cliente.css, css/cocina.css, css/mesera.css

---

## Card Component Pattern

The card pattern is the most widely used component, providing consistent visual containers for information across all interfaces. Cards feature rounded borders, shadows, and standardized padding.

### Card Implementations by Role

| Card Class | File | Purpose | Key Features |
| --- | --- | --- | --- |
| `.order-card` | cajera.css:127-133 | Display order details for cashier | Border, rounded corners, card header/footer |
| `.order-card` | cocina.css:1-6 | Display orders for kitchen staff | State-specific left border colors |
| `.order-card` | mesera.css:270-276 | Display orders for servers | Hover effects, shadow elevation |
| `.menu-item-card` | cliente.css:109-118 | Display menu items for customers | Image header, flexible content area |
| `.client-card` | cliente.css:37-44 | General purpose customer cards | Consistent padding, full height |
| `.summary-card` | cajera.css:72-80 | Display sales summaries | Icon-text layout with flexbox |
| `.table-item` | mesera.css:163-173 | Display table status | State-colored left border, hover transform |

### Card Structure Pattern

```

```

**Sources**: css/cajera.css:127-191, css/cliente.css:109-159, css/cocina.css:1-29, css/mesera.css:270-357

### Order Card Implementation

The `.order-card` pattern appears in three interfaces with role-specific styling:

**Kitchen Interface** [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

:

```css
.order-card {
    border: 1px solid #ddd;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}
.order-card.new { border-left: 5px solid #007bff; }
.order-card.preparing { border-left: 5px solid #ffc107; }
.order-card.ready { border-left: 5px solid #28a745; }
```

**Cashier Interface** [css/cajera.css L127-L133](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L127-L133)

:

```css
.order-card {
    background-color: #f9f9f9;
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 15px;
    margin-bottom: 15px;
}
```

**Server Interface** [css/mesera.css L270-L280](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L270-L280)

:

```css
.order-card {
    background-color: var(--white);
    border-radius: 10px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    padding: 1.5rem;
    transition: all 0.3s ease;
}
.order-card:hover {
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.15);
}
```

**Sources**: css/cocina.css:1-10, css/cajera.css:127-133, css/mesera.css:270-280

### Card Internal Structure Components

All card implementations share common internal structure classes:

**Order Header** [css/cajera.css L135-L150](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L135-L150)

 [css/mesera.css L282-L303](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L282-L303)

:

* `.order-header`: Flexbox container with space-between alignment
* Contains order ID/number on left, timestamp on right
* Consistent margin-bottom for separation from body

**Order Body** [css/cocina.css L22](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L22-L22)

 [css/mesera.css L304-L330](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L304-L330)

:

* `.order-body` / `.order-items`: Scrollable content area
* `.order-item`: Individual line items with quantity, name, price
* `.item-quantity`, `.item-name`, `.item-price`: Semantic naming for item components

**Order Footer** [css/cajera.css L172-L191](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L172-L191)

 [css/mesera.css L331-L357](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L331-L357)

:

* `.order-footer`: Actions and total price
* `.order-total`: Price display with emphasized total
* `.order-actions`: Button group for operations (pay, invoice, etc.)

**Sources**: css/cajera.css:135-191, css/mesera.css:282-357, css/cocina.css:22-27

---

## Modal Overlay Pattern

Modals provide focused interaction contexts for complex operations. The pattern consists of modal dialog, header, body, footer, and backdrop overlay.

### Modal Component Architecture

```

```

**Sources**: css/cajera.css:221-250, css/mesera.css:402-556

### Modal Implementations

| Modal Purpose | File | CSS Classes | Triggered By |
| --- | --- | --- | --- |
| Payment Processing | cajera.css:221-229 | `.payment-details-section` | "Cobrar" button on order card |
| Invoice Generation | cajera.css:231-233 | `#newInvoiceForm` | "Generar Factura" button |
| Payment Confirmation | cajera.css:235-250 | `.confirmation-icon`, `.payment-receipt` | After payment submitted |
| New Order | mesera.css:402-469 | `#newOrderModal`, `.order-items-form` | "Nuevo Pedido" button |
| Table Details | mesera.css:471-556 | `#tableDetailsModal`, `.table-details` | Click on table-item |

### New Order Modal Structure

The new order modal in mesera interface demonstrates full modal pattern implementation:

**Modal Header** [css/mesera.css L406-L415](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L406-L415)

:

```css
#newOrderModal .modal-header {
    background-color: var(--primary-color);
    color: var(--white);
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
}
```

**Modal Body with Form** [css/mesera.css L417-L459](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L417-L459)

:

* `.order-items-form`: Grid layout for item selection
* `.item-select`, `.item-quantity`, `.item-price`: Form controls
* `#selectedItemsList`: Scrollable list of added items
* `.add-item-btn`, `.remove-item-btn`: Action buttons within form

**Modal Footer with Total** [css/mesera.css L461-L468](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L461-L468)

:

```css
.order-total h5 {
    margin: 0;
    font-size: 1.2rem;
}
.order-total span {
    color: var(--primary-color);
}
```

**Sources**: css/mesera.css:402-469

### Payment Modal Implementation

Cashier interface uses modals for payment processing with specialized components:

**Payment Details Section** [css/cajera.css L222-L228](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L222-L228)

:

* Dynamic form that appears when payment method selected
* `#changeAmount`: Read-only field showing calculated change

**Payment Receipt** [css/cajera.css L241-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L241-L250)

:

```css
.payment-receipt {
    background-color: #f9f9f9;
    padding: 15px;
    border-radius: 8px;
    margin-top: 20px;
}
```

**Confirmation Icon** [css/cajera.css L236-L239](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L236-L239)

:

```css
.confirmation-icon {
    font-size: 3rem;
    color: #28a745;
}
```

**Sources**: css/cajera.css:221-250

---

## Sidebar Pattern

Sidebars provide supplementary interfaces that slide in from screen edges. Two main implementations: cart sidebar and notifications sidebar.

### Sidebar Pattern Architecture

```

```

**Sources**: css/style.css:576-693, css/cajera.css:264-360, css/mesera.css:558-624

### Cart Sidebar Implementation

The cart sidebar is a global component defined in `style.css` and extended in `cliente.css`:

**Base Structure** [css/style.css L576-L610](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L610)

:

```css
.cart-sidebar {
    position: fixed;
    top: 0;
    right: -400px;
    width: 400px;
    height: 100%;
    background: #fff;
    transition: right 0.3s ease;
    z-index: 1000;
}
.cart-sidebar.active {
    right: 0;
}
.cart-overlay {
    position: fixed;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    opacity: 0;
    visibility: hidden;
    z-index: 999;
}
.cart-overlay.active {
    opacity: 1;
    visibility: visible;
}
```

**Cart Content Areas** [css/style.css L612-L682](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L612-L682)

:

* `.cart-header`: Fixed header with title and close button
* `.cart-items`: Scrollable middle section with `flex: 1` and `overflow-y: auto`
* `.cart-footer`: Fixed footer with totals and checkout button
* `.empty-cart`: Placeholder when no items present

**Cart Item Structure** [css/style.css L636-L668](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L636-L668)

:

```css
.cart-item {
    display: flex;
    align-items: center;
    padding: 10px 0;
    border-bottom: 1px solid #eee;
}
```

* `.cart-item-image`: Product thumbnail (60x60px)
* `.cart-item-details`: Name and price with flex-grow
* `.cart-item-actions`: Quantity controls and remove button

**Sources**: css/style.css:576-693

### Cliente-Specific Cart Extensions

Customer interface extends cart sidebar with additional styling:

**Sticky Positioning** [css/cliente.css L161-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L161-L164)

:

```
.cart-sidebar {
    position: sticky;
    top: 2rem;
}
```

**Cart Items Scrolling** [css/cliente.css L166-L170](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L166-L170)

:

```
.cart-items {
    max-height: 300px;
    overflow-y: auto;
    margin-bottom: 1rem;
}
```

**Cart Item Components** [css/cliente.css L179-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L224)

:

* `.cart-item-info`: Flexible content area
* `.cart-item-quantity`: Quantity controls with centered buttons
* `.cart-item-remove`: Delete icon with danger color

**Mobile Cart** [css/cliente.css L309-L322](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L309-L322)

:

```css
.offcanvas-header {
    background-color: var(--primary-color);
    color: white;
}
.cart-items-mobile {
    max-height: calc(100vh - 300px);
    overflow-y: auto;
}
```

**Sources**: css/cliente.css:161-322

### Notifications Sidebar Implementation

Notifications sidebar follows same pattern with role-specific content:

**Cajera Notifications** [css/cajera.css L264-L360](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L264-L360)

:

```
.notifications-sidebar {
    position: fixed;
    right: -350px;
    width: 350px;
    height: 100%;
    transition: right 0.3s ease;
    z-index: 1000;
}
.notifications-sidebar.active {
    right: 0;
}
```

**Notification Items** [css/cajera.css L322-L354](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L322-L354)

:

```css
.notification-item {
    display: flex;
    gap: 10px;
    padding: 10px;
    border-bottom: 1px solid #eee;
}
.notification-item.unread {
    background-color: #f0f8ff;
}
```

* `.notification-icon`: Icon indicating notification type
* `.notification-content`: Title and message
* `.notification-time`: Timestamp display

**Mesera Notifications** [css/mesera.css L558-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L558-L624)

:

* Similar structure with `width: 400px` and `right: -400px`
* Matches primary color scheme of server interface
* Full-width on mobile (max-width: 768px)

**Sources**: css/cajera.css:264-360, css/mesera.css:558-624

---

## State Visualization Pattern

State visualization uses color-coded borders, badges, and status indicators to communicate entity states visually. Critical for kitchen order tracking and table management.

### State Visualization Mapping

```

```

**Sources**: css/cocina.css:8-10, css/mesera.css:143-153, css/mesera.css:180-190, css/cliente.css:273-279

### Kitchen Order State Implementation

Kitchen interface uses left border colors for instant state recognition:

**State Classes** [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

:

```css
.order-card.new { border-left: 5px solid #007bff; }
.order-card.preparing { border-left: 5px solid #ffc107; }
.order-card.ready { border-left: 5px solid #28a745; }
```

**State Indicators** [css/cocina.css L21-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L21-L29)

:

* `.time-elapsed`: Red text for orders taking too long
* `.cooking-progress`: Progress bar for preparation tracking
* `.ready-status`: Green bold text when order complete

**Sources**: css/cocina.css:1-29

### Table State Visualization

Server interface uses comprehensive state system for table management:

**Status Dot Legend** [css/mesera.css L136-L153](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L136-L153)

:

```css
.status-dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    margin-right: 5px;
}
.status-dot.available { background-color: var(--success-color); }
.status-dot.occupied { background-color: var(--danger-color); }
.status-dot.reserved { background-color: var(--warning-color); }
```

**Table Item State Borders** [css/mesera.css L180-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L180-L190)

:

```
.table-item.available {
    border-left: 5px solid var(--success-color);
}
.table-item.occupied {
    border-left: 5px solid var(--danger-color);
}
.table-item.reserved {
    border-left: 5px solid var(--warning-color);
}
```

**State-Specific Text Colors** [css/mesera.css L245-L255](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L245-L255)

:

```css
.table-item.available .table-status {
    color: var(--success-color);
}
.table-item.occupied .table-status {
    color: var(--danger-color);
}
.table-item.reserved .table-status {
    color: var(--warning-color);
}
```

**Sources**: css/mesera.css:136-255

### Order Status Display (Customer View)

Customer interface shows historical order statuses:

**Status Classes** [css/cliente.css L273-L279](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L273-L279)

:

```css
.order-status.delivered {
    color: var(--success-color);
}
.order-status.processing {
    color: var(--warning-color);
}
```

**Status Icons** [css/cliente.css L268-L271](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L268-L271)

:

* Each status displays with Font Awesome icon
* Color matches state (green = delivered, yellow = processing)

**Sources**: css/cliente.css:262-279

---

## Header Component Pattern

Headers provide consistent page/section identification across interfaces with role-specific branding and metadata.

### Header Pattern Hierarchy

```

```

**Sources**: css/cajera.css:38-70, css/mesera.css:18-53, css/cajera.css:106-122, css/mesera.css:77-84

### Role-Specific Headers

**Cajera Header** [css/cajera.css L39-L70](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L39-L70)

:

```css
.cajera-header {
    background-color: #fff;
    padding: 20px 0;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.cajera-info {
    display: flex;
    justify-content: flex-end;
    gap: 20px;
}
.info-item {
    display: flex;
    align-items: center;
    gap: 8px;
    color: #666;
}
```

* White background with shadow for elevation
* Right-aligned info items showing cashier name, register, date
* Icon-text pairs using `.info-item` component

**Mesera Header** [css/mesera.css L18-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L18-L52)

:

```css
.mesera-header {
    background-color: var(--primary-color);
    color: var(--white);
    padding: 2rem 0;
    margin-bottom: 2rem;
}
.mesera-info {
    display: flex;
    justify-content: flex-end;
    gap: 1.5rem;
}
```

* Primary color background for brand consistency
* Server name and assigned table count
* Responsive: stacks vertically on mobile (max-width: 768px)

**Sources**: css/cajera.css:39-70, css/mesera.css:18-52

### Section Headers

Section headers provide titles and contextual actions for major content areas:

**Base Structure** [css/cajera.css L106-L122](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L106-L122)

 [css/mesera.css L77-L84](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L77-L84)

:

```css
.section-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}
.section-header h2 {
    font-size: 1.75rem;
    color: var(--primary-color);
    display: flex;
    align-items: center;
}
.header-actions {
    display: flex;
    gap: 10px;
}
```

**Filter Buttons** [css/mesera.css L86-L105](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L86-L105)

:

```css
.table-filters,
.menu-filters {
    display: flex;
    gap: 0.5rem;
}
.btn-filter {
    background-color: var(--gray-light);
    border-radius: 20px;
    transition: all 0.3s ease;
}
.btn-filter:hover,
.btn-filter.active {
    background-color: var(--primary-color);
    color: var(--white);
}
```

* Pill-shaped filter buttons
* Active state with primary color background
* Used for table status filters and menu category filters

**Sources**: css/cajera.css:106-122, css/mesera.css:77-105

### Card Header Pattern

Headers within cards follow consistent structure:

**Order Header** [css/cajera.css L135-L150](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L135-L150)

 [css/mesera.css L282-L303](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L282-L303)

:

```css
.order-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
}
.order-header h5 {
    margin: 0;
    font-size: 1.25rem;
}
.order-time {
    color: #666;
    font-size: 0.9rem;
}
```

* Left side: Order identifier (number or name)
* Right side: Timestamp with icon
* Optional status badge

**Sources**: css/cajera.css:135-150, css/mesera.css:282-303, css/cocina.css:12-19

---

## Responsive Design System

The system implements mobile-first responsive design with three standard breakpoints. Each interface adapts layout, spacing, and component sizing.

### Responsive Breakpoint Strategy

| Breakpoint | Width | Target Devices | Key Adjustments |
| --- | --- | --- | --- |
| Large | `@media (max-width: 992px)` | Tablets, small laptops | Reduce padding, adjust grid columns |
| Medium | `@media (max-width: 768px)` | Tablets (portrait), large phones | Stack layouts, full-width components |
| Small | `@media (max-width: 576px)` | Mobile phones | Minimal padding, vertical stacking |

### Responsive Pattern Implementation

```

```

**Sources**: css/style.css:386-460, css/cliente.css:325-371, css/mesera.css:627-705, css/cajera.css (implied)

### Grid System Responsive Behavior

**Desktop Grid** [css/mesera.css L156-L161](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L156-L161)

:

```
.tables-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1.5rem;
}
```

**Tablet Adjustment** [css/mesera.css L643-L644](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L643-L644)

:

```
.tables-grid {
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
}
```

**Menu Grid** [css/mesera.css L359-L363](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L359-L363)

:

```
.menu-items-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1.5rem;
}
```

**Mobile Menu Grid** [css/mesera.css L657-L658](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L657-L658)

:

```
.menu-items-grid {
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
}
```

**Sources**: css/mesera.css:156-161, css/mesera.css:359-363, css/mesera.css:643-658

### Sidebar Responsive Behavior

**Desktop Sidebar** [css/style.css L576-L588](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L576-L588)

:

```
.cart-sidebar {
    position: fixed;
    right: -400px;
    width: 400px;
    height: 100%;
}
```

**Mobile Sidebar** [css/mesera.css L674-L676](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L674-L676)

:

```
.notifications-sidebar {
    width: 100%;
}
```

* Full viewport width on mobile devices
* Covers entire screen rather than sliding from side

**Sources**: css/style.css:576-588, css/mesera.css:674-676

### Component Spacing Responsive Adjustments

**Section Padding** [css/style.css L138-L459](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L138-L459)

:

```javascript
/* Desktop */
section { padding: 5rem 0; }

/* Tablet (992px) */
section { padding: 4rem 0; }

/* Medium (768px) */
section { padding: 3rem 0; }

/* Mobile (576px) */
section { padding: 2rem 0; }
```

**Customer Interface Spacing** [css/cliente.css L325-L348](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L325-L348)

:

```css
@media (max-width: 768px) {
    .welcome-banner {
        padding: 1.5rem 0;
        margin-bottom: 1.5rem;
    }
    .client-section {
        padding: 1.5rem 0 3rem;
    }
}
```

**Sources**: css/style.css:386-460, css/cliente.css:325-371

### Typography Responsive Scaling

**Carousel Headers** [css/style.css L396-L421](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L396-L421)

:

```javascript
/* Desktop */
.carousel-caption h1 { font-size: 2.5rem; }

/* Tablet (992px) */
.carousel-caption h1 { font-size: 2rem; }

/* Medium (768px) */
.carousel-caption h1 { font-size: 1.5rem; }

/* Mobile (576px) */
.carousel-caption h1 { font-size: 1.2rem; }
```

**Customer Interface Headers** [css/cliente.css L351-L357](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L351-L357)

:

```
@media (max-width: 576px) {
    .welcome-banner h1 { font-size: 1.5rem; }
    .client-section h2 { font-size: 1.3rem; }
    .client-card h3 { font-size: 1.1rem; }
}
```

**Sources**: css/style.css:396-450, css/cliente.css:351-371

### Form Layout Responsive Behavior

**Modal Form Layout** [css/mesera.css L691-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L691-L704)

:

```
@media (max-width: 576px) {
    .order-items-form .row {
        flex-direction: column;
        gap: 0.5rem;
    }
    .order-items-form .col-md-5,
    .order-items-form .col-md-3,
    .order-items-form .col-md-1 {
        width: 100%;
    }
    .add-item-btn {
        width: 100%;
    }
}
```

* Desktop: Horizontal form with columns
* Mobile: Vertical stacking with full-width inputs

**Sources**: css/mesera.css:691-704

### Button Group Responsive Behavior

**Desktop Button Groups** [css/mesera.css L348-L351](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L348-L351)

:

```
.order-actions {
    display: flex;
    gap: 0.5rem;
}
```

**Mobile Button Stacking** [css/mesera.css L660-L667](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L660-L667)

:

```
@media (max-width: 768px) {
    .order-actions {
        flex-direction: column;
        gap: 0.5rem;
    }
    .order-actions .btn {
        width: 100%;
    }
}
```

**Action Button Adjustments** [css/mesera.css L669-L672](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L669-L672)

:

```
.action-buttons .btn {
    flex: none;
    width: 100%;
}
```

**Sources**: css/mesera.css:348-351, css/mesera.css:660-672

---

## Component Composition Patterns

Components combine to create complex interfaces through consistent composition strategies. This section documents how atomic components combine into molecules and organisms.

### Composition Architecture

```

```

**Sources**: css/cajera.css, css/mesera.css, css/cliente.css, css/cocina.css

### Info Item Pattern (Atomic → Molecular)

Simple icon-text pairs used throughout headers:

**Base Component** [css/cajera.css L60-L69](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L60-L69)

:

```css
.info-item {
    display: flex;
    align-items: center;
    gap: 8px;
    color: #666;
}
.info-item i {
    color: #1a3c34;
}
```

**Composition in Headers** [css/cajera.css L54-L58](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L54-L58)

:

```
.cajera-info {
    display: flex;
    justify-content: flex-end;
    gap: 20px;
}
```

* Multiple `.info-item` components arranged horizontally
* Used for cashier name, register number, current date
* Flexible gap allows easy addition/removal of items

**Server Interface Variant** [css/mesera.css L42-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L42-L52)

:

```css
.mesera-info {
    display: flex;
    justify-content: flex-end;
    gap: 1.5rem;
}
.info-item {
    display: flex;
    align-items: center;
    color: var(--white);
}
```

**Sources**: css/cajera.css:54-69, css/mesera.css:36-52

### Order Item Pattern (Molecular)

Displays individual items within orders across multiple interfaces:

**Cajera Implementation** [css/cajera.css L156-L171](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L156-L171)

:

```css
.order-item {
    display: flex;
    justify-content: space-between;
    padding: 5px 0;
    border-bottom: 1px solid #eee;
}
.item-quantity {
    font-weight: bold;
    color: #1a3c34;
}
.item-price {
    color: #1a3c34;
}
```

**Mesera Implementation** [css/mesera.css L308-L330](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L308-L330)

:

```css
.order-item {
    display: flex;
    justify-content: space-between;
    padding: 0.5rem 0;
    border-bottom: 1px solid var(--gray-medium);
}
.item-quantity {
    font-weight: bold;
    color: var(--primary-color);
}
.item-name {
    flex-grow: 1;
    margin-left: 1rem;
    color: var(--dark-color);
}
```

**Cocina Implementation** [css/cocina.css L24-L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L24-L26)

:

```
.order-item {
    display: flex;
    justify-content: space-between;
    margin-bottom: 5px;
}
```

**Sources**: css/cajera.css:156-171, css/mesera.css:308-330, css/cocina.css:24

### Cart Item Composition (Molecular → Organism)

Cart items combine multiple atomic components:

**Cart Item Structure** [css/style.css L636-L668](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L636-L668)

:

```
.cart-item {
    display: flex;
    align-items: center;
}
.cart-item-image img { width: 60px; height: 60px; }
.cart-item-details { flex: 1; }
.cart-item-actions {
    display: flex;
    align-items: center;
    gap: 5px;
}
```

Components within:

* Image thumbnail (atomic)
* Name and price text (atomic)
* Quantity controls: minus button + span + plus button (molecular)
* Remove button (atomic)

**Cliente Cart Item** [css/cliente.css L179-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L179-L224)

:

```css
.cart-item-quantity {
    display: flex;
    align-items: center;
    margin-right: 1rem;
}
.cart-item-quantity button {
    width: 25px;
    height: 25px;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
}
```

**Sources**: css/style.css:636-668, css/cliente.css:179-224

### Table Item Composition (Organism)

Table management combines icon, status, and metadata:

**Table Item Structure** [css/mesera.css L163-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L262)

:

```css
.table-item {
    display: flex;
    align-items: center;
    padding: 1.5rem;
}
.table-icon {
    width: 50px;
    height: 50px;
    background-color: var(--gray-light);
    border-radius: 50%;
    position: relative;
}
.table-number {
    position: absolute;
    top: -5px;
    right: -5px;
    background-color: var(--primary-color);
    width: 24px;
    height: 24px;
    border-radius: 50%;
}
.table-info {
    flex-grow: 1;
}
```

Composed of:

* `.table-icon`: Circle with table icon + badge (molecular)
* `.table-info`: Title, capacity, status text (molecular)
* State class: `.available` / `.occupied` / `.reserved` (atomic)
* Left border color (atomic)

**Sources**: css/mesera.css:163-262

### Notification Item Composition

Notifications combine icon, content, and timestamp:

**Structure** [css/cajera.css L322-L354](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L322-L354)

:

```css
.notification-item {
    display: flex;
    gap: 10px;
    padding: 10px;
    border-bottom: 1px solid #eee;
}
.notification-icon {
    font-size: 1.5rem;
    color: #1a3c34;
}
.notification-content h6 {
    margin: 0;
    font-size: 1rem;
}
.notification-time {
    display: block;
    color: #999;
    font-size: 0.8rem;
    margin-top: 5px;
}
```

Components:

* Icon (atomic)
* Title heading (atomic)
* Message text (atomic)
* Timestamp (atomic)
* Optional unread state (`.notification-item.unread`)

**Sources**: css/cajera.css:322-354

### Summary Card Composition

Sales summary cards combine icon and data:

**Structure** [css/cajera.css L72-L96](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L72-L96)

:

```css
.sales-summary .summary-card {
    background-color: #fff;
    padding: 20px;
    border-radius: 8px;
    display: flex;
    align-items: center;
    gap: 15px;
}
.summary-icon {
    font-size: 2rem;
    color: #1a3c34;
}
.summary-info h3 {
    margin: 0;
    font-size: 1.5rem;
    color: #1a3c34;
}
.summary-info p {
    margin: 0;
    color: #666;
}
```

Composition:

* Large icon (atomic)
* Heading with metric value (atomic)
* Label paragraph (atomic)
* Flexbox alignment creates horizontal layout

**Sources**: css/cajera.css:72-96

---

## Pattern Integration Summary

### Component Reusability Matrix

| Pattern | cajera.css | cliente.css | cocina.css | mesera.css | style.css |
| --- | --- | --- | --- | --- | --- |
| Card (`.order-card`) | ✓ | - | ✓ | ✓ | - |
| Card (`.menu-item`) | - | ✓ | - | ✓ | ✓ |
| Sidebar (fixed overlay) | ✓ | ✓ | - | ✓ | ✓ |
| Modal | ✓ | - | - | ✓ | - |
| State visualization | - | ✓ | ✓ | ✓ | - |
| Header (role-specific) | ✓ | ✓ | - | ✓ | - |
| Header (section) | ✓ | ✓ | - | ✓ | - |
| Responsive breakpoints | ✓ | ✓ | - | ✓ | ✓ |

### Pattern Extension Strategy

The system allows pattern extension through:

1. **Base class inheritance**: Core patterns in `style.css` provide foundation
2. **Role-specific styling**: Each interface adds custom properties while maintaining structure
3. **Modifier classes**: State classes (`.new`, `.occupied`, `.active`) modify appearance
4. **Composition**: Complex components built from simpler patterns

**Example Extension Flow**:

```
style.css: .cart-sidebar (base structure)
    ↓
cliente.css: .cart-sidebar (positioning adjustment)
    ↓
cliente.css: .cart-item (custom item layout)
    ↓
cliente.css: .cart-item-quantity (custom controls)
```

**Sources**: All CSS files referenced throughout this document