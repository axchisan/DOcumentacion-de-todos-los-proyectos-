# Authentication Interface

> **Relevant source files**
> * [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

## Purpose and Scope

This document describes the authentication interface layer defined in [css/login.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css)

 This stylesheet implements the visual presentation and user experience for user authentication, including login forms, registration workflows, and role selection interfaces. The authentication interface serves as the entry point for all user types before routing them to their role-specific dashboards.

This page covers only the presentation layer (CSS styling and component structure). For backend authentication logic and session management, see [Authentication Flow](/axchisan/AmericanFood/7.2-authentication-flow). For the underlying `usuarios` table structure, see [Core Tables](/axchisan/AmericanFood/2.2-core-tables). For security implementation details like password hashing and validation, see [Security Practices](/axchisan/AmericanFood/4.2-security-practices).

**Sources**: [css/login.css L1-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L1-L181)

---

## Component Architecture

The authentication interface implements a card-based design system with three primary component categories: authentication cards, form components, and role selection interfaces. All components are rendered on a gradient background wrapper and inherit base styling from [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

```

```

**Sources**: [css/login.css L2-L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L2-L27)

 [css/login.css L44-L46](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L44-L46)

 [css/login.css L114-L154](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L114-L154)

---

## Login and Registration Cards

The authentication interface uses two card variants that share structural styling but differ in maximum width constraints. Both cards implement a three-section layout: header, form body, and footer.

### Card Structure

| CSS Class | Purpose | Dimensions | Visual Properties |
| --- | --- | --- | --- |
| `.login-body` | Full-page background container | `min-height: 100vh` | Gradient background, flexbox centering |
| `.login-container` | Card positioning wrapper | `max-width: 450px` | Horizontal padding, responsive |
| `.login-card` | Standard login card | Inherits from container | White background, rounded corners, shadow |
| `.register-card` | Extended registration card | `max-width: 550px` | Larger to accommodate additional fields |

### Visual Styling

```

```

The login card header displays the application logo and a descriptive subtitle with the primary brand color `#e63946`. Title text uses `font-weight: 700` for emphasis, while subtitle text uses muted gray `#6c757d`.

**Sources**: [css/login.css L2-L27](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L2-L27)

 [css/login.css L29-L42](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L29-L42)

---

## Form Components

Authentication forms implement a consistent input group pattern with icon prefixes and integrated validation styling. Form controls are stripped of left borders to create visual continuity with the icon prefix section.

### Input Groups

```

```

### Form Element Specifications

| Element | CSS Class | Styling Details | Interaction |
| --- | --- | --- | --- |
| Label | `.form-label` | `font-weight: 500`, `color: #495057` | N/A |
| Icon Prefix | `.input-group-text` | `background-color: #f8f9fa`, no right border | Visual separator |
| Input Field | `.form-control` | No left border, connects to icon | Focus removes default shadow |
| Submit Button | `.btn-primary` | `background-color: #e63946`, hover to `#d32f2f` | Transitions on hover |
| Link | `.forgot-password` | `color: #457b9d`, `font-size: 0.875rem` | Underline on hover |

The form removes Bootstrap's default blue focus shadow (`box-shadow: none`) to maintain visual simplicity, relying instead on subtle border color changes for focus indication.

**Sources**: [css/login.css L48-L75](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L48-L75)

 [css/login.css L77-L85](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L77-L85)

---

## Role Selection Interface

During registration, users must select a role via a modal interface that displays available roles in a responsive grid layout. This component is referenced as `.role-options` and implements click-to-select functionality with visual state feedback.

```

```

### Role Card States

| State | CSS Class | Border Color | Background Color | Trigger |
| --- | --- | --- | --- | --- |
| Default | `.role-option` | `#e9ecef` | Transparent | Initial render |
| Hover | `.role-option:hover` | `#457b9d` | `rgba(69, 123, 157, 0.05)` | Mouse over |
| Selected | `.role-option.selected` | `#e63946` | `rgba(230, 57, 70, 0.05)` | User click |

Each role card displays an icon (2rem font size), a role name heading, and a descriptive paragraph. The grid layout automatically adjusts columns based on available width with a minimum column width of 200px, collapsing to a single column on mobile devices.

**Sources**: [css/login.css L113-L154](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L113-L154)

---

## Password Strength Indicator

The registration form includes a password strength visualization component that provides real-time feedback on password quality. This component uses Bootstrap's progress bar pattern with custom sizing.

### Component Structure

```

```

The password strength indicator is styled with reduced font size (`0.75rem`) to maintain visual hierarchy without overwhelming the form. The progress bar includes minimal bottom margin (`0.25rem`) to maintain tight spacing with the strength text label.

**Sources**: [css/login.css L157-L163](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L157-L163)

---

## Social Authentication Options

The interface includes a social login section that arranges third-party authentication buttons in a horizontal flex layout. This component provides visual separation from standard email/password authentication.

```

```

On screens wider than 576px, social login buttons are displayed horizontally with 1rem gap spacing. On mobile devices, the layout switches to vertical stacking via `flex-direction: column` for improved touch target accessibility.

**Sources**: [css/login.css L87-L91](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L87-L91)

 [css/login.css L174-L176](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L174-L176)

---

## Footer Navigation

The authentication cards include a footer section for secondary navigation, typically used to switch between login and registration modes or return to the public homepage.

### Footer Structure

| CSS Class | Purpose | Styling |
| --- | --- | --- |
| `.login-footer` | Footer container | Top border `1px solid #e9ecef`, padding `1.5rem` |
| `.login-footer a` | Navigation links | Color `#457b9d`, no default underline |
| `.login-footer a:hover` | Hover state | Text underline on hover |
| `.back-to-home` | Homepage link | Inline-block display, top margin `1rem`, font size `0.875rem` |

Links within the footer use the secondary brand color `#457b9d` to differentiate them from primary action buttons while maintaining sufficient contrast for accessibility.

**Sources**: [css/login.css L93-L111](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L93-L111)

---

## Responsive Design Breakpoints

The authentication interface implements a mobile-first responsive design strategy with breakpoint adjustments at 576px screen width. This ensures optimal usability on smartphones and tablets.

```

```

### Breakpoint Adjustments

| Component | Desktop (≥576px) | Mobile (<576px) | Impact |
| --- | --- | --- | --- |
| `.login-container` | `padding: 0 1rem` | `padding: 0 0.5rem` | Preserves edge spacing on small screens |
| `.login-form` | `padding: 1.5rem 2rem 2rem` | `padding: 1.5rem 1rem 1.5rem` | Reduces horizontal padding |
| `.social-login` | `flex-direction: row` | `flex-direction: column` | Stacks buttons vertically |
| `.role-options` | Auto-fill grid | `grid-template-columns: 1fr` | Single column role cards |

The responsive design prioritizes touch-friendly interactions on mobile devices by increasing vertical spacing and ensuring sufficient tap target sizes while reducing horizontal padding to maximize usable width.

**Sources**: [css/login.css L165-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L165-L181)

---

## Integration with Global Styles

The authentication interface inherits foundational styles from [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)

 and overrides specific properties to create a distinct authentication experience separated from the main application interface.

### Color Palette Mapping

```

```

While the global stylesheet defines `--primary-color` as `#FF6B35`, the login interface overrides this with `#e63946` for a distinct visual identity. This creates a clear visual separation between the public/authenticated sections and the authentication flow itself.

### Button Style Inheritance

The `.btn-primary` class is defined in both stylesheets with different color values. The login stylesheet's definition takes precedence when applied to authentication pages:

* **Global Definition**: `background-color: var(--primary-color)` ([css/style.css L85-L88](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L85-L88) )
* **Login Override**: `background-color: #e63946` ([css/login.css L77-L80](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L77-L80) )

This cascade pattern allows the authentication interface to maintain visual consistency with Bootstrap's button patterns while applying brand-specific colors.

**Sources**: [css/login.css L77-L85](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L77-L85)

 [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

 [css/style.css L85-L93](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L85-L93)

---

## Form Validation States

While specific validation state styles are not explicitly defined in `login.css`, the interface is designed to integrate with Bootstrap's validation framework through standard class patterns. The input group structure supports validation feedback messages positioned below form controls.

Form validation integration points:

* `.form-control` accepts Bootstrap validation classes (`.is-valid`, `.is-invalid`)
* `.invalid-feedback` and `.valid-feedback` elements can be inserted after input groups
* Backend validation errors from [crud/registro_INSERT.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php)  are displayed via session messages

The authentication interface relies on the backend validation pipeline documented in [User Registration System](/axchisan/AmericanFood/4.1-user-registration-system) for email format validation, role validation, and password strength requirements.

**Sources**: [css/login.css L58-L65](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L58-L65)

---

## Component Usage Summary

The following table maps UI components to their corresponding CSS classes and typical usage contexts:

| Component | Primary Class | Used In | Key Features |
| --- | --- | --- | --- |
| Page Background | `.login-body` | All auth pages | Gradient background, flexbox centering |
| Login Card | `.login-card` | Login page | Standard width (450px max) |
| Registration Card | `.register-card` | Registration page | Extended width (550px max) |
| Card Header | `.login-header` | Both cards | Logo and title section |
| Form Container | `.login-form` | Both cards | Padded form area |
| Input Group | `.input-group`, `.form-control` | All forms | Icon prefix + input field |
| Submit Button | `.btn-primary` | All forms | Brand-colored action button |
| Password Recovery | `.forgot-password` | Login page | Secondary action link |
| Social Auth | `.social-login` | Both pages | Third-party login buttons |
| Role Selector | `.role-options`, `.role-option` | Registration modal | Grid of selectable role cards |
| Password Meter | `.password-strength` | Registration page | Real-time password feedback |
| Footer Links | `.login-footer` | Both cards | Mode switching and navigation |

**Sources**: [css/login.css L1-L181](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/login.css#L1-L181)