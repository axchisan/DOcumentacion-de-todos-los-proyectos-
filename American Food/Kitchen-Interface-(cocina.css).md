# Kitchen Interface (cocina.css)

> **Relevant source files**
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

This document describes the kitchen-facing user interface defined in [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 This CSS module provides the styling for the order preparation interface used by kitchen staff (`rol='cocinero'`). The interface focuses on displaying incoming orders with clear visual state indicators and minimal design to facilitate quick status updates during food preparation.

For the customer-facing interface, see [Customer Interface (cliente.css)](/axchisan/AmericanFood/5.3.1-customer-interface-(cliente.css)). For cashier payment processing, see [Cashier Interface (cajera.css)](/axchisan/AmericanFood/5.3.2-cashier-interface-(cajera.css)). For server table management, see [Server Interface (mesera.css)](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css)).

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [database/american_food L218-L227](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L218-L227)

---

## Component Architecture

### Component Hierarchy

The kitchen interface uses a card-based layout system with three main structural components organized hierarchically:

```

```

**Diagram: Kitchen Interface Component Tree**

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

### Component Structure Table

| Component Class | Purpose | Key Styles | Line Reference |
| --- | --- | --- | --- |
| `.order-card` | Container for entire order | Border, border-radius, shadow, margin | [css/cocina.css L1-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L6) |
| `.order-header` | Top section with order metadata | Flexbox layout, gray background, border-bottom | [css/cocina.css L12-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L12-L19) |
| `.order-time` | Time tracking display | Part of header | [css/cocina.css L21](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L21-L21) |
| `.time-elapsed` | Elapsed time indicator | Red color (`#dc3545`), smaller font | [css/cocina.css L21](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L21-L21) |
| `.order-body` | Main content area with order details | Padding | [css/cocina.css L22](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L22-L22) |
| `.order-items` | Container for order line items | Margin-bottom | [css/cocina.css L23](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L23-L23) |
| `.order-item` | Individual menu item in order | Flexbox, space-between layout | [css/cocina.css L24](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L24-L24) |
| `.item-quantity` | Quantity indicator | Bold font-weight | [css/cocina.css L25](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L25-L25) |
| `.item-notes` | Special instructions | Gray color, italic style | [css/cocina.css L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L26-L26) |
| `.order-footer` | Bottom section with actions | Padding, border-top, centered text | [css/cocina.css L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L27-L27) |
| `.cooking-progress` | Progress indicator component | Margin-bottom | [css/cocina.css L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L28-L28) |
| `.ready-status` | Ready for pickup indicator | Green color (`#28a745`), bold | [css/cocina.css L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L29-L29) |

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

## State Visualization System

### Order State Classes

The kitchen interface implements a three-state visual system using colored left borders to indicate order status at a glance:

```

```

**Diagram: Order State Transition Flow**

**Sources:** [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

---

### State Color Mapping

| State Class | Border Color | Color Code | Semantic Meaning | Line Reference |
| --- | --- | --- | --- | --- |
| `.order-card.new` | Blue | `#007bff` | New order, not yet started | [css/cocina.css L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L8) |
| `.order-card.preparing` | Yellow/Amber | `#ffc107` | Currently being prepared | [css/cocina.css L9](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L9-L9) |
| `.order-card.ready` | Green | `#28a745` | Ready for pickup/delivery | [css/cocina.css L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L10-L10) |

The state classes apply a `5px solid` left border to the `.order-card` container, creating a visual status bar on the left edge of each order card.

**Sources:** [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

---

## Visual Design Patterns

### Card Styling

```

```

**Diagram: Order Card Visual Composition**

The `.order-card` uses standard card design patterns from [css/style.css L158-L164](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L158-L164)

 with modifications for kitchen-specific needs:

* **Border:** Subtle `#ddd` border provides definition
* **Border Radius:** `8px` creates rounded corners
* **Shadow:** Slight elevation with `0 2px 5px` shadow
* **Spacing:** `20px` margin between cards for visual separation
* **State Border:** Additional `5px` colored left border overrides base border on left side

**Sources:** [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

---

### Layout Components

#### Header Layout

The `.order-header` uses flexbox to create a two-column layout:

```

```

**Diagram: Order Header Flexbox Layout**

* **Display:** `flex` with `justify-content: space-between`
* **Alignment:** `align-items: center` for vertical centering
* **Background:** `#f8f9fa` light gray to distinguish from body
* **Border:** Bottom border `1px solid #ddd` separates from content

**Sources:** [css/cocina.css L12-L19](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L12-L19)

---

#### Body Layout

The `.order-body` contains the order details with simple padding:

* **Padding:** `15px` on all sides
* **Content:** Contains `.order-items`, `.cooking-progress`, and `.ready-status` children
* **Flow:** Vertical stacking of child elements with natural block layout

**Sources:** [css/cocina.css L22-L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L22-L26)

---

#### Footer Layout

The `.order-footer` provides centered action buttons:

* **Padding:** `10px` on all sides
* **Border:** Top border `1px solid #ddd` separates from body
* **Alignment:** `text-align: center` for centered button placement

**Sources:** [css/cocina.css L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L27-L27)

---

## Item Display Components

### Order Items List

The `.order-items` container holds individual `.order-item` elements:

```

```

**Diagram: Order Items Component Structure**

Each `.order-item` uses flexbox with `justify-content: space-between` to create a row layout where quantity and item details are separated.

**Sources:** [css/cocina.css L23-L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L23-L26)

---

### Item Detail Elements

| Element | Styling | Purpose | Line Reference |
| --- | --- | --- | --- |
| `.item-quantity` | `font-weight: bold` | Emphasizes quantity for quick scanning | [css/cocina.css L25](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L25-L25) |
| `.item-notes` | `color: #6c757d` (gray), `font-style: italic` | De-emphasizes special instructions, visually distinct | [css/cocina.css L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L26-L26) |

The `.item-notes` class styles special instructions or modifications to menu items. The gray color (`#6c757d`) and italic font make these secondary to the main item information but still readable.

**Sources:** [css/cocina.css L25-L26](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L25-L26)

---

## Status Indicators

### Time Tracking

The `.time-elapsed` component displays how long an order has been in the system:

* **Color:** `#dc3545` (danger red) from global color system
* **Font Size:** `0.9em` slightly smaller than base text
* **Semantic:** Red indicates urgency for orders taking longer than expected

This component is typically placed within `.order-time` in the `.order-header`.

**Sources:** [css/cocina.css L21](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L21-L21)

---

### Progress and Completion

Two indicator components show cooking status:

| Component | Color | Font Weight | Purpose | Line Reference |
| --- | --- | --- | --- | --- |
| `.cooking-progress` | Default | Default | Shows preparation progress | [css/cocina.css L28](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L28-L28) |
| `.ready-status` | `#28a745` (green) | `bold` | Indicates order completion | [css/cocina.css L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L29-L29) |

The `.ready-status` uses the same green color as the `.order-card.ready` state class, creating visual consistency between the card border and internal status indicator.

**Sources:** [css/cocina.css L28-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L28-L29)

---

## Integration with Order Processing

### Database State Mapping

The CSS state classes map to the `estado` field in the `pedidos` table:

```

```

**Diagram: Database State to CSS Class Mapping**

The kitchen interface receives orders from the `pedidos` table and applies CSS classes based on the current `estado` value. Backend PHP code likely performs this mapping when rendering the HTML.

**Sources:** [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

 [database/american_food L218-L227](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L218-L227)

---

### Order Type Handling

The kitchen interface displays orders regardless of `tipo_pedido`:

| Order Type | Field Set | Display Difference |
| --- | --- | --- |
| `'online'` | `cliente_id`, `informacion_entrega` | May show delivery address |
| `'presencial'` | `mesa_id` | Shows table number |

Both order types flow through the same visual state system (new → preparing → ready).

**Sources:** [database/american_food L218-L227](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L218-L227)

---

## Relationship to Global Styles

### Base Style Inheritance

The kitchen interface inherits base styles from [css/style.css L3-L520](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L520)

:

```

```

**Diagram: Style Inheritance Hierarchy**

While `cocina.css` doesn't explicitly reference the CSS custom properties from `style.css`, the document would inherit them through the cascade if the global stylesheet is included first.

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

---

### Color Palette Alignment

The state colors in `cocina.css` align with Bootstrap and common UI conventions:

| Purpose | cocina.css Color | style.css Variable | Alignment |
| --- | --- | --- | --- |
| Primary/Info | `#007bff` (blue) | `--info-color: #3498DB` | Different shade, same family |
| Warning | `#ffc107` (yellow) | `--warning-color: #F39C12` | Different shade, same family |
| Success | `#28a745` (green) | `--success-color: #27AE60` | Different shade, same family |
| Danger/Time | `#dc3545` (red) | `--danger-color: #E74C3C` | Different shade, same family |

The colors follow semantic conventions (blue=new, yellow=in-progress, green=complete, red=urgent) rather than exact variable matches.

**Sources:** [css/cocina.css L8-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L29)

 [css/style.css L11-L14](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L11-L14)

---

## Design Philosophy

### Minimalist Approach

The kitchen interface is intentionally minimal compared to other role-specific interfaces:

| Interface | CSS Lines | Component Count | Complexity |
| --- | --- | --- | --- |
| `cliente.css` | ~295 lines | 15+ components | High |
| `cajera.css` | ~535 lines | 20+ components | High |
| `mesera.css` | ~535 lines | 20+ components | High |
| `cocina.css` | **29 lines** | **10 components** | **Low** |

This streamlined design serves kitchen staff who need:

* **Quick visual scanning** of order status
* **Minimal distractions** in a fast-paced environment
* **Clear state indicators** at a glance
* **Focus on action** rather than information density

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

### State-Driven Design

The interface prioritizes state visualization over decorative elements:

1. **Color-coded borders** provide instant status recognition
2. **Flexbox layouts** maximize content density without clutter
3. **Bold and italic** styling emphasizes critical information (quantity, notes)
4. **Time elapsed indicator** in red creates urgency awareness
5. **Minimal padding/margins** allow more orders on screen simultaneously

This design pattern is optimized for the kitchen workflow where staff need to:

* Quickly identify new orders
* Track multiple orders in progress
* Recognize completed orders ready for delivery

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

## Responsive Behavior

### Missing Responsive Design

Unlike other role-specific interfaces, `cocina.css` contains **no media queries** or responsive breakpoints. The interface relies on:

1. **Flexbox natural wrapping** in `.order-header` and `.order-item`
2. **Percentage-based widths** inherited from Bootstrap grid if used
3. **Fixed pixel values** for padding and margins that don't adapt to screen size

This suggests the kitchen interface is designed for:

* **Fixed station displays** (desktop monitors in kitchen prep areas)
* **Consistent viewport sizes** across kitchen terminals
* **Landscape orientation** typical of workstation monitors

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

---

### Comparison to Other Interfaces

| Interface | Responsive Breakpoints | Mobile Optimization |
| --- | --- | --- |
| `style.css` | 992px, 768px, 576px | Yes |
| `cliente.css` | Multiple breakpoints | Yes (cart sidebar, offcanvas) |
| `cajera.css` | Multiple breakpoints | Yes |
| `mesera.css` | Multiple breakpoints | Yes |
| `cocina.css` | **None** | **No** |

The absence of responsive design in `cocina.css` indicates kitchen terminals are expected to be stationary workstations rather than mobile devices.

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/style.css L385-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L385-L460)

---

## Usage Context

### Kitchen Workflow Integration

```

```

**Diagram: Kitchen Interface Interaction Flow**

The interface serves as the visual layer for kitchen staff to view and update order states. Backend PHP code handles state transitions and database updates.

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [database/american_food L218-L227](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L218-L227)

---

### Role Access Control

Access to the kitchen interface is controlled through the `usuarios.rol` field:

```

```

**Diagram: Role-Based Access to Kitchen Interface**

Only users with `rol='cocinero'` in the `usuarios` table can access the kitchen interface. The login system validates credentials and routes users to the appropriate interface based on their role.

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [database/american_food L249-L258](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L249-L258)

---

## Summary

The kitchen interface (`cocina.css`) implements a streamlined, state-driven design optimized for food preparation workflows. With only 29 lines of CSS, it provides:

* **Three-state visual system** using colored left borders (blue/yellow/green)
* **Card-based layout** with header, body, and footer sections
* **Flexbox components** for efficient space utilization
* **Time-sensitive indicators** with red elapsed time display
* **Minimal design** to reduce cognitive load in fast-paced kitchen environments

The interface integrates with the `pedidos` table's `estado` field and serves users with `rol='cocinero'`, providing a focused view of orders requiring preparation without the complexity of payment processing, table management, or menu browsing features found in other role-specific interfaces.

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)