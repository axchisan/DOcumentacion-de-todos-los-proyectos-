# Role-Based Interface System

> **Relevant source files**
> * [css/cajera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css)
> * [css/cliente.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css)
> * [css/cocina.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css)
> * [css/mesera.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the role-based interface architecture that provides distinct user experiences for each role in the American Food restaurant management system. The system implements role-specific interfaces through separate CSS files and tailored component layouts, ensuring each user type (admin, cashier, cook, server, customer) accesses only the functionality relevant to their job function.

For implementation details of individual role interfaces, see [Customer Interface](/axchisan/AmericanFood/5.3.1-customer-interface-(cliente.css)), [Cashier Interface](/axchisan/AmericanFood/5.3.2-cashier-interface-(cajera.css)), [Kitchen Interface](/axchisan/AmericanFood/5.3.3-kitchen-interface-(cocina.css)), and [Server Interface](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css)). For global design patterns shared across roles, see [Design System and Global Styles](/axchisan/AmericanFood/5.1-design-system-and-global-styles). For authentication and role selection, see [Authentication Interface](/axchisan/AmericanFood/5.2-authentication-interface).

## Role Definitions

The system defines five distinct user roles stored in the `usuarios` table's `rol` field as an enumerated type:

| Role | Database Value | Purpose | Interface File |
| --- | --- | --- | --- |
| Admin | `admin` | Full system access and configuration | `style.css` (base) |
| Cashier | `cajera` | Payment processing, invoices, sales reports | `css/cajera.css` |
| Cook | `cocinero` | Order preparation, kitchen workflow | `css/cocina.css` |
| Server | `mesera` | Table management, order taking | `css/mesera.css` |
| Customer | `cliente` | Menu browsing, online ordering, reservations | `css/cliente.css` |

**Sources:** [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

## Architecture Overview

The role-based interface system operates through a CSS-driven architecture where each role receives a dedicated stylesheet that defines its unique UI components and layout. After authentication, users are routed to their role-specific interface based on the `rol` field in the `usuarios` table.

```

```

**Diagram: Role-Based Interface Routing Flow**

The system implements a layered CSS architecture where `style.css` provides foundational styles (colors, typography, common components) while role-specific CSS files extend and override these styles with specialized components.

**Sources:** [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)

 [css/cajera.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L10)

 [css/cliente.css L1-L23](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L23)

 [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

 [css/mesera.css L1-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L1-L15)

## Role-to-Interface Mapping

Each role maps to specific database tables and operations, reflected in its interface design:

```

```

**Diagram: Role-to-Interface-to-Data Mapping Architecture**

**Sources:** [css/cajera.css L39-L70](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L39-L70)

 [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

 [css/mesera.css L18-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L18-L52)

 [css/cliente.css L4-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L4-L34)

 [database/american_food L228-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L228-L238)

 [database/american_food L190-L199](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L190-L199)

 [database/american_food L256-L266](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L256-L266)

## CSS Architecture for Role Separation

### Header Components

Each role interface defines a distinct header component that establishes visual identity and displays role-specific information:

| Component | File | Purpose | Key Features |
| --- | --- | --- | --- |
| `.cajera-header` | `cajera.css` | Cashier identification | Displays cashier info, date/time |
| `.mesera-header` | `mesera.css` | Server identification | Shows server info, active tables count |
| `.welcome-banner` | `cliente.css` | Customer greeting | Personalized welcome, navigation hints |

```

```

**Diagram: Role-Specific Header Component Architecture**

**Sources:** [css/cajera.css L38-L70](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L38-L70)

 [css/mesera.css L17-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L17-L52)

 [css/cliente.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L3-L18)

### State Visualization

Different roles require distinct methods of visualizing system state:

**Kitchen Interface** (cocina.css) - Order state through border colors:

* `.order-card.new` - Blue left border (5px solid #007bff) for new orders
* `.order-card.preparing` - Yellow left border (5px solid #ffc107) for orders in progress
* `.order-card.ready` - Green left border (5px solid #28a745) for completed orders

**Server Interface** (mesera.css) - Table status through color-coded indicators:

* `.table-item.available` - Green left border for available tables
* `.table-item.occupied` - Red left border for occupied tables
* `.table-item.reserved` - Yellow left border for reserved tables

**Sources:** [css/cocina.css L1-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L10)

 [css/mesera.css L180-L190](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L180-L190)

## Common Interface Patterns

Despite role-specific customization, all interfaces share common architectural patterns:

### Card-Based Layout

All role interfaces utilize a card component pattern with consistent structure:

```

```

**Diagram: Card Component Inheritance Across Role Interfaces**

**Sources:** [css/cajera.css L127-L133](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L127-L133)

 [css/cocina.css L1-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L6)

 [css/mesera.css L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L163-L173)

 [css/cliente.css L37-L52](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L37-L52)

### Modal Overlays

Complex operations across roles use modal dialogs with consistent structure:

| Modal Type | Roles | CSS Classes | Purpose |
| --- | --- | --- | --- |
| Payment Modal | Cashier | `.payment-details-section`, `#changeAmount` | Process payments, calculate change |
| Invoice Modal | Cashier | `#newInvoiceForm` | Generate customer invoices |
| Table Details Modal | Server | `#tableDetailsModal`, `.table-details` | View/manage table information |
| New Order Modal | Server | `#newOrderModal`, `.order-items-form` | Create orders for tables |

**Sources:** [css/cajera.css L221-L233](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L221-L233)

 [css/mesera.css L401-L469](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L401-L469)

### Notification Systems

Cashier and server interfaces implement slide-in notification sidebars:

**Cashier Notifications:**

* `.notifications-sidebar` - Fixed position, right: -350px (hidden), width: 350px
* `.notifications-sidebar.active` - right: 0 (visible)
* `.notifications-overlay` - Full-screen backdrop with rgba(0,0,0,0.5)

**Server Notifications:**

* `.notifications-sidebar` - Fixed position, right: -400px (hidden), width: 400px
* Transition: right 0.3s ease

**Sources:** [css/cajera.css L264-L293](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L264-L293)

 [css/mesera.css L558-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L558-L624)

## Role-Specific Optimizations

### Information Density

Each interface optimizes information density for its workflow:

**Kitchen Interface** - Minimal, high-contrast design:

* Only essential order information displayed
* Large state indicators (border colors)
* Streamlined for quick glance-and-update workflow
* Minimal padding and compact layout

**Customer Interface** - Rich, engaging presentation:

* Large product images (180px height)
* Detailed descriptions and pricing
* Shopping cart sidebar with full item details
* Order history with complete transaction records

**Cashier Interface** - Data-dense financial display:

* Sales summary cards with key metrics
* Order lists with payment details
* Invoice generation forms
* Sales charts and tables

**Sources:** [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/cliente.css L109-L159](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L109-L159)

 [css/cajera.css L72-L211](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L72-L211)

### Responsive Breakpoints

Role interfaces implement tailored responsive behavior:

| Role | Primary Breakpoint | Adjustments |
| --- | --- | --- |
| Customer | 992px, 768px, 576px | Cart switches to offcanvas, menu grid reduces columns |
| Server | 768px, 576px | Table grid reduces to 150px columns, notification sidebar full-width |
| Cashier | Inherits global | Order cards maintain structure, modals adapt |
| Kitchen | Minimal responsive | Order cards maintain fixed structure for consistency |

**Sources:** [css/cliente.css L325-L371](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L325-L371)

 [css/mesera.css L627-L705](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L627-L705)

### Color Palette Usage

Roles use the shared color palette differently:

```

```

**Diagram: Color Palette Usage Across Role Interfaces**

**Sources:** [css/mesera.css L4-L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L4-L15)

 [css/cocina.css L8-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L8-L10)

 [css/cajera.css L253-L262](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L253-L262)

## Interface Component Hierarchy

The following diagram maps major UI sections to their implementing CSS classes across role interfaces:

```

```

**Diagram: Major UI Component Hierarchy by Role**

**Sources:** [css/cajera.css L39-L359](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L39-L359)

 [css/cocina.css L1-L29](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cocina.css#L1-L29)

 [css/mesera.css L18-L624](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/mesera.css#L18-L624)

 [css/cliente.css L4-L306](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L4-L306)

## Security and Access Control

The role-based interface system provides UI-level separation but relies on backend validation for true security. The `usuarios.rol` field determines interface routing, but server-side code must validate that users can only access data and perform operations appropriate to their role.

**Role Field Definition:**

```

```

The enumerated type enforces valid role values at the database level, preventing invalid role assignments. Each user record includes a `estado` field (`enum('activo','inactivo')`) that can disable accounts without deleting them.

**Sources:** [database/american_food L287-L297](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L287-L297)