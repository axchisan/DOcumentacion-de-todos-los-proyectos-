# Background Images

> **Relevant source files**
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/img/fondo.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg)
> * [assets/img/fondo2.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg)
> * [assets/img/fondohotelcliente2.webp](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp)
> * [assets/img/hotelfondocliente.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/hotelfondocliente.jpg)
> * [assets/img/loginfondo.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/loginfondo.jpg)

## Purpose and Scope

This page documents the background image assets used throughout the Hotel Benedetti system to create visual depth and atmosphere in the user interfaces. It covers the image files themselves, the CSS layering technique used to ensure content readability, and the z-index hierarchy that manages visual stacking.

For information about other image assets like room type images and branding, see [Room Type Images](/GroveLive/hotelBenedetti/9.2-room-type-images) and [Branding and Icons](/GroveLive/hotelBenedetti/9.3-branding-and-icons). For CSS styling patterns in general, see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system).

---

## Background Image Assets

The system uses three primary background image files stored in the `assets/img/` directory:

| File Name | Format | Primary Usage |
| --- | --- | --- |
| `fondo.jpg` | JPEG | Alternative background option |
| `fondo2.jpg` | JPEG | Active background for client interface |
| `fondohotelcliente2.webp` | WebP | Modern format alternative |

The currently active background image is `fondo2.jpg`, which is referenced in the client interface stylesheet. These images typically depict hotel-related scenes or atmospheric photography that reinforces the hospitality brand identity.

**Sources:** [assets/img/fondo.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg)

 [assets/img/fondo2.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg)

 [assets/img/fondohotelcliente2.webp](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp)

---

## Layered Background Implementation

The system employs a sophisticated three-layer approach to background image display that ensures content remains readable while maintaining visual appeal:

```mermaid
flowchart TD

L1["Layer 1 (z-index: auto)<br>Background Image<br>fondo2.jpg"]
L2["Layer 2 (z-index: 1)<br>Dark Overlay<br>rgba(35, 39, 42, 0.8)"]
L3["Layer 3 (z-index: 2)<br>Content Elements<br>header, section, footer"]
CSS["body<br>background-image property"]
Pseudo["body::before<br>pseudo-element"]
Content["body > *<br>direct children"]

CSS --> L1
Pseudo --> L2
Content --> L3

subgraph subGraph0 ["Visual Layers (Z-Index Hierarchy)"]
    L1
    L2
    L3
    L1 --> L2
    L2 --> L3
end
```

**Diagram: Background Layering Architecture**

This three-tier structure creates visual depth while preventing the background image from interfering with text legibility. The overlay opacity (0.8) darkens the image by 80%, allowing subtle texture to show through while maintaining the dark theme aesthetic.

**Sources:** [assets/css/StyleCliente.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L37)

---

## CSS Implementation Details

### Base Background Configuration

The background image is applied to the `body` element with properties that ensure full coverage and fixed positioning:

```mermaid
classDiagram
    class body {
        +background-size: cover
        +background-position: center
        +background-repeat: no-repeat
        +background-attachment: fixed
        +color: #dcddde
        +min-height: 100vh
        +background-image: url('../img/fondo2.jpg')
    }
    class bodyBefore {
        +content: ''
        +position: absolute
        +width: 100%
        +height: 100%
        +z-index: 1
        +background-color: rgba(35, 39, 42, 0.8)
    }
    class bodyChildren {
        +position: relative
        +z-index: 2
    }
    body --> bodyBefore : creates overlay
    body --> bodyChildren : positions above
```

**Diagram: CSS Entity Relationships for Background System**

Key properties explained:

* **`background-size: cover`** - Scales the image to cover the entire viewport while maintaining aspect ratio
* **`background-position: center`** - Centers the focal point of the image
* **`background-attachment: fixed`** - Creates a parallax effect as content scrolls over the fixed background
* **`min-height: 100vh`** - Ensures the body spans at least the full viewport height

**Sources:** [assets/css/StyleCliente.css L8-L19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L19)

---

## Overlay Technique

The semi-transparent overlay is implemented using the `::before` pseudo-element:

```mermaid
flowchart TD

A["body element"]
B["::before pseudo-element"]
C["Absolute positioning"]
D["100% width & height"]
E["rgba(35, 39, 42, 0.8)<br>80% opacity dark overlay"]
F["z-index: 1<br>Above background"]
G["Visual Effect"]
H["Darkens background image"]
I["Maintains #dcddde text legibility"]
J["Preserves dark theme (#23272a)"]

A --> B
B --> C
B --> D
B --> E
B --> F
G --> H
G --> I
G --> J
```

**Diagram: Overlay Implementation Flow**

The overlay uses `rgba(35, 39, 42, 0.8)`, where:

* **RGB values (35, 39, 42)** match the primary dark background color `#23272a` from the color palette
* **Alpha value (0.8)** provides 80% opacity, creating subtle texture visibility
* **Absolute positioning** allows it to cover the entire viewport without affecting layout

**Sources:** [assets/css/StyleCliente.css L21-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L21-L31)

---

## Z-Index Hierarchy Management

The system uses explicit z-index values to manage the visual stacking order:

```mermaid
flowchart TD

Z0["z-index: auto<br>Background Image Layer<br>body background-image"]
Z1["z-index: 1<br>Overlay Layer<br>body::before"]
Z2["z-index: 2<br>Content Layer<br>All direct body children"]
Z1000["z-index: 1000<br>Floating Buttons<br>.floating-buttons"]
Elements["header, section, footer"]
Buttons[".floating-btn"]

Elements --> Z2
Buttons --> Z1000

subgraph subGraph0 ["Z-Index Stacking Context"]
    Z0
    Z1
    Z2
    Z1000
    Z0 --> Z1
    Z1 --> Z2
    Z2 --> Z1000
end
```

**Diagram: Complete Z-Index Hierarchy**

The z-index hierarchy ensures:

1. Background image appears at the bottom (implicit z-index: auto)
2. Dark overlay sits above background (z-index: 1)
3. All page content (header, sections, footer) renders above overlay (z-index: 2)
4. Floating social media buttons appear above all content (z-index: 1000)

This explicit positioning is required because the overlay uses `position: absolute`, which would otherwise cover content elements.

**Sources:** [assets/css/StyleCliente.css L22-L348](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L22-L348)

---

## Content Positioning Strategy

All direct children of the `body` element receive explicit positioning to ensure they appear above the overlay:

```mermaid
flowchart TD

Body["body element"]
Header["header"]
Sections["section elements"]
Footer["footer"]
FloatingBtns[".floating-buttons"]
CSS["position: relative<br>z-index: 2"]
Selector["body > * selector"]

subgraph subGraph0 ["Body Children Positioning"]
    Body
    Header
    Sections
    Footer
    FloatingBtns
    CSS
    Selector
    Body --> Header
    Body --> Sections
    Body --> Footer
    Body --> FloatingBtns
    CSS --> Selector
    Selector --> Header
    Selector --> Sections
    Selector --> Footer
end
```

**Diagram: Content Element Positioning**

The universal child selector `body > *` applies `position: relative` and `z-index: 2` to all direct children, ensuring they participate in the stacking context and render above the overlay without requiring individual positioning declarations.

**Sources:** [assets/css/StyleCliente.css L33-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L33-L37)

---

## Background Properties Reference

The following table summarizes all background-related CSS properties applied to the client interface:

| Property | Value | Purpose |
| --- | --- | --- |
| `background-image` | `url('../img/fondo2.jpg')` | References the background image file |
| `background-size` | `cover` | Scales image to cover viewport |
| `background-position` | `center` | Centers the image focal point |
| `background-repeat` | `no-repeat` | Prevents tiling of the image |
| `background-attachment` | `fixed` | Creates parallax scrolling effect |
| `color` | `#dcddde` | Sets default text color for readability |
| `min-height` | `100vh` | Ensures full viewport coverage |

**Sources:** [assets/css/StyleCliente.css L8-L19](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L19)

---

## Responsive Behavior

The background image system maintains its visual effect across all device sizes. Unlike many responsive elements in the system, the background configuration does not change at different breakpoints (768px and 480px). The `background-size: cover` and `background-position: center` properties ensure the image adapts naturally to different viewport dimensions without requiring media query overrides.

The fixed attachment (`background-attachment: fixed`) may behave differently on mobile devices due to browser limitations, but the core layering and overlay approach remains consistent across all screen sizes.

**Sources:** [assets/css/StyleCliente.css L8-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L486)

---

## Usage Across Interfaces

While this documentation focuses on the client interface implementation in `StyleCliente.css`, the background image technique can be applied to other user interfaces. Currently, the background system is primarily active in:

* **Client Interface** (`cliente.php`) - Uses `fondo2.jpg` with the full layering system
* Other interfaces (Admin, Receptionist, Maid) use solid color backgrounds without images

The pattern established here can serve as a template for adding atmospheric backgrounds to other interfaces while maintaining content readability through the overlay technique.

**Sources:** [assets/css/StyleCliente.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L8-L37)