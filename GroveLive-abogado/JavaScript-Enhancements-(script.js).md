# JavaScript Enhancements (script.js)

> **Relevant source files**
> * [assets/css/style.css](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css)
> * [assets/js/script.js](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js)

## Purpose and Scope

This page documents the client-side JavaScript enhancement layer implemented in `assets/js/script.js`. The script provides progressive enhancement for lawyer profile navigation by intercepting link clicks, displaying a visual loading indicator, and introducing a deliberate delay before navigation. For information about the views that utilize this enhancement, see [Views Overview](/GroveLive/abogado/5.1-views-overview). For styling implementation details, see [Styling System (CSS)](/GroveLive/abogado/5.2-styling-system-(css)).

## Overview

The `script.js` file implements a single-purpose enhancement that improves the user experience when navigating from the lawyer listing page to individual lawyer profile pages. The script operates entirely on the client side and enhances, rather than replaces, standard HTML link functionality.

**Core Functionality:**

* Intercepts clicks on elements with the `.abogado-link` class
* Prevents immediate navigation to allow UI feedback
* Displays a full-screen loading overlay with animated spinner
* Enforces an 800ms delay before completing navigation
* Performs programmatic navigation via `window.location.href`

Sources: [assets/js/script.js L1-L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L23)

## Script Initialization and Loader Creation

The script executes when the DOM is fully loaded using the `DOMContentLoaded` event listener. During initialization, it dynamically constructs the loading overlay element in memory and appends it to the document body.

```

```

**Loader Element Construction:**

| Step | Code Reference | Purpose |
| --- | --- | --- |
| Create container | [assets/js/script.js L3](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L3-L3) | Creates `div` element programmatically |
| Set ID | [assets/js/script.js L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L4-L4) | Assigns `id="loader"` for CSS targeting |
| Add spinner | [assets/js/script.js L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L5-L5) | Injects `<div class="spinner"></div>` child element |
| Attach to DOM | [assets/js/script.js L6](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L6-L6) | Appends to `document.body` |
| Initial state | [assets/js/script.js L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L7-L7) | Hides loader with `display: none` |

The loader element is created dynamically rather than existing in the HTML markup, ensuring it's only present when JavaScript is available and enabled in the browser.

Sources: [assets/js/script.js L1-L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L7)

## Click Event Handling and Link Interception

The script uses `querySelectorAll` to target all elements with the `.abogado-link` class and attaches a click event listener to each. These links are generated in `views/home.php` as the "Ver Perfil" buttons.

```

```

**Event Handler Logic:**

1. **Event Prevention** [assets/js/script.js L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L11-L11) : Calls `e.preventDefault()` to stop default link navigation
2. **URL Extraction** [assets/js/script.js L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L12-L12) : Retrieves `href` attribute using `this.getAttribute("href")`
3. **Loader Display** [assets/js/script.js L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L15-L15) : Sets `loader.style.display = "flex"` to show overlay
4. **Delayed Navigation** [assets/js/script.js L18-L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L18-L20) : Uses `setTimeout` with 800ms delay before navigating

Sources: [assets/js/script.js L9-L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L9-L22)

## Loading Overlay Mechanism

The loading overlay provides visual feedback during the intentional navigation delay. It consists of two components: the overlay container (`#loader`) and the animated spinner (`.spinner`).

### Overlay Container (#loader)

The `#loader` element is styled in [assets/css/style.css L147-L158](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L147-L158)

 with the following characteristics:

| Property | Value | Purpose |
| --- | --- | --- |
| `position` | `fixed` | Covers entire viewport regardless of scroll position |
| `width` / `height` | `100%` | Fills entire screen |
| `background` | `rgba(0, 0, 0, 0.8)` | Semi-transparent dark overlay |
| `display` | `flex` | Enables flexbox centering of spinner |
| `align-items` / `justify-content` | `center` | Centers spinner in viewport |
| `z-index` | `1000` | Ensures overlay appears above all content |

### Spinner Animation (.spinner)

The `.spinner` element is styled in [assets/css/style.css L161-L168](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L161-L168)

 and animated by [assets/css/style.css L171-L174](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L171-L174)

:

**Visual Structure:**

* 50px × 50px circular shape
* 5px border with varying opacity
* Top border colored `#9b59b6` (purple)
* Other borders use `rgba(255, 255, 255, 0.3)` (semi-transparent white)

**Animation:**

* Uses `@keyframes spin` animation
* Rotates from 0deg to 360deg
* Duration: 1 second
* Timing: `linear`
* Iteration: `infinite`

```

```

Sources: [assets/js/script.js L3-L6](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L3-L6)

 [assets/css/style.css L147-L174](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L147-L174)

## Navigation Timing and Delay Implementation

The script implements a fixed 800ms delay between loader display and actual navigation. This delay serves to ensure users see the loading indicator, providing feedback that their action has been registered.

**Timing Sequence:**

| Time | Action | Code Reference |
| --- | --- | --- |
| T+0ms | User clicks `.abogado-link` | - |
| T+0ms | `preventDefault()` called | [assets/js/script.js L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L11-L11) |
| T+0ms | `href` URL captured | [assets/js/script.js L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L12-L12) |
| T+0ms | Loader displayed (`display: flex`) | [assets/js/script.js L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L15-L15) |
| T+0ms | `setTimeout` scheduled | [assets/js/script.js L18](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L18-L18) |
| T+800ms | `setTimeout` callback executes | [assets/js/script.js L18-L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L18-L20) |
| T+800ms | Navigation triggered | [assets/js/script.js L19](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L19-L19) |

**Implementation Details:**

The delay is hardcoded as the second argument to `setTimeout`:

```

```

This delay cannot be configured without modifying the source code. The navigation is performed using `window.location.href` assignment, which triggers a full page reload rather than using modern navigation APIs like the History API.

Sources: [assets/js/script.js L18-L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L18-L20)

## Integration with View Layer

The JavaScript enhancement integrates with the view layer through CSS class selectors. Links must have the `.abogado-link` class to be intercepted by the event handler.

### Home View Integration

In `views/home.php`, the "Ver Perfil" links are styled with the `.btn` class for visual appearance but must also include the `.abogado-link` class for JavaScript interception:

```

```

**CSS Class Responsibilities:**

| Class | Purpose | Defined In |
| --- | --- | --- |
| `.btn` | Visual styling (purple background, padding, border-radius) | [assets/css/style.css L81-L95](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L81-L95) |
| `.abogado-link` | JavaScript event handler targeting | [assets/js/script.js L9](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L9-L9) |

The dual-class approach separates presentation concerns (`.btn`) from behavioral concerns (`.abogado-link`), allowing JavaScript to be disabled without breaking visual styling.

Sources: [assets/js/script.js L9](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L9-L9)

 [assets/css/style.css L81-L95](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L81-L95)

## Progressive Enhancement Architecture

The script implements progressive enhancement by enhancing but not requiring JavaScript functionality. The system degrades gracefully when JavaScript is unavailable.

**Behavior Matrix:**

| JavaScript State | Link Behavior | Loader Display | Navigation Delay |
| --- | --- | --- | --- |
| Enabled | Enhanced (intercepted) | Yes | 800ms |
| Disabled | Standard (immediate) | No | 0ms |

### Fallback Behavior

When JavaScript is disabled or fails to load:

* Links function as standard HTML `<a>` elements
* Navigation occurs immediately on click
* No loader is displayed
* Users still reach the correct destination

This approach ensures the core functionality (viewing lawyer profiles) remains operational even in constrained environments.

Sources: [assets/js/script.js L1-L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L23)

## Dependencies and Execution Context

The script has minimal dependencies and uses only standard DOM APIs available in all modern browsers.

**Browser API Dependencies:**

```

```

**API Compatibility:**

* All APIs used are ES5-compatible
* No transpilation or polyfills required
* Compatible with Internet Explorer 9+
* No external libraries (jQuery, etc.) required

**Loading Context:**

The script is loaded via `<script>` tags in both views:

* `views/home.php` includes the script
* `views/abogadosView.php` includes the script (though no `.abogado-link` elements exist on profile pages)

The inclusion in both views ensures consistent behavior, though the script only activates on pages containing `.abogado-link` elements.

Sources: [assets/js/script.js L1-L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L23)

## Limitations and Considerations

The current implementation has several characteristics that affect its behavior and maintainability:

**Hardcoded Values:**

* 800ms delay cannot be configured without code modification [assets/js/script.js L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L20-L20)
* Loader markup (`<div class="spinner"></div>`) is embedded in JavaScript [assets/js/script.js L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L5-L5)

**Navigation Approach:**

* Uses `window.location.href` which triggers full page reload
* No support for browser history manipulation
* No support for back button navigation without reload

**Selector Coupling:**

* Tightly coupled to `.abogado-link` class name [assets/js/script.js L9](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L9-L9)
* Changes to class name require script modification
* No configuration or data attributes used

**No Error Handling:**

* No fallback if `querySelector` returns no elements
* No validation of `href` attribute existence
* No check for loader element collision (if multiple scripts run)

**Performance Considerations:**

* Creates loader element on every page load, even if never used
* Attaches event listeners to potentially many elements using `forEach`
* No event delegation pattern employed

Sources: [assets/js/script.js L1-L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L23)