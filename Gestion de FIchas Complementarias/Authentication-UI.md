# Authentication UI

> **Relevant source files**
> * [app/login/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx)
> * [components/features.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/features.tsx)
> * [components/login-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx)

## Purpose and Scope

This document describes the user interface components responsible for user authentication in the SENA Gestión Complementarias system. It covers the login page layout, login form component, client-side authentication flow, form validation, and error handling.

For backend authentication endpoints and JWT token generation, see [Authentication Endpoints](/axchisan/gestionComplementarias/6.3-authentication-endpoints). For the overall authentication architecture and security model, see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization). For navigation components and protected route handling, see [Layout and Navigation](/axchisan/gestionComplementarias/5.1-layout-and-navigation).

---

## Login Page Layout

The login page is implemented in [app/login/page.tsx L1-L123](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L1-L123)

 as the primary entry point for unauthenticated users. It features a two-column responsive layout with institutional branding on the left and the login form on the right.

### Layout Structure

```

```

**Sources:** [app/login/page.tsx L1-L123](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L1-L123)

### Visual Design Elements

The page uses a gradient background (`bg-gradient-to-br from-green-50 to-green-100`) and implements the following design patterns:

| Component | Styling | Purpose |
| --- | --- | --- |
| Left Panel | `bg-gradient-to-br from-green-600 to-green-800` | Institutional branding area with SENA green colors |
| Decorative Elements | `bg-white/10 rounded-full` | Abstract circular shapes for visual interest [app/login/page.tsx L13-L14](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L13-L14) |
| Right Panel | White background | Clean form area with centered content |
| Container | `max-w-6xl rounded-2xl shadow-2xl` | Card-style container with shadow depth |
| Grid Layout | `lg:grid-cols-2` | Responsive two-column layout, stacks on mobile |

The left panel displays:

* SENA logo at 80x80 pixels [app/login/page.tsx L18-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L18-L24)
* Four feature highlights with bullet points [app/login/page.tsx L41-L57](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L41-L57)
* Center identification information [app/login/page.tsx L59-L66](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L59-L66)

**Sources:** [app/login/page.tsx L7-L69](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L7-L69)

---

## LoginForm Component

The `LoginForm` component is the core authentication UI element, implemented in [components/login-form.tsx L1-L179](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L1-L179)

 It manages form state, handles user input, and coordinates with the authentication context.

### Component Architecture

```

```

**Sources:** [components/login-form.tsx L20-L179](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L20-L179)

### Form Data Interface

The component defines a `LoginData` interface to type the form state:

```

```

This interface is used for the `formData` state variable initialized at [components/login-form.tsx L23-L27](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L23-L27)

**Sources:** [components/login-form.tsx L14-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L14-L18)

---

## Authentication Flow

The following diagram illustrates the client-side authentication flow from form submission to navigation:

```

```

**Sources:** [components/login-form.tsx L37-L56](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L37-L56)

### Authentication Integration

The `LoginForm` component integrates with the authentication system through the `useAuth` hook:

* **Hook Import**: [components/login-form.tsx L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L12-L12)
* **Hook Usage**: [components/login-form.tsx L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L22-L22)
* **Login Method Call**: [components/login-form.tsx L43](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L43-L43)

The `login` function is called with three parameters:

1. `email` - User's institutional email
2. `password` - User's password
3. `rememberMe` - Boolean flag for session persistence

The function returns a result object with `success` boolean and optional `error` string. On success, the user is redirected to the root path `/` [components/login-form.tsx L47](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L47-L47)

 On failure, the error is displayed to the user [components/login-form.tsx L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L49-L49)

**Sources:** [components/login-form.tsx L22-L50](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L22-L50)

---

## Form Fields and Validation

### Email Field

The email input field is rendered at [components/login-form.tsx L68-L88](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L68-L88)

 with the following characteristics:

| Property | Value | Purpose |
| --- | --- | --- |
| Type | `email` | HTML5 email validation |
| Required | `true` | Cannot submit empty |
| Placeholder | `tu.nombre@sena.edu.co` | Format guidance |
| Icon | `Mail` from lucide-react | Visual indicator |
| Class | `pl-10 h-12` | Left padding for icon, fixed height |
| Disabled | `isLoading \|\| authLoading` | Prevent input during submission |

The field uses controlled component pattern with `value={formData.email}` and `onChange` handler calling `handleInputChange("email", e.target.value)`.

**Sources:** [components/login-form.tsx L69-L88](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L69-L88)

### Password Field

The password input field at [components/login-form.tsx L90-L122](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L90-L122)

 includes password visibility toggle functionality:

| Property | Value | Purpose |
| --- | --- | --- |
| Type | `showPassword ? "text" : "password"` | Toggle visibility |
| Required | `true` | Cannot submit empty |
| Placeholder | `Ingresa tu contraseña` | User guidance |
| Icon | `Lock` from lucide-react | Visual security indicator |
| Toggle Button | `Eye` / `EyeOff` icons | Click to show/hide password |
| Class | `pl-10 pr-10 h-12` | Padding for both icons |

The toggle button at [components/login-form.tsx L109-L120](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L109-L120)

 updates the `showPassword` state variable, switching between text and password input types.

**Sources:** [components/login-form.tsx L91-L122](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L91-L122)

### Remember Me Checkbox

The remember me feature is implemented as a Radix UI checkbox at [components/login-form.tsx L127-L135](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L127-L135)

:

* Controlled by `formData.rememberMe` boolean
* Updates state via `onCheckedChange` callback
* Label: "Recordar sesión" (Remember session)
* Disabled during loading states

This value is passed to the `login` function and affects token persistence strategy in the authentication context.

**Sources:** [components/login-form.tsx L124-L136](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L124-L136)

---

## Input Handling and State Updates

### Input Change Handler

The `handleInputChange` function at [components/login-form.tsx L32-L35](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L32-L35)

 provides centralized input handling:

```

```

This function:

1. Updates the specific field in `formData` using spread operator
2. Clears any existing error message when user modifies input
3. Accepts both string (email, password) and boolean (rememberMe) values

**Sources:** [components/login-form.tsx L32-L35](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L32-L35)

### Form Submission Handler

The `handleSubmit` function at [components/login-form.tsx L37-L56](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L37-L56)

 orchestrates the authentication process:

```

```

Error handling includes:

* **Invalid credentials**: Displays custom error from API or generic message [components/login-form.tsx L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L49-L49)
* **Network errors**: Catches exceptions and displays connection error [components/login-form.tsx L51-L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L51-L52)
* **Loading state**: Always reset in `finally` block [components/login-form.tsx L53-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L53-L55)

**Sources:** [components/login-form.tsx L37-L56](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L37-L56)

---

## Loading States and User Feedback

### Loading Indicators

The component tracks loading state through two sources:

1. **Local `isLoading` state**: Set during form submission [components/login-form.tsx L29-L54](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L29-L54)
2. **Auth context `authLoading`**: Global authentication state [components/login-form.tsx L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L22-L22)

Both states are used to disable form inputs [components/login-form.tsx L85-L150](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L85-L150)

 and modify the submit button:

```

```

The submit button at [components/login-form.tsx L147-L160](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L147-L160)

 displays conditional content:

| State | Display |
| --- | --- |
| Loading | `<Loader2>` spinning icon + "Iniciando sesión..." |
| Not Loading | "Iniciar Sesión" |

**Sources:** [components/login-form.tsx L147-L160](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L147-L160)

### Error Alert

Error messages are displayed using the Alert component from [components/login-form.tsx L60-L65](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L60-L65)

:

```

```

The alert only renders when `error` state has a value and uses the destructive variant (red styling) to indicate failure.

**Sources:** [components/login-form.tsx L60-L65](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L60-L65)

---

## Security Features

### Security Notice Card

A security information card is displayed at [components/login-form.tsx L163-L176](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L163-L176)

 to inform users about security measures:

* **Icon**: `Shield` from lucide-react [components/login-form.tsx L167](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L167-L167)
* **Title**: "Seguridad"
* **Message**: "Tu sesión está protegida con encriptación SSL. Nunca compartas tus credenciales."

This serves both as a trust indicator and security reminder.

**Sources:** [components/login-form.tsx L163-L176](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L163-L176)

### Password Visibility Controls

The password field implements secure password entry with optional visibility:

* **Default state**: Password masked (type="password")
* **Toggle mechanism**: Eye icon button at [components/login-form.tsx L109-L120](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L109-L120)
* **Icons**: `Eye` (show) and `EyeOff` (hide) from lucide-react
* **Accessibility**: Button is disabled during loading states

This allows users to verify their password entry while maintaining security by default.

**Sources:** [components/login-form.tsx L28-L120](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L28-L120)

### Input Security Attributes

All form inputs include security-relevant attributes:

* **Email validation**: HTML5 `type="email"` provides basic format validation
* **Required fields**: Both email and password are required attributes
* **Disabled during submission**: Prevents race conditions and duplicate submissions
* **Error clearing**: Automatic error clearing on input change prevents confusion

**Sources:** [components/login-form.tsx L77-L108](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L77-L108)

---

## Navigation and Footer Links

### Registration Link

The login page includes a registration call-to-action at [app/login/page.tsx L83-L88](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L83-L88)

:

* Text: "¿No tienes cuenta?"
* Link text: "Solicitar acceso"
* Target: `/registro` route
* Styling: Green hover effect matching SENA branding

### Support Link

A support access link is provided at [app/login/page.tsx L89-L94](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L89-L94)

:

* Text: "¿Problemas para acceder?"
* Link text: "Contactar soporte"
* Target: `/soporte` route

### Footer Links

The institutional footer at [app/login/page.tsx L100-L118](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L100-L118)

 includes:

* Copyright notice: "© 2025 SENA - Servicio Nacional de Aprendizaje"
* Policy links: Privacy Policy, Terms of Use, Help
* Responsive layout: Stacks on mobile, side-by-side on desktop

**Sources:** [app/login/page.tsx L82-L118](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L82-L118)

---

## Integration with Features Component

The `Features` component (documented in section 5.1) references authentication state to provide conditional navigation. At [components/features.tsx L46-L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/features.tsx#L46-L52)

 the "Comenzar Ahora" button checks authentication:

```

```

This demonstrates how the authentication UI integrates with the broader application navigation flow.

**Sources:** [components/features.tsx L42-L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/features.tsx#L42-L52)

---

## Component Dependencies

### UI Component Library

The login form uses the following Radix UI and custom components:

| Component | Source | Usage |
| --- | --- | --- |
| `Card`, `CardContent` | `@/components/ui/card` | Container styling |
| `Button` | `@/components/ui/button` | Submit button |
| `Input` | `@/components/ui/input` | Text inputs |
| `Checkbox` | `@/components/ui/checkbox` | Remember me |
| `Alert`, `AlertDescription` | `@/components/ui/alert` | Error messages |

**Sources:** [components/login-form.tsx L6-L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L6-L10)

### Icon Library

Lucide React icons used in the authentication UI:

| Icon | Purpose | Location |
| --- | --- | --- |
| `Mail` | Email field indicator | [components/login-form.tsx L75](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L75-L75) |
| `Lock` | Password field indicator | [components/login-form.tsx L97](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L97-L97) |
| `Eye` / `EyeOff` | Password visibility toggle | [components/login-form.tsx L116-L118](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L116-L118) |
| `AlertCircle` | Error alert icon | [components/login-form.tsx L62](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L62-L62) |
| `Loader2` | Loading spinner | [components/login-form.tsx L154](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L154-L154) |
| `Shield` | Security notice icon | [components/login-form.tsx L167](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L167-L167) |

**Sources:** [components/login-form.tsx L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L11-L11)

### Next.js Hooks

* **`useRouter`**: Navigation after successful login [components/login-form.tsx L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L21-L21)
* **`useAuth`**: Authentication context integration [components/login-form.tsx L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L22-L22)

**Sources:** [components/login-form.tsx L21-L22](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx#L21-L22)

---

## Responsive Design

The login page implements responsive breakpoints:

| Breakpoint | Layout | Behavior |
| --- | --- | --- |
| Mobile (< 1024px) | Single column | Left panel stacks above form |
| Desktop (≥ 1024px) | `lg:grid-cols-2` | Two-column side-by-side layout |
| Footer | `sm:flex-row` | Stacks on small, horizontal on larger screens |

The form itself maintains consistent width with `max-w-md mx-auto` [app/login/page.tsx L73](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L73-L73)

 ensuring readability across devices.

**Sources:** [app/login/page.tsx L9-L102](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L9-L102)

---

## Summary

The Authentication UI provides a polished, secure login experience with the following key characteristics:

* **Two-panel design**: Institutional branding with functional form area
* **Comprehensive validation**: HTML5 validation, required fields, error feedback
* **State management**: Controlled components with centralized state updates
* **Loading states**: Visual feedback during authentication process
* **Error handling**: User-friendly error messages with automatic clearing
* **Security features**: Password visibility toggle, SSL notice, input sanitization
* **Responsive design**: Mobile-first approach with desktop optimization
* **Accessibility**: Proper labels, keyboard navigation, disabled states

The component integrates seamlessly with the authentication context (see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization)) and coordinates with backend endpoints (see [Authentication Endpoints](/axchisan/gestionComplementarias/6.3-authentication-endpoints)) to provide a complete authentication solution.