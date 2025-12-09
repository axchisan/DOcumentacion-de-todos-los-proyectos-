# Design System and Global Styles

> **Relevant source files**
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

This document covers the foundational design system defined in [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 which establishes the visual language and reusable components for the American Food restaurant application. This file provides CSS custom properties for colors, typography standards, and global UI components including navigation, buttons, carousels, forms, footers, and the cart sidebar system.

For role-specific interface implementations that build upon these global styles, see the following pages:

* Authentication interface: [5.2](/axchisan/AmericanFood/5.2-authentication-interface)
* Customer interface: [5.3.1](/axchisan/AmericanFood/5.3.1-customer-interface-(cliente.css))
* Cashier interface: [5.3.2](/axchisan/AmericanFood/5.3.2-cashier-interface-(cajera.css))
* Kitchen interface: [5.3.3](/axchisan/AmericanFood/5.3.3-kitchen-interface-(cocina.css))
* Server interface: [5.3.4](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css))

**Sources**: [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

---

## CSS Custom Properties (Color System)

The design system uses CSS custom properties defined in the `:root` selector to maintain a consistent color palette throughout the application.

### Color Palette

| Variable | Hex Value | Purpose |
| --- | --- | --- |
| `--primary-color` | `#FF6B35` | Primary brand color, navbar, primary buttons |
| `--secondary-color` | `#C0392B` | Secondary brand color, hover states, emphasis |
| `--accent-color` | `#F9A826` | Accent elements, footer headings |
| `--light-color` | `#FFF3E0` | Light backgrounds, section alternation |
| `--dark-color` | `#2C3E50` | Footer background, dark elements |
| `--text-color` | `#333333` | Primary text color |
| `--text-light` | `#FFFFFF` | Light text on dark backgrounds |
| `--success-color` | `#27AE60` | Success states, confirmations |
| `--warning-color` | `#F39C12` | Warning states, alerts |
| `--danger-color` | `#E74C3C` | Error states, destructive actions |
| `--info-color` | `#3498DB` | Informational elements |
| `--gray-light` | `#F5F5F5` | Light gray backgrounds |
| `--gray-medium` | `#E0E0E0` | Medium gray, borders, dividers |
| `--gray-dark` | `#9E9E9E` | Dark gray, muted text |

### Color System Architecture

```

```

**Sources**: [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

---

## Typography System

### Font Stack

The application uses a progressive font stack prioritizing modern, readable typefaces:

```

```

**Fallback Hierarchy**:

1. `Poppins` - Primary web font (requires external loading)
2. `Segoe UI` - Modern Windows system font
3. `Tahoma` - Cross-platform sans-serif
4. `Geneva` - macOS alternative
5. `Verdana` - Universal fallback
6. `sans-serif` - Generic sans-serif

### Typography Specifications

| Element | Font Weight | Line Height | Bottom Margin |
| --- | --- | --- | --- |
| `body` | Normal (400) | 1.6 | N/A |
| `h1, h2, h3, h4, h5, h6` | Bold (700) | Inherited | 1rem |
| Links (`a`) | Normal | Inherited | N/A |

### Heading Sizes (Context-Dependent)

| Context | Element | Font Size |
| --- | --- | --- |
| Carousel caption | `h1` | 2.5rem (992px+), 2rem (768px-992px), 1.5rem (576px-768px), 1.2rem (<576px) |
| Menu cards | `h5` | 1.25rem |
| Cart items | `h6` | 16px |

**Sources**: [css/style.css L21-L31](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L21-L31)

 [css/style.css L127-L130](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L127-L130)

 [css/style.css L386-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L386-L460)

 [css/style.css L509-L511](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L509-L511)

 [css/style.css L654-L656](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L654-L656)

---

## Global Navigation System

### Navbar Component (.navbar)

The navigation bar uses a fixed design pattern with the following characteristics:

| Property | Value | Purpose |
| --- | --- | --- |
| Background | `var(--primary-color)` | Brand color consistency |
| Padding | `1rem 0` | Vertical spacing |
| Box Shadow | `0 2px 10px rgba(0, 0, 0, 0.1)` | Visual elevation |

### Navigation Links (.navbar-dark .navbar-nav .nav-link)

Navigation links implement interactive states:

```

```

### Brand and Login Elements

| Class | Purpose | Styling |
| --- | --- | --- |
| `.brand-text` | Logo/brand name | Font size: 1.5rem, weight: 700, letter-spacing: 1px |
| `.btn-login` | Login button | Background: `var(--secondary-color)`, border-radius: 4px, opacity: 0.9 on hover |

**Sources**: [css/style.css L43-L82](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L43-L82)

---

## Button System

The button system provides primary and outline variants with consistent interaction patterns.

### Button Variants

| Class | Background | Border | Hover Background | Hover Border |
| --- | --- | --- | --- | --- |
| `.btn-primary` | `var(--primary-color)` | `var(--primary-color)` | `var(--secondary-color)` | `var(--secondary-color)` |
| `.btn-outline-primary` | Transparent | `var(--primary-color)` | `var(--primary-color)` | `var(--primary-color)` |

### Button State Transitions

```

```

**Sources**: [css/style.css L84-L103](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L84-L103)

---

## Carousel Component

The carousel system provides a full-width hero section with responsive height adjustments and caption overlays.

### Carousel Structure

| Class | Purpose | Default Height | Responsive Heights |
| --- | --- | --- | --- |
| `.carousel-item` | Carousel slide container | 600px | 450px (≤992px), 350px (≤768px), 300px (≤576px) |
| `.carousel-caption` | Text overlay | N/A | Adjusts padding and positioning at breakpoints |

### Carousel Caption Specifications

| Property | Default | 992px | 768px | 576px |
| --- | --- | --- | --- | --- |
| Background | `rgba(0, 0, 0, 0.5)` | Same | Same | Same |
| Padding | 2rem | 1.5rem | 1rem | 0.8rem |
| Border Radius | 8px | Same | Same | Same |
| Max Width | 600px | Same | Same | Same |
| Bottom Position | 5rem | 2rem | 1rem | 0.5rem |
| `h1` Font Size | 2.5rem | 2rem | 1.5rem | 1.2rem |
| `p` Font Size | 1.2rem | Same | 1rem | 0.9rem |

**Sources**: [css/style.css L105-L135](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L105-L135)

 [css/style.css L386-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L386-L460)

---

## Card Components

### Info Cards (.info-card)

Informational cards used for feature highlights and content organization:

| Property | Value |
| --- | --- |
| Background | `var(--text-light)` |
| Padding | 2rem |
| Border Radius | 8px |
| Box Shadow | `0 4px 15px rgba(0, 0, 0, 0.1)` |

**List Styling**: Info cards include custom list bullets using Font Awesome icons:

* Content: `\f105` (chevron-right icon)
* Font Family: "Font Awesome 6 Free"
* Color: `var(--primary-color)`

### Menu Item Cards (.menu-item, .menu-card)

Two menu card implementations exist in the file:

**Standard Menu Item (`.menu-item`)**:

| Property | Value |
| --- | --- |
| Background | `var(--text-light)` |
| Border Radius | 8px |
| Box Shadow | `0 4px 15px rgba(0, 0, 0, 0.1)` |
| Margin Bottom | 2rem (30px in grid variant) |
| Transform on Hover | `translateY(-5px)` |
| Transition | `transform 0.3s ease` |

**Image Specifications**:

* Width: 100%
* Height: 200px
* Object Fit: cover

**Menu Card Alternative (`.menu-card`)**:

Similar styling with specific additions:

* Text alignment: center
* Button background: `#e67e22`
* Button hover: `#d35400`
* Price color: `#e67e22`, font weight: bold

```

```

**Sources**: [css/style.css L158-L196](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L158-L196)

 [css/style.css L198-L238](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L198-L238)

 [css/style.css L483-L536](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L483-L536)

 [css/style.css L539-L570](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L539-L570)

---

## Form Components

### Reservation Forms (.reservation-form)

| Property | Value |
| --- | --- |
| Background | `var(--text-light)` |
| Padding | 2rem |
| Border Radius | 8px |
| Box Shadow | `0 4px 15px rgba(0, 0, 0, 0.1)` |
| Heading Color | `var(--primary-color)` |
| Heading Alignment | center |

### Contact Forms (.contact-form)

Identical styling to reservation forms, promoting consistency across form interactions.

### Contact Info Cards (.contact-info)

| Property | Value |
| --- | --- |
| Text Alignment | center |
| Padding | 2rem |
| Background | `var(--light-color)` |
| Border Radius | 8px |
| Margin Bottom | 2rem |
| Box Shadow | `0 4px 15px rgba(0, 0, 0, 0.1)` |
| Height | 100% |
| Icon Font Size | 2.5rem |
| Icon Color | `var(--primary-color)` |

**Sources**: [css/style.css L240-L287](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L240-L287)

---

## Footer Component

The footer implements a multi-column layout with social icons, links, and hours information.

### Footer Structure

| Element | Styling |
| --- | --- |
| `.footer` | Background: `var(--dark-color)`, color: `var(--text-light)`, padding: `3rem 0 1.5rem` |
| `.footer h3` | Color: `var(--accent-color)`, margin-bottom: 1.5rem |
| `.social-icons a` | Display: inline-block, size: 40x40px, background: `rgba(255, 255, 255, 0.1)`, border-radius: 50% |

### Social Icon Interaction

```

```

### Footer Links (.footer-links)

| Property | Value |
| --- | --- |
| List Style | none |
| Padding Left | 0 |
| Item Margin Bottom | 0.8rem |
| Link Color | `var(--text-light)` |
| Hover Color | `var(--accent-color)` |
| Hover Padding Left | 5px (animated) |

### Copyright Section (.copyright)

| Property | Value |
| --- | --- |
| Margin Top | 2rem |
| Padding Top | 1.5rem |
| Border Top | `1px solid rgba(255, 255, 255, 0.1)` |
| Text Alignment | center |

**Sources**: [css/style.css L312-L383](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L312-L383)

---

## Cart Sidebar System

The cart sidebar is a fixed-position component that slides in from the right side of the viewport, providing shopping cart functionality across the application.

### Cart Sidebar Architecture

```

```

### Cart Sidebar Specifications

| Class | Position | Width | Height | Background | Z-Index | Transition |
| --- | --- | --- | --- | --- | --- | --- |
| `.cart-sidebar` | `fixed` | 400px | 100% | `#fff` | 1000 | `right 0.3s ease` |
| `.cart-sidebar.active` | `fixed` | 400px | 100% | `#fff` | 1000 | Slides to right: 0 |

### Cart Overlay Specifications

| Class | Position | Dimensions | Background | Z-Index | Initial State |
| --- | --- | --- | --- | --- | --- |
| `.cart-overlay` | `fixed` | 100% x 100% | `rgba(0, 0, 0, 0.5)` | 999 | opacity: 0, visibility: hidden |
| `.cart-overlay.active` | `fixed` | 100% x 100% | `rgba(0, 0, 0, 0.5)` | 999 | opacity: 1, visibility: visible |

### Cart Component Breakdown

#### Cart Header (.cart-header)

* Padding: 15px
* Border Bottom: `1px solid #ddd`
* Display: flex, space-between alignment

#### Cart Items Container (.cart-items)

* Flex: 1 (takes remaining space)
* Overflow Y: auto (scrollable)
* Padding: 15px

#### Empty Cart State (.empty-cart)

* Text Alignment: center
* Padding: 20px
* Icon Font Size: 50px
* Icon Color: `#ccc`

#### Cart Item (.cart-item)

| Section | Properties |
| --- | --- |
| Container | Display: flex, align-items: center, padding: 10px 0, border-bottom: `1px solid #eee` |
| Image (`.cart-item-image img`) | Size: 60x60px, object-fit: cover, margin-right: 15px |
| Details (`.cart-item-details`) | Flex: 1, h6 font-size: 16px, p color: #666 |
| Actions (`.cart-item-actions`) | Display: flex, gap: 5px |

#### Cart Footer (.cart-footer)

* Padding: 15px
* Border Top: `1px solid #ddd`

#### Cart Total (.cart-total)

* Paragraph Margin: 5px 0
* Total Class: font-weight: bold

#### Cart Badge (.cart-badge)

| Property | Value |
| --- | --- |
| Background | `#ff5733` |
| Color | `#fff` |
| Border Radius | 50% |
| Padding | 2px 6px |
| Font Size | 12px |
| Position | relative, top: -10px, left: -5px |

### Cart State Management

```

```

**Sources**: [css/style.css L575-L703](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L575-L703)

---

## Section Layout System

The application uses consistent section padding with alternating background colors.

### Section Base Styles

| Selector | Padding | Purpose |
| --- | --- | --- |
| `section` | 5rem 0 | Default vertical spacing |
| `section` (≤992px) | 4rem 0 | Tablet spacing |
| `section` (≤768px) | 3rem 0 | Mobile landscape spacing |
| `section` (≤576px) | 2rem 0 | Mobile portrait spacing |

### Section Background Colors

| Class | Background Color | Purpose |
| --- | --- | --- |
| `.welcome-section` | `var(--light-color)` | Welcome/intro content |
| `.menu-section` | `var(--text-light)` | Menu display sections |
| `.reservation-section` | `var(--gray-light)` | Reservation forms |
| `.contact-section` | `var(--text-light)` | Contact information |
| `.cocina-section` | Default | Kitchen management (40px padding override) |

**Sources**: [css/style.css L137-L156](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L137-L156)

 [css/style.css L385-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L385-L460)

 [css/style.css L463-L486](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L463-L486)

---

## Responsive Design Strategy

The design system implements a mobile-first responsive strategy with four primary breakpoints.

### Breakpoint Hierarchy

| Breakpoint | Max Width | Target Devices | Key Adjustments |
| --- | --- | --- | --- |
| Desktop | Default | Desktops, large laptops | Full-size components |
| Tablet | 992px | Tablets, small laptops | Reduced carousel height (600px → 450px), section padding (5rem → 4rem) |
| Mobile Landscape | 768px | Large phones landscape, small tablets | Further reduced carousel (350px), section padding (3rem), condensed typography |
| Mobile Portrait | 576px | Small phones | Minimal carousel (300px), section padding (2rem), smallest font sizes |

### Responsive Component Adjustments

```

```

### Media Query Structure

The responsive system uses max-width media queries in descending order:

1. `@media (max-width: 992px)` - Lines 386-403
2. `@media (max-width: 768px)` - Lines 405-430
3. `@media (max-width: 576px)` - Lines 432-460

**Sources**: [css/style.css L385-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L385-L460)

---

## Kitchen and Menu Management Styles

Additional styles for administrative kitchen and menu management interfaces.

### Kitchen Section (.cocina-section)

| Class | Purpose | Styling |
| --- | --- | --- |
| `.cocina-section` | Section container | Padding: 40px 0 (overrides base section padding) |
| `.cocina-card` | Card container | Background: #fff, border-radius: 8px, box-shadow: `0 2px 10px rgba(0, 0, 0, 0.1)`, padding: 20px |
| `.menu-table` | Table layout | Vertical align: middle for th and td |

### Menu Grid System

| Class | Purpose | Styling |
| --- | --- | --- |
| `.menu-grid` | Grid container | Margin top: 20px |
| `.menu-actions` | Action button container | Display: flex, justify-content: space-between, gap: 10px |
| `.menu-actions .btn` | Action buttons | Flex: 1 (equal width) |

### Image Placeholder (.menu-img-placeholder)

Used when menu items lack images:

| Property | Value |
| --- | --- |
| Width | 100% |
| Height | 200px |
| Background | `var(--gray-medium)` |
| Display | flex (centered content) |
| Color | `var(--gray-dark)` |
| Font Size | 1rem |
| Border Radius | 8px |
| Margin Bottom | 15px |

**Sources**: [css/style.css L463-L570](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L463-L570)

---

## Order Confirmation System

### Order Total Modal (.order-total-modal)

Minimal styling for order total display:

* Paragraphs: margin: 0, font-weight: bold

### Confirmation Icon (.confirmation-icon)

| Property | Value |
| --- | --- |
| Font Size | 50px |
| Color | `#28a745` (success green) |
| Margin Bottom | 15px |

**Sources**: [css/style.css L694-L703](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L694-L703)

---

## Design System Integration Map

```

```

This design system serves as the foundation for all role-specific interfaces documented in sections [5.2](/axchisan/AmericanFood/5.2-authentication-interface) through [5.3.4](/axchisan/AmericanFood/5.3.4-server-interface-(mesera.css)), ensuring visual consistency while allowing customization for specific user needs.

**Sources**: [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)