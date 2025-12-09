# Frontend Architecture

> **Relevant source files**
> * [app/static/css/carrito.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css)
> * [app/static/css/empleado_dashboard.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css)
> * [app/static/css/gestion_citas_pendientes.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css)
> * [app/static/css/gestion_promociones.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css)
> * [app/static/css/trabajar_citas.css](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css)
> * [app/static/js/carrito.js](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js)
> * [app/templates/base.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html)
> * [app/templates/carrito.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html)
> * [app/templates/productos.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html)
> * [app/templates/servicios.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html)

## Purpose and Scope

This document describes the frontend architecture of Casa Bella, including the Jinja2 templating system, static asset organization, theme management, and client-side interactivity patterns. It covers how HTML templates inherit from `base.html`, how CSS and JavaScript files are structured, the dark/light theme system implementation, and AJAX integration patterns for dynamic features.

For information about backend route handling and blueprint organization, see [Blueprint Organization](/GroveLive/CasaBella/3.1-blueprint-organization). For data models and database schema, see [Data Models](/GroveLive/CasaBella/3.2-data-models).

---

## Template Inheritance System

Casa Bella uses Flask's Jinja2 templating engine with a hierarchical template inheritance pattern. All page templates extend from a single base template that provides consistent navigation, layout, and theme support.

### Template Hierarchy

```

```

**Sources:** [app/templates/base.html L1-L270](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L270)

 [app/templates/productos.html L1-L2](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L1-L2)

 [app/templates/servicios.html L1-L2](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L1-L2)

 [app/templates/carrito.html L1-L2](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L1-L2)

### Template Block Structure

The base template defines several blocks that child templates can override:

| Block Name | Purpose | Default Content | Typical Usage |
| --- | --- | --- | --- |
| `{% block title %}` | Page title in `<title>` tag | "Casa Bella - Salón de Belleza y Distribuidora" | Override with page-specific title |
| `{% block styles %}` | Additional CSS includes | None | Link page-specific stylesheets |
| `{% block content %}` | Main page content | None | **Required** - all page content goes here |
| `{% block scripts %}` | Additional JavaScript | None | Include page-specific scripts and inline JS |

**Sources:** [app/templates/base.html L6](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L6-L6)

 [app/templates/base.html L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L22-L22)

 [app/templates/base.html L184](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L184-L184)

 [app/templates/base.html L268](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L268-L268)

### Example: Product Page Template Structure

```xml
{% extends "base.html" %}
{% block title %}Productos - Casa Bella{% endblock %}
{% block styles %}
    <link rel="stylesheet" href="{{ url_for('static', filename='css/productos.css') }}">
{% endblock %}
{% block content %}
    <!-- Page-specific content -->
{% endblock %}
{% block scripts %}
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script>
        // Page-specific JavaScript
    </script>
{% endblock %}
```

**Sources:** [app/templates/productos.html L1-L7](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L1-L7)

 [app/templates/productos.html L73-L128](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L73-L128)

---

## Base Template Architecture

The `base.html` template at [app/templates/base.html L1-L270](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L1-L270)

 provides the foundational structure for all pages, including navigation, user menu, footer, and theme system.

### Base Template Components

```

```

**Sources:** [app/templates/base.html L3-L23](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L3-L23)

 [app/templates/base.html L24-L181](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L24-L181)

 [app/templates/base.html L187-L261](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L187-L261)

 [app/templates/base.html L263-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L263-L269)

### Role-Based Navigation

The navigation menu dynamically renders based on the authenticated user's role. The template uses Jinja2 conditionals to check `current_user.rol`:

| User State | Navigation Items | File Reference |
| --- | --- | --- |
| **Guest** (not authenticated) | Inicio, Servicios, Productos, Login dropdown | [app/templates/base.html L91-L101](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L91-L101) |
| **Cliente** | Inicio, Servicios, Productos, Reservar Cita, Dashboard Cliente | [app/templates/base.html L40-L55](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L40-L55) |
| **Admin** | Inicio, Panel Admin, Gestión Usuarios, Gestión Productos, Gestión Servicios, Gestión Promociones, Citas Pendientes | [app/templates/base.html L57-L78](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L57-L78) |
| **Empleado** | Inicio, Panel Empleado, Trabajar Citas | [app/templates/base.html L80-L89](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L80-L89) |

**Implementation pattern:**

```
{% if current_user.is_authenticated %}
    {% if current_user.rol == 'cliente' %}
        <!-- Cliente menu items -->
    {% elif current_user.rol == 'admin' %}
        <!-- Admin menu items -->
    {% elif current_user.rol == 'empleado' %}
        <!-- Empleado menu items -->
    {% endif %}
{% else %}
    <!-- Guest menu items -->
{% endif %}
```

**Sources:** [app/templates/base.html L39-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L39-L102)

### User Context Indicators

The base template displays contextual information in the navigation bar:

**Favorites Count Badge** (Cliente only):

```

```

**Sources:** [app/templates/base.html L112-L120](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L112-L120)

**Cart Item Count Badge** (Cliente only):

```

```

**Sources:** [app/templates/base.html L121-L129](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L121-L129)

**User Dropdown Menu:**

```

```

**Sources:** [app/templates/base.html L154-L176](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L154-L176)

---

## Static Asset Management

Static assets are organized by type and loaded through Flask's `url_for('static', filename='...')` helper function.

### Static Asset Organization

```markdown
app/static/
├── css/
│   ├── base.css                       # Core styles, CSS variables
│   ├── productos.css                  # Product catalog styles
│   ├── servicios.css                  # Service catalog styles
│   ├── carrito.css                    # Shopping cart styles
│   ├── gestion_promociones.css        # Promotion management styles
│   ├── gestion_citas_pendientes.css   # Admin appointment calendar styles
│   ├── trabajar_citas.css             # Employee appointment calendar styles
│   └── empleado_dashboard.css         # Employee dashboard styles
├── js/
│   ├── theme-toggle.js                # Theme switching logic
│   └── carrito.js                     # Cart quantity/deletion logic
└── images/
    ├── casa-bella-logo.jpeg           # Brand logo
    ├── favicon.ico                    # Browser favicon
    ├── default.jpg                    # Fallback image
    ├── producto_*.jpg                 # Product images
    └── servicio_*.jpg                 # Service images
```

**Sources:** [app/templates/base.html L9-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L9-L21)

 [app/templates/base.html L266-L267](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L267)

 [app/static/css/](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/)

 [app/static/js/carrito.js L1](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L1-L1)

### CSS Loading Strategy

The base template loads stylesheets in a specific order to ensure proper cascade precedence:

1. **Bootstrap CSS** (CDN) - [app/templates/base.html L14](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L14-L14)
2. **Bootstrap Icons** (CDN) - [app/templates/base.html L16](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L16-L16)
3. **Google Fonts** (CDN) - [app/templates/base.html L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L18-L18)
4. **Base CSS** (local) - [app/templates/base.html L20](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L20-L20)
5. **Page-specific CSS** (local) - [app/templates/base.html L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L22-L22)

Child templates add page-specific stylesheets by overriding the `{% block styles %}` block:

```

```

**Sources:** [app/templates/productos.html L3-L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L3-L5)

 [app/templates/servicios.html L3-L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L3-L5)

 [app/templates/carrito.html L3-L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L3-L5)

### JavaScript Loading Strategy

JavaScript files are loaded at the end of the `<body>` to avoid blocking page render:

1. **Bootstrap JS Bundle** (CDN) - Loaded in base template [app/templates/base.html L264](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L264-L264)
2. **Theme Toggle JS** - Loaded in base template [app/templates/base.html L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L266)
3. **jQuery** (CDN) - Loaded in page templates as needed [app/templates/productos.html L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L74-L74)
4. **Page-specific JS** - Loaded via `{% block scripts %}` [app/templates/productos.html L73-L128](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L73-L128)

**Sources:** [app/templates/base.html L263-L269](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L263-L269)

 [app/templates/productos.html L73-L128](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L73-L128)

 [app/templates/servicios.html L73-L128](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L73-L128)

---

## Theme System

Casa Bella implements a dual-theme system (dark/light) using CSS custom properties (variables) and data attributes. The theme persists across sessions using `localStorage`.

### Theme Architecture

```

```

**Sources:** [app/templates/base.html L24](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L24-L24)

 [app/templates/base.html L106-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L106-L108)

 [app/templates/base.html L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L266)

 [app/static/css/gestion_promociones.css L332-L342](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L332-L342)

### Theme Implementation Details

**Initial Theme Setup:**

The `<body>` tag is initialized with `data-theme="dark"`:

```

```

**Sources:** [app/templates/base.html L24](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L24-L24)

**Theme Toggle Button:**

```

```

**Sources:** [app/templates/base.html L106-L108](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L106-L108)

**Theme Switching Logic (theme-toggle.js):**

While the actual JavaScript file is not provided, the typical implementation pattern loads the saved theme from `localStorage` on page load and toggles it on button click, updating both the `data-theme` attribute and the button icon.

**Sources:** [app/templates/base.html L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L266-L266)

### CSS Variable System

CSS files define theme-specific variables using attribute selectors:

**Dark Theme Variables:**

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L250-L254](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L250-L254)

 [app/static/css/trabajar_citas.css L274-L278](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L274-L278)

 [app/static/css/empleado_dashboard.css L163-L169](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L163-L169)

**Light Theme Variables:**

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L256-L260](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L256-L260)

 [app/static/css/trabajar_citas.css L280-L284](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L280-L284)

 [app/static/css/empleado_dashboard.css L171-L177](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L171-L177)

**Common CSS Variables Used Throughout:**

| Variable | Purpose | Dark Value Example | Light Value Example |
| --- | --- | --- | --- |
| `--text-primary` | Primary text color | `#f0f0f0` | `#333333` |
| `--text-secondary` | Secondary text color | `#b0b0b0` | `#666666` |
| `--card-bg-dark` | Card background with transparency | `rgba(26, 33, 48, 0.9)` | `rgba(255, 255, 255, 0.9)` |
| `--border-color` | Border color | Set per theme | Set per theme |
| `--gradient-primary` | Primary gradient for buttons/headers | Typically blue gradient | Typically blue gradient |
| `--secondary-color` | Accent color | Set per theme | Set per theme |
| `--success-color` | Success state color | Set per theme | Set per theme |
| `--danger-color` | Danger/error state color | Set per theme | Set per theme |
| `--shadow-elegant` | Box shadow definition | Set per theme | Set per theme |

**Sources:** [app/static/css/gestion_citas_pendientes.css L250-L260](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L250-L260)

 [app/static/css/empleado_dashboard.css L163-L177](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L163-L177)

 [app/static/css/gestion_promociones.css L332-L342](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L332-L342)

---

## AJAX Integration Patterns

Casa Bella uses jQuery AJAX for dynamic client-side features without full page reloads. The primary use cases are favorites management, cart operations, and promotion management.

### Favorites System AJAX Flow

```

```

**Sources:** [app/templates/productos.html L93-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L93-L119)

 [app/templates/servicios.html L93-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L93-L119)

### Favorites AJAX Implementation

**Check Favorite Status on Page Load:**

```

```

**Sources:** [app/templates/productos.html L82-L91](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L82-L91)

 [app/templates/servicios.html L82-L91](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L82-L91)

**Toggle Favorite on Click:**

```

```

**Sources:** [app/templates/productos.html L93-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L93-L119)

 [app/templates/servicios.html L93-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L93-L119)

### Cart Operations AJAX Flow

```

```

**Sources:** [app/templates/carrito.html L77-L109](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L77-L109)

 [app/templates/carrito.html L110-L130](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L110-L130)

 [app/static/js/carrito.js L2-L29](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L2-L29)

### Cart AJAX Implementation

**Update Quantity:**

```

```

**Sources:** [app/templates/carrito.html L77-L99](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L77-L99)

**Delete Item:**

```

```

**Sources:** [app/templates/carrito.html L110-L129](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L110-L129)

 [app/static/js/carrito.js L10-L28](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L10-L28)

**Recalculate Total:**

```

```

**Sources:** [app/templates/carrito.html L101-L107](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L101-L107)

 [app/static/js/carrito.js L2-L8](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/js/carrito.js#L2-L8)

### AJAX Endpoint Summary

| Endpoint | Method | Purpose | Request Format | Response Format |
| --- | --- | --- | --- | --- |
| `/client/check_favorito/<item_type>/<item_id>` | GET | Check if item is favorited | URL params | `{success: bool, is_favorite: bool}` |
| `/client/agregar_favorito/<item_type>/<item_id>` | POST | Toggle favorite status | URL params | `{success: bool, added: bool, message: str}` |
| `/client/actualizar_cantidad/<detalle_id>` | POST | Update cart item quantity | `{increment: bool}` | `{success: bool, cantidad: int, precio_unitario: float}` |
| `/client/eliminar_del_carrito/<detalle_id>` | POST | Remove item from cart | No body | `{success: bool, message: str}` |

**Sources:** [app/templates/productos.html L85-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L85-L119)

 [app/templates/carrito.html L77-L130](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L77-L130)

---

## Responsive Design Strategy

All CSS files implement mobile-responsive design using CSS media queries with a mobile-first approach. The breakpoints follow Bootstrap's standard convention.

### Responsive Breakpoints

| Breakpoint | Max Width | Target Devices | Common Adjustments |
| --- | --- | --- | --- |
| Desktop | Default | Desktops, laptops | Full-size layouts |
| Tablet | `max-width: 992px` | Tablets | Stack columns, reduce padding |
| Mobile (Large) | `max-width: 768px` | Large phones | Single column layouts, smaller fonts |
| Mobile (Small) | `max-width: 576px` | Small phones | Compact UI, reduced spacing |

**Sources:** [app/static/css/carrito.css L332-L403](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L332-L403)

 [app/static/css/gestion_citas_pendientes.css L268-L343](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L268-L343)

 [app/static/css/gestion_promociones.css L372-L451](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L372-L451)

### Responsive Patterns

**Grid to Stack Conversion:**

Desktop cart items use CSS Grid with 4 columns:

```

```

Mobile cart items stack vertically:

```

```

**Sources:** [app/static/css/carrito.css L125-L131](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L125-L131)

 [app/static/css/carrito.css L332-L348](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L332-L348)

**Table to Card Conversion:**

Admin pages use responsive tables that switch to card-based layouts on mobile:

```

```

**Sources:** [app/static/css/gestion_promociones.css L139-L147](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L139-L147)

 [app/static/css/gestion_promociones.css L190-L234](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L190-L234)

 [app/static/css/gestion_promociones.css L386-L388](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L386-L388)

**Font Size Scaling:**

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L10-L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L10-L18)

 [app/static/css/gestion_citas_pendientes.css L274-L276](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L274-L276)

 [app/static/css/gestion_citas_pendientes.css L314-L316](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L314-L316)

**Button and Spacing Adjustments:**

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L77-L86](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L77-L86)

 [app/static/css/trabajar_citas.css L77-L86](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L77-L86)

 [app/static/css/gestion_promociones.css L89-L137](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L89-L137)

---

## External Dependencies

Casa Bella integrates several external libraries and services loaded from CDNs for frontend functionality.

### CDN Dependencies

```

```

**Sources:** [app/templates/base.html L14-L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L14-L18)

 [app/templates/base.html L264](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L264-L264)

 [app/templates/productos.html L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L74-L74)

### CDN Resource Table

| Resource | Version | CDN URL | Usage |
| --- | --- | --- | --- |
| Bootstrap CSS | 5.3.0 | `https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css` | Core UI framework |
| Bootstrap Icons | 1.10.0 | `https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css` | Icon library |
| Bootstrap JS | 5.3.0 | `https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js` | Interactive components |
| Google Fonts | N/A | `https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;500;600;700&family=Inter:wght@300;400;500;600;700&display=swap` | Typography |
| jQuery | 3.6.0 | `https://code.jquery.com/jquery-3.6.0.min.js` | AJAX operations |

**Sources:** [app/templates/base.html L14](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L14-L14)

 [app/templates/base.html L16](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L16-L16)

 [app/templates/base.html L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L18-L18)

 [app/templates/base.html L264](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L264-L264)

 [app/templates/productos.html L74](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L74-L74)

### Font System

Casa Bella uses two Google Fonts for typography hierarchy:

**Playfair Display** (Serif):

* Used for headings (h1, h2, h5, .card-title, .modal-title)
* Weights: 400, 500, 600, 700
* Creates elegant, formal aesthetic

**Inter** (Sans-serif):

* Used for body text, buttons, labels, descriptions
* Weights: 300, 400, 500, 600, 700
* Provides clean, readable interface text

**Sources:** [app/templates/base.html L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L18-L18)

 [app/static/css/gestion_citas_pendientes.css L10-L18](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L10-L18)

 [app/static/css/carrito.css L10-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L10-L21)

### Bootstrap Icon Usage

Bootstrap Icons are used throughout the interface for visual indicators:

| Icon Class | Usage Context | Location |
| --- | --- | --- |
| `bi-moon-stars` | Theme toggle button | Navigation bar |
| `bi-heart` / `bi-heart-fill` | Favorite buttons (empty/filled) | Product/service cards |
| `bi-cart` / `bi-cart-plus` | Shopping cart button/add button | Navigation, product cards |
| `bi-calendar-plus` | Book appointment button | Service cards |
| `bi-star` | Reviews link | Product/service cards |
| `bi-bell` | Notifications button | Navigation bar |
| `bi-person-circle` | User dropdown | Navigation bar |
| `bi-box-seam` | Stock indicator | Product cards |
| `bi-clock` | Duration indicator | Service cards |

**Sources:** [app/templates/base.html L107](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L107-L107)

 [app/templates/base.html L113](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L113-L113)

 [app/templates/base.html L122](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L122-L122)

 [app/templates/base.html L135](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/base.html#L135-L135)

 [app/templates/productos.html L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L46-L46)

 [app/templates/productos.html L50](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L50-L50)

 [app/templates/productos.html L54](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L54-L54)

 [app/templates/productos.html L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L57-L57)

 [app/templates/servicios.html L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L46-L46)

 [app/templates/servicios.html L50](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L50-L50)

 [app/templates/servicios.html L54](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L54-L54)

 [app/templates/servicios.html L57](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L57-L57)

---

## CSS Animation System

Casa Bella implements various CSS animations to enhance user experience and visual feedback.

### Animation Types

**Page Load Animations:**

```

```

**Sources:** [app/static/css/empleado_dashboard.css L180-L183](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L180-L183)

 [app/static/css/gestion_promociones.css L345-L348](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L345-L348)

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L263-L266](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L263-L266)

**Row Stagger Animations:**

```

```

**Sources:** [app/static/css/empleado_dashboard.css L185-L198](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L185-L198)

 [app/static/css/gestion_promociones.css L350-L370](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L350-L370)

**Hover Animations:**

```

```

**Sources:** [app/static/css/carrito.css L91-L105](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L91-L105)

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L77-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L77-L102)

 [app/static/css/trabajar_citas.css L77-L116](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L77-L116)

**Ripple Effect:**

```

```

**Sources:** [app/static/css/carrito.css L262-L278](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L262-L278)

### Transition Properties

Common transition patterns used throughout the CSS:

| Property | Typical Duration | Easing Function | Usage |
| --- | --- | --- | --- |
| `all` | 0.3s - 0.4s | `ease` | General hover effects |
| `transform` | 0.3s | `ease` | Scale, translate effects |
| `box-shadow` | 0.3s | `ease` | Shadow depth changes |
| `color` | 0.3s | `ease` | Text color changes |
| `opacity` | 0.5s | `ease` | Fade in/out |
| `background` | 0.3s | `ease` | Background color changes |

**Sources:** [app/static/css/carrito.css L91-L105](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/carrito.css#L91-L105)

 [app/static/css/gestion_citas_pendientes.css L83-L102](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L83-L102)

 [app/static/css/empleado_dashboard.css L83-L91](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/empleado_dashboard.css#L83-L91)

---

## Form Handling Patterns

Forms in Casa Bella follow consistent patterns for submission, validation feedback, and user interaction.

### Flash Message Display

Flash messages are displayed as dismissible alerts at the top of page content:

```

```

**Alert Categories:**

* `success` - Green background, success icon
* `danger` - Red background, error icon
* `warning` - Yellow background, warning icon
* `info` - Blue background, info icon

**Sources:** [app/templates/productos.html L16-L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L16-L22)

 [app/templates/servicios.html L16-L22](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/servicios.html#L16-L22)

 [app/templates/carrito.html L10-L19](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L10-L19)

### Form Validation Styling

Bootstrap form validation classes are used:

```

```

**Sources:** [app/static/css/gestion_citas_pendientes.css L228-L231](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_citas_pendientes.css#L228-L231)

 [app/static/css/trabajar_citas.css L252-L255](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/trabajar_citas.css#L252-L255)

 [app/static/css/gestion_promociones.css L310-L313](https://github.com/GroveLive/CasaBella/blob/5f618972/app/static/css/gestion_promociones.css#L310-L313)

---

## Progressive Enhancement Strategy

Casa Bella implements progressive enhancement by:

1. **Server-Side Rendering First**: All pages render complete HTML from Flask templates
2. **CSS Enhancement**: Visual improvements via CSS without breaking core functionality
3. **JavaScript Enhancement**: Dynamic features (AJAX, animations) enhance but don't replace core functionality
4. **Graceful Degradation**: Forms work with full page refreshes if JavaScript fails

**Example: Favorite Button**

* **Base functionality**: Link to favorites page (works without JS)
* **Enhanced functionality**: AJAX toggle without page reload (requires JS)

**Example: Shopping Cart**

* **Base functionality**: Form submission to update quantities (works without JS)
* **Enhanced functionality**: Inline quantity updates with AJAX (requires JS)

**Sources:** [app/templates/productos.html L50-L59](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L50-L59)

 [app/templates/productos.html L93-L119](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/productos.html#L93-L119)

 [app/templates/carrito.html L77-L130](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/carrito.html#L77-L130)