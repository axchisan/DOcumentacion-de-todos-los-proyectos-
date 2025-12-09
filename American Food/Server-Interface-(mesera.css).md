# Server Interface (mesera.css)

> **Relevant source files**
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

This document describes the server/waitress interface styling system defined in [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)

 The `mesera.css` stylesheet provides the visual presentation layer for restaurant servers (meseras) who manage tables, take orders, and coordinate between customers and the kitchen. This interface is optimized for front-of-house operations including table status tracking, order management, and menu browsing for order-taking.

For the customer-facing interface, see [Customer Interface (cliente.css)](/axchisan/AmericanFood/5.3.1-customer-interface-(cliente.css)). For payment processing, see [Cashier Interface (cajera.css)](/axchisan/AmericanFood/5.3.2-cashier-interface-(cajera.css)). For kitchen order preparation, see [Kitchen Interface (cocina.css)](/axchisan/AmericanFood/5.3.3-kitchen-interface-(cocina.css)).

**Sources:** [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

---

## Interface Architecture

The mesera interface consists of four primary functional areas organized as distinct sections on the page.

### High-Level Component Structure

```

```

**Sources:** [css/mesera.css L17-L76](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L17-L76)

 [css/mesera.css L155-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L155-L262)

 [css/mesera.css L264-L357](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L264-L357)

 [css/mesera.css L359-L400](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L359-L400)

 [css/mesera.css L401-L555](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L401-L555)

 [css/mesera.css L557-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L557-L624)

---

## CSS Custom Properties

The mesera interface inherits from and overrides the global color system defined in `style.css`.

| Variable | Value | Usage |
| --- | --- | --- |
| `--primary-color` | `#FF6B35` | Header background, accents, badges |
| `--secondary-color` | `#333` | Dark text, borders |
| `--success-color` | `#28a745` | Available table state |
| `--danger-color` | `#dc3545` | Occupied table state |
| `--warning-color` | `#ffc107` | Reserved table state |
| `--gray-light` | `#f8f9fa` | Card backgrounds, table icons |
| `--gray-medium` | `#dee2e6` | Borders, dividers |
| `--gray-dark` | `#6c757d` | Secondary text |
| `--dark-color` | `#212529` | Primary text |
| `--white` | `#fff` | Card backgrounds, text on primary |

**Sources:** [css/mesera.css L4-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L4-L15)

 [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

---

## Header Component

The `.mesera-header` provides server identification and status information at the top of the interface.

### Header Structure

```

```

### Header Styles

* **Background:** Primary color (`#FF6B35`)
* **Padding:** `2rem 0` vertical spacing
* **Color:** White text for high contrast
* **Layout:** Flexbox with title on left, info items aligned right

**Key Classes:**

* `.mesera-header` - Main container
* `.mesera-info` - Right-aligned stat display using `display: flex` with `gap: 1.5rem`
* `.info-item` - Individual stat with icon and text

**Sources:** [css/mesera.css L17-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L17-L52)

---

## Table Management System

The core functionality of the mesera interface is table management, implemented through a grid-based visualization system with color-coded status indicators.

### Table Grid Layout

```

```

### Grid Configuration

The `.tables-grid` uses CSS Grid with responsive column sizing:

```
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
gap: 1.5rem;
```

This creates a flexible grid that adapts to screen width, with each table card having a minimum width of 200px.

**Sources:** [css/mesera.css L155-L161](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L155-L161)

### Table Item Component

Each `.table-item` represents a single restaurant table with the following internal structure:

```

```

### Table State Visualization

Tables utilize a border-left indicator combined with color-coded text to communicate status:

| State Class | Border Color | Status Text Color | Semantic Meaning |
| --- | --- | --- | --- |
| `.available` | `--success-color` (#28a745) | Green | Table ready for customers |
| `.occupied` | `--danger-color` (#dc3545) | Red | Currently serving customers |
| `.reserved` | `--warning-color` (#ffc107) | Yellow | Reserved for upcoming party |

The state is applied through combined class selectors:

* `.table-item.available` - Available table styling
* `.table-item.occupied` - Occupied table styling
* `.table-item.reserved` - Reserved table styling

**Hover Effect:** All table items respond to hover with `transform: translateY(-5px)` and enhanced shadow for tactile feedback.

**Sources:** [css/mesera.css L163-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L262)

### Table Status Legend

A visual legend helps servers quickly understand the color coding:

```

```

**Sources:** [css/mesera.css L126-L153](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L126-L153)

---

## Order Display System

The `.orders-section` shows active orders for the server's tables, allowing quick status checks and management actions.

### Order Card Structure

```

```

### Order Item Styling

Each `.order-item` uses flexbox to distribute content:

* **Quantity:** Bold, primary color (`#FF6B35`)
* **Name:** Flex-grow to fill space, left margin `1rem`
* **Price:** Right-aligned, medium font-weight

Items are separated by bottom borders (`border-bottom: 1px solid var(--gray-medium)`).

**Sources:** [css/mesera.css L264-L357](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L264-L357)

---

## Menu Section for Order Taking

The `.menu-section` displays available menu items in a grid layout, enabling servers to quickly browse and select items when taking orders.

### Menu Grid Configuration

```
display: grid;
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
gap: 1.5rem;
```

### Menu Item Card

```

```

**Hover Behavior:** Menu items lift up 5px and increase shadow on hover for interactive feedback.

**Sources:** [css/mesera.css L359-L400](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L359-L400)

---

## Modal Interfaces

The mesera interface uses two primary modals for detailed interactions that require focus.

### Modal System Overview

```

```

### Table Details Modal (#tableDetailsModal)

**Purpose:** Display comprehensive table information and provide quick action buttons.

**Key Components:**

* `.table-icon-large` - 70x70px circular icon with table number badge
* `.table-info-details` - Text information (capacity, location, status)
* `.action-buttons` - Flexbox grid of action buttons
* `#currentOrderList` - Scrollable list (max-height: 200px) of current order items

**Sources:** [css/mesera.css L470-L556](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L470-L556)

### New Order Modal (#newOrderModal)

**Purpose:** Create new orders by selecting menu items and quantities.

**Layout:**

* Modal header uses primary color background (`#FF6B35`)
* `.order-items-form` - Grid layout for item selection with 4 columns: * Item select dropdown (col-md-5) * Quantity input (col-md-3) * Price display (col-md-3, read-only) * Add button (col-md-1)
* `#selectedItemsList` - Real-time order summary with remove buttons
* `.order-total` - Running total display

**Interactive Elements:**

* `.add-item-btn` - Adds selected item to order
* `.remove-item-btn` - Removes item from order (0.3rem 0.5rem padding)
* Item select, quantity, and price fields use `font-size: 0.9rem`

**Sources:** [css/mesera.css L401-L469](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L401-L469)

---

## Notification System

The notification sidebar provides real-time updates from the kitchen about order status changes.

### Notification Sidebar Architecture

```

```

### Sidebar Behavior

**Positioning:**

* `position: fixed` - Always visible when active
* `right: -400px` - Hidden off-screen by default
* `width: 400px` - Fixed width panel
* `height: 100%` - Full viewport height

**Activation:**

* `.notifications-sidebar.active` - Slides in (`right: 0`)
* `.notifications-overlay.active` - Backdrop becomes visible
* Transition: `right 0.3s ease`

**Internal Layout:**

* `.notifications-header` - Fixed header with close button
* `.notifications-list` - Scrollable content area (`max-height: calc(100% - 120px)`)
* `.notifications-footer` - Fixed footer with action buttons

**Sources:** [css/mesera.css L557-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L557-L624)

---

## Responsive Design System

The mesera interface adapts to different screen sizes through three primary breakpoints.

### Responsive Breakpoints

```

```

### Breakpoint: 768px

**Changes at max-width 768px:**

| Element | Desktop | Tablet (≤768px) |
| --- | --- | --- |
| `.mesera-header h1` | 2rem | 1.5rem |
| `.mesera-header p` | 1rem | 0.9rem |
| `.mesera-info` | Row flexbox | Column flexbox, right-aligned |
| `.tables-grid` | minmax(200px, 1fr) | minmax(150px, 1fr) |
| `.table-item` | Row flexbox | Column flexbox, centered text |
| `.table-icon` | margin-right: 1rem | margin-bottom: 1rem |
| `.menu-items-grid` | minmax(200px, 1fr) | minmax(150px, 1fr) |
| `.order-actions` | Row flexbox | Column flexbox |
| `.order-actions .btn` | Auto width | 100% width |
| `.action-buttons .btn` | flex: 1 | flex: none, 100% width |
| `.notifications-sidebar` | 400px width | 100% width |

**Sources:** [css/mesera.css L627-L677](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L627-L677)

### Breakpoint: 576px

**Additional changes at max-width 576px:**

| Element | Change |
| --- | --- |
| `.section-header` | Column layout with 1rem gap |
| `.table-filters`, `.menu-filters` | Wrap and center-justify |
| `.order-items-form .row` | Column layout with 0.5rem gap |
| `.order-items-form` columns | All 100% width |
| `.add-item-btn` | 100% width |

**Purpose:** Optimize for very small screens (phones in portrait) by stacking all content vertically.

**Sources:** [css/mesera.css L679-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L679-L705)

---

## Component Class Reference

### Layout Classes

| Class | Purpose | Display Type |
| --- | --- | --- |
| `.mesera-header` | Server identification banner | Block |
| `.tables-section` | Table management area | Block |
| `.orders-section` | Active orders list | Block |
| `.menu-section` | Menu browsing area | Block |
| `.section-header` | Section title + filters | Flexbox row |

### Card Components

| Class | Purpose | Notable Styles |
| --- | --- | --- |
| `.mesera-card` | Generic card container | White bg, 10px radius, shadow |
| `.table-item` | Individual table display | Flexbox, cursor pointer, hover lift |
| `.order-card` | Order information card | White bg, hover shadow increase |
| `.menu-item` | Menu product card | Overflow hidden, hover lift |

### Grid Layouts

| Class | Grid Configuration | Responsive Behavior |
| --- | --- | --- |
| `.tables-grid` | minmax(200px, 1fr) | 150px at ≤768px |
| `.menu-items-grid` | minmax(200px, 1fr) | 150px at ≤768px |
| `.orders-list` | Flexbox column | No change |

### State Indicators

| Class | Border Left | Text Color | Background Usage |
| --- | --- | --- | --- |
| `.available` | Green (#28a745) | Green | Success state |
| `.occupied` | Red (#dc3545) | Red | Danger state |
| `.reserved` | Yellow (#ffc107) | Yellow | Warning state |

**Sources:** [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

---

## Integration with Global Styles

The mesera interface inherits foundational styles from `style.css` and extends them with role-specific components.

### Inherited Elements

```

```

### Variable Override Pattern

The mesera interface redefines CSS variables at the `:root` level to ensure consistent theming across the interface, even if these values duplicate those in `style.css`. This defensive pattern ensures the mesera interface remains functional even if loaded independently.

**Sources:** [css/mesera.css L3-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L3-L15)

 [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

---

## Usage Patterns

### Typical DOM Structure

A typical mesera interface HTML structure references these classes as follows:

```html
<body>
  <!-- Header -->
  <header class="mesera-header">
    <div class="container">
      <h1>Servidor: [Name]</h1>
      <div class="mesera-info">
        <div class="info-item">...</div>
      </div>
    </div>
  </header>

  <!-- Tables Section -->
  <section class="tables-section">
    <div class="section-header">
      <h2>Gestión de Mesas</h2>
      <div class="table-filters">
        <button class="btn-filter active">Todas</button>
        ...
      </div>
    </div>
    <div class="tables-grid">
      <div class="table-item available">...</div>
      <div class="table-item occupied">...</div>
    </div>
  </section>

  <!-- Orders Section -->
  <section class="orders-section">
    <div class="orders-list">
      <div class="order-card">...</div>
    </div>
  </section>

  <!-- Menu Section -->
  <section class="menu-section">
    <div class="menu-items-grid">
      <div class="menu-item">...</div>
    </div>
  </section>

  <!-- Modals -->
  <div id="tableDetailsModal" class="modal">...</div>
  <div id="newOrderModal" class="modal">...</div>

  <!-- Notifications -->
  <div class="notifications-sidebar">...</div>
  <div class="notifications-overlay"></div>
</body>
```

This structure assumes Bootstrap 5 modal components for `#tableDetailsModal` and `#newOrderModal`, with custom styling applied via mesera.css.

**Sources:** [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

---

## Filter System

Both tables and menu sections implement filter buttons to help servers quickly find specific items.

### Filter Button Styling

**Default State:**

* Background: `--gray-light` (#f8f9fa)
* Color: `--dark-color` (#212529)
* Border-radius: `20px` (pill shape)
* Padding: `0.5rem 1rem`

**Active/Hover State:**

* Background: `--primary-color` (#FF6B35)
* Color: `--white`
* Transition: `all 0.3s ease`

The `.btn-filter.active` class marks the currently selected filter, providing visual feedback to the user.

**Sources:** [css/mesera.css L85-L105](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L85-L105)

---

## Performance Considerations

### CSS Grid Performance

The interface uses CSS Grid for both `.tables-grid` and `.menu-items-grid`, which provides optimal performance for layout calculations compared to flexbox for two-dimensional layouts.

**Grid Properties:**

* `repeat(auto-fill, minmax(Xpx, 1fr))` - Browser-optimized column calculation
* `gap` property - More efficient than margins for grid spacing

### Transition Optimization

All transitions are applied to properties that can be GPU-accelerated:

* `transform` (translateY) - Composited layer
* `right` position - For slide-in sidebar
* `box-shadow` - Can be optimized by browser

**No transitions on:**

* Layout properties (width, height, margin, padding)
* Background-color (acceptable in small doses)

### Scrollable Container Strategy

Large lists use `overflow-y: auto` with `max-height` constraints:

* `#selectedItemsList` - 200px max
* `#currentOrderList` - 200px max
* `.notifications-list` - calc(100% - 120px)

This prevents the entire page from scrolling when interacting with list contents, improving perceived performance.

**Sources:** [css/mesera.css L171-L178](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L171-L178)

 [css/mesera.css L566-L567](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L566-L567)

 [css/mesera.css L442-L447](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L442-L447)

---

## Summary

The `mesera.css` stylesheet implements a comprehensive interface for restaurant servers with the following characteristics:

**Core Functionality:**

* Table management with visual status indicators (available/occupied/reserved)
* Order display and management system
* Menu browsing for order-taking
* Modal interfaces for detailed operations
* Real-time notification system

**Design Patterns:**

* CSS Grid for responsive card layouts
* Flexbox for internal card arrangements
* Color-coded state visualization
* Fixed-position notification sidebar with slide-in animation
* Three-tier responsive breakpoint system (desktop/tablet/mobile)

**Technical Features:**

* CSS custom property-based theming
* GPU-accelerated transforms for animations
* Scrollable containers for large datasets
* Mobile-first responsive design
* Accessibility through hover states and clear visual hierarchy

The interface extends the global design system defined in [style.css](/axchisan/AmericanFood/5.1-design-system-and-global-styles) while adding server-specific components optimized for table and order management workflows.

**Sources:** [css/mesera.css L1-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L705)

 [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)