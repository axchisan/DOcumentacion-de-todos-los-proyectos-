# Design System and Styling

> **Relevant source files**
> * [src/backend/gestionRecursos/get_categories.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/get_categories.php)
> * [src/backend/gestionRecursos/get_user_groups.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/get_user_groups.php)
> * [src/backend/perfil/uploads/profile_6814422884f7d-465971915_519394657743559_5751152004256211003_n.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/uploads/profile_6814422884f7d-465971915_519394657743559_5751152004256211003_n.jpg)
> * [src/frontend/inicio/css/styles.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css)
> * [src/frontend/panel/css/styles-panel.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css)
> * [src/frontend/repositorio/css/repositorio.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css)
> * [src/uploads/6805c8bb76358_cover.png](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/uploads/6805c8bb76358_cover.png)
> * [src/uploads/6805c8bb765ca.pdf](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/uploads/6805c8bb765ca.pdf)

This document covers the comprehensive design system and styling architecture used across the El Rincón de ADSO platform. It details the CSS custom properties, component patterns, responsive design strategy, and styling conventions implemented in the frontend layer.

For information about responsive design patterns and breakpoints specifically, see [Responsive Design](/axchisan/El-rincon-de-ADSO/8.2-responsive-design). For details about the landing page structure and hero components, see [Landing Page and Navigation](/axchisan/El-rincon-de-ADSO/8.3-landing-page-and-navigation).

---

## Purpose and Scope

The design system provides a consistent visual language across all pages of the platform through:

* **CSS Custom Properties (CSS Variables)** for theming and consistency
* **Component-based styling patterns** for reusable UI elements
* **Responsive design utilities** for mobile-first development
* **Coffee-themed color palette** aligned with the platform's branding

The system is distributed across three main stylesheets:

* [src/frontend/inicio/css/styles.css L1-L3000](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1-L3000)  - Core design system and landing page styles
* [src/frontend/repositorio/css/repositorio.css L1-L1700](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L1-L1700)  - Repository browser specific styles
* [src/frontend/panel/css/styles-panel.css L1-L2500](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L1-L2500)  - User dashboard specific styles

---

## CSS Architecture Overview

The styling architecture follows a modular approach where base design tokens are defined once and reused across all components. The system uses CSS custom properties (CSS variables) extensively for maintainability and theming.

```mermaid
flowchart TD

RootVars["CSS :root Variables<br>(styles.css:1-59)"]
Reset["CSS Reset<br>(styles.css:61-102)"]
BaseElements["Base HTML Elements<br>(styles.css:67-103)"]
Container[".container<br>(styles.css:105-110)"]
Section[".section variants<br>(styles.css:112-145)"]
Grid["Grid Systems<br>(styles.css:1271-1577)"]
Navbar[".navbar<br>(styles.css:272-649)"]
Hero[".hero<br>(styles.css:668-821)"]
Buttons[".btn variants<br>(styles.css:1107-1241)"]
Cards["Card Components<br>(styles.css:1277-1577)"]
Forms["Form Components<br>(styles.css:878-1106)"]
RepoStyles["repositorio.css<br>Repository Browser"]
PanelStyles["styles-panel.css<br>User Dashboard"]

RootVars --> BaseElements
RootVars --> Container
RootVars --> Section
RootVars --> Navbar
RootVars --> Hero
RootVars --> Buttons
RootVars --> Cards
RootVars --> Forms
Container --> RepoStyles
Buttons --> RepoStyles
Cards --> RepoStyles
Container --> PanelStyles
Buttons --> PanelStyles
Cards --> PanelStyles

subgraph subGraph4 ["Page-Specific Styles"]
    RepoStyles
    PanelStyles
end

subgraph subGraph3 ["Component Styles"]
    Navbar
    Hero
    Buttons
    Cards
    Forms
end

subgraph subGraph2 ["Layout System"]
    Container
    Section
    Grid
end

subgraph subGraph1 ["Base Styles"]
    Reset
    BaseElements
end

subgraph subGraph0 ["Design Token Layer"]
    RootVars
end
```

**Sources:** [src/frontend/inicio/css/styles.css L1-L3000](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1-L3000)

 [src/frontend/repositorio/css/repositorio.css L1-L1700](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L1-L1700)

 [src/frontend/panel/css/styles-panel.css L1-L2500](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L1-L2500)

---

## Design Tokens (CSS Custom Properties)

All design tokens are defined as CSS custom properties in the `:root` selector, making them globally accessible throughout the application.

### Color Palette

The platform uses a coffee-themed color system with warm, earthy tones:

```mermaid
flowchart TD

Coffee["--color-coffee<br>#654321"]
CoffeeDark["--color-coffee-dark<br>#4a3219"]
CoffeeLight["--color-coffee-light<br>#8b7355"]
Latte["--color-latte<br>#e6d9cc"]
Cappuccino["--color-cappuccino<br>#d8ccc0"]
Mocha["--color-mocha-light<br>#c8b6a6"]
Almond["--color-almond<br>#efe5dc"]
White["--color-white<br>#ffffff"]
OffWhite["--color-off-white<br>#f9f5f2"]
Gray50["--color-gray-50<br>#f9f6f4"]
Gray800["--color-gray-800<br>#362f29"]
Buttons["Buttons"]
HoverStates["Hover States"]
Accents["Accent Elements"]
SectionBg["Section Backgrounds"]
CardBg["Card Backgrounds"]

Coffee --> Buttons
CoffeeDark --> HoverStates
CoffeeLight --> Accents
Latte --> SectionBg
White --> CardBg

subgraph subGraph2 ["Neutral Colors"]
    White
    OffWhite
    Gray50
    Gray800
end

subgraph subGraph1 ["Background Colors"]
    Latte
    Cappuccino
    Mocha
    Almond
end

subgraph subGraph0 ["Primary Colors"]
    Coffee
    CoffeeDark
    CoffeeLight
end
```

**Color Variable Definitions:**

| Variable Name | Hex Value | Usage |
| --- | --- | --- |
| `--color-coffee` | `#654321` | Primary buttons, headings, borders |
| `--color-coffee-dark` | `#4a3219` | Button hover states, dark accents |
| `--color-coffee-light` | `#8b7355` | Secondary elements, gradients |
| `--color-latte` | `#e6d9cc` | Section backgrounds, hover backgrounds |
| `--color-cappuccino` | `#d8ccc0` | Alternative backgrounds |
| `--color-mocha-light` | `#c8b6a6` | Tertiary backgrounds |
| `--color-almond` | `#efe5dc` | Body background |
| `--color-white` | `#ffffff` | Card backgrounds, text on dark |
| `--color-off-white` | `#f9f5f2` | Input backgrounds |

**Gray Scale** (warm-toned):

| Variable | Hex | Usage |
| --- | --- | --- |
| `--color-gray-50` | `#f9f6f4` | Lightest backgrounds |
| `--color-gray-100` | `#f2ede9` | Subtle backgrounds |
| `--color-gray-200` | `#e5ddd7` | Borders, dividers |
| `--color-gray-300` | `#d4c9c1` | Disabled states |
| `--color-gray-400` | `#b3a69b` | Placeholder text |
| `--color-gray-500` | `#8c7f73` | Secondary text |
| `--color-gray-600` | `#6d6258` | Body text |
| `--color-gray-700` | `#50473f` | Dark text |
| `--color-gray-800` | `#362f29` | Headings, primary text |
| `--color-gray-900` | `#211c18` | Darkest text |

**Accent Colors** (for status indicators):

| Variable | Hex | Usage |
| --- | --- | --- |
| `--color-green` | `#4caf50` | Success states, online status |
| `--color-red` | `#f44336` | Error states, notifications |
| `--color-blue` | `#2196f3` | Info states |
| `--color-amber` | `#ffc107` | Warning states |

**Sources:** [src/frontend/inicio/css/styles.css L1-L39](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1-L39)

 [src/frontend/panel/css/styles-panel.css L1-L40](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L1-L40)

---

### Typography System

The typography system defines two font stacks and a comprehensive scale:

```mermaid
flowchart TD

TextSm[".text-sm<br>0.875rem"]
TextLg[".text-lg<br>1.125rem"]
TextXl[".text-xl<br>1.25rem"]
Text2xl[".text-2xl<br>1.5rem"]
Text3xl[".text-3xl<br>1.875rem"]
Text4xl[".text-4xl<br>2.25rem"]
W400["Regular: 400"]
W500["Medium: 500<br>.font-medium"]
W600["Semibold: 600<br>.font-semibold"]
W700["Bold: 700<br>.font-bold"]
Sans["--font-sans<br>'Helvetica Neue', Arial, sans-serif"]
Serif["--font-serif<br>'Georgia', 'Times New Roman', serif"]
H1["h1<br>2.5rem (3rem @768px)"]
H2["h2<br>2rem (2.25rem @768px)"]
H3["h3<br>1.5rem"]
H4["h4<br>1.25rem"]
H5["h5<br>1.125rem"]
H6["h6<br>1rem"]
BodyText["body text<br>line-height: 1.5"]

Sans --> BodyText
Sans --> H1
Sans --> H2

subgraph subGraph2 ["Heading Scale"]
    H1
    H2
    H3
    H4
    H5
    H6
end

subgraph subGraph0 ["Font Families"]
    Sans
    Serif
end

subgraph subGraph3 ["Utility Classes"]
    TextSm
    TextLg
    TextXl
    Text2xl
    Text3xl
    Text4xl
end

subgraph subGraph1 ["Font Weights"]
    W400
    W500
    W600
    W700
end
```

**Typography Variables:**

```
--font-serif: "Georgia", "Times New Roman", serif;
--font-sans: "Helvetica Neue", Arial, sans-serif;
```

**Base Configuration:**

* Root font size: `16px`
* Body font: `var(--font-sans)`
* Body color: `var(--color-gray-800)`
* Line height: `1.5`
* Font smoothing: Antialiased

**Heading Styles:**

| Element | Desktop Size | Mobile Size | Line Height | Weight |
| --- | --- | --- | --- | --- |
| `h1` | `3rem` (48px) | `2.5rem` (40px) | `1.2` | `700` |
| `h2` | `2.25rem` (36px) | `2rem` (32px) | `1.2` | `700` |
| `h3` | `1.5rem` (24px) | `1.5rem` (24px) | `1.2` | `700` |
| `h4` | `1.25rem` (20px) | `1.25rem` (20px) | `1.2` | `700` |
| `h5` | `1.125rem` (18px) | `1.125rem` (18px) | `1.2` | `700` |
| `h6` | `1rem` (16px) | `1rem` (16px) | `1.2` | `700` |

**Utility Classes:**

| Class | Size | Usage |
| --- | --- | --- |
| `.text-sm` | `0.875rem` (14px) | Small text, captions |
| `.text-lg` | `1.125rem` (18px) | Emphasized body text |
| `.text-xl` | `1.25rem` (20px) | Large text |
| `.text-2xl` | `1.5rem` (24px) | Extra large text |
| `.text-3xl` | `1.875rem` (30px) | Display text |
| `.text-4xl` | `2.25rem` (36px) | Hero text |

**Weight Utilities:**

* `.font-medium` - `font-weight: 500`
* `.font-semibold` - `font-weight: 600`
* `.font-bold` - `font-weight: 700`

**Color Utilities:**

* `.text-coffee` - `color: var(--color-coffee)`
* `.text-white` - `color: var(--color-white)`
* `.text-gray` - `color: var(--color-gray-600)`
* `.text-light` - `color: var(--color-gray-400)`

**Sources:** [src/frontend/inicio/css/styles.css L40-L270](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L40-L270)

 [src/frontend/panel/css/styles-panel.css L42-L43](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L42-L43)

---

### Shadow System

A five-level shadow system provides depth hierarchy:

```mermaid
flowchart TD

Sm["--shadow-sm<br>Subtle elevation"]
Default["--shadow<br>Default cards"]
Md["--shadow-md<br>Interactive elements"]
Lg["--shadow-lg<br>Modals, dropdowns"]
Xl["--shadow-xl<br>Highest elevation"]
SmallCards["Small indicators"]
CardDefault[".resource-card<br>.book-card"]
Navbar[".navbar<br>.search-container"]
HoverStates["Card hover states<br>.cta-container"]
HeroImages["Hero images<br>Emphasized elements"]

Sm --> SmallCards
Default --> CardDefault
Md --> Navbar
Lg --> HoverStates
Xl --> HeroImages

subgraph subGraph1 ["Usage Examples"]
    SmallCards
    CardDefault
    Navbar
    HoverStates
    HeroImages
end

subgraph subGraph0 ["Shadow Levels"]
    Sm
    Default
    Md
    Lg
    Xl
end
```

**Shadow Definitions:**

```
--shadow-sm: 0 2px 4px 0 rgba(101, 67, 33, 0.08);
--shadow: 0 2px 5px 0 rgba(101, 67, 33, 0.15), 0 1px 3px 0 rgba(101, 67, 33, 0.1);
--shadow-md: 0 5px 8px -1px rgba(101, 67, 33, 0.18), 0 3px 5px -1px rgba(101, 67, 33, 0.12);
--shadow-lg: 0 12px 18px -3px rgba(101, 67, 33, 0.2), 0 5px 8px -2px rgba(101, 67, 33, 0.15);
--shadow-xl: 0 22px 28px -5px rgba(101, 67, 33, 0.25), 0 12px 12px -5px rgba(101, 67, 33, 0.18);
```

All shadows use the coffee color (`rgba(101, 67, 33, ...)`) at varying opacities to maintain visual coherence with the color palette.

**Sources:** [src/frontend/inicio/css/styles.css L44-L49](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L44-L49)

 [src/frontend/panel/css/styles-panel.css L46-L50](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L46-L50)

---

### Border Radius System

A consistent border radius scale maintains visual harmony:

| Variable | Value | Usage |
| --- | --- | --- |
| `--border-radius-sm` | `0.125rem` (2px) | Small elements, toggles |
| `--border-radius` | `0.25rem` (4px) | Default radius |
| `--border-radius-md` | `0.375rem` (6px) | Buttons, inputs |
| `--border-radius-lg` | `0.5rem` (8px) | Cards, containers |
| `--border-radius-xl` | `0.75rem` (12px) | Large cards |
| `--border-radius-2xl` | `1rem` (16px) | Hero images |
| `--border-radius-full` | `9999px` | Pills, badges, avatars |

**Sources:** [src/frontend/inicio/css/styles.css L52-L58](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L52-L58)

 [src/frontend/panel/css/styles-panel.css L53-L59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L53-L59)

---

## Component Library

### Button System

The button system provides multiple variants with consistent styling:

```mermaid
flowchart TD

BtnBase[".btn<br>(styles.css:1107-1119)"]
Primary[".btn--primary<br>Coffee background"]
Secondary[".btn--secondary<br>White with coffee border"]
Outline[".btn--outline<br>Transparent background"]
Gray[".btn--gray<br>Neutral background"]
Default["Default State"]
Hover["Hover State<br>translateY(-3px)<br>Enhanced shadow"]
Active["Active State<br>Ripple effect"]

BtnBase --> Primary
BtnBase --> Secondary
BtnBase --> Outline
BtnBase --> Gray
Primary --> Default
Primary --> Hover
Primary --> Active

subgraph States ["States"]
    Default
    Hover
    Active
end

subgraph subGraph1 ["Button Variants"]
    Primary
    Secondary
    Outline
    Gray
end

subgraph subGraph0 ["Base Button Class"]
    BtnBase
end
```

**Base Button Styles** (`.btn`):

```css
.btn {
  display: inline-block;
  padding: 0.75rem 1.5rem;
  font-weight: 500;
  text-align: center;
  border-radius: var(--border-radius-md);
  transition: all 0.3s ease;
  cursor: pointer;
  border: 2px solid transparent;
  letter-spacing: 0.5px;
  position: relative;
  overflow: hidden;
}
```

**Button Variants:**

| Class | Background | Text Color | Border | Hover Effect |
| --- | --- | --- | --- | --- |
| `.btn--primary` | `var(--color-coffee)` | White | Coffee | Darker background, lift up |
| `.btn--secondary` | White | Coffee | Coffee | Fill with coffee color |
| `.btn--outline` | Transparent | Coffee | Coffee | Light coffee background |
| `.btn--gray` | Gray-200 | Gray-800 | None | Gray-300 background |

**Interactive States:**

1. **Hover**: All buttons lift by `3px` (`translateY(-3px)`) and enhance shadows
2. **Active**: Secondary buttons show ripple animation effect
3. **Transition**: All state changes use `0.3s` ease timing

**Specialized Button Classes:**

* `.search-button` - Used in search bars, coffee-light background
* `.filter-button` - Gray background with latte hover
* `.navbar__menu-item--button` - Navigation button variant

**Sources:** [src/frontend/inicio/css/styles.css L1107-L1241](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1107-L1241)

 [src/frontend/repositorio/css/repositorio.css L590-L657](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L590-L657)

---

### Card Components

Cards are the primary content containers, with multiple specialized variants:

```mermaid
flowchart TD

ResourceCard[".resource-card<br>(repositorio.css:781-923)"]
BookCard[".book-card<br>(styles.css:1436-1565)"]
FeatureCard[".feature-card<br>(styles.css:1277-1363)"]
CommentCard[".comment-card<br>(styles.css:1618-1762)"]
DocumentCard[".document-card<br>(repositorio.css:926-945)"]
ImageContainer[".resource-card__image-container"]
Content[".resource-card__content"]
Meta[".resource-card__meta"]
Actions[".resource-card__actions"]
Category[".resource-card__category<br>Pill badge"]
Format[".resource-card__format<br>Absolute positioned"]
Duration[".resource-card__duration<br>Video duration"]
PlayButton[".resource-card__play-button<br>Hover overlay"]

ResourceCard --> ImageContainer
ResourceCard --> Content
ImageContainer --> Format
ImageContainer --> Duration
ImageContainer --> PlayButton
Content --> Category

subgraph subGraph2 ["Card Elements"]
    Category
    Format
    Duration
    PlayButton
end

subgraph subGraph1 ["Card Structure"]
    ImageContainer
    Content
    Meta
    Actions
    Content --> Meta
    Content --> Actions
end

subgraph subGraph0 ["Card Types"]
    ResourceCard
    BookCard
    FeatureCard
    CommentCard
    DocumentCard
end
```

**Base Resource Card** (`.resource-card`):

```css
.resource-card {
  background-color: var(--color-white);
  border-radius: var(--border-radius-lg);
  overflow: hidden;
  box-shadow: var(--shadow-md);
  transition: all 0.3s ease;
  border: 1px solid var(--color-gray-200);
  height: 100%;
  display: flex;
  flex-direction: column;
}

.resource-card:hover {
  transform: translateY(-10px);
  box-shadow: var(--shadow-xl);
  border-color: var(--color-coffee-light);
}
```

**Card Components:**

| Component | Styles | Purpose |
| --- | --- | --- |
| `.resource-card__image-container` | `height: 200px`, `overflow: hidden` | Contains cover image |
| `.resource-card__image` | `width: 100%`, `object-fit: cover` | Cover image, scales on hover |
| `.resource-card__format` | Absolute top-right, coffee background | Shows format type (PDF, MP4, etc.) |
| `.resource-card__duration` | Absolute bottom-right, semi-transparent | Video duration display |
| `.resource-card__play-button` | Centered overlay, opacity 0 | Play button icon, visible on hover |
| `.resource-card__content` | `padding: 1.5rem`, flex column | Main content area |
| `.resource-card__category` | Pill-shaped badge, coffee background | Category indicator |
| `.resource-card__title` | `1.25rem`, coffee color | Resource title |
| `.resource-card__author` | `0.875rem`, gray-600 | Author/creator name |
| `.resource-card__meta` | Flex row, small icons + text | Views, ratings, date |
| `.resource-card__actions` | Flex column, margin-top auto | Action buttons |

**Feature Card** (`.feature-card`):

Special card variant used on landing page for feature highlights:

* Coffee top border (`4px solid`)
* Gradient background overlay on hover
* Circular icon container (`70px` diameter)
* Icon rotates `360deg` on hover

**Comment Card** (`.comment-card`):

Used for displaying user comments and feedback:

* Left coffee border (`4px`, transitions opacity)
* Avatar, username, date in header
* Star rating display
* Footer with action buttons

**Sources:** [src/frontend/repositorio/css/repositorio.css L780-L945](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L780-L945)

 [src/frontend/inicio/css/styles.css L1277-L1762](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1277-L1762)

 [src/frontend/panel/css/styles-panel.css L674-L859](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L674-L859)

---

### Navigation Bar

The navbar is a sticky component with responsive behavior:

```mermaid
flowchart TD

Nav[".navbar<br>(styles.css:272-649)"]
Container[".navbar__container"]
Logo[".navbar__logo"]
Menu[".navbar__menu<br>Desktop only"]
Toggle[".navbar__toggle<br>Mobile only"]
Mobile[".navbar__mobile<br>Mobile only"]
Profile[".navbar__profile"]
MenuItem[".navbar__menu-item"]
ActiveItem[".navbar__menu-item--active<br>Underline indicator"]
ButtonItem[".navbar__menu-item--button<br>Coffee button style"]
ProfileImg[".navbar__profile-img<br>40px circular avatar"]
Badge[".navbar__notification-badge<br>Red notification count"]
Dropdown[".navbar__profile-menu<br>Absolute positioned"]

Menu --> MenuItem
Profile --> ProfileImg
Profile --> Badge
Profile --> Dropdown

subgraph subGraph2 ["Profile Components"]
    ProfileImg
    Badge
    Dropdown
end

subgraph subGraph1 ["Menu Items"]
    MenuItem
    ActiveItem
    ButtonItem
    MenuItem --> ActiveItem
    MenuItem --> ButtonItem
end

subgraph subGraph0 ["Navbar Structure"]
    Nav
    Container
    Logo
    Menu
    Toggle
    Mobile
    Profile
    Nav --> Container
    Container --> Logo
    Container --> Menu
    Container --> Toggle
    Container --> Profile
end
```

**Navbar Base Styles:**

```css
.navbar {
  position: sticky;
  top: 0;
  z-index: 100;
  background-color: var(--color-white);
  box-shadow: var(--shadow-md);
  border-bottom: 3px solid var(--color-coffee);
}
```

**Key Features:**

1. **Sticky Positioning**: Remains visible at top during scroll (`position: sticky; top: 0`)
2. **High z-index**: `z-index: 100` ensures it stays above content
3. **Coffee Border**: `3px solid` bottom border for brand consistency
4. **Responsive Toggle**: Hamburger menu appears on mobile (`< 768px`)

**Desktop Menu** (`.navbar__menu`):

* Hidden on mobile (`display: none`)
* Flex layout on desktop (`display: flex` at `768px+`)
* Horizontal menu items with hover effects

**Mobile Menu** (`.navbar__mobile`):

* Shown when toggle activated
* Full-width dropdown
* Vertical layout with left-border hover indicators

**Profile Component**:

| Element | Styles | Behavior |
| --- | --- | --- |
| `.navbar__profile-img` | `40px` circular, `border: 2px solid coffee-light` | Scales `1.1` on hover |
| `.navbar__notification-badge` | Red circle, absolute positioned top-right | Shows unread count |
| `.navbar__profile-menu` | Absolute dropdown, `display: none` by default | Toggles with `.active` class |

**Responsive Breakpoints:**

* `< 768px`: Mobile menu, toggle visible
* `>= 768px`: Desktop menu, toggle hidden
* `>= 1024px`: Larger logo and spacing

**Sources:** [src/frontend/inicio/css/styles.css L272-L649](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L272-L649)

 [src/frontend/repositorio/css/repositorio.css L96-L415](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L96-L415)

 [src/frontend/panel/css/styles-panel.css L116-L272](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L116-L272)

---

### Form Components

Form styling focuses on accessibility and visual feedback:

```mermaid
flowchart TD

SearchContainer[".search-container<br>(repositorio.css:562-567)"]
SearchBox[".search-box<br>Flex container"]
SearchInput["input<br>2px coffee border"]
SearchButton[".search-button<br>Coffee-light background"]
FilterContainer[".filter-container<br>Grid layout"]
FilterGroup[".filter-group<br>Flex column"]
FilterLabel["label<br>Gray-700, 0.875rem"]
FilterSelect["select<br>Custom dropdown arrow"]
Default["Default<br>Gray-200 border"]
Focus["Focus<br>Coffee border<br>3px coffee shadow"]
Hover["Hover<br>White background"]

SearchInput --> Default
SearchInput --> Focus
FilterSelect --> Hover

subgraph States ["States"]
    Default
    Focus
    Hover
end

subgraph subGraph1 ["Filter Components"]
    FilterContainer
    FilterGroup
    FilterLabel
    FilterSelect
    FilterContainer --> FilterGroup
    FilterGroup --> FilterLabel
    FilterGroup --> FilterSelect
end

subgraph subGraph0 ["Search Components"]
    SearchContainer
    SearchBox
    SearchInput
    SearchButton
    SearchBox --> SearchInput
    SearchBox --> SearchButton
end
```

**Input Styles:**

```css
.search-box input {
  flex-grow: 1;
  padding: 0.75rem 1rem;
  border: 2px solid var(--color-gray-200);
  border-radius: var(--border-radius-md) 0 0 var(--border-radius-md);
  font-size: 1rem;
  transition: all 0.3s ease;
}

.search-box input:focus {
  outline: none;
  border-color: var(--color-coffee);
  box-shadow: 0 0 0 3px rgba(101, 67, 33, 0.1);
}
```

**Select Dropdowns:**

* Custom dropdown arrow using SVG data URI
* Background color transitions on focus
* Consistent padding: `0.75rem 1rem`
* Coffee-colored arrow icon

**Form Groups** (`.filter-group`):

* Flex column layout
* Label above input/select
* Consistent spacing with `gap` or margins

**Input States:**

| State | Border | Background | Shadow |
| --- | --- | --- | --- |
| Default | `1px solid gray-200` | `off-white` | None |
| Focus | `2px solid coffee` | `white` | `0 0 0 3px rgba(coffee, 0.1)` |
| Hover | - | `white` | - |
| Disabled | `gray-300` | `gray-100` | None |

**Sources:** [src/frontend/inicio/css/styles.css L878-L1106](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L878-L1106)

 [src/frontend/repositorio/css/repositorio.css L562-L664](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L562-L664)

---

## Layout System

### Container and Section System

```mermaid
flowchart TD

Container[".container<br>max-width: 1200px<br>padding: 0 1rem"]
Section[".section<br>padding: 4rem 0"]
White[".section--white<br>White background"]
Latte[".section--latte<br>Latte background"]
Cappuccino[".section--cappuccino<br>Cappuccino background"]
Mocha[".section--mocha<br>Mocha-light background"]
Dark[".section--dark<br>Coffee background<br>White text"]
Gray[".section--gray<br>Gray-100 background"]
Community[".section--community<br>Pattern overlay"]
Content["Page Content"]

Container --> Content
Section --> White
Section --> Latte
Section --> Cappuccino
Section --> Mocha
Section --> Dark
Section --> Gray
Section --> Community

subgraph subGraph1 ["Section Variants"]
    White
    Latte
    Cappuccino
    Mocha
    Dark
    Gray
    Community
end

subgraph subGraph0 ["Layout Hierarchy"]
    Container
    Section
    Section --> Container
end
```

**Container Class:**

```css
.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}
```

Provides centered, constrained-width content area across all pages.

**Section System:**

Base section provides vertical spacing:

```css
.section {
  padding: 4rem 0;
}
```

**Section Background Variants:**

| Class | Background | Text Color | Use Case |
| --- | --- | --- | --- |
| `.section--white` | White | Default | Clean content areas |
| `.section--latte` | Latte (#e6d9cc) | Default | Subtle alternating sections |
| `.section--cappuccino` | Cappuccino (#d8ccc0) | Default | Warm backgrounds |
| `.section--mocha` | Mocha-light (#c8b6a6) | Default | Deeper backgrounds |
| `.section--dark` | Coffee (#654321) | White | High contrast sections |
| `.section--gray` | Gray-100 (#f2ede9) | Default | Neutral sections |
| `.section--community` | White + pattern | Default | Special decorative sections |

**Pattern Overlays:**

Several sections use SVG pattern overlays via `::before` or `::after` pseudo-elements:

```
.section--community::before {
  content: "";
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  background-image: url("data:image/svg+xml,...");
  z-index: -1;
}
```

**Sources:** [src/frontend/inicio/css/styles.css L104-L156](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L104-L156)

---

### Grid Systems

Multiple grid patterns accommodate different content types:

```mermaid
flowchart TD

ResourceGrid[".resources-grid<br>Responsive grid"]
BooksGrid[".books__grid<br>4-column max"]
FeaturesGrid[".features__grid<br>3-column max"]
Mobile["Mobile<br>1 column"]
Tablet["Tablet (640px+)<br>2 columns"]
Desktop["Desktop (1024px+)<br>3-4 columns"]

ResourceGrid --> Mobile
ResourceGrid --> Tablet
ResourceGrid --> Desktop
BooksGrid --> Mobile
BooksGrid --> Tablet
BooksGrid --> Desktop
FeaturesGrid --> Mobile
FeaturesGrid --> Tablet
FeaturesGrid --> Desktop

subgraph Breakpoints ["Breakpoints"]
    Mobile
    Tablet
    Desktop
end

subgraph subGraph0 ["Resource Grids"]
    ResourceGrid
    BooksGrid
    FeaturesGrid
end
```

**Base Resource Grid:**

```
.resources-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
}

@media (min-width: 640px) {
  .resources-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .resources-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

**Grid Variants:**

| Grid Class | Mobile | Tablet (640px+) | Desktop (1024px+) | Gap |
| --- | --- | --- | --- | --- |
| `.resources-grid` | 1 col | 2 col | 3 col | 2rem |
| `.books__grid` | 1 col | 2 col | 4 col | 2rem |
| `.features__grid` | 1 col | 3 col | 3 col | 2rem |
| `.resources-grid--documents` | 1 col | 2 col | 3 col | 2rem |
| `.forum-topics` | 1 col | 1 col | 1 col | 1rem |

**Centering and Alignment:**

* `justify-items: center` for panel grids
* `max-width: 350px` on resource cards in panel
* `margin: 0 auto` for centered single-column layouts

**Sources:** [src/frontend/repositorio/css/repositorio.css L750-L778](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L750-L778)

 [src/frontend/inicio/css/styles.css L1271-L1577](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1271-L1577)

 [src/frontend/panel/css/styles-panel.css L655-L672](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L655-L672)

---

## Responsive Design Strategy

### Mobile-First Approach

The system uses a mobile-first approach where base styles target mobile devices, with progressively enhanced layouts for larger screens:

```mermaid
flowchart TD

Base["Base Styles<br>Mobile < 640px"]
Small["Small Tablets<br>640px+"]
Medium["Tablets<br>768px+"]
Large["Desktop<br>1024px+"]
MobileNav["Mobile navigation<br>Vertical stack<br>Toggle menu"]
DesktopNav["Desktop navigation<br>Horizontal flex<br>No toggle"]
OneCol["1-column grids"]
TwoCol["2-column grids"]
ThreeCol["3-4 column grids"]

Base --> MobileNav
Medium --> DesktopNav
Base --> OneCol
Small --> TwoCol
Large --> ThreeCol

subgraph subGraph0 ["Breakpoint Strategy"]
    Base
    Small
    Medium
    Large
    Base --> Small
    Small --> Medium
    Medium --> Large
end
```

**Key Breakpoints:**

| Breakpoint | Width | Primary Changes |
| --- | --- | --- |
| Mobile Base | `< 640px` | Single column, stacked navigation, simplified layouts |
| Small | `640px+` | 2-column grids, expanded search filters |
| Medium | `768px+` | Desktop navigation, horizontal menu, hero side-by-side |
| Large | `1024px+` | 3-4 column grids, maximum spacing, largest typography |

**Component Responsiveness:**

**Navbar:**

* Mobile: Hamburger toggle, vertical dropdown menu
* Desktop (768px+): Horizontal menu, no toggle

**Hero Section:**

* Mobile: Stacked (image below text)
* Desktop (768px+): Side-by-side flex layout

**Resource Grids:**

* Mobile: 1 column
* Tablet (640px+): 2 columns
* Desktop (1024px+): 3-4 columns

**Typography:**

* `h1`: 2.5rem mobile → 3rem desktop
* `h2`: 2rem mobile → 2.25rem desktop

**Utility Classes:**

The system doesn't heavily rely on breakpoint-specific utilities, instead using responsive grid systems and component-level media queries.

**Sources:** [src/frontend/inicio/css/styles.css L197-L649](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L197-L649)

 [src/frontend/repositorio/css/repositorio.css L337-L415](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L337-L415)

 [src/frontend/panel/css/styles-panel.css L259-L272](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L259-L272)

---

## File Organization and Usage

### Stylesheet Distribution

```mermaid
flowchart TD

CoreCSS["styles.css<br>8.64 importance<br>Landing page + global"]
RepoCSS["repositorio.css<br>3.28 importance<br>Repository browser"]
PanelCSS["styles-panel.css<br>3.04 importance<br>User dashboard"]
Index["index.php"]
Login["login.php"]
Repositorio["repositorio.php"]
VerVideo["ver_video.php"]
VerLibro["ver_libro.php"]
VerDoc["ver_documento.php"]
PanelUsuario["panel-usuario.php"]
Perfil["perfil.php"]

CoreCSS --> Index
CoreCSS --> Login
CoreCSS --> RepoCSS
RepoCSS --> Repositorio
RepoCSS --> VerVideo
RepoCSS --> VerLibro
RepoCSS --> VerDoc
CoreCSS --> PanelCSS
PanelCSS --> PanelUsuario
PanelCSS --> Perfil

subgraph subGraph4 ["Pages Using Panel"]
    PanelUsuario
    Perfil
end

subgraph subGraph3 ["Pages Using Repo"]
    Repositorio
    VerVideo
    VerLibro
    VerDoc
end

subgraph subGraph2 ["Pages Using Core"]
    Index
    Login
end

subgraph subGraph1 ["Feature-Specific Styles"]
    RepoCSS
    PanelCSS
end

subgraph subGraph0 ["Core Styles"]
    CoreCSS
end
```

**Style Inheritance:**

1. **Core Styles** ([src/frontend/inicio/css/styles.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css) ) - Base design system * CSS variables (colors, typography, shadows, etc.) * Global resets and base element styles * Common components (navbar, buttons, cards) * Landing page specific styles (hero, features, CTA) * Footer styles
2. **Repository Styles** ([src/frontend/repositorio/css/repositorio.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css) ) - Extends core * Redefines CSS variables (same values for consistency) * Repository-specific header and search components * Resource card variations * Tab navigation system * Filter components
3. **Panel Styles** ([src/frontend/panel/css/styles-panel.css](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css) ) - Extends core * Redefines CSS variables (same values) * User dashboard specific layouts * Tab navigation system (different from repo) * Empty state components * User profile header * Badge and achievement styles

**Common Pattern:**

Each stylesheet starts by redefining the CSS variables, ensuring consistency even if stylesheets are loaded independently:

```css
:root {
  --color-coffee: #654321;
  --color-coffee-dark: #4a3219;
  /* ... etc ... */
}
```

**Sources:** [src/frontend/inicio/css/styles.css L1-L3000](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L1-L3000)

 [src/frontend/repositorio/css/repositorio.css L1-L1700](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L1-L1700)

 [src/frontend/panel/css/styles-panel.css L1-L2500](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L1-L2500)

---

## Special Effects and Animations

### Transition Patterns

Common transition patterns used throughout:

```
/* Standard interactive element */
transition: all 0.3s ease;

/* Transform-specific (better performance) */
transition: transform 0.3s ease, box-shadow 0.3s ease;

/* Smooth background changes */
transition: background-color 0.3s ease;
```

### Hover Effects

**Card Lift:**

```
.resource-card:hover {
  transform: translateY(-10px);
  box-shadow: var(--shadow-xl);
}
```

**Button Lift:**

```
.btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 12px rgba(101, 67, 33, 0.3);
}
```

**Image Zoom:**

```
.resource-card:hover .resource-card__image {
  transform: scale(1.1);
  filter: brightness(1.05);
}
```

**Icon Rotation:**

```
.feature-card:hover .feature-card__icon {
  transform: rotate(360deg) scale(1.1);
}
```

### Animations

**Spin Animation** (for decorative elements):

```
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.avatar-decoration {
  animation: spin 30s linear infinite;
}
```

**Ripple Effect** (for buttons):

```
@keyframes ripple {
  /* Defined in styles but not shown in provided excerpt */
}
```

**Fade In Up** (for hero sections):

```
@keyframes fadeInUp {
  0% {
    opacity: 0;
    transform: translateY(30px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
```

**Sources:** [src/frontend/panel/css/styles-panel.css L333-L340](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/css/styles-panel.css#L333-L340)

 [src/frontend/repositorio/css/repositorio.css L491-L501](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/css/repositorio.css#L491-L501)

---

## Utility Classes

### Text Utilities

| Class | Property | Value |
| --- | --- | --- |
| `.text-center` | `text-align` | `center` |
| `.text-coffee` | `color` | `var(--color-coffee)` |
| `.text-accent` | `color` | `var(--color-coffee-light)` |
| `.text-white` | `color` | `var(--color-white)` |
| `.text-gray` | `color` | `var(--color-gray-600)` |
| `.text-light` | `color` | `var(--color-gray-400)` |
| `.uppercase` | `text-transform` | `uppercase` |

### Font Weight Utilities

| Class | Property | Value |
| --- | --- | --- |
| `.font-bold` | `font-weight` | `700` |
| `.font-semibold` | `font-weight` | `600` |
| `.font-medium` | `font-weight` | `500` |

### Display Utilities

| Class | Usage |
| --- | --- |
| `.hidden` | `display: none` - Used for mobile menu toggle |

**Sources:** [src/frontend/inicio/css/styles.css L207-L270](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/css/styles.css#L207-L270)

---

## Best Practices and Conventions

### Component Naming (BEM-like)

The codebase follows a BEM-inspired naming convention:

```
.block__element--modifier
```

Examples:

* `.navbar__logo`
* `.navbar__menu-item--active`
* `.resource-card__image-container`
* `.btn--primary`

### CSS Variable Usage

Always use CSS variables for:

* Colors (never hard-code hex values in component styles)
* Shadow levels
* Border radii
* Font families
* Common spacing values

### Hover State Patterns

Standard hover effects:

1. Lift element: `transform: translateY(-Xpx)`
2. Enhance shadow: Apply deeper shadow level
3. Color transition: Darken or lighten colors
4. Scale transforms: Usually `scale(1.05)` to `scale(1.1)`

### Z-Index Scale

Implicit z-index hierarchy:

* Navbar: `z-index: 100`
* Dropdowns/Modals: `z-index: 1000`
* Background patterns: `z-index: -1`
* Tooltips: `z-index: 1`

### Responsive Images

Images use:

```
img {
  max-width: 100%;
  height: auto;
  display: block;
}
```

### Flexbox vs Grid

* **Flexbox**: Used for component-level layouts (navbar, card content, button groups)
* **Grid**: Used for page-level layouts (resource grids, multi-column sections)

**Sources:** All CSS files referenced throughout this document