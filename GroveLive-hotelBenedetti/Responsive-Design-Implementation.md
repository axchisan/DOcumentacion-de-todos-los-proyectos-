# Responsive Design Implementation

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleLogin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css)
> * [assets/css/StyleMucama.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)

## Purpose and Scope

This document details the responsive design implementation across all user interfaces in the Hotel Benedetti system. It covers the mobile-first breakpoint strategy, layout adaptation patterns, and component-specific responsive behaviors that ensure optimal user experience across desktop (1200px+), tablet (768px-1199px), and mobile (≤767px) devices.

For information about the overall CSS styling system and color palette, see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system). For JavaScript client-side interactions that complement responsive behavior, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic).

---

## Breakpoint Architecture

The system employs a two-breakpoint responsive strategy consistently applied across all five user interface stylesheets:

| Breakpoint | Target Devices | Implementation |
| --- | --- | --- |
| `max-width: 768px` | Tablets and small tablets | Primary layout restructuring, navigation stacking, component width adjustments |
| `max-width: 480px` | Mobile phones | Further size reductions, typography scaling, aggressive spacing optimization |
| Default (>768px) | Desktop computers | Full-featured layout with maximum visual density |

### Breakpoint Consistency Across Interfaces

```

```

**Sources:** [assets/css/StyleAdmin.css L280-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L380)

 [assets/css/StyleCliente.css L399-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L399-L486)

 [assets/css/StyleLogin.css L174-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L174-L196)

 [assets/css/StyleMucama.css L217-L281](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L217-L281)

 [assets/css/StyleRecepcionista.css L327-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L327-L407)

---

## Layout Adaptation Patterns

### Container Responsive Behavior

The `.container` class adapts its padding and width across breakpoints to optimize content density:

**Desktop (Default)**

* `max-width: 1200px` with `padding: 20px`
* Centered with `margin: 0 auto`

**Tablet (≤768px)**

* Padding reduced to `15px`

**Mobile (≤480px)**

* Login container: `width: 90%` with `padding: 15px`

### Background Image Strategy

All interfaces use `background-attachment: fixed` with a semi-transparent overlay pattern:

```

```

This layering strategy remains consistent across all viewport sizes, ensuring readability on all devices.

**Sources:** [assets/css/StyleAdmin.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L8-L37)

 [assets/css/StyleCliente.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L9-L37)

 [assets/css/StyleLogin.css L9-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L9-L34)

 [assets/css/StyleMucama.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L9-L37)

 [assets/css/StyleRecepcionista.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L9-L37)

---

## Navigation Responsive Patterns

### Desktop to Mobile Navigation Transformation

Navigation elements transform from horizontal flex layouts to vertical stacks on smaller screens:

```

```

**Admin Interface Example:**

* Desktop: [assets/css/StyleAdmin.css L74-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L74-L97)
* Tablet: [assets/css/StyleAdmin.css L289-L297](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L289-L297)

**Client Interface Example:**

* Desktop: [assets/css/StyleCliente.css L65-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L65-L84)
* Tablet: [assets/css/StyleCliente.css L404-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L404-L407)

**Receptionist/Maid Interfaces:**

* Desktop: [assets/css/StyleRecepcionista.css L65-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L65-L83)  / [assets/css/StyleMucama.css L65-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L65-L83)
* Tablet: [assets/css/StyleRecepcionista.css L332-L335](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L332-L335)  / [assets/css/StyleMucama.css L222-L225](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L222-L225)

**Sources:** [assets/css/StyleAdmin.css L74-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L74-L97)

 [assets/css/StyleAdmin.css L289-L297](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L289-L297)

 [assets/css/StyleCliente.css L65-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L65-L84)

 [assets/css/StyleCliente.css L404-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L404-L407)

 [assets/css/StyleRecepcionista.css L65-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L65-L83)

 [assets/css/StyleRecepcionista.css L332-L335](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L332-L335)

---

## Grid System Responsive Behavior

### CSS Grid Auto-Fit Pattern

The system extensively uses CSS Grid with `repeat(auto-fit, minmax())` for responsive card layouts:

```

```

**Grid Definitions:**

* `.room-grid`: [assets/css/StyleCliente.css L152-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L156)
* `.room-types-grid`: [assets/css/StyleCliente.css L175-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L175-L179)
* `.testimonial-grid`: [assets/css/StyleCliente.css L231-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L231-L235)
* `.faq-grid`: [assets/css/StyleCliente.css L320-L324](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L320-L324)

**Dashboard Stats Flexbox:**

* Desktop: [assets/css/StyleAdmin.css L126-L131](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L131)
* Tablet override: [assets/css/StyleAdmin.css L303-L310](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L310)

**Sources:** [assets/css/StyleCliente.css L152-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L152-L179)

 [assets/css/StyleCliente.css L231-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L231-L235)

 [assets/css/StyleCliente.css L320-L324](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L320-L324)

 [assets/css/StyleAdmin.css L126-L131](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L131)

 [assets/css/StyleAdmin.css L303-L310](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L310)

---

## Dashboard Component Responsiveness

### Statistics Card Adaptation

The admin dashboard `.stat-card` components adapt their width across breakpoints:

| Viewport | Width | Behavior |
| --- | --- | --- |
| Desktop | `width: 30%` | Three cards per row |
| Tablet (≤768px) | `width: 80%` with `flex-direction: column` | Single column, centered |
| Mobile (≤480px) | `width: 100%` | Full width cards |

```

```

**Chart Canvas Sizing:**

* Desktop: [assets/css/StyleAdmin.css L164-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L164-L172)
* Tablet: [assets/css/StyleAdmin.css L312-L314](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L312-L314)
* Mobile: [assets/css/StyleAdmin.css L365-L367](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L365-L367)

**Sources:** [assets/css/StyleAdmin.css L133-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L133-L156)

 [assets/css/StyleAdmin.css L303-L310](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L310)

 [assets/css/StyleAdmin.css L353-L363](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L353-L363)

 [assets/css/StyleAdmin.css L164-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L164-L172)

 [assets/css/StyleAdmin.css L312-L314](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L312-L314)

 [assets/css/StyleAdmin.css L365-L367](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L365-L367)

---

## Typography Scaling System

### Hierarchical Font Size Reduction

The system applies consistent typography scaling across breakpoints:

```

```

**Typography Breakdowns by Interface:**

| Element | Desktop | Tablet (≤768px) | Mobile (≤480px) |
| --- | --- | --- | --- |
| `header h1` (Admin) | 24px | 20px | 18px |
| `header h1` (Cliente) | 28px | 24px | 20px |
| `section h2` (Admin) | 22px | No change | 20px |
| `section h2` (Cliente) | 26px | No change | 22px |
| `header .logo img` | 150px | No change | 120px |
| `.stat-card h3` | 16px | No change | 14px |
| `.stat-card p` | 24px | No change | 20px |

**Sources:** [assets/css/StyleAdmin.css L69-L71](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L69-L71)

 [assets/css/StyleAdmin.css L285-L287](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L285-L287)

 [assets/css/StyleAdmin.css L341-L343](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L341-L343)

 [assets/css/StyleCliente.css L58-L62](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L58-L62)

 [assets/css/StyleCliente.css L400-L402](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L400-L402)

 [assets/css/StyleCliente.css L440-L442](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L440-L442)

---

## Form and Input Element Responsiveness

### Form Component Sizing

Form elements scale down on smaller viewports to maintain touch-friendly targets while optimizing space:

```

```

**Admin Interface Forms:**

* Desktop: [assets/css/StyleAdmin.css L183-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L183-L210)
* Tablet: [assets/css/StyleAdmin.css L328-L333](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L328-L333)
* Mobile: No additional changes beyond tablet

**Cliente Booking Form:**

* Desktop: [assets/css/StyleCliente.css L260-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L294)
* Mobile: [assets/css/StyleCliente.css L448-L457](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L448-L457)

**Login Form:**

* Desktop: [assets/css/StyleLogin.css L86-L134](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L86-L134)
* Mobile: [assets/css/StyleLogin.css L184-L187](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L184-L187)

**Sources:** [assets/css/StyleAdmin.css L183-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L183-L210)

 [assets/css/StyleAdmin.css L328-L333](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L328-L333)

 [assets/css/StyleCliente.css L260-L294](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L294)

 [assets/css/StyleCliente.css L448-L457](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L448-L457)

 [assets/css/StyleLogin.css L86-L134](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L86-L134)

 [assets/css/StyleLogin.css L184-L187](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L184-L187)

---

## Table Responsive Strategies

### Data Table Adaptations

Tables in employee management, room lists, and guest tracking interfaces adapt through font size and padding reduction:

```

```

**Table Cell Padding Progression:**

| Interface | Desktop | Tablet (≤768px) | Mobile (≤480px) |
| --- | --- | --- | --- |
| Admin (Employee/Room) | `padding: 0.8em` | `padding: 0.5em` | `padding: 0.4em` |
| Receptionist (Guest/Room) | `padding: 12px` | `padding: 8px` | `padding: 6px` |
| Maid (Room) | `padding: 12px` | `padding: 8px` | `padding: 6px` |

**Admin Table Implementation:**

* Desktop: [assets/css/StyleAdmin.css L213-L267](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L267)
* Tablet: [assets/css/StyleAdmin.css L316-L326](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L316-L326)
* Mobile: [assets/css/StyleAdmin.css L369-L379](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L369-L379)

**Receptionist Table Implementation:**

* Desktop: [assets/css/StyleRecepcionista.css L165-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L249)
* Tablet: [assets/css/StyleRecepcionista.css L341-L351](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L341-L351)
* Mobile: [assets/css/StyleRecepcionista.css L387-L397](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L387-L397)

**Maid Table Implementation:**

* Desktop: [assets/css/StyleMucama.css L168-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L205)
* Tablet: [assets/css/StyleMucama.css L231-L238](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L231-L238)
* Mobile: [assets/css/StyleMucama.css L273-L280](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L273-L280)

**Sources:** [assets/css/StyleAdmin.css L213-L267](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L267)

 [assets/css/StyleAdmin.css L316-L326](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L316-L326)

 [assets/css/StyleAdmin.css L369-L379](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L369-L379)

 [assets/css/StyleRecepcionista.css L165-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L249)

 [assets/css/StyleRecepcionista.css L341-L351](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L341-L351)

 [assets/css/StyleRecepcionista.css L387-L397](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L387-L397)

 [assets/css/StyleMucama.css L168-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L168-L205)

 [assets/css/StyleMucama.css L231-L238](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L231-L238)

 [assets/css/StyleMucama.css L273-L280](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L273-L280)

---

## Room Type Card Responsiveness

### Card Component Scaling

The `.room-type-card` components in the client interface scale text and image content:

```

```

**Room Type Card Structure:**

* Base styles: [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)
* Mobile adjustments: [assets/css/StyleCliente.css L474-L485](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L474-L485)

**Card Image Sizing:**

* Fixed at `height: 200px` with `object-fit: cover` for all viewports
* Width: `100%` allows flexible container adaptation

**Sources:** [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)

 [assets/css/StyleCliente.css L474-L485](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L474-L485)

---

## Floating Button Responsiveness

### Social Media Button Scaling

The `.floating-btn` components for Instagram and WhatsApp scale down on smaller devices:

```

```

**Positioning and Size Progression:**

| Viewport | Button Size | Image Size | Position |
| --- | --- | --- | --- |
| Desktop | 50×50px | 30×30px | `bottom: 20px, right: 20px` |
| Tablet (≤768px) | 45×45px | 25×25px | `bottom: 15px, right: 15px` |
| Mobile (≤480px) | 40×40px | 20×20px | `bottom: 10px, right: 10px` |

**Implementation:**

* Desktop: [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)
* Tablet: [assets/css/StyleCliente.css L418-L432](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L418-L432)
* Mobile: [assets/css/StyleCliente.css L459-L472](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L459-L472)

**Sources:** [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

 [assets/css/StyleCliente.css L418-L432](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L418-L432)

 [assets/css/StyleCliente.css L459-L472](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L459-L472)

---

## Notification Component Responsiveness

### Notification Card Adaptations

Notification sections maintain consistent background colors but adjust padding and font sizes:

```

```

**Cliente Notifications:**

* Desktop: [assets/css/StyleCliente.css L114-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L114-L149)
* No tablet-specific changes
* Mobile text size adjustment implicit through parent scaling

**Receptionist Notifications:**

* Desktop: [assets/css/StyleRecepcionista.css L112-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L112-L156)
* Tablet button adjustments: [assets/css/StyleRecepcionista.css L353-L359](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L353-L359)
* Mobile: [assets/css/StyleRecepcionista.css L379-L385](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L379-L385)

**Maid Notifications:**

* Desktop: [assets/css/StyleMucama.css L112-L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L112-L143)
* Tablet button adjustments: [assets/css/StyleMucama.css L240-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L240-L245)
* Mobile: [assets/css/StyleMucama.css L265-L271](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L265-L271)

**Sources:** [assets/css/StyleCliente.css L114-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L114-L149)

 [assets/css/StyleRecepcionista.css L112-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L112-L156)

 [assets/css/StyleRecepcionista.css L353-L359](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L353-L359)

 [assets/css/StyleRecepcionista.css L379-L385](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L379-L385)

 [assets/css/StyleMucama.css L112-L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L112-L143)

 [assets/css/StyleMucama.css L240-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L240-L245)

 [assets/css/StyleMucama.css L265-L271](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L265-L271)

---

## Dialog/Modal Responsiveness

### Dialog Element Adaptations

The receptionist interface uses native `<dialog>` elements that scale for mobile:

```

```

**Dialog Form Elements:**

* Desktop: [assets/css/StyleRecepcionista.css L252-L316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L316)
* Tablet adjustments: [assets/css/StyleRecepcionista.css L353-L359](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L353-L359)
* Mobile adjustments: [assets/css/StyleRecepcionista.css L399-L406](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L399-L406)

**Sources:** [assets/css/StyleRecepcionista.css L252-L316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L316)

 [assets/css/StyleRecepcionista.css L353-L359](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L353-L359)

 [assets/css/StyleRecepcionista.css L399-L406](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L399-L406)

---

## Section Padding and Spacing System

### Vertical Rhythm Adjustments

Section padding reduces progressively across breakpoints to maximize content visibility:

```

```

**Admin Interface:**

* Desktop `main`: [assets/css/StyleAdmin.css L100-L102](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L100-L102)
* Tablet `main`: [assets/css/StyleAdmin.css L299-L301](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L299-L301)

**Cliente Interface:**

* Desktop `section`: [assets/css/StyleCliente.css L87-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L87-L91)
* Tablet `section`: [assets/css/StyleCliente.css L409-L411](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L409-L411)

**Receptionist/Maid Interfaces:**

* Desktop `main`: [assets/css/StyleRecepcionista.css L86-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L86-L90)  / [assets/css/StyleMucama.css L86-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L86-L90)
* Tablet `main`: [assets/css/StyleRecepcionista.css L337-L339](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L337-L339)  / [assets/css/StyleMucama.css L227-L229](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleMucama.css#L227-L229)

**Sources:** [assets/css/StyleAdmin.css L100-L102](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L100-L102)

 [assets/css/StyleAdmin.css L299-L301](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L299-L301)

 [assets/css/StyleCliente.css L87-L91](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L87-L91)

 [assets/css/StyleCliente.css L409-L411](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L409-L411)

 [assets/css/StyleRecepcionista.css L86-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L86-L90)

 [assets/css/StyleRecepcionista.css L337-L339](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L337-L339)

---

## Responsive Design Decision Tree

The following diagram illustrates the CSS cascade and decision flow for responsive styling:

```

```

**Sources:** All CSS files demonstrate this cascading pattern through their media query organization.

---

## Summary of Responsive Techniques

The Hotel Benedetti system achieves responsive design through:

1. **Consistent Breakpoints**: Two breakpoints (768px, 480px) applied uniformly across all interfaces
2. **CSS Grid Auto-Fit**: `repeat(auto-fit, minmax(300px, 1fr))` enables automatic column reflow
3. **Flexbox Direction Switching**: Navigation changes from `row` to `column` on tablets
4. **Progressive Typography Scaling**: Headers and body text reduce 10-20% at each breakpoint
5. **Padding/Spacing Reduction**: 20-25% reduction in whitespace at tablet, further 10-15% at mobile
6. **Table Optimization**: Font size and padding adjustments maintain readability while saving space
7. **Touch Target Maintenance**: Buttons remain at least 40px×40px for mobile usability
8. **Unified Background Strategy**: Fixed background with overlay consistent across all viewports

This mobile-first approach ensures optimal user experience without requiring separate mobile templates or JavaScript-based layout switching.