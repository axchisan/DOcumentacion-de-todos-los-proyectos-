# Component Library

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)

## Purpose and Scope

This page documents the reusable UI components used throughout the Hotel Benedetti system, including their CSS class definitions, visual styling, and interaction states. These components maintain visual consistency across all user interfaces (Administrator, Cliente, Recepcionista, Mucama).

For information about the overall color scheme and theming strategy, see [Color Palette and Theming](/GroveLive/hotelBenedetti/8.1-color-palette-and-theming). For information about how these components are arranged into layouts, see [Layout Patterns](/GroveLive/hotelBenedetti/8.3-layout-patterns). For implementation details of the JavaScript that manipulates these components, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic).

---

## Component Taxonomy

The component library consists of seven primary component categories, each with specific variants and interaction states:

```

```

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

---

## Button Components

### Button Variants

The system implements four distinct button variants, each with specific semantic meaning and color coding:

| Variant | Primary Color | Hover Color | Usage Context | CSS Selector Examples |
| --- | --- | --- | --- | --- |
| **Primary Action** | `#9b59b6` | `#8e44ad` | Submit forms, confirm actions, primary navigation | `form button`, `.confirm-reservation`, `.mark-as-cleaned`, `#refresh-rooms-button` |
| **Edit Action** | `#36A2EB` | N/A (opacity) | Modify existing records | `.edit-employee`, `.edit-room` |
| **Destructive Action** | `#FF6384` / `#f44336` | `#d32f2f` | Delete records, cancel operations | `.delete-employee`, `.delete-room`, `.cancel-reservation` |
| **Floating Social** | `#9b59b6` / `#25D366` | `#8e44ad` / `#20b358` | External links (Instagram, WhatsApp) | `.floating-btn.instagram`, `.floating-btn.whatsapp` |

### Primary Action Buttons

Primary buttons use the signature purple color and appear in forms, navigation, and action panels:

```

```

**Admin Interface Implementation:**

* [assets/css/StyleAdmin.css L198-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L210)  - Form submit buttons
* [assets/css/StyleAdmin.css L94-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L94-L97)  - Navigation buttons with hover

**Cliente Interface Implementation:**

* [assets/css/StyleCliente.css L279-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L279-L294)  - Booking form button with full width
* [assets/css/StyleCliente.css L214-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L214-L228)  - Room details button with rounded style

**Recepcionista/Mucama Interface Implementation:**

* [assets/css/StyleRecepcionista.css L212-L225](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L212-L225)  - Refresh rooms button
* [assets/css/StyleMucama.css L152-L166](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L152-L166)  - Room refresh with purple styling

**Sources:** [assets/css/StyleAdmin.css L198-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L210)

 [assets/css/StyleCliente.css L279-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L279-L294)

 [assets/css/StyleRecepcionista.css L212-L225](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L212-L225)

### Edit and Delete Buttons

Action buttons within tables use distinct colors to indicate their purpose:

```

```

**Implementation:**

* [assets/css/StyleAdmin.css L251-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L251-L266)  - Employee and room list action buttons
* Common pattern: Blue for edit operations, red for destructive operations

**Sources:** [assets/css/StyleAdmin.css L241-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L241-L266)

### Floating Action Buttons

The Cliente interface features fixed-position social media buttons:

```

```

**Implementation Details:**

* [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)  - Complete floating button system
* Uses `transform: scale(1.1)` for hover effect
* Circular shape achieved with `border-radius: 50%`
* Contains 30x30px logo images

**Sources:** [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

---

## Form Components

### Form Container Styling

Forms across all interfaces share a consistent container style with white background cards:

```

```

**Admin Interface (Light Theme):**

* [assets/css/StyleAdmin.css L175-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L210)  - White background forms
* Border color: `#ddd` for neutral appearance
* Simple focus state without shadow

**Dark Theme Interfaces (Cliente, Recepcionista, Mucama):**

* [assets/css/StyleCliente.css L246-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L294)  - Dark form with purple accents
* Background: `#36393f` for inputs
* Border: `#7289da` transitioning to `#9b59b6` on focus
* Focus shadow: `0 0 5px rgba(155, 89, 182, 0.5)`

**Sources:** [assets/css/StyleAdmin.css L175-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L210)

 [assets/css/StyleCliente.css L246-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L294)

 [assets/css/StyleRecepcionista.css L261-L289](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L261-L289)

### Input Element Specifications

| Property | Admin Interface | Dark Theme Interfaces |
| --- | --- | --- |
| `background-color` | Default (white) | `#36393f` |
| `color` | Default (black) | `#dcddde` |
| `border` | `1px solid #ddd` | `1px solid #7289da` |
| `border-radius` | `3px` | `4px` |
| `padding` | `0.5em` | `10px` |
| `font-size` | `16px` | `16px` |
| `:focus border-color` | N/A | `#9b59b6` |
| `:focus box-shadow` | N/A | `0 0 5px rgba(155, 89, 182, 0.5)` |

**Sources:** [assets/css/StyleAdmin.css L188-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L188-L196)

 [assets/css/StyleCliente.css L260-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L277)

---

## Table Components

### Table Structure Pattern

Tables follow a consistent structure across all admin/staff interfaces:

```

```

**Admin Interface Table Styling:**

* [assets/css/StyleAdmin.css L213-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L266)  - Light theme with gray headers
* Headers: `#f4f4f4` background
* Even rows: `#f9f9f9` background for zebra striping
* Action buttons inline with custom colors

**Dark Theme Table Styling (Recepcionista, Mucama):**

* [assets/css/StyleRecepcionista.css L165-L202](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L202)  - Guest list table
* [assets/css/StyleMucama.css L168-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L205)  - Room list table
* Headers: `#9b59b6` (purple) with white text
* Body: `#36393f` background
* Hover: `#40444b` for row highlighting

**Sources:** [assets/css/StyleAdmin.css L213-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L266)

 [assets/css/StyleRecepcionista.css L165-L202](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L202)

 [assets/css/StyleMucama.css L168-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L205)

### Table Action Button Pattern

Action buttons within table cells follow this implementation pattern:

```

```

**Button Class Mapping:**

* `.edit-employee`, `.edit-room`: Edit operations (blue)
* `.delete-employee`, `.delete-room`: Delete operations (red)
* `.checkout-button`: Guest checkout (purple)
* `.mark-as-cleaned`: Room cleaning completion (purple)

**Sources:** [assets/css/StyleAdmin.css L241-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L241-L266)

 [assets/css/StyleRecepcionista.css L189-L202](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L189-L202)

 [assets/css/StyleMucama.css L192-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L192-L205)

---

## Card Components

### Card Component Variants

Five distinct card types serve different content purposes:

```

```

**Sources:** [assets/css/StyleAdmin.css L126-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L172)

 [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)

 [assets/css/StyleCliente.css L121-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L149)

### Stat Card (Dashboard)

Used exclusively in the admin dashboard for displaying metrics:

**Visual Specification:**

* Background: `white`
* Padding: `1em`
* Border-radius: `5px`
* Box-shadow: `0 0 10px rgba(0, 0, 0, 0.1)`
* Width: `30%` (desktop), `80%` (tablet), `100%` (mobile)
* Hover effect: `transform: translateY(-5px)`

**Content Structure:**

* `h3` element: `font-size: 16px`, `margin-bottom: 10px`
* `p` element: `font-size: 24px`, `font-weight: bold` (displays metric value)

**Implementation:**

* [assets/css/StyleAdmin.css L133-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L133-L156)  - Complete stat card styling

**Sources:** [assets/css/StyleAdmin.css L126-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L156)

### Room Type Card

Displays room information in the Cliente interface:

```

```

**Implementation:**

* [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)  - Complete room type card system

**Sources:** [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)

### Notification Card

Used across interfaces for displaying system notifications:

**Admin/Light Theme:**

* Background: `#36393f`
* Text color: Default dark text

**Dark Themes (Cliente, Recepcionista, Mucama):**

* Background: `#36393f`
* Text color: `#dcddde` / `#b0b3b8`
* Layout: `display: flex`, `justify-content: space-between`, `align-items: center`
* Padding: `15px`
* Border-radius: `5px`
* Margin-bottom: `10px`

**Button Integration:**

* `.mark-as-read`: Purple button (`#9b59b6`)
* `.confirm-reservation`: Purple button
* `.cancel-reservation`: Red button (`#f44336`)

**Implementation:**

* [assets/css/StyleCliente.css L121-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L149)  - Cliente notification styling
* [assets/css/StyleRecepcionista.css L118-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L118-L156)  - Recepcionista with action buttons
* [assets/css/StyleMucama.css L118-L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L118-L143)  - Mucama notification styling

**Sources:** [assets/css/StyleCliente.css L121-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L149)

 [assets/css/StyleRecepcionista.css L118-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L118-L156)

 [assets/css/StyleMucama.css L118-L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L118-L143)

---

## Modal and Dialog Components

### Dialog Element Styling

The Recepcionista interface uses native `<dialog>` elements for check-in/check-out forms:

```

```

**Dialog Styling:**

* [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)  - Complete dialog system
* Background: `#2c2f33` (dark theme)
* Border: `none` (removes default browser border)
* Box-shadow: `0 2px 10px rgba(0, 0, 0, 0.3)`

**Form Layout:**

* Flex column layout with `gap: 15px`
* Labels: `color: #dcddde`, `font-size: 16px`
* Inputs: Same styling as standard forms (see Form Components section)

**Button Pair Pattern:**

* Submit button: Purple (`#9b59b6`) for confirming actions
* Cancel button: Red (`#f44336`) for dismissing dialog
* Both use `border-radius: 20px` and `padding: 10px 15px`

**Sources:** [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

---

## Navigation Components

### Header Navigation Pattern

All interfaces include header navigation with consistent styling:

```

```

**Admin Navigation (Light Container):**

* [assets/css/StyleAdmin.css L49-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L49-L97)  - Header and nav styling
* Background: `#444` for nav bar
* Buttons with transparent background
* Hover: `#9b59b6` background

**Dark Theme Navigation (Cliente, Recepcionista, Mucama):**

* [assets/css/StyleCliente.css L39-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L39-L84)  - Cliente header styling
* [assets/css/StyleRecepcionista.css L39-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L39-L83)  - Recepcionista header
* [assets/css/StyleMucama.css L39-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L39-L83)  - Mucama header
* Links (not buttons) with purple text color
* Hover: Darker purple text with dark background

**Responsive Behavior:**

* Desktop: Horizontal flex layout
* Mobile (≤768px): Vertical stack with `flex-direction: column`

**Sources:** [assets/css/StyleAdmin.css L49-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L49-L97)

 [assets/css/StyleCliente.css L39-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L39-L84)

 [assets/css/StyleRecepcionista.css L39-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L39-L83)

---

## Interaction States

### Global State Transition Pattern

All interactive components implement consistent state transitions:

```

```

**Sources:** Multiple CSS files implementing consistent transitions

### Hover State Specifications

| Component Type | Hover Effect | Transition Duration | CSS Properties Changed |
| --- | --- | --- | --- |
| **Primary Buttons** | Background darkens | `0.3s ease` | `background-color: #8e44ad` |
| **Navigation Links** | Background + color | `0.3s ease` | `background-color: #36393f`, `color: #8e44ad` |
| **Cards (Stat/Room)** | Lift effect | `0.3s ease` | `transform: translateY(-5px)` |
| **Floating Buttons** | Scale + shadow | `0.3s ease` | `transform: scale(1.1)`, `box-shadow` |
| **Table Rows** | Background change | N/A | `background-color: #40444b` |
| **Action Buttons** | Opacity | `0.3s ease` | `opacity: 0.9` |

**Sources:** [assets/css/StyleAdmin.css L144-L145](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L144-L145)

 [assets/css/StyleCliente.css L190-L192](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L190-L192)

 [assets/css/StyleCliente.css L362-L365](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L362-L365)

### Focus State Specifications

Focus states are primarily implemented for form inputs:

```

```

**Implementation:**

* [assets/css/StyleCliente.css L272-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L272-L277)  - Booking form focus states
* [assets/css/StyleRecepcionista.css L283-L288](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L283-L288)  - Dialog input focus
* Border transitions to purple (`#9b59b6`)
* Soft purple glow via box-shadow
* Native outline removed for custom styling

**Sources:** [assets/css/StyleCliente.css L272-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L272-L277)

 [assets/css/StyleRecepcionista.css L283-L288](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L283-L288)

---

## Responsive Component Behavior

### Breakpoint-Based Adjustments

Components adapt at two primary breakpoints: 768px (tablet) and 480px (mobile).

```

```

**Component-Specific Responsive Patterns:**

| Component | Desktop | Tablet (≤768px) | Mobile (≤480px) |
| --- | --- | --- | --- |
| **Stat Card Width** | `30%` | `80%` | `100%` |
| **Logo Size** | `150px` | `150px` | `120px` |
| **Header h1 Font** | `24px-28px` | `20px-24px` | `18px-20px` |
| **Table Font** | `16px` | `14px` | `12px` |
| **Floating Button** | `50x50px` | `45x45px` | `40x40px` |
| **Form Input Font** | `16px` | `14px` | `14px` |
| **Navigation** | Horizontal | Vertical | Vertical |

**Implementation References:**

* [assets/css/StyleAdmin.css L280-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L334)  - Admin responsive breakpoints
* [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)  - Cliente responsive breakpoints
* [assets/css/StyleRecepcionista.css L327-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L407)  - Recepcionista responsive breakpoints
* [assets/css/StyleMucama.css L217-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L217-L281)  - Mucama responsive breakpoints

**Sources:** [assets/css/StyleAdmin.css L280-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L380)

 [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

 [assets/css/StyleRecepcionista.css L327-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L407)

 [assets/css/StyleMucama.css L217-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L217-L281)

---

## Component Grid Layouts

Several components use CSS Grid for responsive arrangement:

### Grid-Based Component Containers

```

```

**Grid Pattern:**

* Uses `repeat(auto-fit, minmax(300px, 1fr))` for automatic responsive columns
* Minimum column width: `300px`
* Gap between items: `20px`
* Automatically adjusts column count based on viewport width

**Dashboard Stats Exception:**

* Uses Flexbox with `flex-wrap: wrap` instead of Grid
* Items have explicit width percentages (`30%`, `80%`, `100%`)
* Space distribution via `justify-content: space-around`

**Implementation:**

* [assets/css/StyleAdmin.css L126-L131](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L131)  - Dashboard stats flex container
* [assets/css/StyleCliente.css L152-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L156)  - Room grid
* [assets/css/StyleCliente.css L175-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L175-L179)  - Room types grid
* [assets/css/StyleCliente.css L231-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L231-L235)  - Testimonial grid
* [assets/css/StyleCliente.css L320-L324](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L320-L324)  - FAQ grid

**Sources:** [assets/css/StyleAdmin.css L126-L131](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L131)

 [assets/css/StyleCliente.css L152-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L179)

 [assets/css/StyleCliente.css L231-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L231-L235)

 [assets/css/StyleCliente.css L320-L324](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L320-L324)

---

## Component Usage Map

This diagram maps components to their usage contexts across user interfaces:

```

```

**Sources:** All CSS files listed above