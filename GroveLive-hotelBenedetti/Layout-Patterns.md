# Layout Patterns

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)

This document explains the CSS layout systems used throughout the Hotel Benedetti application to create responsive, visually cohesive interfaces. It covers the z-index layering strategy for background overlays, Flexbox patterns for navigation and forms, CSS Grid systems for responsive galleries, container centering techniques, and responsive breakpoint adaptations.

For color schemes and theming, see [Color Palette and Theming](/GroveLive/hotelBenedetti/8.1-color-palette-and-theming). For UI component styling details, see [Component Library](/GroveLive/hotelBenedetti/8.2-component-library).

---

## Background Overlay and Z-Index Layering

All user interfaces implement a consistent three-layer stacking system to achieve visual depth with background images overlaid by a darkened semi-transparent layer, upon which content is rendered.

### Layer Structure

```

```

**Sources:** [assets/css/StyleAdmin.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L8-L37)

 [assets/css/StyleCliente.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L37)

 [assets/css/StyleMucama.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L8-L37)

 [assets/css/StyleRecepcionista.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L8-L37)

### Implementation Pattern

| Layer | Selector | Z-Index | Purpose |
| --- | --- | --- | --- |
| Background Image | `body` | 0 (default) | Provides visual texture via `background-image`, set to `cover` with `fixed` attachment |
| Dark Overlay | `body::before` | 1 | Creates semi-transparent dark layer (80% opacity) using `rgba(35, 39, 42, 0.8)` |
| Content Layer | `body > *` | 2 | Ensures all direct children (header, main, footer) render above overlay |
| Floating UI | `.floating-buttons` | 1000 | Social media buttons remain above all other content (Cliente interface only) |

**Sources:** [assets/css/StyleAdmin.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L31)

 [assets/css/StyleCliente.css L22-L348](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L22-L348)

The pseudo-element overlay technique allows background images to be darkened without affecting content readability:

```

```

**Sources:** [assets/css/StyleAdmin.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L31)

---

## Flexbox Layout Patterns

Flexbox is the primary layout system for navigation, forms, dashboard statistics, and button groups across all interfaces.

### Navigation Bar Layout

```

```

**Desktop Navigation** (`nav` element):

* **Display**: `flex`
* **Direction**: row (default)
* **Justify**: `space-around` (Admin) or `center` (other interfaces)
* **Gap**: `15px`

**Sources:** [assets/css/StyleAdmin.css L74-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L74-L80)

 [assets/css/StyleCliente.css L65-L70](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L65-L70)

 [assets/css/StyleMucama.css L65-L69](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L65-L69)

 [assets/css/StyleRecepcionista.css L65-L69](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L65-L69)

**Mobile Navigation** (at `max-width: 768px`):

* **Direction**: `column`
* **Gap**: `10px`

**Sources:** [assets/css/StyleAdmin.css L289-L297](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L289-L297)

 [assets/css/StyleCliente.css L404-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L404-L407)

 [assets/css/StyleMucama.css L222-L226](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L222-L226)

 [assets/css/StyleRecepcionista.css L332-L336](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L332-L336)

### Dashboard Statistics Layout

The Admin interface uses a flexible card layout for displaying statistics:

```

```

**Desktop Configuration**:

* `display: flex`
* `flex-wrap: wrap`
* `justify-content: space-around`
* Each `.stat-card` has `width: 30%`

**Sources:** [assets/css/StyleAdmin.css L126-L142](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L142)

**Mobile Configuration** (at `max-width: 768px`):

* `flex-direction: column`
* `align-items: center`
* Each `.stat-card` has `width: 80%`

**Sources:** [assets/css/StyleAdmin.css L303-L310](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L310)

### Floating Button Stack

The Cliente interface implements a vertical button stack for social media links:

```

```

This creates a column of circular buttons (`.floating-btn`) fixed to the viewport's bottom-right corner.

**Sources:** [assets/css/StyleCliente.css L340-L348](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L348)

---

## CSS Grid Layout Patterns

CSS Grid provides responsive, adaptive layouts for content galleries including room cards, testimonials, and FAQ items.

### Responsive Grid System

```

```

**Sources:** [assets/css/StyleCliente.css L152-L324](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L324)

### Grid Implementation Patterns

| Grid Container | Selector | Min Column Width | Use Case |
| --- | --- | --- | --- |
| Room Gallery | `.room-grid` | 300px | Available rooms display |
| Room Types | `.room-types-grid` | 300px | Room type cards with images |
| Testimonials | `.testimonial-grid` | 300px | Customer testimonial cards |
| FAQ Items | `.faq-grid` | 300px | Frequently asked questions |

All grids use the same responsive pattern:

```

```

The `auto-fit` keyword automatically adjusts the number of columns based on available space, while `minmax(300px, 1fr)` ensures columns never shrink below 300px and grow equally to fill available space.

**Sources:** [assets/css/StyleCliente.css L152-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L156)

### Dashboard Chart Layout

The Admin dashboard uses a flexible grid for Chart.js visualizations:

```

```

This creates a flexible layout where charts (`canvas` elements) wrap to new rows as needed, each constrained to 400px maximum width.

**Sources:** [assets/css/StyleAdmin.css L158-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L158-L172)

---

## Container and Centering Patterns

### Main Container Pattern

All interfaces use a consistent container pattern for centering and constraining content width:

```

```

**Sources:** [assets/css/StyleAdmin.css L39-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L39-L47)

 [assets/css/StyleCliente.css L87-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L87-L91)

 [assets/css/StyleMucama.css L86-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L86-L90)

 [assets/css/StyleRecepcionista.css L86-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L86-L90)

### Container Specifications

| Interface | Container Selector | Max Width | Horizontal Centering |
| --- | --- | --- | --- |
| Admin | `.container` | 1200px | `margin: 0 auto` |
| Cliente | `section` | 1200px | `margin: 0 auto` |
| Mucama | `main` | 1200px | `margin: 0 auto` |
| Recepcionista | `main` | 1200px | `margin: 0 auto` |

All use the standard CSS horizontal centering technique: `max-width` combined with `margin: 0 auto`.

**Sources:** [assets/css/StyleAdmin.css L39-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L39-L47)

 [assets/css/StyleCliente.css L87-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L87-L91)

### Text Centering

Section headers consistently use `text-align: center` across all interfaces:

```

```

**Sources:** [assets/css/StyleAdmin.css L114-L118](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L114-L118)

 [assets/css/StyleCliente.css L94-L99](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L94-L99)

### Form Centering

Booking and dialog forms use centered containers with maximum widths:

```

```

**Sources:** [assets/css/StyleCliente.css L246-L252](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L252)

---

## Responsive Breakpoint System

The application implements a two-tier responsive strategy with breakpoints at 768px and 480px.

### Responsive Breakpoint Logic

```

```

**Sources:** [assets/css/StyleAdmin.css L280-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L380)

 [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

### Breakpoint Adaptations

#### @media (max-width: 768px) - Tablet/Mobile Transition

| Element | Desktop | Tablet |
| --- | --- | --- |
| `nav` | `flex-direction: row` | `flex-direction: column` |
| `.stat-card` | `width: 30%` | `width: 80%` |
| `canvas` | `max-width: 400px` | `max-width: 300px` |
| Table font | 16px | 14px |
| Button padding | 0.5em 1em | 0.5em (uniform) |
| Form input font | 16px | 14px |

**Sources:** [assets/css/StyleAdmin.css L280-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L334)

#### @media (max-width: 480px) - Mobile Optimization

| Element | Tablet | Mobile |
| --- | --- | --- |
| Logo | `max-width: 150px` | `max-width: 120px` |
| `h1` | 24px | 18-20px |
| `h2` | 26px | 20-22px |
| `.stat-card` | `width: 80%` | `width: 100%` |
| `.stat-card p` | 24px | 20px |
| Table font | 14px | 12px |
| Table padding | 0.8em | 0.4-0.6em |

**Sources:** [assets/css/StyleAdmin.css L336-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L336-L380)

 [assets/css/StyleCliente.css L435-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L435-L486)

### Navigation Responsive Pattern

```

```

**Implementation**:

```

```

**Sources:** [assets/css/StyleAdmin.css L74-L292](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L74-L292)

---

## Form Layout Patterns

Forms across all interfaces use a consistent vertical block layout with full-width inputs.

### Standard Form Structure

```

```

**Sources:** [assets/css/StyleAdmin.css L175-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L210)

 [assets/css/StyleCliente.css L246-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L246-L294)

### Form Input Specifications

```

```

**Sources:** [assets/css/StyleAdmin.css L188-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L188-L196)

### Dialog Form Layout

Receptionist interface dialogs use flexbox for form layout:

```

```

This creates equal spacing between all form elements without manual margin management.

**Sources:** [assets/css/StyleRecepcionista.css L261-L265](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L261-L265)

---

## Table Layout Patterns

Tables in Admin, Mucama, and Recepcionista interfaces use full-width layouts with consistent styling.

### Table Structure

```

```

**Sources:** [assets/css/StyleAdmin.css L213-L240](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L240)

 [assets/css/StyleMucama.css L168-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L190)

 [assets/css/StyleRecepcionista.css L165-L250](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L250)

### Table Specifications by Interface

| Interface | Table Selector | Header BG | Border Color | Hover Effect |
| --- | --- | --- | --- | --- |
| Admin | `#employees-list table`, `#rooms-list table` | `#f4f4f4` | `#ddd` | None |
| Mucama | `#room-list table` | `#9b59b6` | `#4a4d52` | `#40444b` |
| Recepcionista | `#guest-list table`, `#room-list table` | `#9b59b6` | `#4a4d52` | `#40444b` |

**Sources:** [assets/css/StyleAdmin.css L213-L240](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L240)

 [assets/css/StyleMucama.css L168-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L190)

 [assets/css/StyleRecepcionista.css L165-L250](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L250)

### Responsive Table Adaptations

Tables reduce font size and padding at mobile breakpoints:

```

```

**Sources:** [assets/css/StyleAdmin.css L316-L379](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L316-L379)

---

## Fixed Positioning for UI Elements

Certain UI elements use `position: fixed` to remain visible during scrolling.

### Floating Social Media Buttons

Cliente interface implements fixed-position social media buttons:

```

```

These buttons remain in the bottom-right corner regardless of scroll position, providing persistent access to Instagram and WhatsApp links.

**Sources:** [assets/css/StyleCliente.css L340-L348](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L348)

### Footer Positioning

Admin interface uses absolute positioning for the footer:

```

```

This pins the footer to the bottom of the viewport when content is short.

**Sources:** [assets/css/StyleAdmin.css L269-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L269-L277)

Other interfaces use static positioning for footers, allowing them to flow naturally after content:

```

```

**Sources:** [assets/css/StyleCliente.css L390-L396](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L390-L396)

---

## Summary Table: Layout Patterns by Interface

| Pattern | Admin | Cliente | Mucama | Recepcionista |
| --- | --- | --- | --- | --- |
| Background Overlay | ✓ | ✓ | ✓ | ✓ |
| Flexbox Navigation | ✓ | ✓ | ✓ | ✓ |
| Flexbox Dashboard | ✓ | ✗ | ✗ | ✗ |
| CSS Grid Cards | ✗ | ✓ | ✗ | ✗ |
| Centered Container | ✓ | ✓ | ✓ | ✓ |
| Responsive Tables | ✓ | ✗ | ✓ | ✓ |
| Fixed Floating Buttons | ✗ | ✓ | ✗ | ✗ |
| Dialog Forms | ✗ | ✗ | ✗ | ✓ |
| 768px Breakpoint | ✓ | ✓ | ✓ | ✓ |
| 480px Breakpoint | ✓ | ✓ | ✓ | ✓ |

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)