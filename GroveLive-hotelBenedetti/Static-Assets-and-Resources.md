# Static Assets and Resources

> **Relevant source files**
> * [assets/img/doble.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg)
> * [assets/img/fondo.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg)
> * [assets/img/fondo2.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg)
> * [assets/img/fondohotelcliente2.webp](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp)
> * [assets/img/individual.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg)
> * [assets/img/instagram-logo.png](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png)
> * [assets/img/logohotel.png](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png)
> * [assets/img/suite.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/suite.jpg)
> * [assets/img/whatsapp-logo.png](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/whatsapp-logo.png)

## Purpose and Scope

This document catalogs the static image assets used throughout the Hotel Benedetti management system. These assets are stored in the `assets/img/` directory and include background images, room type photographs, branding elements, and social media icons. This page provides a comprehensive inventory of all image resources, their locations, file formats, and usage contexts across the different user interfaces.

For information about how these assets are styled and positioned using CSS, see [CSS Styling System](/GroveLive/hotelBenedetti/5.1-css-styling-system). For details on how client-side JavaScript loads and displays these images dynamically, see [JavaScript Client-Side Logic](/GroveLive/hotelBenedetti/5.2-javascript-client-side-logic).

## Asset Directory Structure

The Hotel Benedetti system organizes all static image assets within a centralized directory structure:

```

```

**Sources:** [assets/img/fondo.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg#L1-L1)

 [assets/img/fondo2.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg#L1-L1)

 [assets/img/fondohotelcliente2.webp L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L1)

 [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/logohotel.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png#L1-L1)

 [assets/img/instagram-logo.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png#L1-L1)

## Asset Usage by Interface

Different user interfaces in the system utilize different combinations of these static assets to create role-appropriate visual experiences:

```

```

**Sources:** [assets/img/fondo.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg#L1-L1)

 [assets/img/fondo2.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg#L1-L1)

 [assets/img/fondohotelcliente2.webp L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L1)

 [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/logohotel.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png#L1-L1)

 [assets/img/instagram-logo.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png#L1-L1)

## Background Images

The system employs three primary background images to create visual depth and establish distinct atmospheres for different user contexts:

### Primary Background (fondo.jpg)

* **File Path:** `assets/img/fondo.jpg`
* **Format:** JPEG
* **Usage Context:** Employee interfaces (Admin, Receptionist, Maid)
* **Purpose:** Provides a professional, neutral background for operational interfaces
* **Implementation:** Referenced in CSS files via `background-image` property with overlay effects to ensure text readability

### Secondary Background (fondo2.jpg)

* **File Path:** `assets/img/fondo2.jpg`
* **Format:** JPEG
* **Usage Context:** Public-facing login and index pages
* **Purpose:** Creates an inviting first impression for visitors
* **Implementation:** Applied to login and landing page containers with z-index layering

### Client Interface Background (fondohotelcliente2.webp)

* **File Path:** `assets/img/fondohotelcliente2.webp`
* **Format:** WebP (optimized for web performance)
* **Usage Context:** Guest-facing client interface
* **Purpose:** Provides a luxurious, hospitality-themed backdrop for the booking experience
* **Implementation:** Specifically optimized format for faster loading on client-facing pages

**Sources:** [assets/img/fondo.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg#L1-L1)

 [assets/img/fondo2.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg#L1-L1)

 [assets/img/fondohotelcliente2.webp L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L1)

## Room Type Images

Room type images are displayed to clients during the browsing and booking process. These photographs showcase the accommodations available at Hotel Benedetti:

| Asset File | Room Type | Dimensions | Format | Usage |
| --- | --- | --- | --- | --- |
| `individual.jpg` | Single Room | Standard | JPEG | Displayed in room browsing gallery |
| `doble.jpg` | Double Room | Standard | JPEG | Displayed in room browsing gallery |

```

```

These images are dynamically loaded by the client-side JavaScript when users browse available rooms. The image selection corresponds to the `tipo` field in the `Habitacion2` database model, which stores room type information.

**Sources:** [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

## Branding and Icons

### Hotel Logo (logohotel.png)

* **File Path:** `assets/img/logohotel.png`
* **Format:** PNG with transparency
* **Dimensions:** 64x64 pixels
* **Usage Contexts:** * Header/navigation bars across all interfaces * Login page branding * Favicon reference
* **Color Space:** sRGB
* **Transparency:** Alpha channel enabled for flexible placement over various backgrounds

### Social Media Icons (instagram-logo.png)

* **File Path:** `assets/img/instagram-logo.png`
* **Format:** PNG with transparency
* **Dimensions:** 39x39 pixels
* **Usage Contexts:** * Footer social media links * Client interface external link sections * Marketing and contact pages
* **Purpose:** Provides external link to hotel's Instagram profile

```

```

**Sources:** [assets/img/logohotel.png L1-L3](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png#L1-L3)

 [assets/img/instagram-logo.png L1-L11](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png#L1-L11)

## CSS Asset References

Static assets are referenced throughout the CSS styling system using relative paths. The typical reference pattern from CSS files is:

| CSS File | Reference Pattern | Example Assets |
| --- | --- | --- |
| `StyleAdmin.css` | `url('../assets/img/...')` | `fondo.jpg`, `logohotel.png` |
| `StyleCliente.css` | `url('../assets/img/...')` | `fondohotelcliente2.webp`, `individual.jpg`, `doble.jpg` |
| `StyleLogin.css` | `url('../assets/img/...')` | `fondo2.jpg`, `logohotel.png` |
| `StyleRecepcionista.css` | `url('../assets/img/...')` | `fondo.jpg`, `logohotel.png` |
| `StyleMucama.css` | `url('../assets/img/...')` | `fondo.jpg`, `logohotel.png` |

Common CSS implementation patterns include:

1. **Background Image Application:** * `background-image: url('../assets/img/fondo.jpg')` * `background-size: cover` * `background-position: center` * `background-attachment: fixed`
2. **Overlay Effects:** * Semi-transparent overlays using `::before` pseudo-elements * Gradient overlays for improved text contrast * Z-index layering to separate content from backgrounds
3. **Logo Placement:** * Header logos using `<img>` tags with `src` attribute * Flexbox positioning for responsive alignment * Consistent sizing across interfaces

**Sources:** [assets/img/fondo.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg#L1-L1)

 [assets/img/fondo2.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg#L1-L1)

 [assets/img/fondohotelcliente2.webp L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L1)

 [assets/img/logohotel.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png#L1-L1)

 [assets/img/instagram-logo.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png#L1-L1)

## File Format Specifications

The system uses a mix of image formats optimized for different purposes:

```

```

### Format Selection Rationale

* **JPEG** is used for photographic content (backgrounds, room images) where file size reduction is important and transparency is not needed
* **PNG** is used for branding elements (logos, icons) requiring transparency and crisp edges
* **WebP** is used for the client interface background to optimize loading performance for guest-facing pages

**Sources:** [assets/img/fondo.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg#L1-L1)

 [assets/img/fondo2.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg#L1-L1)

 [assets/img/fondohotelcliente2.webp L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L1)

 [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/logohotel.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png#L1-L1)

 [assets/img/instagram-logo.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png#L1-L1)

## Asset Loading and Performance

The static assets are loaded through different mechanisms depending on the usage context:

1. **CSS Background Images:** Loaded automatically when stylesheets are applied to DOM elements
2. **HTML Image Tags:** Direct `<img>` references for logos and icons
3. **JavaScript Dynamic Loading:** Room images loaded via `cliente.js` based on database queries

Performance considerations:

* Background images use CSS `background-size: cover` for responsive scaling
* WebP format provides ~30% file size reduction compared to equivalent JPEG quality
* PNG logos are kept small (64x64, 39x39) to minimize load times
* All images are served from the same domain, avoiding cross-origin loading delays

**Sources:** [assets/img/fondo.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo.jpg#L1-L1)

 [assets/img/fondo2.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondo2.jpg#L1-L1)

 [assets/img/fondohotelcliente2.webp L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/fondohotelcliente2.webp#L1-L1)

 [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/logohotel.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/logohotel.png#L1-L1)

 [assets/img/instagram-logo.png L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/instagram-logo.png#L1-L1)