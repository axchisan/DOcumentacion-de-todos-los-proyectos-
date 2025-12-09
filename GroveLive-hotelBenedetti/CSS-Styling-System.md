# CSS Styling System

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleIndex.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css)
> * [assets/css/StyleLogin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css)
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)

## Purpose and Scope

This document describes the CSS styling architecture used across all user interfaces in the Hotel Benedetti system. It covers the color palette, typography, component patterns, layout systems, and responsive design strategies that create a consistent visual identity. For JavaScript-driven UI interactions and DOM manipulation, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic). For overall responsive layout implementation details, see [Responsive Design Implementation](/GroveLive/hotelBenedetti/5.3-responsive-design-implementation).

## Overview of CSS Architecture

The Hotel Benedetti system employs six role-specific CSS files that implement a unified dark-themed design system with purple accent colors. Each file styles a distinct user interface while maintaining consistent visual patterns.

### CSS File Distribution

```

```

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

 [assets/css/StyleMucama.css L1-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L1-L281)

 [assets/css/StyleLogin.css L1-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L1-L196)

 [assets/css/StyleIndex.css L1-L169](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css#L1-L169)

## Global Reset and Box Model

All CSS files begin with an identical global reset that normalizes browser default styles and enforces consistent box-sizing behavior:

```

```

This universal selector ensures that all elements use the `border-box` box model, where padding and borders are included in the element's total width and height. This prevents layout calculation errors and simplifies responsive design.

**Sources:** [assets/css/StyleAdmin.css L2-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L2-L6)

 [assets/css/StyleCliente.css L2-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L2-L6)

 [assets/css/StyleRecepcionista.css L2-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L2-L6)

 [assets/css/StyleMucama.css L2-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L2-L6)

 [assets/css/StyleLogin.css L2-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L2-L6)

 [assets/css/StyleIndex.css L2-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleIndex.css#L2-L6)

## Color Palette System

The styling system employs a carefully coordinated dark theme with purple accents that maintains visual consistency across all interfaces.

### Core Color Categories

```

```

**Sources:** [assets/css/StyleAdmin.css L29-L96](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L29-L96)

 [assets/css/StyleCliente.css L16-L82](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L16-L82)

 [assets/css/StyleRecepcionista.css L16-L82](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L16-L82)

### Color Usage Patterns

| Color Code | Variable Name (Conceptual) | Primary Usage | File References |
| --- | --- | --- | --- |
| `#9b59b6` | Primary Purple | Buttons, links, headers, brand elements | All CSS files |
| `#8e44ad` | Hover Purple | Button/link hover states | All CSS files |
| `#23272a` | Overlay Background | `body::before` pseudo-element | All CSS files |
| `#2c2f33` | Container Background | Headers, sections, forms | All CSS files except StyleAdmin |
| `#36393f` | Element Background | Cards, notifications, inputs | All CSS files |
| `#dcddde` | Primary Text | Headings, body text | All CSS files |
| `#b0b3b8` | Secondary Text | Descriptions, muted content | StyleCliente, StyleRecepcionista |
| `#7289da` | Focus Border | Input/select focus states | StyleLogin, StyleCliente |
| `#36A2EB` | Edit Action | Edit buttons in tables | StyleAdmin |
| `#FF6384` / `#f44336` | Destructive Action | Delete, cancel buttons | StyleAdmin, StyleRecepcionista, StyleLogin |

**Sources:** [assets/css/StyleAdmin.css L96-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L96-L210)

 [assets/css/StyleCliente.css L73-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L73-L294)

 [assets/css/StyleLogin.css L83-L144](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L83-L144)

## Background and Layering System

All interfaces use a consistent three-layer visual hierarchy achieved through background images, overlay pseudo-elements, and z-index positioning.

### Z-Index Layering Strategy

```

```

The overlay system is implemented identically across all files:

```

```

This creates an 80% opacity dark overlay (`rgba(35, 39, 42, 0.8)`) over the background image while ensuring all content remains visible above it.

**Sources:** [assets/css/StyleAdmin.css L22-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L37)

 [assets/css/StyleCliente.css L22-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L22-L37)

 [assets/css/StyleLogin.css L25-L39](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L25-L39)

### Background Image Assets

| Interface | Background Image Path | Usage Context |
| --- | --- | --- |
| Admin | `../img/fondo2.jpg` | Dashboard and management screens |
| Cliente | `../img/fondo2.jpg` | Client-facing booking interface |
| Index | `../img/fondo2.jpg` | Landing page |
| Recepcionista | `../img/fondo.jpg` | Receptionist operations |
| Mucama | `../img/fondo.jpg` | Housekeeping interface |
| Login | `../img/fondohotelcliente2.webp` | Authentication forms |

All background images use `background-attachment: fixed` to create a parallax-like effect where the background remains stationary during scrolling.

**Sources:** [assets/css/StyleAdmin.css L9-L19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L9-L19)

 [assets/css/StyleCliente.css L9-L19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L9-L19)

 [assets/css/StyleLogin.css L9-L22](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L9-L22)

## Typography System

The system uses a minimalist typography approach with `sans-serif` font stacks and a hierarchical sizing system.

### Font Hierarchy

```

```

### Font Family Declaration

All interfaces use either `font-family: Arial, sans-serif` (StyleAdmin) or `font-family: sans-serif` (all other interfaces). The generic `sans-serif` fallback ensures consistent rendering across platforms.

**Sources:** [assets/css/StyleAdmin.css L10](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L10-L10)

 [assets/css/StyleCliente.css L10](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L10-L10)

### Heading Styles by Interface

| Element | StyleAdmin | StyleCliente/Recepcionista/Mucama | StyleLogin | StyleIndex |
| --- | --- | --- | --- | --- |
| `header h1` | 24px (desktop) → 20px (768px) → 18px (480px) | 28px → 24px → 20px | 24px → 20px | 28px → 24px → 20px |
| `section h2` | 22px → 20px | 26px → 22px | N/A | 26px → 22px → 20px |
| `section h3` | 18px → 16px | 20px → 18px | N/A | N/A |

**Sources:** [assets/css/StyleAdmin.css L69-L123](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L69-L123)

 [assets/css/StyleCliente.css L58-L109](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L58-L109)

 [assets/css/StyleLogin.css L71-L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L71-L79)

## Component Library

The system defines reusable component patterns that appear across multiple interfaces with consistent styling.

### Button Components

```

```

**Button Base Styles:**

* Padding: `0.5em 1em` or `8px 12px` / `10px 15px`
* Border: `none`
* Border-radius: `20px` (rounded pill shape) or `3px` (slight rounding)
* Cursor: `pointer`
* Transition: `background-color 0.3s ease` or combined with `box-shadow 0.3s ease`
* Font-weight: `500`

**Sources:** [assets/css/StyleAdmin.css L198-L211](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L211)

 [assets/css/StyleCliente.css L214-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L214-L228)

 [assets/css/StyleLogin.css L116-L144](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L116-L144)

### Form Components

Form styling is consistent across login, booking, and admin interfaces:

```

```

**Sources:** [assets/css/StyleLogin.css L87-L104](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L87-L104)

 [assets/css/StyleCliente.css L260-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L277)

 [assets/css/StyleAdmin.css L188-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L188-L196)

### Table Components

Tables in admin, receptionist, and maid interfaces share consistent styling patterns:

| Element | Styling Rules |
| --- | --- |
| `table` | `width: 100%`, `border-collapse: collapse`, `background-color: #36393f` or white |
| `th` | `background-color: #9b59b6`, `color: white`, `padding: 12px` |
| `td` | `padding: 12px` or `0.8em`, `border: 1px solid #ddd` or `border-bottom: 1px solid #4a4d52` |
| `tr:nth-child(even)` | `background-color: #f9f9f9` (StyleAdmin) |
| `tr:hover` | `background-color: #40444b` (dark theme tables) |

**Sources:** [assets/css/StyleAdmin.css L213-L240](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L240)

 [assets/css/StyleRecepcionista.css L165-L189](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L189)

 [assets/css/StyleMucama.css L168-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L190)

### Card Components

Card patterns appear in dashboard stats, room types, testimonials, and FAQ sections:

```

```

**Specific Card Implementations:**

* `.stat-card` in [assets/css/StyleAdmin.css L133-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L133-L156)
* `.room-type-card` in [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)
* `.testimonial-card` in [assets/css/StyleCliente.css L237-L243](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L237-L243)
* `.faq-item` in [assets/css/StyleCliente.css L326-L337](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L326-L337)
* `.notification` in [assets/css/StyleCliente.css L121-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L149)  [assets/css/StyleRecepcionista.css L118-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L118-L156)

**Sources:** [assets/css/StyleAdmin.css L133-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L133-L156)

 [assets/css/StyleCliente.css L181-L243](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L243)

### Navigation Components

Navigation bars use flexbox layouts with consistent link styling:

```

```

**Sources:** [assets/css/StyleAdmin.css L74-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L74-L97)

 [assets/css/StyleCliente.css L65-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L65-L84)

 [assets/css/StyleRecepcionista.css L65-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L65-L83)

## Layout Systems

The CSS employs two primary layout systems: Flexbox for one-dimensional layouts and CSS Grid for two-dimensional responsive galleries.

### Flexbox Layouts

```

```

**Key Flexbox Patterns:**

* **Horizontal centering**: `display: flex; justify-content: center;`
* **Space distribution**: `justify-content: space-around;`
* **Vertical stacking**: `flex-direction: column;`
* **Wrapping**: `flex-wrap: wrap;`
* **Gap spacing**: `gap: 15px;`

**Sources:** [assets/css/StyleAdmin.css L74-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L74-L80)

 [assets/css/StyleAdmin.css L126-L162](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L162)

 [assets/css/StyleCliente.css L65-L70](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L65-L70)

### CSS Grid Layouts

Grid layouts are used extensively for responsive card galleries with automatic column fitting:

```

```

**Grid-Based Components:**

| Class | Location | Purpose |
| --- | --- | --- |
| `.room-grid` | StyleCliente.css | Room browsing cards |
| `.room-types-grid` | StyleCliente.css | Room type showcases |
| `.testimonial-grid` | StyleCliente.css | Customer testimonials |
| `.faq-grid` | StyleCliente.css | FAQ items |

The `repeat(auto-fit, minmax(300px, 1fr))` pattern creates responsive grids that automatically adjust column count based on available width while maintaining a minimum column width of 300px.

**Sources:** [assets/css/StyleCliente.css L152-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L179)

 [assets/css/StyleCliente.css L231-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L231-L235)

 [assets/css/StyleCliente.css L320-L324](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L320-L324)

## Responsive Design Strategy

The system implements a mobile-first responsive strategy with two critical breakpoints.

### Breakpoint Architecture

```

```

### Responsive Transformations at 768px

**Navigation:**

```

```

**Grid Layouts:**

* `.stat-card` width changes from `30%` to `80%`
* Dashboard stats switch to `flex-direction: column; align-items: center;`
* Table font sizes reduce to `14px`

**Spacing:**

* Container padding reduces to `15px`
* Section padding reduces to `20px 15px`
* Main padding reduces to `1em`

**Sources:** [assets/css/StyleAdmin.css L280-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L334)

 [assets/css/StyleCliente.css L399-L433](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L433)

 [assets/css/StyleRecepcionista.css L327-L360](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L360)

### Responsive Transformations at 480px

**Typography:**

* `header h1`: `18px` → `20px` depending on interface
* `section h2`: `20px` → `22px`
* `section h3`: `16px` → `18px`
* Form inputs/buttons: `14px` → `16px` font size

**Layout:**

* `.stat-card` width becomes `100%`
* Canvas max-width becomes `100%`
* Logo max-width reduces to `120px`

**Spacing:**

* Form padding reduces to `15px`
* Input/button padding reduces to `8px`

**Sources:** [assets/css/StyleAdmin.css L336-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L336-L380)

 [assets/css/StyleCliente.css L435-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L435-L486)

 [assets/css/StyleLogin.css L174-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L174-L196)

### Responsive Design Summary Table

| Element | Desktop | 768px Breakpoint | 480px Breakpoint |
| --- | --- | --- | --- |
| Container padding | `20px` | `15px` | Varies |
| Header h1 | `24-28px` | `20-24px` | `18-20px` |
| Navigation | `flex-direction: row` | `flex-direction: column` | `flex-direction: column` |
| Stat card width | `30%` | `80%` | `100%` |
| Table font size | `16px` | `14px` | `12px` |
| Logo max-width | `150px` | `150px` | `120px` |
| Form input font | `16px` | `16px` | `14px` |

**Sources:** All CSS files, responsive sections

## Interactive States and Transitions

All interactive elements implement consistent transition effects for smooth visual feedback.

### Hover State Patterns

```

```

### Focus State Patterns

Form inputs and selects implement focus states for accessibility:

```

```

This creates a purple glow effect that provides clear visual feedback when an input field is focused.

**Sources:** [assets/css/StyleLogin.css L100-L104](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L100-L104)

 [assets/css/StyleCliente.css L272-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L272-L277)

 [assets/css/StyleRecepcionista.css L284-L288](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L284-L288)

### Transition Timing

All transitions use consistent timing functions:

| Property | Duration | Easing |
| --- | --- | --- |
| `background-color` | `0.3s` | `ease` |
| `color` | `0.3s` | `ease` |
| `transform` | `0.3s` | `ease` |
| `box-shadow` | `0.3s` | `ease` |
| `opacity` | `0.3s` | `ease` |

**Sources:** [assets/css/StyleAdmin.css L91-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L91-L146)

 [assets/css/StyleCliente.css L77-L227](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L77-L227)

## Specialized Components

### Floating Action Buttons

The client interface includes fixed-position social media buttons:

```

```

**Sources:** [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

### Dashboard Charts

The admin interface includes special canvas styling for Chart.js visualizations:

```

```

**Sources:** [assets/css/StyleAdmin.css L164-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L164-L172)

### Dialog/Modal Styling

The receptionist interface includes dialog element styling:

```

```

**Sources:** [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

## Consistency Patterns Across Interfaces

### Common CSS Structures

All six CSS files follow identical structural patterns:

1. **Global Reset** ([lines 2-6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/lines 2-6)  in all files)
2. **Body Background Setup** ([lines 9-19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/lines 9-19)  in all files)
3. **Overlay Pseudo-Element** ([lines 22-31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/lines 22-31)  in all files)
4. **Z-Index Content Layering** ([lines 34-37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/lines 34-37)  in all files)
5. **Header Styling** ([lines 40-66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/lines 40-66)  in all files)
6. **Responsive Media Queries** (final sections in all files)

### Shared Class Names

| Class Name | Used In | Purpose |
| --- | --- | --- |
| `.logo` | All interfaces | Logo container |
| `.notification` | Cliente, Recepcionista, Mucama | Notification cards |
| `.mark-as-read` | Cliente, Recepcionista, Mucama | Notification dismiss button |
| `.hidden` | Admin | Hide/show sections |
| `.stat-card` | Admin | Dashboard statistics |

**Sources:** Multiple CSS files, common class patterns

## Performance Considerations

The CSS system implements several performance optimizations:

1. **Fixed Background Attachment**: `background-attachment: fixed` creates parallax effects efficiently
2. **Transform-based Animations**: Hover effects use `transform: translateY(-5px)` instead of position changes for GPU acceleration
3. **Transition Selectivity**: Only specific properties are transitioned rather than `all`
4. **Box-Sizing Optimization**: Universal `box-sizing: border-box` prevents layout thrashing

**Sources:** [assets/css/StyleAdmin.css L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L15-L15)

 [assets/css/StyleAdmin.css L145](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L145-L145)

## Accessibility Features

The CSS system includes several accessibility considerations:

1. **Focus States**: All interactive elements have visible focus states with purple borders and box shadows
2. **Color Contrast**: Light text (`#dcddde`) on dark backgrounds (`#2c2f33`, `#36393f`) provides sufficient contrast
3. **Font Sizing**: Base font sizes use `px` units but are large enough (14-16px) for readability
4. **Hover Feedback**: All clickable elements provide visual hover feedback
5. **Button Sizing**: Minimum button padding ensures touch targets meet accessibility guidelines

**Sources:** [assets/css/StyleLogin.css L100-L104](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L100-L104)

 [assets/css/StyleCliente.css L272-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L272-L277)