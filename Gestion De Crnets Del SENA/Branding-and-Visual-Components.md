# Branding and Visual Components

> **Relevant source files**
> * [assets/images/sena_logo.png](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/assets/images/sena_logo.png)
> * [lib/screens/id_card_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart)
> * [lib/screens/splash_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart)
> * [lib/widgets/sena_logo.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart)

## Purpose and Scope

This document covers the visual branding components that establish the SENA institutional identity throughout the application. These components include the SENA logo widget and barcode/QR code generators used for identification purposes.

For custom form input widgets, see [Custom Form Widgets](/axchisan/AppGestionCarnetsSENA/6.1-custom-form-widgets). For theming, color schemes, and styling patterns, see [Theme and Styling](/axchisan/AppGestionCarnetsSENA/6.3-theme-and-styling).

## Overview

The application implements a consistent visual brand identity through reusable widgets that display institutional branding (SENA logo) and identification elements (barcodes). These components are used across multiple screens to maintain visual consistency and reinforce the application's institutional affiliation.

```

```

**Sources:** [lib/widgets/sena_logo.dart L1-L64](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L1-L64)

 [lib/screens/splash_screen.dart L1-L90](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L1-L90)

 [lib/screens/id_card_screen.dart L1-L194](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L1-L194)

## SENA Logo Widget

The `SenaLogo` widget provides a standardized, reusable component for displaying the SENA institutional logo throughout the application. It loads the logo image from assets and provides a fallback mechanism if the asset fails to load.

### Widget Definition

| Component | Type | Location |
| --- | --- | --- |
| **Class Name** | `SenaLogo` | [lib/widgets/sena_logo.dart L4-L64](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L4-L64) |
| **Parent Class** | `StatelessWidget` | - |
| **Asset Path** | `assets/images/sena_logo.png` | [lib/widgets/sena_logo.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L19-L19) |

### Constructor Parameters

The `SenaLogo` widget accepts the following parameters:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `width` | `double` | `150` | Width of the logo in logical pixels |
| `height` | `double` | `50` | Height of the logo in logical pixels |
| `showShadow` | `bool` | `false` | Whether to display a drop shadow around the logo |

**Sources:** [lib/widgets/sena_logo.dart L5-L14](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L5-L14)

### Asset Loading Strategy

The widget implements a robust asset loading mechanism with error handling:

```

```

**Sources:** [lib/widgets/sena_logo.dart L18-L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L18-L62)

The error handler provides a fallback UI that:

* Creates a green container matching SENA brand colors ([lib/widgets/sena_logo.dart L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L29-L29) )
* Displays "SENA" text at 40% of the specified height ([lib/widgets/sena_logo.dart L36-L38](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L36-L38) )
* Maintains the same dimensions as the requested logo size ([lib/widgets/sena_logo.dart L26-L27](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L26-L27) )

### Shadow Decoration

When `showShadow` is set to `true`, the widget wraps the logo in a `Container` with the following shadow properties:

| Property | Value | Purpose |
| --- | --- | --- |
| **Color** | `Colors.black.withOpacity(0.1)` | 10% black transparency for subtle shadow |
| **Blur Radius** | `8` | Soft shadow edge |
| **Offset** | `Offset(0, 2)` | 2 pixels downward, creating depth |
| **Border Radius** | `8` | Rounded corners matching logo container |

**Sources:** [lib/widgets/sena_logo.dart L46-L59](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L46-L59)

## Usage Across Screens

The `SenaLogo` widget is consistently used across multiple screens with different size configurations to establish visual hierarchy and brand presence.

### Screen-Specific Implementations

```

```

**Sources:** [lib/screens/splash_screen.dart L43-L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L43-L62)

 [lib/screens/id_card_screen.dart L75-L78](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L75-L78)

### SplashScreen Implementation

The splash screen features the largest and most prominent logo display:

* **Dimensions:** 170x170 pixels ([lib/screens/splash_screen.dart L59-L60](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L59-L60) )
* **Container:** White background with 250x250 dimensions and 40 pixels padding ([lib/screens/splash_screen.dart L43-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L43-L47) )
* **Decoration:** Elevated card effect with shadow and rounded corners ([lib/screens/splash_screen.dart L50-L56](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L50-L56) )
* **Context:** Centered on screen with "Carnet Digital" heading below ([lib/screens/splash_screen.dart L64-L79](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L64-L79) )

This configuration creates a strong initial brand impression during the 2-second loading period.

### IdCardScreen Implementation

The ID card screen uses a more compact logo placement:

* **Dimensions:** 110x80 pixels ([lib/screens/id_card_screen.dart L76-L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L76-L77) )
* **Position:** Top-left corner of the virtual ID card ([lib/screens/id_card_screen.dart L71-L78](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L71-L78) )
* **Layout Context:** Appears opposite the user's profile photo in a two-column header ([lib/screens/id_card_screen.dart L71-L101](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L71-L101) )
* **Purpose:** Institutional validation indicator on the digital credential

**Sources:** [lib/screens/splash_screen.dart L36-L89](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L36-L89)

 [lib/screens/id_card_screen.dart L41-L173](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L41-L173)

## Barcode Generation Components

The application uses barcode components to encode and display the apprentice's identification number in machine-readable format on the virtual ID card.

### BarcodeGenerator Widget

The `BarcodeGenerator` widget is responsible for rendering barcodes from identification data. While the implementation file is not provided in the current context, its usage can be inferred from the ID card screen.

| Property | Type | Description |
| --- | --- | --- |
| **Import Path** | `../widgets/barcode_generator.dart` | Widget location |
| **Data Input** | `String` | Identification number to encode |
| **Width** | `double` | Barcode display width |
| **Height** | `double` | Barcode display height |

**Sources:** [lib/screens/id_card_screen.dart L6](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L6-L6)

### ID Card Integration

```

```

**Sources:** [lib/screens/id_card_screen.dart L156-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L156-L169)

The barcode is integrated into the ID card layout as follows:

1. **Data Source:** Uses `aprendiz.idIdentificacion` field, with "N/A" fallback ([lib/screens/id_card_screen.dart L157](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L157-L157) )
2. **Dimensions:** 200 pixels wide by 60 pixels tall ([lib/screens/id_card_screen.dart L158-L159](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L158-L159) )
3. **Position:** Bottom section of the ID card, below user information ([lib/screens/id_card_screen.dart L155-L161](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L155-L161) )
4. **Label:** Human-readable ID text displayed immediately below the barcode in monospace font ([lib/screens/id_card_screen.dart L162-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L162-L169) )

### External Package Dependencies

The barcode functionality relies on external Flutter packages:

| Package | Purpose | Configuration |
| --- | --- | --- |
| `barcode_widget` | Standard barcode generation (Code128, Code39, etc.) | Defined in pubspec.yaml |
| `qr_flutter` | QR code generation capabilities | Defined in pubspec.yaml |

These packages provide the underlying rendering engines for barcode generation, while the `BarcodeGenerator` widget provides a consistent interface for use throughout the application.

**Sources:** Referenced in high-level system diagrams

## Component Composition on ID Card

The ID card screen demonstrates how branding and identification components work together to create a cohesive digital credential:

```

```

**Sources:** [lib/screens/id_card_screen.dart L48-L173](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L48-L173)

### Visual Hierarchy

The ID card establishes clear visual hierarchy through component sizing and placement:

1. **Institutional Authority:** SENA logo (110x80) establishes authenticity ([lib/screens/id_card_screen.dart L75-L78](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L75-L78) )
2. **Personal Identity:** Profile photo (100x100) identifies the individual ([lib/screens/id_card_screen.dart L79-L100](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L79-L100) )
3. **Textual Information:** Name, ID, program details in descending text sizes ([lib/screens/id_card_screen.dart L104-L153](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L104-L153) )
4. **Machine-Readable ID:** Barcode provides scanning functionality ([lib/screens/id_card_screen.dart L156-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L156-L169) )

### Container Styling

The ID card container uses branding colors and styling:

* **Border:** 4-pixel SENA green border for institutional branding ([lib/screens/id_card_screen.dart L53-L56](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L53-L56) )
* **Background:** White background for contrast and readability ([lib/screens/id_card_screen.dart L52](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L52-L52) )
* **Shadow:** Subtle elevation effect for depth ([lib/screens/id_card_screen.dart L58-L63](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L58-L63) )
* **Border Radius:** 16-pixel rounded corners for modern appearance ([lib/screens/id_card_screen.dart L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L57-L57) )

**Sources:** [lib/screens/id_card_screen.dart L48-L65](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L48-L65)

## Asset Management

The application follows Flutter's standard asset management approach for loading branding imagery.

### Asset Declaration

While the pubspec.yaml file is not included in the provided files, the asset reference indicates the following structure:

```
assets/
  images/
    sena_logo.png
```

The logo asset is referenced as `'assets/images/sena_logo.png'` in [lib/widgets/sena_logo.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L19-L19)

### Error Handling Strategy

The `SenaLogo` widget implements a defensive programming approach to asset loading:

1. **Primary Attempt:** Load image from asset bundle using `Image.asset()`
2. **Error Detection:** Use `errorBuilder` callback to detect loading failures ([lib/widgets/sena_logo.dart L23](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L23-L23) )
3. **Fallback Rendering:** Display styled container with text when asset is unavailable ([lib/widgets/sena_logo.dart L25-L42](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L25-L42) )

This strategy ensures the application remains functional even if asset files are missing or corrupted, preventing crashes and providing visual feedback that matches the brand identity.

**Sources:** [lib/widgets/sena_logo.dart L18-L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L18-L44)

## Design Patterns and Best Practices

### Parameterized Components

Both branding components follow a parameterized design pattern that enables:

* **Size Flexibility:** Dimensions can be adjusted per usage context
* **Consistent API:** All instances use the same widget interface
* **Default Values:** Sensible defaults for common use cases
* **Override Capability:** Specific screens can customize as needed

### Separation of Concerns

The widget architecture maintains clear boundaries:

| Concern | Responsibility | Location |
| --- | --- | --- |
| **Visual Rendering** | How logos and barcodes appear | `SenaLogo`, `BarcodeGenerator` widgets |
| **Data Provision** | What data to display | Screen-level components |
| **Styling Constants** | Brand colors and dimensions | `AppColors` utility (see [#6.3](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/#6.3) <br> ) |
| **Asset Management** | Loading image resources | Flutter asset system |

This separation allows each component to be modified independently without affecting others.

**Sources:** [lib/widgets/sena_logo.dart L1-L64](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/sena_logo.dart#L1-L64)

 [lib/screens/id_card_screen.dart L1-L194](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/id_card_screen.dart#L1-L194)