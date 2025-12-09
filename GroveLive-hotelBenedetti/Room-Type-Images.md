# Room Type Images

> **Relevant source files**
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/img/doble.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg)
> * [assets/img/individual.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg)
> * [assets/img/suite.jpg](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/suite.jpg)
> * [assets/js/cliente.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js)

## Purpose and Scope

This document catalogs the room type imagery assets used in the Hotel Benedetti client-facing interface to showcase available accommodations. These images provide visual representations of the three room categories (individual, double, and suite) displayed to guests browsing the hotel's offerings.

For information about background images used across different interfaces, see [Background Images](/GroveLive/hotelBenedetti/9.1-background-images). For branding assets like logos and social media icons, see [Branding and Icons](/GroveLive/hotelBenedetti/9.3-branding-and-icons).

---

## Image Asset Inventory

The system includes three primary room type images stored in the `assets/img/` directory:

| Asset File | Room Type | Purpose | Dimensions |
| --- | --- | --- | --- |
| `individual.jpg` | Single/Individual Room | Displays single occupancy accommodations | Standard JPEG |
| `doble.jpg` | Double/Twin Room | Displays double occupancy accommodations | Standard JPEG |
| `suite.jpg` | Suite | Displays premium suite accommodations | Standard JPEG |

**Sources:** [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/suite.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/suite.jpg#L1-L1)

---

## Asset Integration Flow

The following diagram illustrates how room type images flow from the file system through the CSS styling layer to the rendered client interface:

```

```

**Sources:** [assets/css/StyleCliente.css L174-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L174-L228)

 [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/suite.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/suite.jpg#L1-L1)

---

## CSS Styling Implementation

The room type cards utilize a dedicated CSS styling system defined in `StyleCliente.css`. The styling provides consistent presentation, responsive behavior, and interactive hover effects.

### Grid Container

The room type cards are displayed in a responsive CSS Grid layout:

```

```

This configuration ensures cards automatically wrap to new rows based on viewport width, with each card maintaining a minimum width of 300px.

**Sources:** [assets/css/StyleCliente.css L175-L179](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L175-L179)

### Card Structure

Each room type card contains:

* An image element with standardized dimensions
* Room type heading
* Descriptive text
* Optional action button

```

```

**Sources:** [assets/css/StyleCliente.css L181-L188](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L188)

### Image Specifications

Room type images are styled with specific constraints to maintain visual consistency:

| Property | Value | Purpose |
| --- | --- | --- |
| `width` | 100% | Fills card container width |
| `height` | 200px | Fixed height for uniform appearance |
| `object-fit` | cover | Crops/scales image to fill dimensions |
| `border-radius` | 8px | Rounded corners matching card style |
| `margin-bottom` | 15px | Spacing between image and text |

**Sources:** [assets/css/StyleCliente.css L194-L200](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L194-L200)

### Interaction States

The cards feature a hover effect that lifts them visually:

```

```

This provides tactile feedback when users interact with room type selections.

**Sources:** [assets/css/StyleCliente.css L190-L192](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L190-L192)

---

## Room Type to Image Mapping

The following diagram shows the relationship between room types stored in the database and their corresponding image assets:

```

```

**Sources:** [assets/css/StyleCliente.css L174-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L174-L228)

---

## Responsive Design Behavior

The room type image presentation adapts across different viewport sizes using the responsive grid system and mobile-specific styling adjustments.

### Desktop Layout (> 768px)

* Grid displays up to 3 cards per row (based on available width)
* Images maintain 200px height
* Full padding and spacing applied

### Tablet Layout (≤ 768px)

* Grid adjusts to 2 cards per row or single column
* Images maintain consistent aspect ratio
* No specific overrides needed due to `auto-fit` grid behavior

### Mobile Layout (≤ 480px)

Mobile-specific adjustments are applied to optimize for smaller screens:

| Element | Adjustment | CSS Line |
| --- | --- | --- |
| `.room-type-card h3` | `font-size: 18px` | 474-476 |
| `.room-type-card p` | `font-size: 13px` | 478-480 |
| `.room-details-btn` | Reduced padding: `8px 12px`, `font-size: 14px` | 482-485 |

**Sources:** [assets/css/StyleCliente.css L435-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L435-L486)

---

## Usage Context in Client Interface

Room type images are displayed in the `#room-types` section of the client interface (`cliente.php`). This section serves as a static showcase of accommodation options, distinct from the dynamic room availability display.

### Distinction from Dynamic Room Listings

| Feature | Room Type Cards (Static) | Available Rooms (Dynamic) |
| --- | --- | --- |
| **Purpose** | Showcase accommodation types | Display bookable inventory |
| **Data Source** | Static HTML with hardcoded images | `HabitacionController.php` API |
| **Section ID** | `#room-types` | `#rooms` |
| **JavaScript** | None required | `loadRooms()` function |
| **Images** | Room type images (`individual.jpg`, etc.) | None used |
| **Content** | Fixed marketing content | Real-time availability data |

**Sources:** [assets/css/StyleCliente.css L174-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L174-L228)

 [assets/js/cliente.js L15-L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/cliente.js#L15-L41)

---

## File Format and Technical Specifications

All room type images are stored as JPEG files with the following characteristics:

### JPEG Metadata

Based on EXIF data present in the files:

* **Format:** JFIF (JPEG File Interchange Format)
* **Color Space:** RGB
* **Compression:** Standard JPEG compression
* **Resolution:** Varies by image (original dimensions preserved)

### Browser Rendering

The CSS `object-fit: cover` property ensures:

1. Images fill the 200px height constraint
2. Aspect ratio is maintained
3. Images are center-cropped if necessary
4. No distortion occurs during scaling

**Sources:** [assets/css/StyleCliente.css L194-L200](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L194-L200)

 [assets/img/individual.jpg L1-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L6)

 [assets/img/doble.jpg L1-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L6)

 [assets/img/suite.jpg L1-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/suite.jpg#L1-L6)

---

## Color Scheme Integration

Room type cards integrate with the system-wide dark theme established in the visual design system (see [Color Palette and Theming](/GroveLive/hotelBenedetti/8.1-color-palette-and-theming)):

| Element | Color | Variable/Value | Purpose |
| --- | --- | --- | --- |
| Card Background | Dark Gray | `#36393f` | Primary card surface |
| Text (Headings) | Light Gray | `#dcddde` | High contrast titles |
| Text (Descriptions) | Medium Gray | `#b0b3b8` | Secondary content |
| Action Button | Purple | `#9b59b6` | Brand color for CTAs |
| Button Hover | Dark Purple | `#8e44ad` | Interactive state |

The room images provide visual contrast against these dark backgrounds, creating visual focus on the photography.

**Sources:** [assets/css/StyleCliente.css L181-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L181-L228)

---

## Implementation Pattern

The room type image system follows a simple static asset pattern:

```

```

Unlike the dynamic room availability system which uses `HabitacionController.php` and the `loadRooms()` function, room type images are statically referenced in the HTML markup and require no JavaScript interaction.

**Sources:** [assets/css/StyleCliente.css L174-L228](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L174-L228)

---

## Maintenance and Updates

To update or replace room type images:

1. **Prepare new image files:** * Save as JPEG format * Recommended minimum width: 600px for retina displays * Aspect ratio: Approximately 3:2 or 4:3 works well with `object-fit: cover`
2. **Replace files in directory:** * Upload to `assets/img/` * Maintain existing filenames: `individual.jpg`, `doble.jpg`, `suite.jpg` * No code changes required if filenames remain consistent
3. **Clear browser cache:** * Users may need to hard-refresh to see new images * Consider versioning strategy for cache busting if needed

**Sources:** [assets/img/individual.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/individual.jpg#L1-L1)

 [assets/img/doble.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/doble.jpg#L1-L1)

 [assets/img/suite.jpg L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/img/suite.jpg#L1-L1)