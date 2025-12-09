# Dashboard and Analytics

> **Relevant source files**
> * [assets/css/StyleAdmin.css](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css)
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)

## Purpose and Scope

The Dashboard and Analytics subsystem provides administrators with real-time visibility into hotel operations through a centralized interface displaying key performance metrics and visual data representations. This page documents the dashboard's data aggregation mechanisms, Chart.js-based visualizations, and the communication patterns between the frontend statistics display and backend data queries.

For information about the broader administrative interface including employee and room CRUD operations, see [Administrator Interface](/GroveLive/hotelBenedetti/3.2-administrator-interface). For details about the underlying data models queried by the dashboard, see [Data Models and Database Schema](/GroveLive/hotelBenedetti/2.3-data-models-and-database-schema).

---

## Dashboard Architecture Overview

The dashboard operates as a distinct section within the admin interface, automatically loading when administrators navigate to the system. The architecture follows a standard client-server pattern where frontend JavaScript initiates statistics requests, the backend aggregates data from multiple database tables, and the frontend renders both numeric statistics and interactive charts.

**Dashboard Component Flow**

```

```

**Sources:** [assets/js/admin.js L8-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L35)

 [assets/js/admin.js L38-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L38-L140)

 [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

---

## Statistics Tracking

The dashboard tracks six primary metrics organized into two categories: room status distribution and employee role distribution.

### Tracked Metrics

| Metric ID | Display Label | Database Query | Purpose |
| --- | --- | --- | --- |
| `total_habitaciones` | Total de Habitaciones | `SELECT COUNT(*) FROM habitaciones` | Overall room inventory count |
| `habitaciones_ocupadas` | Habitaciones Ocupadas | `SELECT COUNT(*) WHERE estado='ocupada'` | Currently occupied rooms |
| `habitaciones_disponibles` | Habitaciones Disponibles | `SELECT COUNT(*) WHERE estado='disponible'` | Rooms ready for check-in |
| `habitaciones_en_limpieza` | Habitaciones en Limpieza | `SELECT COUNT(*) WHERE estado='en limpieza'` | Rooms being cleaned by maids |
| `total_recepcionistas` | Recepcionistas | `SELECT COUNT(*) FROM empleados WHERE cargo='recepcionista'` | Front desk staff count |
| `total_mucamas` | Mucamas | `SELECT COUNT(*) FROM empleados WHERE cargo='mucama'` | Housekeeping staff count |

### Backend Data Aggregation

The `getDashboardStats` action in `AdminController.php` executes six independent SQL queries to aggregate statistics. Each query uses PDO prepared statements with `COUNT(*)` aggregations and conditional `WHERE` clauses for filtering.

```

```

**Sources:** [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

---

## Data Visualization with Chart.js

The dashboard integrates the Chart.js library to render two interactive visualizations: a pie chart displaying room status distribution and a bar chart showing employee role distribution.

### Chart Instance Management

The JavaScript module maintains global references to chart instances (`roomStatusChartInstance` and `employeeChartInstance`) to enable proper cleanup before re-rendering. When statistics are reloaded, existing chart instances are destroyed using `chart.destroy()` to prevent memory leaks and canvas rendering conflicts.

```

```

**Sources:** [assets/js/admin.js L5-L6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L5-L6)

 [assets/js/admin.js L56-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L56-L61)

### Room Status Pie Chart

The room status visualization uses a pie chart to display the proportional distribution of rooms across three states: occupied, available, and in cleaning.

**Chart Configuration:**

* **Type:** `'pie'`
* **Canvas Element:** `#roomStatusChart`
* **Labels:** `['Ocupadas', 'Disponibles', 'En Limpieza']`
* **Data Source:** `[stats.habitaciones_ocupadas, stats.habitaciones_disponibles, stats.habitaciones_en_limpieza]`
* **Colors:** `['#FF6384', '#36A2EB', '#FFCE56']` (red, blue, yellow)
* **Title:** `'Estado de Habitaciones'`

**Sources:** [assets/js/admin.js L64-L90](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L64-L90)

### Employee Distribution Bar Chart

The employee chart uses a vertical bar chart to compare the number of receptionists versus maids currently employed.

**Chart Configuration:**

* **Type:** `'bar'`
* **Canvas Element:** `#employeeChart`
* **Labels:** `['Recepcionistas', 'Mucamas']`
* **Data Source:** `[stats.total_recepcionistas, stats.total_mucamas]`
* **Colors:** `['#36A2EB', '#FFCE56']` (blue, yellow)
* **Title:** `'Distribución de Empleados'`
* **Y-axis:** Begins at zero with step size of 1 to display whole employee counts

**Sources:** [assets/js/admin.js L93-L124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L93-L124)

---

## Frontend Implementation

### JavaScript Module Structure

The dashboard functionality is encapsulated within `loadDashboardStats()`, which is automatically invoked when the dashboard navigation button is clicked. The function handles the complete request-response-render cycle.

**Key Functions and Their Responsibilities:**

| Function | Lines | Purpose |
| --- | --- | --- |
| `loadDashboardStats()` | [38-140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/38-140) | Orchestrates fetch request, error handling, DOM updates, and chart creation |
| Chart instance variables | [5-6](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/5-6) | Global references for cleanup: `roomStatusChartInstance`, `employeeChartInstance` |
| DOM updates | [46-53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/46-53) | Sets `textContent` for six stat card elements |
| Chart.js instantiation | [64-124](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/64-124) | Creates pie and bar chart instances with configuration objects |

### DOM Element Mapping

The frontend references specific HTML element IDs to display statistics:

```

```

**Sources:** [assets/js/admin.js L46-L53](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L46-L53)

 [assets/css/StyleAdmin.css L126-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L172)

### Error Handling

All error scenarios trigger SweetAlert2 modal dialogs displaying user-friendly error messages. Two error paths exist:

1. **Server-side errors:** `data.success === false` triggers an error alert with `data.message`
2. **Network/parsing errors:** `.catch()` block handles exceptions with `error.message`

**Sources:** [assets/js/admin.js L126-L139](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L126-L139)

---

## Backend API Endpoint

The dashboard queries a single GET endpoint: `AdminController.php?action=getDashboardStats`. The controller executes six database queries sequentially and returns a JSON response.

### Request Format

```
GET ../controllers/AdminController.php?action=getDashboardStats
```

No additional parameters are required.

### Response Format

```

```

### Database Query Implementation

Each statistic is retrieved through an independent prepared statement. The pattern follows:

1. Define SQL query with `COUNT(*)` aggregation and optional `WHERE` clause
2. Prepare statement: `$stmt = $conn->prepare($query)`
3. Execute: `$stmt->execute()`
4. Fetch result: `$stmt->fetch(PDO::FETCH_ASSOC)`
5. Extract count with null coalescing: `['total'] ?? 0`

**Example Query for Occupied Rooms:**

```

```

**Sources:** [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

---

## User Interface Design

### Stat Card Layout

Stat cards display individual metrics as white cards with shadow effects. The layout uses flexbox with wrapping to arrange cards horizontally, adjusting to three cards per row on desktop.

**CSS Classes and Properties:**

| Class | Lines | Key Properties |
| --- | --- | --- |
| `.dashboard-stats` | [126-131](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/126-131) | `display: flex`, `flex-wrap: wrap`, `justify-content: space-around` |
| `.stat-card` | [133-146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/133-146) | `width: 30%`, `padding: 1em`, `background-color: white`, `border-radius: 5px` |
| `.stat-card:hover` | [144-146](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/144-146) | `transform: translateY(-5px)` (lift effect) |
| `.stat-card h3` | [148-151](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/148-151) | `font-size: 16px` (metric label) |
| `.stat-card p` | [153-156](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/153-156) | `font-size: 24px`, `font-weight: bold` (numeric value) |

### Chart Container Styling

Charts are rendered on `<canvas>` elements styled with white backgrounds and shadow effects to match the stat card aesthetic.

**Canvas Styling:**

* `max-width: 400px`
* `background-color: white`
* `padding: 10px`
* `border-radius: 5px`
* `box-shadow: 0 0 10px rgba(0, 0, 0, 0.1)`

The `.dashboard-charts` container uses flexbox with `space-around` justification to distribute charts evenly.

**Sources:** [assets/css/StyleAdmin.css L126-L172](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L126-L172)

### Responsive Breakpoints

The dashboard layout adapts to smaller screens through two media query breakpoints:

**768px Breakpoint:**

* Stat cards switch to vertical stacking: `flex-direction: column`
* Card width increases to `80%` for better mobile readability
* Canvas max-width reduces to `300px`

**480px Breakpoint:**

* Stat cards expand to full width: `width: 100%`
* Font sizes reduce: `h3` to `14px`, `p` to `20px`
* Canvas scales to full container width: `max-width: 100%`

**Sources:** [assets/css/StyleAdmin.css L280-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L280-L334)

 [assets/css/StyleAdmin.css L336-L380](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/css/StyleAdmin.css#L336-L380)

---

## Navigation Integration

The dashboard section is activated through the navigation button system implemented in the admin interface. The navigation pattern uses button click handlers that hide all sections and reveal the selected section by toggling CSS classes.

**Navigation Flow:**

```

```

The dashboard button is programmatically clicked on page load to ensure the dashboard displays by default when administrators access the admin interface.

**Sources:** [assets/js/admin.js L8-L30](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L30)

---

## Data Refresh Behavior

The dashboard statistics are refreshed in two scenarios:

1. **Initial page load:** The dashboard button is auto-clicked, triggering `loadDashboardStats()`
2. **Manual navigation:** When administrators navigate away from the dashboard and return by clicking the dashboard navigation button

The system does not implement automatic polling or real-time updates. Administrators must manually navigate to other sections and return to the dashboard to refresh statistics. This design choice reduces server load and database query frequency.

**Sources:** [assets/js/admin.js L19-L26](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L19-L26)

 [assets/js/admin.js L30](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L30-L30)