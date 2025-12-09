# Cashier Interface (cajera.css)

> **Relevant source files**
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

This document describes the CSS styling system for the cashier role interface (`cajera.css`), which provides the presentation layer for financial operations staff in the American Food restaurant management system. The cashier interface enables payment processing, sales monitoring, invoice generation, and order management for completed transactions.

For information about the global design system and base styles, see [Design System and Global Styles](/axchisan/AmericanFood/5.1-design-system-and-global-styles). For other role-specific interfaces, see [Customer Interface](/axchisan/AmericanFood/5.3.1-customer-interface-(cliente.css)), [Kitchen Interface](/axchisan/AmericanFood/5.3.3-kitchen-interface-(cocina.css)), and [Server Interface](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css)).

**Sources**: [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)

## Interface Architecture

The cashier interface implements a dashboard-style layout with five primary functional areas: header with cashier information, sales summary metrics, orders awaiting payment, sales analytics, and invoice management. The interface uses a card-based design pattern with distinct sections for different operational tasks.

```

```

**Sources**: [css/cajera.css L4-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L4-L402)

## Color Scheme and Branding

The interface uses a dark green color palette that distinguishes it from other role interfaces. The primary brand color `#1a3c34` appears in the navbar, footer, and notification headers, with gold accents (`#ffd700`) for active states and highlights.

| Element | Color | Purpose |
| --- | --- | --- |
| Primary Background | `#1a3c34` | Navbar, footer, notification header |
| Active/Accent | `#ffd700` | Active navigation links, footer headings |
| Hover State | `#2a5c54` | Dropdown menu hover |
| Content Background | `#fff` | Section cards, modals |
| Page Background | `#f5f5f5` | Body, overall page |
| Order Cards | `#f9f9f9` | Individual order backgrounds |
| Text Primary | `#1a3c34` | Headings, emphasis |
| Text Secondary | `#666` | Body text, descriptions |

**Sources**: [css/cajera.css L4-L370](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L4-L370)

## Header Component System

The header system consists of two main components: the navigation bar inherited from global styles and the cashier-specific header section displaying cashier information and session metadata.

```

```

### CSS Classes

* **`.cajera-header`**: Main header container with white background and subtle shadow ([css/cajera.css L39-L43](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L39-L43) )
* **`.cajera-info`**: Flexbox container for info items, aligned to the right ([css/cajera.css L54-L58](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L54-L58) )
* **`.info-item`**: Individual info display with icon and text ([css/cajera.css L60-L69](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L60-L69) )

The header uses a two-column layout: left side for title/subtitle, right side for metadata (timestamp, cashier name, session info).

**Sources**: [css/cajera.css L38-L70](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L38-L70)

## Sales Summary Dashboard

The sales summary section displays key financial metrics in a card-based grid layout. Each card contains an icon and numerical data for quick visual scanning of daily performance.

```

```

### Summary Card Styling

The `.summary-card` class ([css/cajera.css L72-L80](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L72-L80)

) defines:

* White background with 8px border radius
* Flexbox layout with 15px gap between icon and content
* Box shadow: `0 2px 4px rgba(0, 0, 0, 0.1)`
* Padding: 20px

The `.summary-icon` ([css/cajera.css L82-L85](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L82-L85)

) uses Font Awesome icons at 2rem size in the primary color. The `.summary-info` container ([css/cajera.css L87-L96](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L87-L96)

) holds the metric value (h3) and description (p).

**Sources**: [css/cajera.css L71-L96](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L71-L96)

## Orders Section Architecture

The orders section is the primary workspace for payment processing. It displays pending orders in a card format with search functionality and action buttons.

```

```

### Order Card Components

The `.order-card` ([css/cajera.css L127-L133](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L127-L133)

) uses:

* Background: `#f9f9f9` (slightly off-white)
* Border: `1px solid #ddd`
* Border radius: 8px
* Padding: 15px
* Bottom margin: 15px for stacking

Individual order items (`.order-item`, [css/cajera.css L156-L161](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L156-L161)

) display with:

* Flexbox layout with `justify-content: space-between`
* 5px vertical padding
* Bottom border for visual separation

**Sources**: [css/cajera.css L98-L191](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L98-L191)

## Modal System

The interface implements three modal types for payment processing, invoice generation, and payment confirmation. Each modal follows Bootstrap modal patterns with custom styling.

### Payment Modal

```

```

The `.payment-details-section` ([css/cajera.css L222-L224](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L222-L224)

) adds top margin spacing. The `#changeAmount` field ([css/cajera.css L226-L228](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L226-L228)

) has a light gray background (`#f5f5f5`) to indicate read-only status.

### Invoice Modal

The invoice modal (`#newInvoiceForm`, [css/cajera.css L231-L233](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L231-L233)

) uses form labels with medium font weight (500) for clarity. It collects customer information for invoice generation linked to the `facturas` table in the database.

### Payment Confirmation Modal

```

```

The `.confirmation-icon` ([css/cajera.css L236-L239](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L236-L239)

) displays a large green checkmark icon at 3rem size with success color `#28a745`. The `.payment-receipt` section ([css/cajera.css L241-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L241-L250)

) provides a formatted receipt view with light gray background.

**Sources**: [css/cajera.css L221-L250](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L221-L250)

## Sales Analytics Section

The sales section displays financial analytics through charts and tabular data. It uses two main container types for different visualization formats.

```

```

Both `.sales-chart-container` ([css/cajera.css L198-L204](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L198-L204)

) and `.sales-table-container` ([css/cajera.css L206-L211](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L206-L211)

) share identical styling:

* White background
* 20px padding
* 8px border radius
* Subtle drop shadow
* 20px bottom margin (chart only)

This consistent styling creates visual cohesion between different data visualization types.

**Sources**: [css/cajera.css L192-L211](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L192-L211)

## Invoices Section

The invoices section (`.invoices-section`, [css/cajera.css L213-L219](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L213-L219)

) manages invoice records with identical styling to the orders section:

* White background
* 30px padding
* 8px border radius
* Box shadow for depth

This section likely contains a table or list of generated invoices with options to view, print, or email customer receipts.

**Sources**: [css/cajera.css L213-L219](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L213-L219)

## Notifications System

The notifications system implements a slide-in sidebar with overlay, providing real-time alerts for order status changes, payment confirmations, and system messages.

```

```

### Notification Badge

The `.notification-badge` ([css/cajera.css L253-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L253-L262)

) displays on the navbar:

* Background: `#dc3545` (red)
* Color: white
* Border radius: 50% (circular)
* Position: relative with top/left offsets (-10px, -5px)
* Font size: 0.75rem

### Sidebar Behavior

The `.notifications-sidebar` ([css/cajera.css L264-L278](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L264-L278)

) uses:

* Fixed positioning at `right: -350px` (off-screen)
* Width: 350px
* Height: 100%
* Transition: `right 0.3s ease`
* Z-index: 1000

When `.active` class is applied, the sidebar slides to `right: 0`.

### Overlay Backdrop

The `.notifications-overlay` ([css/cajera.css L280-L293](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L280-L293)

) provides:

* Fixed full-screen coverage
* Background: `rgba(0, 0, 0, 0.5)` (50% black)
* Z-index: 999 (below sidebar)
* Initially `display: none`
* Shows with `.active` class

### Notification Items

Individual notifications (`.notification-item`, [css/cajera.css L322-L327](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L322-L327)

) use flexbox with 10px gap. Unread notifications (`.unread` class, [css/cajera.css L329-L331](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L329-L331)

) have a light blue background (`#f0f8ff`).

The `.notifications-list` container ([css/cajera.css L316-L320](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L316-L320)

) is scrollable with `max-height: calc(100% - 120px)` to accommodate header and footer.

**Sources**: [css/cajera.css L252-L360](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L252-L360)

## Footer Component

The footer (`.footer`, [css/cajera.css L362-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L362-L402)

) matches the navbar styling:

* Background: `#1a3c34` (dark green)
* Color: white
* Padding: 30px vertical

Footer sections include:

* **Footer headings** (`h5`): Gold color (`#ffd700`) for section titles
* **`.footer-links`**: Unstyled list with 10px bottom margins per item
* **Link hover states**: Gold color (`#ffd700`) on hover
* **`.contact-info`**: Contact details with icons (10px right margin)
* **`.copyright`**: Centered copyright text at 0.9rem

**Sources**: [css/cajera.css L361-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L361-L402)

## Integration with Database Tables

The cashier interface interacts with multiple database tables through backend PHP services:

| Interface Section | Database Table | Operations |
| --- | --- | --- |
| Orders Section | `pedidos` | Read pending orders, update payment status |
| Payment Modal | `pagos` | Insert payment records with method, amount, change |
| Invoice Modal | `facturas` | Insert invoice records with customer details |
| Sales Summary | `pedidos`, `pagos` | Aggregate queries for daily/weekly metrics |
| Sales Section | `pagos`, `detalles_pedidos` | Analytics queries for charts/tables |
| Notifications | Multiple tables | Real-time updates via server push or polling |

The interface assumes backend endpoints exist for:

* Fetching pending orders (status: `pendiente_pago`)
* Processing payment transactions
* Generating invoice PDFs
* Retrieving sales analytics
* Managing notification state

**Sources**: [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)

 database schema from context

## Responsive Considerations

Unlike other role interfaces, `cajera.css` does not include explicit media queries. The interface relies on Bootstrap's responsive grid system (assumed from the overall architecture) for mobile adaptation. Key considerations:

* **Order cards**: Stack vertically on mobile
* **Summary cards**: Likely use Bootstrap's `.col-*` classes for grid layout
* **Sidebar width**: Fixed 350px may need adjustment for narrow screens
* **Search box**: Max-width of 250px prevents overflow on tablets

The absence of custom breakpoints suggests this interface is primarily designed for desktop/tablet use in a point-of-sale setting where cashiers work at fixed stations.

**Sources**: [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)

## Component Hierarchy Reference

The following table maps CSS classes to their parent-child relationships:

| Parent Class | Child Classes | Purpose |
| --- | --- | --- |
| `.cajera-header` | `.cajera-info`, `.info-item` | Header layout |
| `.sales-summary` | `.summary-card`, `.summary-icon`, `.summary-info` | Metrics dashboard |
| `.orders-section` | `.section-header`, `.order-card` | Order management |
| `.order-card` | `.order-header`, `.order-items`, `.order-footer` | Order structure |
| `.order-header` | `h5`, `.order-time` | Order identification |
| `.order-items` | `.order-item`, `.item-quantity`, `.item-price` | Line items |
| `.order-footer` | `.order-total`, `.order-actions` | Total & actions |
| `.sales-section` | `.sales-chart-container`, `.sales-table-container` | Analytics display |
| `.notifications-sidebar` | `.notifications-header`, `.notifications-list`, `.notifications-footer` | Notification panel |
| `.notifications-list` | `.notification-item`, `.notification-icon`, `.notification-content` | Notification entries |
| `.footer` | `.footer-links`, `.contact-info`, `.copyright` | Footer sections |

**Sources**: [css/cajera.css L1-L402](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L402)