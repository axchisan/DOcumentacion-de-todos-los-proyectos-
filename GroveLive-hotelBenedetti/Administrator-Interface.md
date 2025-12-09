# Administrator Interface

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)

## Purpose and Scope

This page documents the Administrator Interface, a role-specific web interface that provides hotel administrators with comprehensive tools for managing employees and rooms, along with a dashboard displaying real-time operational statistics and visualizations.

For information about authentication and role-based access control that determines administrator access, see [Authentication System](/GroveLive/hotelBenedetti/3.1-authentication-system). For other role-specific interfaces (receptionist, maid, client), see their respective pages under section 3.

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

 [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

---

## Interface Components Overview

The administrator interface consists of three primary sections, each with dedicated functionality:

| Section | Purpose | Key Operations |
| --- | --- | --- |
| Dashboard | Display operational statistics and visualizations | View room status counts, employee distribution, Chart.js pie/bar charts |
| Employees | Manage hotel staff (recepcionistas, mucamas) | Create, read, update, delete employee records |
| Rooms | Manage room inventory | Create, read, update, delete room records |

The interface uses a single-page application pattern where navigation between sections does not trigger full page reloads. Instead, sections are toggled between visible and hidden states using CSS classes.

**Sources:** [assets/js/admin.js L10-L30](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L10-L30)

 [assets/css/StyleAdmin.css L104-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L104-L111)

---

## Architecture and Navigation Flow

### Three-Tier Section Architecture

```

```

The navigation system uses event listeners attached to navigation buttons [assets/js/admin.js L13-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L13-L27)

 When a button is clicked, all sections receive the `.hidden` class [assets/css/StyleAdmin.css L109-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L109-L111)

 then the target section has this class removed, making it visible. The system also triggers appropriate data loading functions based on the active section.

**Sources:** [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)

 [assets/css/StyleAdmin.css L104-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L104-L111)

---

## Dashboard Section

### Statistics Display

The dashboard provides real-time operational metrics through six stat cards displaying:

| Metric | Data Source | Display Element ID |
| --- | --- | --- |
| Total Rooms | `COUNT(*) FROM habitaciones` | `#total-habitaciones` |
| Occupied Rooms | `COUNT(*) WHERE estado = 'ocupada'` | `#habitaciones-ocupadas` |
| Available Rooms | `COUNT(*) WHERE estado = 'disponible'` | `#habitaciones-disponibles` |
| Cleaning Rooms | `COUNT(*) WHERE estado = 'en limpieza'` | `#habitaciones-en-limpieza` |
| Total Receptionists | `COUNT(*) FROM empleados WHERE cargo = 'recepcionista'` | `#total-recepcionistas` |
| Total Maids | `COUNT(*) FROM empleados WHERE cargo = 'mucama'` | `#total-mucamas` |

The backend queries execute separate COUNT operations for each metric [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

 The frontend receives a JSON response with a `stats` object containing all six values, which are then inserted into their respective DOM elements [assets/js/admin.js L46-L53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L46-L53)

**Sources:** [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

 [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

### Chart.js Visualizations

The dashboard implements two Chart.js visualizations to provide graphical representations of operational data:

#### Room Status Pie Chart

```

```

The pie chart visualizes room status distribution with three segments:

* **Occupied** (red `#FF6384`): Rooms with `estado = 'ocupada'`
* **Available** (blue `#36A2EB`): Rooms with `estado = 'disponible'`
* **Cleaning** (yellow `#FFCE56`): Rooms with `estado = 'en limpieza'`

Implementation details:

* Chart type: `'pie'` [assets/js/admin.js L66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L66-L66)
* Data binding: `stats.habitaciones_ocupadas`, `stats.habitaciones_disponibles`, `stats.habitaciones_en_limpieza` [assets/js/admin.js L71-L73](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L71-L73)
* Instance management: Previous chart instance destroyed before creating new one [assets/js/admin.js L56-L58](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L56-L58)

#### Employee Distribution Bar Chart

The bar chart displays employee counts by role type (Recepcionistas vs Mucamas) using:

* Chart type: `'bar'` [assets/js/admin.js L95](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L95-L95)
* Y-axis: Count with `beginAtZero: true` and `stepSize: 1` [assets/js/admin.js L117-L120](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L117-L120)
* Colors: Blue for receptionists, yellow for maids [assets/js/admin.js L101](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L101-L101)

Both chart instances are stored in global variables (`roomStatusChartInstance`, `employeeChartInstance`) to enable proper cleanup when the dashboard reloads [assets/js/admin.js L5-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L6)

**Sources:** [assets/js/admin.js L56-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L56-L124)

 [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

---

## Employee Management System

### Employee CRUD Operations Flow

```

```

**Sources:** [assets/js/admin.js L143-L352](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L352)

 [controllers/AdminController.php L20-L309](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L309)

### Employee Data Validation Rules

The system enforces strict validation rules during employee creation and updates:

| Validation Type | Rule | Implementation | Error Response |
| --- | --- | --- | --- |
| Role Restriction | Only `'recepcionista'` or `'mucama'` allowed | [controllers/AdminController.php L29-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L121) | "Rol no válido. Solo se permiten recepcionistas y mucamas." |
| Email Uniqueness | No duplicate emails across all users | [controllers/AdminController.php L38-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L135) | "El email ya está registrado." |
| DNI Uniqueness | No duplicate DNI values | [controllers/AdminController.php L51-L149](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L149) | "El DNI ya está registrado." |
| Password Hashing | All passwords hashed with `PASSWORD_BCRYPT` | [controllers/AdminController.php L26-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L26-L111) | N/A |
| Optional Password Update | Password field can be empty during updates | [controllers/AdminController.php L111-L161](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L111-L161) | N/A |

The administrator role cannot create other administrators through this interface; only operational staff (receptionists and maids) can be managed here.

**Sources:** [controllers/AdminController.php L20-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L190)

### Employee Form Structure

The employee form captures seven fields:

* **Hidden Field:** `#employee-id` - Set during edit operations, empty for new employees [assets/js/admin.js L211](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L211-L211)
* **Personal Info:** `#employee-name`, `#employee-lastname` [assets/js/admin.js L212-L213](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L212-L213)
* **Contact Info:** `#employee-email`, `#employee-telefono` [assets/js/admin.js L214-L215](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L214-L215)
* **Identity:** `#employee-dni` [assets/js/admin.js L216](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L216-L216)
* **Security:** `#employee-password` - Required for creation, optional for updates [assets/js/admin.js L217](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L217-L217)
* **Role Selection:** `#employee-role` - Dropdown with recepcionista/mucama options [assets/js/admin.js L218](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L218-L218)

The form submit button text dynamically changes between "Agregar Empleado" and "Actualizar Empleado" based on whether `#employee-id` has a value [assets/js/admin.js L248-L283](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L248-L283)

**Sources:** [assets/js/admin.js L208-L299](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L299)

### Employee Table Display

The employee list renders as an HTML table with columns for:

* Nombre, Apellido, Email, Teléfono, DNI, Rol
* Acciones column containing Edit and Delete buttons

Each row is generated dynamically from the employees array [assets/js/admin.js L168-L182](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L168-L182)

 The Edit button has class `.edit-employee` [assets/css/StyleAdmin.css L251-L255](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L251-L255)

 styled in blue (`#36A2EB`), while Delete button has class `.delete-employee` [assets/css/StyleAdmin.css L257-L261](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L257-L261)

 styled in red (`#FF6384`).

**Sources:** [assets/js/admin.js L143-L205](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L143-L205)

 [assets/css/StyleAdmin.css L212-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L212-L266)

---

## Room Management System

### Room CRUD Operations Flow

The room management operations follow a similar pattern to employee management but interact exclusively with the `Habitacion2` model:

```

```

**Sources:** [assets/js/admin.js L355-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L355-L552)

 [controllers/AdminController.php L191-L316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L191-L316)

### Room Data Structure

Rooms in the system have the following attributes:

| Field | Type | Purpose | Constraints |
| --- | --- | --- | --- |
| `id` | integer | Primary key | Auto-increment |
| `numero_habitacion` | string | Room identifier (e.g., "101") | Must be unique [controllers/AdminController.php L197-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L235) |
| `tipo` | string | Room type (e.g., "individual", "doble", "suite") | No validation constraint |
| `precio` | decimal | Nightly rate | Cast to float [controllers/AdminController.php L194-L221](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L194-L221) |
| `estado` | string | Operational status | Values: 'disponible', 'ocupada', 'en limpieza' |

New rooms default to `estado = 'disponible'` [controllers/AdminController.php L213](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L213-L213)

 Administrators can modify the status field when updating existing rooms [controllers/AdminController.php L222-L242](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L222-L242)

**Sources:** [controllers/AdminController.php L191-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L191-L249)

### Room Validation Logic

The primary validation constraint enforced during room operations is uniqueness of `numero_habitacion`:

**During Creation (addRoom):**

```

```

If any rows are returned, operation fails with "El número de habitación ya existe." [controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207)

**During Update (updateRoom):**

```

```

This allows the room to keep its current number but prevents conflicts with other rooms [controllers/AdminController.php L224-L235](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L224-L235)

The validation pattern mirrors the email/DNI uniqueness checks in employee management, ensuring data integrity across the system.

**Sources:** [controllers/AdminController.php L191-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L191-L245)

### Room Form Fields

The room form interface includes:

* **Hidden Field:** `#room-id` - Populated during edit, empty for new rooms [assets/js/admin.js L419](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L419-L419)
* **Room Number:** `#room-number` - Text input for room identifier [assets/js/admin.js L420](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L420-L420)
* **Room Type:** `#room-type` - Select dropdown (individual/doble/suite) [assets/js/admin.js L421](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L421-L421)
* **Price:** `#room-price` - Numeric input for nightly rate [assets/js/admin.js L422](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L422-L422)
* **Status:** `#room-status` - Select dropdown (disponible/ocupada/en limpieza) [assets/js/admin.js L423](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L423-L423)

The submit button text toggles between "Agregar Habitación" and "Actualizar Habitación" based on the presence of `roomId` [assets/js/admin.js L450-L483](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L450-L483)

**Sources:** [assets/js/admin.js L416-L499](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L416-L499)

---

## Visual Design System

### Color Palette and Theming

The administrator interface implements a dark theme with strategic use of the signature purple accent color:

| Element Type | Color Value | CSS Class/Selector | Usage |
| --- | --- | --- | --- |
| Background Overlay | `rgba(35, 39, 42, 0.8)` | `body::before` [assets/css/StyleAdmin.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L31) | Darkens background image |
| Container Background | `#f4f4f4` | `.container` [assets/css/StyleAdmin.css L43](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L43-L43) | Light content area |
| Header/Footer | `#333` | `header`, `footer` [assets/css/StyleAdmin.css L51-L277](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L51-L277) | Dark frames |
| Navigation Bar | `#444` | `nav` [assets/css/StyleAdmin.css L75](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L75-L75) | Slightly lighter than header |
| Purple Accent | `#9b59b6` | `nav button:hover`, `form button` [assets/css/StyleAdmin.css L96-L199](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L96-L199) | Interactive elements |
| Purple Hover | `#8e44ad` | `form button:hover` [assets/css/StyleAdmin.css L209](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L209-L209) | Darker purple for hover state |
| Edit Blue | `#36A2EB` | `.edit-employee`, `.edit-room` [assets/css/StyleAdmin.css L253](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L253-L253) | Edit action buttons |
| Delete Red | `#FF6384` | `.delete-employee`, `.delete-room` [assets/css/StyleAdmin.css L259](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L259-L259) | Delete action buttons |

The purple color `#9b59b6` is referenced in multiple contexts throughout documentation as the "Benedetti Hotel" brand color.

**Sources:** [assets/css/StyleAdmin.css L1-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L1-L380)

### Background Image Implementation

The interface uses a layered background approach:

1. **Base Image Layer:** `url('../img/fondo2.jpg')` applied to body with `cover`, `center`, `no-repeat`, `fixed` [assets/css/StyleAdmin.css L9-L18](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L9-L18)
2. **Overlay Layer:** Pseudo-element `body::before` creates darkening effect [assets/css/StyleAdmin.css L22-L31](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L22-L31)
3. **Content Layer:** All body children set to `z-index: 2` to appear above overlay [assets/css/StyleAdmin.css L34-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L34-L37)

This three-layer system creates visual depth while maintaining content readability.

**Sources:** [assets/css/StyleAdmin.css L8-L37](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L8-L37)

### Form Styling

Forms share consistent styling across employee and room sections:

* **Container:** White background (`background-color: white`), rounded corners (`border-radius: 5px`), box shadow [assets/css/StyleAdmin.css L175-L181](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L181)
* **Labels:** Block display with bottom margin [assets/css/StyleAdmin.css L183-L186](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L183-L186)
* **Inputs/Selects:** Full width, padding `0.5em`, light border `#ddd`, `font-size: 16px` [assets/css/StyleAdmin.css L188-L196](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L188-L196)
* **Submit Buttons:** Purple background `#9b59b6`, white text, no border [assets/css/StyleAdmin.css L198-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L198-L210)

The form design prioritizes clarity and accessibility with generous spacing and clear visual hierarchy.

**Sources:** [assets/css/StyleAdmin.css L175-L210](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L175-L210)

### Table Styling

Both employee and room tables use identical styling patterns:

* **Table:** Full width, collapsed borders, white background, box shadow [assets/css/StyleAdmin.css L213-L220](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L213-L220)
* **Cells:** Padding `0.8em`, left-aligned text, `#ddd` borders [assets/css/StyleAdmin.css L222-L229](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L222-L229)
* **Headers:** Light gray background `#f4f4f4` [assets/css/StyleAdmin.css L231-L234](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L231-L234)
* **Zebra Striping:** Even rows have `#f9f9f9` background [assets/css/StyleAdmin.css L236-L239](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L236-L239)
* **Action Buttons:** Inline with padding `0.5em`, right margin `0.5em` [assets/css/StyleAdmin.css L241-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L241-L249)

**Sources:** [assets/css/StyleAdmin.css L212-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L212-L266)

### Dashboard Statistics Cards

The stat cards use a consistent design pattern:

* **Layout:** Flexbox with `space-around` justification, 30% width per card [assets/css/StyleAdmin.css L126-L141](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L141)
* **Styling:** White background, rounded corners, box shadow, centered text [assets/css/StyleAdmin.css L134-L141](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L134-L141)
* **Interaction:** Transform `translateY(-5px)` on hover creates lift effect [assets/css/StyleAdmin.css L144-L146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L144-L146)
* **Typography:** Small heading (`16px`), large value (`24px`, bold) [assets/css/StyleAdmin.css L148-L156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L148-L156)

Cards are responsive, stacking vertically on mobile devices with increased width [assets/css/StyleAdmin.css L303-L363](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L363)

**Sources:** [assets/css/StyleAdmin.css L125-L363](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L125-L363)

---

## Responsive Design Strategy

The interface implements mobile-first responsive breakpoints:

### Tablet Layout (max-width: 768px)

Key adaptations at this breakpoint [assets/css/StyleAdmin.css L280-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L334)

:

* Navigation switches to vertical column layout [assets/css/StyleAdmin.css L289-L292](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L289-L292)
* Stat cards stack vertically at 80% width [assets/css/StyleAdmin.css L303-L310](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L303-L310)
* Chart canvas max-width reduced to 300px [assets/css/StyleAdmin.css L312-L314](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L312-L314)
* Table font size reduced to 14px [assets/css/StyleAdmin.css L316-L326](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L316-L326)
* Form elements font size reduced to 14px [assets/css/StyleAdmin.css L328-L333](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L328-L333)

### Mobile Layout (max-width: 480px)

Additional adaptations at this breakpoint [assets/css/StyleAdmin.css L336-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L336-L380)

:

* Logo max-width reduced to 120px [assets/css/StyleAdmin.css L337-L339](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L337-L339)
* Stat cards expand to 100% width [assets/css/StyleAdmin.css L353-L355](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L353-L355)
* Chart canvas expands to 100% width [assets/css/StyleAdmin.css L365-L367](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L365-L367)
* Table font size further reduced to 12px [assets/css/StyleAdmin.css L369-L379](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L369-L379)

The responsive strategy maintains functionality across all device sizes while optimizing space usage and touch target sizes for mobile devices.

**Sources:** [assets/css/StyleAdmin.css L279-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L279-L380)

---

## API Communication Patterns

### Request Structure

All administrator operations use the action-based routing pattern where the `action` parameter determines backend behavior:

**GET Requests:**

```
../controllers/AdminController.php?action={actionName}[&additionalParams]
```

Supported GET actions:

* `getDashboardStats` - Returns statistics object
* `getEmployees` - Returns employees array
* `getEmployeeById&employeeId={id}` - Returns single employee
* `getRooms` - Returns rooms array
* `getRoomById&roomId={id}` - Returns single room

**POST Requests:**

```

```

Supported POST actions:

* `addEmployee` - Creates employee (requires: name, lastname, email, telefono, dni, password, role)
* `updateEmployee` - Updates employee (requires: employeeId + same fields as add)
* `deleteEmployee` - Deletes employee (requires: employeeId)
* `addRoom` - Creates room (requires: roomNumber, roomType, roomPrice)
* `updateRoom` - Updates room (requires: roomId, roomNumber, roomType, roomPrice, roomStatus)
* `deleteRoom` - Deletes room (requires: roomId)

**Sources:** [assets/js/admin.js L39-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L39-L552)

 [controllers/AdminController.php L17-L328](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L328)

### Response Format

All API endpoints return JSON responses with a consistent structure:

**Success Response:**

```

```

**Error Response:**

```

```

Specific response formats:

* Dashboard stats: `{success: true, stats: {total_habitaciones, habitaciones_ocupadas, ...}}` [controllers/AdminController.php L292-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L292-L302)
* Employee list: `{success: true, employees: [{id, nombre, apellido, ...}, ...]}` [controllers/AdminController.php L304](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L304-L304)
* Single employee: `{success: true, user: {id, nombre, apellido, ...}}` [controllers/AdminController.php L308](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L308-L308)
* Room list: `{success: true, rooms: [{id, numero_habitacion, tipo, ...}, ...]}` [controllers/AdminController.php L311](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L311-L311)
* Single room: `{success: true, room: {id, numero_habitacion, ...}}` [controllers/AdminController.php L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L315-L315)

**Sources:** [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

### Error Handling and User Feedback

The interface uses SweetAlert2 library for all user notifications:

**Success Notifications:**

```

```

Auto-dismiss after 1.5 seconds, used for create/update/delete operations [assets/js/admin.js L239-L532](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L239-L532)

**Error Notifications:**

```

```

Requires user acknowledgment, displays error details [assets/js/admin.js L126-L256](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L126-L256)

**Confirmation Dialogs:**

```

```

Used before destructive delete operations [assets/js/admin.js L305-L512](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L305-L512)

**Sources:** [assets/js/admin.js L126-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L126-L552)

---

## Integration with Data Models

The administrator interface interacts with three primary model classes:

### Usuario2 Model Integration

Used for employee management operations:

**Methods Called by AdminController:**

* `create()` - Creates new user record [controllers/AdminController.php L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L72-L72)
* `update()` - Updates existing user [controllers/AdminController.php L163](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L163-L163)
* `delete($id)` - Deletes user and cascades to empleado [controllers/AdminController.php L189](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L189-L189)
* `getEmployees()` - Returns employees with role filtering [controllers/AdminController.php L304](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L304-L304)
* `getById($id)` - Retrieves single user by ID [controllers/AdminController.php L308](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L308-L308)

**Properties Set:**

* `nombre`, `apellido`, `email`, `telefono`, `dni`, `password` (hashed), `rol`

**Sources:** [controllers/AdminController.php L4-L309](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L4-L309)

### Empleado Model Integration

Manages employee-specific data (cargo):

**Methods Called by AdminController:**

* `create()` - Creates empleado record after usuario creation [controllers/AdminController.php L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L79-L79)
* `updateCargo($usuario_id)` - Updates employee cargo field [controllers/AdminController.php L168](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L168-L168)

**Properties Set:**

* `usuario_id` - Foreign key to usuarios table
* `cargo` - Employee position (recepcionista or mucama)

The empleado record is created transactionally with usuario. If empleado creation fails, the usuario record is rolled back [controllers/AdminController.php L88-L92](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L88-L92)

**Sources:** [controllers/AdminController.php L5-L168](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L5-L168)

### Habitacion2 Model Integration

Manages room inventory:

**Methods Called by AdminController:**

* `create()` - Creates new room [controllers/AdminController.php L215](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L215-L215)
* `update()` - Updates room details [controllers/AdminController.php L244](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L244-L244)
* `delete($id)` - Deletes room [controllers/AdminController.php L248](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L248-L248)
* `getAll()` - Returns all rooms [controllers/AdminController.php L311](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L311-L311)
* `getById($id)` - Retrieves single room [controllers/AdminController.php L315](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L315-L315)

**Properties Set:**

* `numero_habitacion`, `tipo`, `precio`, `estado`

All room modifications go through these model methods, ensuring consistent business logic application across the system.

**Sources:** [controllers/AdminController.php L6-L316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L6-L316)