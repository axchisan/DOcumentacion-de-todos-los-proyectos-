# Frontend Components

> **Relevant source files**
> * [app/login/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx)
> * [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)
> * [components/features.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/features.tsx)
> * [components/navigation.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx)
> * [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)
> * [components/solicitudes-list.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx)

## Purpose and Scope

This document describes the React component architecture and UI implementation of the SENA Gestión Complementarias frontend. It covers the component hierarchy, reusable UI patterns, state management strategies, and styling approach using Next.js 15.2.4 with React 18.3.1.

For information about API integration and backend endpoints, see [Backend API](/axchisan/gestionComplementarias/6-backend-api). For authentication flows and protected routes, see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization). For role-specific workflows that these components enable, see [User Roles and Workflows](/axchisan/gestionComplementarias/4-user-roles-and-workflows).

---

## Component Architecture Overview

The frontend follows a component-based architecture using Next.js App Router with a combination of server components and client components. The UI is built on Radix UI primitives with Tailwind CSS for styling.

**Diagram: Frontend Component Hierarchy**

```

```

**Sources:** [components/navigation.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx)

 [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)

 [components/solicitudes-list.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx)

 [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)

 [components/login-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx)

 [app/login/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx)

---

## Technology Stack and UI Libraries

The frontend utilizes a modern React ecosystem with type-safe components and accessible UI primitives.

| Technology | Version | Purpose |
| --- | --- | --- |
| Next.js | 15.2.4 | React framework with App Router, SSR, and API routes |
| React | 18.3.1 | UI library with hooks and concurrent features |
| TypeScript | ^5 | Type safety and IntelliSense |
| Radix UI | Various | Unstyled, accessible component primitives |
| Tailwind CSS | ^3.4.1 | Utility-first styling framework |
| react-hook-form | ^7.54.2 | Form state management and validation |
| Zod | ^3.24.1 | Schema validation for forms |
| Lucide React | ^0.468.0 | Icon library |
| date-fns | ^4.1.0 | Date formatting and manipulation |

**Styling Approach:**

* Tailwind utility classes for layout and spacing: `className="flex items-center space-x-2"`
* Custom color palette using SENA brand colors (green-600, green-700)
* Responsive design with mobile-first breakpoints: `md:`, `lg:` prefixes
* Dark mode not implemented (institutional branding requirement)

**Sources:** [package.json](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json)

 [tailwind.config.ts](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/tailwind.config.ts)

---

## State Management Architecture

The application uses a combination of React Context API for global authentication state and local component state for UI-specific data.

**Diagram: State Management Flow**

```

```

**Sources:** [lib/auth-context.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/lib/auth-context.tsx)

 [components/navigation.tsx L16-L26](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L16-L26)

 [components/coordinador-dashboard.tsx L76-L105](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L76-L105)

 [components/solicitudes-list.tsx L51-L87](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L51-L87)

### AuthContext Implementation

The `AuthProvider` component wraps the entire application and provides authentication state globally:

**Key exports from AuthContext:**

* `user`: Current user object with `{ id, name, email, role, cedula, centroId, centro }`
* `token`: JWT token string for API authentication
* `isAuthenticated`: Boolean indicating login status
* `login(email, password)`: Async function to authenticate
* `logout()`: Function to clear session and redirect

**Usage pattern in components:**

```

```

Components check `isAuthenticated` before rendering protected content and include `token` in fetch headers: `Authorization: Bearer ${token}`.

**Sources:** [lib/auth-context.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/lib/auth-context.tsx)

 [components/navigation.tsx L16](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L16-L16)

---

## Component Communication Patterns

Components communicate through props, callbacks, and shared context. The following patterns are consistently used:

### Parent-to-Child: Props

Parent components pass data and configuration to children via props.

Example: `Navigation` passing role-based navigation items [components/navigation.tsx L49-L82](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L49-L82)

:

```

```

### Child-to-Parent: Callbacks

Child components notify parents of events through callback props.

Example: `SolicitudDetalleModal` with approval/rejection callbacks [components/solicitud-detalle-modal.tsx L100-L107](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L100-L107)

:

```

```

### Sibling Communication: Shared State

Siblings communicate by lifting state to common parent or using context.

Example: `Navigation` and `NotificationPanel` sharing notification state [components/navigation.tsx L14-L26](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L14-L26)

:

* `Navigation` tracks `showNotifications` state and `notificationCount`
* Polling updates count every 30 seconds
* Panel closure triggers count refresh

**Sources:** [components/navigation.tsx L14-L47](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L14-L47)

 [components/solicitud-detalle-modal.tsx L100-L107](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L100-L107)

---

## Form Handling with react-hook-form and Zod

Forms use `react-hook-form` for state management with `Zod` schemas for validation. This provides type-safe form handling with automatic error messages.

**Form Validation Pattern:**

```

```

**Login Form Example** [components/login-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx)

:

* Fields: `email` (email validation), `password` (min 6 characters)
* On submit: calls `login()` from `AuthContext`
* Error handling: displays API errors below form
* Loading state: disables button with spinner

**Sources:** [components/login-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx)

 [components/solicitud-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-form.tsx)

---

## Layout and Navigation Components

### Navigation Component

The `Navigation` component provides the main app header with role-based navigation links.

**Component Structure** [components/navigation.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx)

:

| Section | Lines | Functionality |
| --- | --- | --- |
| State Management | 13-25 | Mobile menu toggle, notification panel visibility, notification count |
| Notification Polling | 18-42 | Fetches unread count every 30 seconds when authenticated |
| Role-Based Links | 49-82 | Returns different nav items based on `user.role` |
| Desktop Navigation | 114-168 | Full navigation with user info and logout button |
| Mobile Navigation | 171-237 | Collapsible hamburger menu for mobile devices |

**Role-Based Navigation Items:**

| Role | Available Routes |
| --- | --- |
| INSTRUCTOR | `/` (Home), `/mis-solicitudes`, `/nueva-solicitud` |
| COORDINADOR | `/` (Home), `/solicitudes-pendientes`, `/todas-solicitudes`, `/reportes-coordinador`, `/gestion-instructores` |
| ADMIN | `/` (Home), `/dashboard-admin`, `/gestion-usuarios`, `/reportes-admin`, `/configuracion` |

**Notification Badge Logic** [components/navigation.tsx L139-L143](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L139-L143)

:

* Shows red badge with count when `notificationCount > 0`
* Displays "99+" if count exceeds 99
* Updates via polling mechanism

**Sources:** [components/navigation.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx)

### NotificationPanel Component

Side panel component that displays user notifications with real-time updates. Integrates with the notification API endpoint.

**Features:**

* Mark as read functionality
* Mark all as read
* Notification types: `NUEVA_SOLICITUD`, `APROBADA`, `RECHAZADA`, `EN_REVISION`
* Click to navigate to related solicitud

**Sources:** [components/notification-panel.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/notification-panel.tsx)

---

## Authentication UI Components

### LoginPage

Full-page authentication screen with institutional branding.

**Layout Structure** [app/login/page.tsx L6-L122](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L6-L122)

:

```

```

**Visual Design:**

* Left panel: Gradient background with SENA logo, system description, and feature list
* Right panel: Clean white background with centered login form
* Decorative circles: Overlaid on left panel for visual interest [app/login/page.tsx L13-L14](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L13-L14)
* Responsive: Stacks vertically on mobile with `grid lg:grid-cols-2`

**Sources:** [app/login/page.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx)

### LoginForm Component

Handles credential submission with validation and error handling.

**Form Fields:**

* `email`: Text input with email validation
* `password`: Password input with minimum length validation
* Submit button: Shows loading spinner during authentication

**Error Handling:**

* Displays API errors in red alert below form
* Field-level validation errors from Zod schema
* Network error handling with user-friendly messages

**Flow:**

1. User enters credentials
2. Form validation via Zod schema
3. Submit calls `login()` from `AuthContext`
4. On success: Redirect to role-appropriate dashboard
5. On failure: Display error message

**Sources:** [components/login-form.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/login-form.tsx)

 [app/login/page.tsx L79](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L79-L79)

---

## Dashboard Components

Dashboard components display role-specific metrics, statistics, and quick actions.

### CoordinadorDashboard Component

Comprehensive dashboard for coordinators with tabs for different views.

**Diagram: CoordinadorDashboard Data Flow**

```

```

**Sources:** [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)

**Statistics Tracked** [components/coordinador-dashboard.tsx L29-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L29-L55)

:

| Category | Metrics |
| --- | --- |
| Solicitudes | Total, pending, approved, rejected |
| Performance | Approval rate percentage, average response time (days), urgent requests (>7 days) |
| Instructors | Total instructors, active instructors, instructors with solicitudes |
| Training | Total enrolled students, approved training hours, active programs |
| Monthly Goals | Processed requests vs. target, response time vs. goal |

**Tab Structure** [components/coordinador-dashboard.tsx L467-L773](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L467-L773)

:

1. **Resumen Ejecutivo**: Performance metrics with progress bars, quick action buttons
2. **Solicitudes Recientes**: Card list of last 5 solicitudes with priority badges
3. **Mis Instructores**: Grid of instructor cards showing active solicitudes and training hours
4. **Mi Perfil**: Personal information and aggregated statistics

**Priority Calculation** [components/coordinador-dashboard.tsx L255-L263](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L255-L263)

:

* `ALTA` (High): Pending > 7 days
* `MEDIA` (Medium): Pending 3-7 days
* `BAJA` (Low): Pending < 3 days or not pending

**Sources:** [components/coordinador-dashboard.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx)

---

## Solicitud Management Components

Components for creating, viewing, listing, and managing training solicitudes.

### SolicitudesList Component

Displays paginated, filterable list of solicitudes with export capabilities.

**Filtering Capabilities** [components/solicitudes-list.tsx L89-L102](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L89-L102)

:

| Filter Type | Implementation | Lines |
| --- | --- | --- |
| Search | Matches program name, solicitud code, or ficha number | 89-93 |
| Estado | Dropdown: All, BORRADOR, PENDIENTE, EN_REVISION, APROBADA, RECHAZADA | 95 |
| Year | Dropdown: All, 2024, 2023 | 97-99 |

**Statistics Display** [components/solicitudes-list.tsx L180-L191](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L180-L191)

:

* 5 metric cards showing total, drafts, pending, approved, rejected counts
* Color-coded badges matching estado colors

**Export Functionality** [components/solicitudes-list.tsx L104-L146](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L104-L146)

:

* PDF export: Calls `/api/solicitudes/{id}/export?format=pdf`
* Excel export: Calls `/api/solicitudes/{id}/export?format=excel`
* Download triggered via blob URL and anchor element click

**Empty States:**

* No solicitudes: Shows "Create your first solicitud" message
* No results: Shows "Adjust filters" message

**Sources:** [components/solicitudes-list.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx)

### SolicitudDetalleModal Component

Modal dialog displaying complete solicitud details with approval/rejection actions for coordinators.

**Information Sections** [components/solicitud-detalle-modal.tsx L246-L566](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L246-L566)

:

| Section | Content | Lines |
| --- | --- | --- |
| Header | Code, status badge, ficha number | 249-264 |
| Instructor Info | Name, cedula, email, especialidad | 266-292 |
| Program Info | Code, duration, modality, cupo, version | 294-337 |
| Company Info | Name, representative, location, NIT | 339-381 |
| Special Programs | Badges for selected special programs/convenios | 383-402 |
| Schedule | Enrollment dates, course dates, detailed weekly schedule | 404-469 |
| Learning Objectives | Numbered list of program objectives | 471-495 |
| Justification | Instructor's justification text | 497-507 |
| Review Comments | Coordinator's review comments (if any) | 509-524 |
| Actions | Approve/Reject buttons with comment textarea (coordinators only) | 526-565 |

**Coordinator Actions** [components/solicitud-detalle-modal.tsx L527-L565](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L527-L565)

:

* Only shown when `userRole === "COORDINADOR"` and `estado === "PENDIENTE"`
* Textarea for adding review comments
* Approve button: Triggers `onApprove(id, comentarios)` callback
* Reject button: Triggers `onReject(id, comentarios)` callback

**Special Programs Display** [components/solicitud-detalle-modal.tsx L207-L226](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L207-L226)

:

* Checks boolean flags on solicitud object
* Displays badges for all enabled programs
* Includes custom program if `otroEspecificar` is filled

**Sources:** [components/solicitud-detalle-modal.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx)

---

## Reusable UI Components

The application uses Radix UI primitives wrapped with custom styling. These components are located in `components/ui/`.

**Core UI Components:**

| Component | Source | Purpose |
| --- | --- | --- |
| Button | `components/ui/button.tsx` | Primary, secondary, outline, ghost variants with sizes |
| Card | `components/ui/card.tsx` | Container with header, title, description, content sections |
| Input | `components/ui/input.tsx` | Text input with focus styling and error states |
| Select | `components/ui/select.tsx` | Dropdown selection with Radix SelectPrimitive |
| Badge | `components/ui/badge.tsx` | Status indicators with color variants |
| Dialog | `components/ui/dialog.tsx` | Modal overlay using Radix DialogPrimitive |
| Tabs | `components/ui/tabs.tsx` | Tab navigation using Radix TabsPrimitive |
| Textarea | `components/ui/textarea.tsx` | Multi-line text input |
| Label | `components/ui/label.tsx` | Form field labels with accessibility |
| Progress | `components/ui/progress.tsx` | Progress bar for metrics |

**Button Variants Usage:**

```

```

**Badge Usage for Status:**

Estado badges follow consistent color coding [components/solicitudes-list.tsx L35-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L35-L49)

:

* BORRADOR: Gray background
* PENDIENTE: Yellow background
* EN_REVISION: Blue background
* APROBADA: Green background with checkmark icon
* RECHAZADA: Red background with X icon

**Sources:** [components/ui/button.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/button.tsx)

 [components/ui/card.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/card.tsx)

 [components/ui/badge.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/badge.tsx)

 [components/solicitudes-list.tsx L148-L178](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L148-L178)

---

## Icon System

The application uses Lucide React for icons, providing consistent, accessible SVG icons.

**Common Icons and Their Usage:**

| Icon | Import | Usage Context |
| --- | --- | --- |
| FileText | `lucide-react` | Solicitudes, documents |
| User | `lucide-react` | User profile, instructor info |
| Building | `lucide-react` | Centro, company info |
| Calendar | `lucide-react` | Dates, scheduling |
| Clock | `lucide-react` | Pending status, time |
| CheckCircle | `lucide-react` | Approved status, success |
| XCircle | `lucide-react` | Rejected status, error |
| AlertTriangle | `lucide-react` | Urgent, warnings |
| Eye | `lucide-react` | View details action |
| Edit | `lucide-react` | Edit action |
| Plus | `lucide-react` | Create new action |
| Search | `lucide-react` | Search/filter functionality |
| BarChart3 | `lucide-react` | Reports, analytics |
| Bell | `lucide-react` | Notifications |

**Icon Sizing Convention:**

* Small icons in badges/buttons: `h-3 w-3`
* Regular icons in UI: `h-4 w-4`
* Large icons in headers: `h-5 w-5`
* Hero/empty state icons: `h-8 w-8` or `h-12 w-12`

**Sources:** [components/navigation.tsx L6](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L6-L6)

 [components/coordinador-dashboard.tsx L9-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L9-L25)

---

## Responsive Design Patterns

The application uses mobile-first responsive design with Tailwind breakpoints.

**Breakpoint Strategy:**

| Breakpoint | Min Width | Usage |
| --- | --- | --- |
| Default (mobile) | 0px | Single column, hamburger menu |
| `sm:` | 640px | Slight layout adjustments |
| `md:` | 768px | Two-column grids, show desktop nav |
| `lg:` | 1024px | Three-column grids, full layouts |
| `xl:` | 1280px | Maximum width containers |

**Navigation Responsive Behavior** [components/navigation.tsx L114-L237](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L114-L237)

:

* Desktop (≥768px): Horizontal nav bar with full links
* Mobile (<768px): Hamburger menu with collapsible drawer
* Notification button: Always visible on both versions

**Grid Responsive Patterns:**

```

```

**Mobile Optimizations:**

* Touch-friendly button sizes (minimum 44x44px)
* Reduced spacing on small screens
* Simplified layouts with vertical stacking
* Bottom sheet style for modals on mobile

**Sources:** [components/navigation.tsx L170-L237](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L170-L237)

 [components/coordinador-dashboard.tsx L383-L464](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L383-L464)

---

## Data Loading and Error States

Components implement consistent patterns for loading, error, and empty states.

**Loading State Pattern:**

```

```

Used in:

* [components/coordinador-dashboard.tsx L295-L304](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L295-L304)
* [components/solicitudes-list.tsx L192-L201](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L192-L201)

**Error State Pattern:**

```

```

Used in: [components/solicitudes-list.tsx L203-L218](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L203-L218)

**Empty State Pattern:**

```

```

Used in: [components/solicitudes-list.tsx L394-L414](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L394-L414)

**Sources:** [components/coordinador-dashboard.tsx L295-L304](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L295-L304)

 [components/solicitudes-list.tsx L192-L218](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L192-L218)

---

## Component Testing and Type Safety

All components are written in TypeScript with strict type checking enabled.

**Interface Definitions:**

Components define clear TypeScript interfaces for props and data structures.

Example from `SolicitudDetalleModal` [components/solicitud-detalle-modal.tsx L15-L98](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L15-L98)

:

```

```

**Type Safety Benefits:**

* IntelliSense autocomplete in VSCode
* Compile-time error detection
* Refactoring safety
* Self-documenting code

**Zod Schema Integration:**

Forms use Zod for runtime validation that also infers TypeScript types:

```

```

**Sources:** [components/solicitud-detalle-modal.tsx L15-L107](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitud-detalle-modal.tsx#L15-L107)

 [components/coordinador-dashboard.tsx L29-L74](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L29-L74)

---

## Performance Optimizations

The frontend implements several performance optimization techniques.

**Client Component Marking:**

Only interactive components are marked with `"use client"` directive to enable server components where possible:

* [components/navigation.tsx L1](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L1-L1)
* [components/coordinador-dashboard.tsx L1](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/coordinador-dashboard.tsx#L1-L1)
* [components/solicitudes-list.tsx L1](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/solicitudes-list.tsx#L1-L1)

**Polling Optimization:**

Notification count polling uses cleanup to prevent memory leaks [components/navigation.tsx L18-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L18-L25)

:

```

```

**Conditional Rendering:**

Components check authentication state before rendering protected content to avoid unnecessary API calls:

```

```

**Image Optimization:**

Next.js Image component used for logos [app/login/page.tsx L18-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L18-L24)

:

* Automatic format optimization (WebP)
* Lazy loading by default
* Responsive image sizing

**Sources:** [components/navigation.tsx L1-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/navigation.tsx#L1-L25)

 [app/login/page.tsx L18-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/app/login/page.tsx#L18-L24)

---

## Accessibility Features

Components implement WCAG 2.1 accessibility standards through Radix UI primitives.

**Keyboard Navigation:**

* All interactive elements are keyboard accessible
* Tab order follows visual order
* Enter/Space trigger buttons
* Escape closes modals and dropdowns

**ARIA Labels:**

* Radix components include proper ARIA attributes automatically
* Dialog components have `aria-labelledby` pointing to title
* Buttons have descriptive `aria-label` when using only icons

**Focus Management:**

* Visible focus indicators on all interactive elements
* Focus trap in modal dialogs
* Focus return to trigger element when closing modals

**Screen Reader Support:**

* Semantic HTML elements (`<nav>`, `<main>`, `<button>`)
* Status messages announced via ARIA live regions
* Form errors associated with inputs via `aria-describedby`

**Color Contrast:**

* All text meets WCAG AA standards
* Status badges have sufficient contrast ratios
* Focus indicators are highly visible

**Sources:** [components/ui/button.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/button.tsx)

 [components/ui/dialog.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/dialog.tsx)

 [components/ui/label.tsx](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/components/ui/label.tsx)