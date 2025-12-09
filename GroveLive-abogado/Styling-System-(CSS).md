# Styling System (CSS)

> **Relevant source files**
> * [assets/css/style.css](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css)

## Purpose and Scope

This document explains the CSS styling system implemented in `assets/css/style.css`. The system provides a dark-themed visual design for the lawyer management application using CSS custom properties for theming, component-based styling for layouts and cards, and responsive design patterns. This document covers the theme variable system, global styles, component classes, typography styles, button patterns, the loading overlay animation, and responsive breakpoints.

For information about how these styles are applied in the HTML views, see [Lawyer Listing View](/GroveLive/abogado/5.1.1-lawyer-listing-view-(home.php)) and [Lawyer Profile View](/GroveLive/abogado/5.1.2-lawyer-profile-view-(abogadosview.php)). For the JavaScript that interacts with the loading spinner styles, see [JavaScript Enhancements](/GroveLive/abogado/5.3-javascript-enhancements-(script.js)).

**Sources:** [assets/css/style.css L1-L175](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L175)

---

## Theme System - CSS Custom Properties

The stylesheet implements a centralized theming system using CSS custom properties defined in the `:root` pseudo-class. This allows consistent color and transition values throughout the application.

### Theme Variables

| Variable Name | Value | Purpose |
| --- | --- | --- |
| `--primary-color` | `#1e1f26` | Dark background color for body |
| `--secondary-color` | `#6f42c1` | Purple accent for headings, borders, buttons |
| `--accent-color` | `#e74c3c` | Red hover state for interactive elements |
| `--text-color` | `#ffffff` | White text color |
| `--light-bg` | `#292b36` | Lighter dark background for cards/containers |
| `--border-color` | `#44475a` | Border color (currently unused) |
| `--shadow` | `0 2px 10px rgba(0, 0, 0, 0.3)` | Default box shadow |
| `--transition` | `all 0.3s ease` | Standard transition timing |

**Sources:** [assets/css/style.css L1-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L10)

### CSS Variable Propagation Diagram

```

```

**Sources:** [assets/css/style.css L1-L120](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L120)

---

## Global Styles and Resets

### Universal Reset

The stylesheet applies a global reset using the universal selector to eliminate browser default spacing:

```

```

The `box-sizing: border-box` ensures that padding and border are included in element width calculations.

**Sources:** [assets/css/style.css L12-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L12-L16)

### Body Container

The `body` element establishes the page container with centered content:

| Property | Value | Purpose |
| --- | --- | --- |
| `font-family` | `'Segoe UI', Tahoma, Geneva, Verdana, sans-serif` | System font stack |
| `background-color` | `var(--primary-color)` | Dark background |
| `color` | `var(--text-color)` | White text |
| `padding` | `20px` | Inner spacing |
| `max-width` | `1200px` | Constrains content width |
| `margin` | `0 auto` | Centers the container |

**Sources:** [assets/css/style.css L18-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L18-L25)

### Typography Styles

#### Heading 1 (h1)

* **Color:** `var(--secondary-color)` (purple)
* **Alignment:** Center
* **Margin:** `30px 0`
* **Font size:** `2.5rem`
* **Border:** `2px solid var(--secondary-color)` bottom border with `10px` padding
* **Usage:** Page titles in both home and profile views

**Sources:** [assets/css/style.css L27-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L27-L34)

#### Heading 4 (h4)

* **Color:** `var(--secondary-color)` (purple)
* **Margin bottom:** `10px`
* **Font size:** `1.3rem`
* **Usage:** Section labels in profile view (e.g., "Nombre:", "Especialidad:")

**Sources:** [assets/css/style.css L36-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L36-L40)

#### Paragraphs (p)

* **Margin bottom:** `15px`
* **Color:** `var(--text-color)` (white)
* **Usage:** Lawyer detail content in profile view

**Sources:** [assets/css/style.css L42-L45](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L42-L45)

#### Links (a)

* **Default color:** `var(--secondary-color)` (purple)
* **Hover color:** `var(--accent-color)` (red)
* **Hover decoration:** Underline
* **Transition:** `var(--transition)` (0.3s ease)

**Sources:** [assets/css/style.css L47-L56](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L47-L56)

---

## Layout Components

### Menu Grid (.menu-grid)

The `.menu-grid` class creates a responsive grid layout for displaying lawyer cards on the home view.

| Property | Value | Purpose |
| --- | --- | --- |
| `display` | `grid` | CSS Grid layout |
| `grid-template-columns` | `repeat(auto-fill, minmax(300px, 1fr))` | Responsive columns with 300px minimum |
| `gap` | `20px` | Space between grid items |
| `margin-top` | `30px` | Separation from page heading |

The `auto-fill` keyword with `minmax(300px, 1fr)` creates a responsive grid that automatically adjusts the number of columns based on available width. Each column has a minimum width of `300px` and expands to fill available space.

**Usage in views:** Applied to the container holding lawyer card listings in [views/home.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php)

**Sources:** [assets/css/style.css L59-L64](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L59-L64)

---

## Card Components

### Menu Item Card (.menu-item)

The `.menu-item` class styles individual lawyer cards in the grid layout.

| Property | Value | Purpose |
| --- | --- | --- |
| `background-color` | `var(--light-bg)` | Lighter dark card background |
| `border-radius` | `8px` | Rounded corners |
| `padding` | `20px` | Internal spacing |
| `box-shadow` | `var(--shadow)` | Elevation effect |
| `transition` | `var(--transition)` | Smooth hover animation |
| `border-left` | `4px solid var(--secondary-color)` | Purple accent stripe |

**Hover State:**

* **Transform:** `translateY(-5px)` - Lifts card up
* **Box shadow:** `0 5px 15px rgba(0, 0, 0, 0.4)` - Enhanced shadow

**Usage in views:** Applied to each lawyer card in the home listing.

**Sources:** [assets/css/style.css L66-L78](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L66-L78)

### Lawyer Profile Container (.abogado-container)

The `.abogado-container` class styles the detail card on the profile view.

| Property | Value | Purpose |
| --- | --- | --- |
| `background-color` | `var(--light-bg)` | Matches menu-item background |
| `padding` | `20px` | Internal spacing |
| `border-radius` | `8px` | Rounded corners |
| `box-shadow` | `var(--shadow)` | Elevation effect |
| `border-left` | `4px solid var(--secondary-color)` | Purple accent stripe |
| `margin-bottom` | `20px` | Space before back button |

This class shares visual styling with `.menu-item` but lacks hover effects since it is not interactive.

**Usage in views:** Applied to the lawyer detail container in [views/abogadosView.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php)

**Sources:** [assets/css/style.css L98-L105](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L98-L105)

---

## Button Components

### Component Class Hierarchy

```

```

**Sources:** [assets/css/style.css L81-L120](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L81-L120)

### Generic Button (.btn)

Styles for primary action buttons.

| Property | Value |
| --- | --- |
| `display` | `inline-block` |
| `background-color` | `var(--secondary-color)` (purple) |
| `color` | `white` |
| `padding` | `10px 15px` |
| `border-radius` | `6px` |
| `margin-top` | `10px` |
| `transition` | `var(--transition)` |
| `text-decoration` | `none` |
| `font-weight` | `bold` |

**Hover State:** Background changes to `var(--accent-color)` (red).

**Sources:** [assets/css/style.css L81-L95](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L81-L95)

### Back Button (.volver)

Specialized button style for the back navigation link.

| Property | Value | Difference from `.btn` |
| --- | --- | --- |
| `padding` | `10px 20px` | More horizontal padding |
| `margin-top` | `20px` | Larger top margin |

All other properties match `.btn`. The increased padding and margin provide better visual separation on the profile view.

**Sources:** [assets/css/style.css L107-L120](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L107-L120)

---

## Loading Overlay System

The CSS defines styles for a loading overlay that appears during navigation transitions. This system works in conjunction with the JavaScript in `script.js` (see [JavaScript Enhancements](/GroveLive/abogado/5.3-javascript-enhancements-(script.js))).

### Loading Overlay Container (#loader)

| Property | Value | Purpose |
| --- | --- | --- |
| `position` | `fixed` | Positioned relative to viewport |
| `top`, `left` | `0` | Covers entire viewport |
| `width`, `height` | `100%` | Full screen coverage |
| `background` | `rgba(0, 0, 0, 0.8)` | Semi-transparent black overlay |
| `display` | `flex` | Flexbox for centering |
| `align-items`, `justify-content` | `center` | Centers spinner |
| `z-index` | `1000` | Above all other content |

**Sources:** [assets/css/style.css L147-L158](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L147-L158)

### Spinner Animation (.spinner)

The `.spinner` class creates an animated loading indicator.

| Property | Value | Purpose |
| --- | --- | --- |
| `width`, `height` | `50px` | Square dimensions |
| `border` | `5px solid rgba(255, 255, 255, 0.3)` | Semi-transparent white border |
| `border-top` | `5px solid #9b59b6` | Purple top border |
| `border-radius` | `50%` | Circular shape |
| `animation` | `spin 1s linear infinite` | Continuous rotation |

### Spin Keyframes

```

```

The animation rotates the spinner 360 degrees over 1 second, creating a continuous spinning effect. The purple top border creates a visual indicator of rotation against the semi-transparent border.

**Sources:** [assets/css/style.css L161-L174](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L161-L174)

### Loading Overlay Architecture

```

```

**Sources:** [assets/css/style.css L147-L174](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L147-L174)

 [assets/js/script.js](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js)

---

## Responsive Design

The stylesheet implements mobile-responsive design using two breakpoints.

### Tablet Breakpoint (@media (max-width: 768px))

**Grid Layout:**

* `.menu-grid` changes to `grid-template-columns: 1fr` (single column layout)

**Typography:**

* `h1` font size reduces to `2rem` (from `2.5rem`)

**Spacing:**

* `body` padding reduces to `15px` (from `20px`)

This breakpoint ensures that lawyer cards stack vertically on tablet-sized screens rather than attempting multi-column layouts with insufficient space.

**Sources:** [assets/css/style.css L123-L135](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L123-L135)

### Mobile Breakpoint (@media (max-width: 480px))

**Typography:**

* `h1` font size further reduces to `1.8rem`

**Card Styling:**

* `.menu-item` padding reduces to `15px` (from `20px`)

This additional breakpoint provides further optimization for small mobile screens by reducing font sizes and internal padding.

**Sources:** [assets/css/style.css L137-L145](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L137-L145)

### Responsive Behavior Diagram

```

```

**Sources:** [assets/css/style.css L123-L145](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L123-L145)

---

## CSS Class to View Mapping

The following table maps CSS classes to their usage locations in the view templates:

| CSS Class | View | HTML Element | Purpose |
| --- | --- | --- | --- |
| `.menu-grid` | `views/home.php` | `<div>` container | Grid layout for lawyer cards |
| `.menu-item` | `views/home.php` | `<div>` per lawyer | Individual lawyer card styling |
| `.btn` | `views/home.php` | `<a>` link | "Ver Perfil" action buttons |
| `.abogado-container` | `views/abogadosView.php` | `<div>` container | Profile detail card |
| `.volver` | `views/abogadosView.php` | `<a>` link | Back to listing navigation |
| `#loader` | Created by `script.js` | `<div>` overlay | Loading screen container |
| `.spinner` | Created by `script.js` | `<div>` inside `#loader` | Animated loading indicator |

**Sources:** [assets/css/style.css L1-L175](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L175)

 [views/home.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php)

 [views/abogadosView.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php)

 [assets/js/script.js](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js)

---

## Summary

The `style.css` file implements a component-based styling system with the following characteristics:

1. **Centralized Theming:** CSS custom properties in `:root` enable consistent color and transition values
2. **Dark Theme:** Purple and red accent colors (`#6f42c1`, `#e74c3c`) on dark backgrounds (`#1e1f26`, `#292b36`)
3. **Responsive Grid:** Auto-filling grid layout adapts from multi-column to single-column based on viewport width
4. **Card Pattern:** Both listing and profile views use consistent card styling with left border accent
5. **Interactive Feedback:** Hover states on cards and buttons provide visual feedback with color and transform changes
6. **Loading Animation:** CSS-animated spinner with keyframe rotation for navigation transitions
7. **Mobile Optimization:** Two breakpoint strategy (768px, 480px) adjusts layouts, typography, and spacing

The system prioritizes simplicity with no CSS preprocessors, frameworks, or build tools—pure CSS3 with modern features like custom properties, grid layout, and flexbox.

**Sources:** [assets/css/style.css L1-L175](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L175)