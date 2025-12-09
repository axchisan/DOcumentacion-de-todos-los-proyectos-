# Color Palette and Theming

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/css/StyleCliente.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css)
> * [assets/css/StyleLogin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css)
> * [assets/css/StyleRecepcionista.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css)

## Purpose and Scope

This document details the systematic color palette and theming strategy used across all user interfaces in the Hotel Benedetti system. It explains the color values, their semantic meanings, application patterns, and how the dark theme with purple accents creates visual consistency across the Administrator, Client, Receptionist, and Maid interfaces.

For information about how these colors are applied to specific UI components (buttons, forms, tables), see [Component Library](/GroveLive/hotelBenedetti/8.2-component-library). For layout and positioning strategies, see [Layout Patterns](/GroveLive/hotelBenedetti/8.3-layout-patterns).

---

## Color Hierarchy

The Hotel Benedetti system employs a carefully structured color hierarchy built around a dark theme with purple (#9b59b6) as the primary brand accent. All interfaces share this foundational palette to maintain visual cohesion while allowing role-specific variations.

### Primary Color Values

| Color | Hex Value | Usage | CSS Variable Locations |
| --- | --- | --- | --- |
| **Dark Base** | `#23272a` | Overlay layer, deepest background | Body overlay across all CSS files |
| **Dark Surface** | `#2c2f33` | Primary containers, header, footer | Container backgrounds |
| **Dark Elevated** | `#36393f` | Cards, form inputs, elevated elements | Interactive components |
| **Light Text** | `#dcddde` | Primary text color | All text content |
| **Secondary Text** | `#b0b3b8` | Supplementary text, descriptions | Notification details, metadata |

### Accent Colors

| Color | Hex Value | Semantic Meaning | Applied To |
| --- | --- | --- | --- |
| **Primary Purple** | `#9b59b6` | Brand identity, primary actions | Buttons, links, headers |
| **Dark Purple** | `#8e44ad` | Hover states for primary actions | Interactive element hover |
| **Action Blue** | `#36A2EB` | Edit/modify operations | Edit buttons in tables |
| **Destructive Red** | `#FF6384` | Delete/cancel operations | Delete buttons, cancel actions |
| **Dark Red** | `#f44336` / `#d32f2f` | Cancel confirmations, errors | Error messages, cancel buttons |
| **Success Green** | `#25D366` | WhatsApp integration | Floating WhatsApp button |

### Border and Divider Colors

| Color | Hex Value | Usage |
| --- | --- | --- |
| **Primary Border** | `#7289da` | Form input borders |
| **Neutral Border** | `#ddd` | Table cells, general dividers |
| **Subtle Divider** | `#4a4d52` | Table row separators |

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

 [assets/css/StyleLogin.css L1-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L1-L196)

---

## Dark Theme Implementation

### Background Layering Strategy

The system creates visual depth through a three-layer background system combined with image overlays:

```

```

**Dark Overlay Implementation**

All interfaces apply a semi-transparent dark overlay over background images to ensure text readability:

[assets/css/StyleAdmin.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L31)

 - Admin overlay implementation
[assets/css/StyleCliente.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L22-L31)

 - Client overlay implementation
[assets/css/StyleRecepcionista.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L22-L31)

 - Receptionist overlay implementation
[assets/css/StyleLogin.css L25-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L25-L34)

 - Login overlay implementation

The overlay uses `rgba(35, 39, 42, 0.8)` with 80% opacity, positioned absolutely with `z-index: 1`, while content layers are elevated with `z-index: 2`.

**Sources:** [assets/css/StyleAdmin.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L8-L37)

 [assets/css/StyleCliente.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L9-L37)

 [assets/css/StyleRecepcionista.css L9-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L9-L37)

 [assets/css/StyleLogin.css L9-L45](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L9-L45)

---

## Color Application by Interface Element

### Navigation and Headers

```

```

**Header Backgrounds:**

* Admin interface: `#333` [assets/css/StyleAdmin.css L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L51-L51)
* All other interfaces: `#2c2f33` [assets/css/StyleCliente.css L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L41-L41)  [assets/css/StyleRecepcionista.css L41](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L41-L41)

**Navigation Link Colors:**

* Default state: `#9b59b6` (purple) [assets/css/StyleCliente.css L73](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L73-L73)
* Hover state: `#8e44ad` text with `#36393f` background [assets/css/StyleCliente.css L80-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L80-L83)
* Admin navigation: Purple background on hover `#9b59b6` [assets/css/StyleAdmin.css L96](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L96-L96)

**Sources:** [assets/css/StyleAdmin.css L50-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L50-L97)

 [assets/css/StyleCliente.css L40-L84](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L40-L84)

 [assets/css/StyleRecepcionista.css L40-L83](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L40-L83)

---

### Form Elements

| Element | Default State | Focus State | Purpose |
| --- | --- | --- | --- |
| **Input Background** | `#36393f` | Same | Dark elevated surface |
| **Input Border** | `#7289da` | `#9b59b6` | Blue default → Purple focus |
| **Input Text** | `#dcddde` | Same | Light text for readability |
| **Focus Shadow** | None | `0 0 5px rgba(155, 89, 182, 0.5)` | Purple glow |

**Form Input Styling:**

[assets/css/StyleCliente.css L260-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L277)

 demonstrates the complete form input color system:

* Background: `#36393f`
* Border: `#7289da` (default) → `#9b59b6` (focus)
* Text: `#dcddde`
* Focus effect includes shadow with 50% opacity purple

**Sources:** [assets/css/StyleAdmin.css L188-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L188-L196)

 [assets/css/StyleCliente.css L260-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L260-L277)

 [assets/css/StyleRecepcionista.css L272-L288](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L272-L288)

 [assets/css/StyleLogin.css L87-L104](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L87-L104)

---

### Button Color Semantics

```

```

**Primary Buttons:**

* Background: `#9b59b6` [assets/css/StyleAdmin.css L199](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L199-L199)
* Hover: `#8e44ad` [assets/css/StyleAdmin.css L209](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L209-L209)
* Usage: Form submissions, confirmations, primary actions

**Edit Buttons:**

* Background: `#36A2EB` (blue) [assets/css/StyleAdmin.css L253](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L253-L253)
* Hover: `opacity: 0.9` [assets/css/StyleAdmin.css L264](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L264-L264)
* Location: Employee and room management tables

**Delete Buttons:**

* Background: `#FF6384` (red) [assets/css/StyleAdmin.css L259](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L259-L259)
* Hover: `opacity: 0.9` [assets/css/StyleAdmin.css L264](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L264-L264)
* Usage: Destructive operations requiring confirmation

**Cancel Buttons:**

* Background: `#f44336` [assets/css/StyleLogin.css L138](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L138-L138)
* Hover: `#d32f2f` [assets/css/StyleLogin.css L143](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L143-L143)
* Context: Registration form cancellation, dialog dismissal

**Sources:** [assets/css/StyleAdmin.css L198-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L266)

 [assets/css/StyleCliente.css L136-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L136-L149)

 [assets/css/StyleRecepcionista.css L130-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L130-L156)

 [assets/css/StyleLogin.css L116-L144](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L116-L144)

---

## Interactive State Colors

### Hover and Focus States

All interactive elements implement consistent hover state color transitions:

| Element Type | Default | Hover/Active | Transition |
| --- | --- | --- | --- |
| **Primary Buttons** | `#9b59b6` | `#8e44ad` | `background-color 0.3s ease` |
| **Navigation Links** | `#9b59b6` text | `#8e44ad` + `#36393f` bg | `color 0.3s, background-color 0.3s` |
| **Table Rows** | Transparent | `#40444b` | Immediate |
| **Cards (stat-card)** | No transform | `translateY(-5px)` | `transform 0.3s ease` |
| **Floating Buttons** | `scale(1)` | `scale(1.1)` | `transform 0.3s ease` |

**Table Row Hover:**
[assets/css/StyleRecepcionista.css L185-L187](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L185-L187)

 - Guest list table hover: `#40444b`
[assets/css/StyleRecepcionista.css L247-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L247-L249)

 - Room list table hover: `#40444b`

**Card Hover Effects:**
[assets/css/StyleAdmin.css L144-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L144-L146)

 - Dashboard stat cards lift with `translateY(-5px)`

**Sources:** [assets/css/StyleAdmin.css L91-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L91-L266)

 [assets/css/StyleCliente.css L77-L365](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L77-L365)

 [assets/css/StyleRecepcionista.css L76-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L76-L249)

---

## Notification and Alert Colors

### Notification Components

```

```

**Client Interface Notifications:**
[assets/css/StyleCliente.css L114-L119](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L114-L119)

 - Notification section: `#2c2f33` background
[assets/css/StyleCliente.css L121-L129](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L121-L129)

 - Individual notifications: `#36393f` background
[assets/css/StyleCliente.css L136-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L136-L149)

 - Mark-as-read button: `#9b59b6` → `#8e44ad` hover

**Receptionist Interface Notifications:**
[assets/css/StyleRecepcionista.css L112-L123](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L112-L123)

 - Notification styling with same background hierarchy
[assets/css/StyleRecepcionista.css L140-L147](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L140-L147)

 - Confirm reservation: `#9b59b6` (purple)
[assets/css/StyleRecepcionista.css L149-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L149-L156)

 - Cancel reservation: `#f44336` (red)

**Sources:** [assets/css/StyleCliente.css L114-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L114-L149)

 [assets/css/StyleRecepcionista.css L112-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L112-L156)

---

## Table Styling Colors

### Data Table Color System

| Element | Color | Purpose |
| --- | --- | --- |
| **Table Background** | `#36393f` | Base table surface |
| **Header Background** | `#9b59b6` (Recepcionista)`#f4f4f4` (Admin) | Column headers |
| **Header Text** | `white` (Recepcionista)Default (Admin) | Header labels |
| **Row Borders** | `#4a4d52` or `#ddd` | Cell separation |
| **Alternating Rows** | `#f9f9f9` (Admin only) | Visual distinction |
| **Hover State** | `#40444b` | Interactive feedback |

**Receptionist Table Headers:**
[assets/css/StyleRecepcionista.css L181-L183](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L181-L183)

 - Purple headers (`#9b59b6`) for guest and room lists

**Admin Table Styling:**
[assets/css/StyleAdmin.css L213-L239](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L239)

 - Light theme with `#f4f4f4` headers and alternating row colors

**Sources:** [assets/css/StyleAdmin.css L213-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L266)

 [assets/css/StyleRecepcionista.css L165-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L165-L249)

---

## Special Component Colors

### Floating Action Buttons

The client interface includes social media floating buttons with distinct brand colors:

| Button | Background | Hover | Purpose |
| --- | --- | --- | --- |
| **Instagram** | `#9b59b6` | `#8e44ad` | Social media link |
| **WhatsApp** | `#25D366` | `#20b358` | Contact link |

[assets/css/StyleCliente.css L373-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L373-L387)

 - Floating button color definitions

### Dashboard Stat Cards

[assets/css/StyleAdmin.css L133-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L133-L146)

 - Stat cards use `white` backgrounds with hover elevation, contrasting with the dark theme to highlight key metrics.

**Sources:** [assets/css/StyleCliente.css L340-L387](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L340-L387)

 [assets/css/StyleAdmin.css L126-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L146)

---

## Dialog and Modal Colors

Dialog elements maintain the dark theme consistency:

| Element | Color | Applied In |
| --- | --- | --- |
| **Dialog Background** | `#2c2f33` | All modal containers |
| **Dialog Text** | `#dcddde` | Modal content |
| **Input Fields** | `#36393f` bg, `#7289da` border | Form inputs in dialogs |
| **Submit Button** | `#9b59b6` → `#8e44ad` | Primary actions |
| **Cancel Button** | `#f44336` → `#d32f2f` | Dismissal actions |

[assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

 provides the complete dialog color system used for checkout confirmation and cancellation dialogs.

**Sources:** [assets/css/StyleRecepcionista.css L252-L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L252-L315)

---

## Color Palette Summary Diagram

```

```

**Sources:** All CSS files: [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/css/StyleCliente.css L1-L486](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleCliente.css#L1-L486)

 [assets/css/StyleRecepcionista.css L1-L407](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L1-L407)

 [assets/css/StyleLogin.css L1-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L1-L196)

---

## Interface-Specific Color Variations

While all interfaces share the core dark theme and purple accent system, minor variations exist:

### Admin Interface

* Container background: `#f4f4f4` (light) [assets/css/StyleAdmin.css L43](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L43-L43)
* Table headers: `#f4f4f4` (light) [assets/css/StyleAdmin.css L233](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L233-L233)
* Uses alternating row colors for tables [assets/css/StyleAdmin.css L236-L239](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L236-L239)

### Client, Receptionist, and Maid Interfaces

* Fully dark themed throughout
* Purple headers in tables (Receptionist) [assets/css/StyleRecepcionista.css L181-L183](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L181-L183)
* Consistent `#2c2f33` and `#36393f` hierarchy

### Login Interface

* Simplified color palette focusing on form clarity
* Container: `#2c2f33` [assets/css/StyleLogin.css L42](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L42-L42)
* Error text: `#f44336` [assets/css/StyleLogin.css L170](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L170-L170)
* Single-purpose interface with minimal chrome

**Sources:** [assets/css/StyleAdmin.css L39-L239](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L39-L239)

 [assets/css/StyleRecepcionista.css L181-L183](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleRecepcionista.css#L181-L183)

 [assets/css/StyleLogin.css L37-L171](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleLogin.css#L37-L171)