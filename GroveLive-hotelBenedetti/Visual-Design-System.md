# Visual Design System

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)

## Purpose and Scope

This document provides an overview of the Hotel Benedetti system's visual design identity, documenting the cohesive dark theme, color palette, typography, and UI component patterns that create a consistent user experience across all interfaces. This page focuses on the high-level design principles and their implementation across the codebase.

For detailed information about specific aspects of the visual system:

* Color usage and theming specifications: see [Color Palette and Theming](/GroveLive/hotelBenedetti/8.1-color-palette-and-theming)
* Reusable UI component patterns and states: see [Component Library](/GroveLive/hotelBenedetti/8.2-component-library)
* Layout systems and grid structures: see [Layout Patterns](/GroveLive/hotelBenedetti/8.3-layout-patterns)
* CSS implementation details and file structure: see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system)

---

## Visual Design Philosophy

The Hotel Benedetti system employs a **dark theme with purple accents** as its core visual identity. This design approach serves multiple purposes:

1. **Brand Consistency**: The purple color (#9b59b6) establishes visual continuity across all four role-specific interfaces
2. **Visual Hierarchy**: Dark backgrounds (#2c2f33, #36393f) with light text (#dcddde) create clear content separation
3. **Attention Direction**: Purple interactive elements guide users to actionable items
4. **Professional Aesthetic**: The dark theme conveys sophistication appropriate for hospitality management

Sources: [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

---

## Color System Architecture

The color system follows a hierarchical structure with three primary categories:

### Background Colors

| Color Code | Usage | CSS Reference |
| --- | --- | --- |
| `rgba(35, 39, 42, 0.8)` | Overlay layer darkening background images | `body::before` |
| `#23272a` | Darkest background (admin container) | `.container` background |
| `#2c2f33` | Primary dark surface (headers, sections) | `header`, section backgrounds |
| `#36393f` | Secondary dark surface (cards, notifications) | `.notification`, `.stat-card` |
| `#40444b` | Hover state for table rows | `tr:hover` |

### Accent Colors

| Color Code | Purpose | Application |
| --- | --- | --- |
| `#9b59b6` | Primary accent (purple) | Buttons, links, headers, interactive elements |
| `#8e44ad` | Hover accent (darker purple) | Button/link hover states |
| `#36A2EB` | Secondary accent (blue) | Edit buttons |
| `#FF6384` | Destructive accent (red) | Delete buttons, cancel actions |
| `#FFCE56` | Warning accent (yellow) | Maid role identifier |

### Text Colors

| Color Code | Usage |
| --- | --- |
| `#dcddde` | Primary text color |
| `#b0b3b8` | Secondary/muted text |
| `white` | High-contrast text on colored backgrounds |

Sources: [assets/css/StyleAdmin.css L29-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L29-L37)

 [assets/css/StyleCliente.css L16-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L16-L37)

 [assets/css/StyleRecepcionista.css L29-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L29-L37)

---

## CSS File Structure and Role Mapping

**Diagram: CSS Files to User Role Mapping**

```

```

Sources: [assets/css/StyleAdmin.css L1-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L20)

 [assets/css/StyleCliente.css L1-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L20)

 [assets/css/StyleMucama.css L1-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L20)

 [assets/css/StyleRecepcionista.css L1-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L20)

---

## Typography System

The system uses a simple, consistent typography hierarchy:

| Element | Font Family | Size | Weight | Usage |
| --- | --- | --- | --- | --- |
| Body text | `Arial, sans-serif` (Admin)`sans-serif` (Others) | 16px | 400 | Default text |
| `header h1` | Inherited | 24-28px | 400 | Page titles |
| `section h2` | Inherited | 22-26px | 400 | Section titles |
| `section h3` | Inherited | 16-20px | 400 | Subsection titles |
| Navigation links | Inherited | 16px | 500 | Header navigation |
| Buttons | Inherited | 16px | 500 | Interactive elements |
| `.stat-card p` | Inherited | 24px | 700 | Dashboard statistics |

**Font Weight Convention**: The system uses `font-weight: 500` consistently for all interactive elements (buttons, links) to provide subtle emphasis.

Sources: [assets/css/StyleAdmin.css L10-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L10-L91)

 [assets/css/StyleCliente.css L10-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L10-L84)

---

## Component Design Patterns

**Diagram: Core UI Component Hierarchy**

```

```

Sources: [assets/css/StyleAdmin.css L39-L267](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L39-L267)

 [assets/css/StyleCliente.css L152-L295](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L295)

---

## Responsive Design Strategy

The visual system implements a **mobile-first responsive strategy** with two primary breakpoints:

### Breakpoint Structure

```

```

### Responsive Adjustments by Breakpoint

| Element | Desktop | Tablet (≤768px) | Mobile (≤480px) |
| --- | --- | --- | --- |
| `nav` | `flex-direction: row` | `flex-direction: column` | `flex-direction: column` |
| `header h1` | 24-28px | 24px | 18-20px |
| `.stat-card` | `width: 30%` | `width: 80%` | `width: 100%` |
| Table font size | 16px | 14px | 12px |
| Form inputs | padding: 0.5em | padding: 0.4em | padding: 0.4em |
| `.floating-btn` | 50px × 50px | 45px × 45px | 40px × 40px |

Sources: [assets/css/StyleAdmin.css L280-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L380)

 [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

 [assets/css/StyleMucama.css L217-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L217-L281)

 [assets/css/StyleRecepcionista.css L327-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L407)

---

## Background Image System

All interfaces utilize a layered background approach:

**Diagram: Background Layer Architecture**

```

```

**Implementation Pattern**:

1. `body` sets `background-image: url('../img/fondo.jpg')` with `background-attachment: fixed` for parallax effect
2. `body::before` creates a pseudo-element overlay with 80% opacity dark color
3. All content elements have `position: relative` and `z-index: 2` to appear above overlay

Sources: [assets/css/StyleAdmin.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L9-L37)

 [assets/css/StyleCliente.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L9-L37)

 [assets/css/StyleMucama.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L9-L37)

 [assets/css/StyleRecepcionista.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L9-L37)

---

## Button Design System

**Diagram: Button State Machine**

```

```

### Button Variants by Context

| Button Class | Background Color | Border Radius | Usage |
| --- | --- | --- | --- |
| `form button` | `#9b59b6` | `3px` | Admin forms |
| `.room-details-btn` | `#9b59b6` | `20px` | Client interface actions |
| `.mark-as-read` | `#9b59b6` | `20px` | Notification actions |
| `.confirm-reservation` | `#9b59b6` | `20px` | Receptionist confirmations |
| `.cancel-reservation` | `#f44336` (red) | `20px` | Destructive actions |
| `.edit-employee` | `#36A2EB` (blue) | `3px` | Edit actions |
| `.delete-employee` | `#FF6384` (red) | `3px` | Delete actions |
| `.floating-btn.instagram` | `#9b59b6` | `50%` | Social media link |
| `.floating-btn.whatsapp` | `#25D366` (green) | `50%` | WhatsApp link |

Sources: [assets/css/StyleAdmin.css L198-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L266)

 [assets/css/StyleCliente.css L136-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L136-L294)

 [assets/css/StyleRecepcionista.css L130-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L130-L315)

---

## Form Design Patterns

All forms across the system follow a consistent visual structure:

**Common Form Styling**:

* Container background: `#2c2f33` (dark surface)
* Input background: `#36393f` (slightly lighter)
* Border color: `#7289da` (blue-gray) or `#ddd` (admin)
* Focus border: `#9b59b6` (purple accent)
* Focus shadow: `0 0 5px rgba(155, 89, 182, 0.5)`
* Submit button: Purple accent with hover darkening

**Admin Form Specifics** [assets/css/StyleAdmin.css L175-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L210)

:

* White background for form container (`background-color: white`)
* Light gray inputs (`border: 1px solid #ddd`)
* Designed to contrast with dark outer container

**Client/Staff Form Specifics** [assets/css/StyleCliente.css L246-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L294)

 [assets/css/StyleRecepcionista.css L261-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L261-L315)

:

* Dark theme forms matching overall interface
* Blue-gray border focus (`border-color: #7289da`)
* Purple focus effects

Sources: [assets/css/StyleAdmin.css L175-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L210)

 [assets/css/StyleCliente.css L246-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L294)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

 [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

---

## Table Design System

**Diagram: Table Component Structure**

```

```

### Table Styling by Interface

| Interface | Header Background | Row Hover | Even Row | Border |
| --- | --- | --- | --- | --- |
| Admin | `#f4f4f4` (light gray) | N/A | `#f9f9f9` | `#ddd` |
| Receptionist | `#9b59b6` (purple) | `#40444b` | No change | `#4a4d52` |
| Maid | `#9b59b6` (purple) | `#40444b` | No change | `#4a4d52` |

**Action Buttons in Tables**: Edit/delete buttons use color-coding:

* Edit: `#36A2EB` (blue)
* Delete: `#FF6384` (red)

Sources: [assets/css/StyleAdmin.css L212-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L212-L266)

 [assets/css/StyleRecepcionista.css L165-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L249)

 [assets/css/StyleMucama.css L168-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L205)

---

## Card Component System

The system uses card components for displaying grouped information:

### Stat Cards (Dashboard)

**Visual Structure** [assets/css/StyleAdmin.css L126-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L156)

:

* Background: `white`
* Box shadow: `0 0 10px rgba(0, 0, 0, 0.1)`
* Border radius: `5px`
* Width: `30%` (desktop), flexible on mobile
* Hover effect: `transform: translateY(-5px)`

### Room Type Cards (Client Interface)

**Visual Structure** [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)

:

* Background: `#36393f` (dark surface)
* Image height: `200px` with `object-fit: cover`
* Border radius: `8px`
* Shadow: `0 2px 5px rgba(0, 0, 0, 0.3)`
* Hover transform: `translateY(-5px)`

### Notification Cards

**Visual Structure** [assets/css/StyleCliente.css L121-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L149)

 [assets/css/StyleRecepcionista.css L118-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L118-L156)

:

* Background: `#36393f`
* Layout: `display: flex; justify-content: space-between`
* Contains message text and action button
* Consistent across Cliente, Recepcionista, and Mucama interfaces

Sources: [assets/css/StyleAdmin.css L126-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L172)

 [assets/css/StyleCliente.css L121-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L228)

 [assets/css/StyleRecepcionista.css L112-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L112-L156)

---

## Navigation Design

All interfaces share a consistent header navigation pattern:

**Header Structure**:

```go
header
├── .logo (centered logo image, max-width: 150px)
├── h1 (hotel title)
└── nav (horizontal links/buttons)
```

**Navigation Styling**:

* Background: `#444` (Admin) or `#2c2f33` (Others)
* Link color: `white` (Admin) or `#9b59b6` (Others)
* Hover effect: `background-color: #9b59b6` (Admin) or `#36393f` (Others)
* Display: `flex` with `justify-content: center`
* Gap: `15px` between items

**Responsive Behavior**:

* Desktop: Horizontal flex layout
* Tablet/Mobile: `flex-direction: column` with reduced gap

Sources: [assets/css/StyleAdmin.css L73-L98](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L73-L98)

 [assets/css/StyleCliente.css L65-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L65-L84)

 [assets/css/StyleRecepcionista.css L65-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L65-L83)

---

## Special UI Elements

### Floating Action Buttons (Client Interface)

**Implementation** [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

:

* Position: `fixed` at `bottom: 20px; right: 20px`
* Shape: Circular (`border-radius: 50%`)
* Size: `50px × 50px` (responsive down to 40px)
* Z-index: `1000` (above all content)
* Colors: Purple for Instagram, green (`#25D366`) for WhatsApp
* Hover effect: `transform: scale(1.1)`

### Dashboard Charts

**Canvas Styling** [assets/css/StyleAdmin.css L158-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L158-L172)

:

* Max-width: `400px` (flexible on mobile)
* Background: `white`
* Padding: `10px`
* Border radius: `5px`
* Box shadow for depth

### Dialog Elements

**Dialog Styling** [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

:

* Background: `#2c2f33` (dark theme)
* Border: `none` with `border-radius: 8px`
* Form layout: `flex-direction: column` with `gap: 15px`
* Input styling matches standard form inputs

Sources: [assets/css/StyleAdmin.css L158-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L158-L172)

 [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

 [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

---

## Visual Consistency Mechanisms

The visual design system maintains consistency through:

1. **Shared Color Variables**: All CSS files use identical hex codes for purple (`#9b59b6`), backgrounds (`#2c2f33`, `#36393f`), and text (`#dcddde`)
2. **Common CSS Reset** [assets/css/StyleAdmin.css L1-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L6) : All files start with identical universal reset: ``` ```
3. **Identical Background Pattern**: All interfaces use the same `body::before` overlay technique
4. **Standardized Component Classes**: `.notification`, `button`, `form`, `table` follow same structure with role-specific color variations
5. **Consistent Hover Effects**: Interactive elements universally darken on hover (purple `#9b59b6` → `#8e44ad`)
6. **Uniform Responsive Breakpoints**: All files use `@media (max-width: 768px)` and `@media (max-width: 480px)`

Sources: [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

---

## Integration with JavaScript

The visual design system integrates with JavaScript functionality through:

1. **Dynamic Class Manipulation**: JS adds/removes `.hidden` class to show/hide sections
2. **SweetAlert2 Integration**: Custom alerts match the dark theme and purple accents
3. **Chart.js Styling**: Dashboard charts inherit color scheme from canvas background
4. **DOM Updates**: JavaScript-generated content inherits parent CSS styling

For details on JavaScript implementation, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic).

---

## Related Documentation

* **[Color Palette and Theming](/GroveLive/hotelBenedetti/8.1-color-palette-and-theming)**: Detailed color specifications and usage guidelines
* **[Component Library](/GroveLive/hotelBenedetti/8.2-component-library)**: Complete catalog of reusable UI components and their states
* **[Layout Patterns](/GroveLive/hotelBenedetti/8.3-layout-patterns)**: Grid systems, flexbox usage, and positioning strategies
* **[CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system)**: Implementation details and file organization
* **[Responsive Design Implementation](/GroveLive/hotelBenedetti/5.3-responsive-design-implementation)**: Mobile-first strategy and breakpoint behavior