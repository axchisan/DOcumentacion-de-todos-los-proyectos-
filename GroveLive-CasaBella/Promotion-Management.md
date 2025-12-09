# Promotion Management

> **Relevant source files**
> * [app/routes/admin.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py)
> * [app/routes/client.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py)
> * [app/templates/dashboard_admin.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/dashboard_admin.html)

## Purpose and Scope

This document describes the promotion management system in Casa Bella, which allows administrators to create time-bound discount campaigns for products and services. The system handles creation, modification, and deletion of promotions through AJAX-based interfaces, automatic application of discounts during checkout, and filtering of items by active promotions.

For information about browsing products/services with promotions as a client, see [5.1](/GroveLive/CasaBella/5.1-product-and-service-catalog). For checkout processing with promotional pricing, see [5.2](/GroveLive/CasaBella/5.2-shopping-cart-and-checkout).

---

## System Overview

The promotion system implements time-based discount campaigns that apply percentage discounts to either products or services. Promotions are managed exclusively by administrators and automatically apply to qualifying items based on current date/time validation. The system uses a polymorphic design where a single `Promocion` entity can reference either a `Producto` or `Servicio` through nullable foreign keys.

**Key characteristics:**

* Date-range activation (fecha_inicio to fecha_fin)
* Percentage-based discounts (0-100%)
* Polymorphic item association (producto OR servicio)
* AJAX-driven CRUD operations
* Automatic price calculation at cart addition
* Historical preservation in completed transactions

**Sources:**

* [app/routes/admin.py L683-L914](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L683-L914)
* [app/routes/client.py L128-L153](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L128-L153)

---

## Data Model and Relationships

### Promocion Entity Structure

The `Promocion` model stores discount campaigns with temporal constraints and polymorphic item references:

| Field | Type | Description |
| --- | --- | --- |
| `id_promocion` | Integer (PK) | Unique identifier |
| `nombre` | String | Promotion display name |
| `descripcion` | Text | Optional detailed description |
| `descuento` | Decimal | Percentage discount (0-100) |
| `fecha_inicio` | DateTime | Activation start date |
| `fecha_fin` | DateTime | Expiration date |
| `id_producto` | Integer (FK, nullable) | Reference to Producto |
| `id_servicio` | Integer (FK, nullable) | Reference to Servicio |

```

```

**Diagram: Promocion Data Model Relationships**

**Polymorphic Design:** Exactly one of `id_producto` or `id_servicio` must be non-null. This is enforced at the application layer during creation and editing operations.

**Sources:**

* [app/routes/admin.py L744-L761](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L744-L761)
* [app/models/promociones.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/promociones.py)  (referenced)

---

## Admin Promotion Management Interface

### Route: admin.gestion_promociones

The primary management interface displays all promotions with their associated products/services and current status.

**Endpoint:** `GET /admin/gestion_promociones`

**Implementation:** [app/routes/admin.py L683-L707](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L683-L707)

**Query optimization:** Uses `joinedload` to prevent N+1 queries:

```

```

**Template rendering:** [app/templates/gestion_promociones.html](https://github.com/GroveLive/CasaBella/blob/5f618972/app/templates/gestion_promociones.html)

 (referenced at line 699)

The interface provides:

* List of all promotions with product/service names
* Status indicators (Activa, Futura, Expirada) based on current date
* AJAX action buttons (Edit, Delete)
* Modal form for creating new promotions

```

```

**Diagram: Promotion Management Page Load Flow**

**Sources:**

* [app/routes/admin.py L683-L707](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L683-L707)
* [app/routes/admin.py L691-L695](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L691-L695)

---

## AJAX-Based CRUD Operations

All promotion create, update, and delete operations use AJAX to avoid full page reloads. The admin blueprint implements JSON endpoints that validate AJAX requests using the `X-Requested-With` header.

### Creating Promotions

**Endpoint:** `POST /admin/agregar_promocion`

**Implementation:** [app/routes/admin.py L709-L795](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L709-L795)

**Request validation:**

```

```

**Required JSON payload:**

```

```

**Business rules enforced:**

1. Discount must be 0 < descuento ≤ 100 ([line 736](https://github.com/GroveLive/CasaBella/blob/5f618972/line 736) )
2. fecha_inicio must be before fecha_fin ([line 738-739](https://github.com/GroveLive/CasaBella/blob/5f618972/line 738-739) )
3. item_type must be 'producto' or 'servicio' ([line 741-742](https://github.com/GroveLive/CasaBella/blob/5f618972/line 741-742) )
4. Referenced item must exist (404 if not found) ([line 745, 749](https://github.com/GroveLive/CasaBella/blob/5f618972/line 745, 749) )

**Polymorphic item assignment:**

```

```

**Post-creation notification:** [app/routes/admin.py L764-L771](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L764-L771)

### Editing Promotions

**Endpoint:** `POST /admin/editar_promocion/<int:id_promocion>`

**Implementation:** [app/routes/admin.py L797-L881](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L797-L881)

Follows identical validation rules as creation. Updates all fields of existing `Promocion` instance and creates a notification upon success.

### Deleting Promotions

**Endpoint:** `POST /admin/eliminar_promocion/<int:id_promocion>`

**Implementation:** [app/routes/admin.py L883-L914](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L883-L914)

**Deletion process:**

1. Validate AJAX request ([line 889-890](https://github.com/GroveLive/CasaBella/blob/5f618972/line 889-890) )
2. Retrieve Promocion or 404 ([line 892](https://github.com/GroveLive/CasaBella/blob/5f618972/line 892) )
3. Store item reference for notification ([line 895-896](https://github.com/GroveLive/CasaBella/blob/5f618972/line 895-896) )
4. Delete record with IntegrityError handling ([line 897-898](https://github.com/GroveLive/CasaBella/blob/5f618972/line 897-898) )
5. Create notification ([line 900-906](https://github.com/GroveLive/CasaBella/blob/5f618972/line 900-906) )

```

```

**Diagram: AJAX Promotion CRUD Workflow**

**Sources:**

* [app/routes/admin.py L709-L795](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L709-L795)
* [app/routes/admin.py L797-L881](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L797-L881)
* [app/routes/admin.py L883-L914](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L883-L914)

---

## Automatic Promotion Application

### Cart Addition with Dynamic Pricing

When clients add items to their cart, the system automatically detects and applies active promotions. The discounted price is stored in `DetalleCarrito.precio_unitario` for transaction consistency.

**Implementation:** [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)

**Active promotion query:**

```

```

**Price calculation:**

```

```

**For products:** [app/routes/client.py L132-L143](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L132-L143)

**For services:** [app/routes/client.py L144-L153](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L144-L153)

### Catalog Filtering by Promotions

Clients can filter product and service listings to show only items with active promotions.

**Products route:** `GET /client/productos?filter=promotions`
**Implementation:** [app/routes/client.py L74-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L74-L94)

```

```

**Services route:** `GET /client/servicios?filter=promotions`
**Implementation:** [app/routes/client.py L52-L72](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L72)

### Invoice Display

Promotions are displayed on generated PDF invoices to show customers the discounts applied at purchase time.

**PDF generation logic:** [app/routes/client.py L403-L428](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L403-L428)

```

```

**Note:** Invoices query promotions at display time, but use the stored `precio_unitario` from `DetalleVenta` for calculations, ensuring historical accuracy even if promotions expire.

```

```

**Diagram: Promotion Application at Cart Addition**

**Sources:**

* [app/routes/client.py L106-L178](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L106-L178)
* [app/routes/client.py L128-L153](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L128-L153)
* [app/routes/client.py L403-L428](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L403-L428)

---

## Date-Based Activation Logic

Promotions use UTC datetime comparison to determine active status. The pattern `fecha_inicio <= now <= fecha_fin` appears consistently throughout the codebase.

### Active Promotion Detection

**Standard query pattern:**

```

```

**Used in:**

* Client services route ([line 67](https://github.com/GroveLive/CasaBella/blob/5f618972/line 67) )
* Client products route ([line 89](https://github.com/GroveLive/CasaBella/blob/5f618972/line 89) )
* Client carrito route ([line 193](https://github.com/GroveLive/CasaBella/blob/5f618972/line 193) )
* Client dashboard ([line 473](https://github.com/GroveLive/CasaBella/blob/5f618972/line 473) )
* Cart addition logic ([lines 139, 147](https://github.com/GroveLive/CasaBella/blob/5f618972/lines 139, 147) )
* Invoice generation ([lines 408, 419, 623, 635](https://github.com/GroveLive/CasaBella/blob/5f618972/lines 408, 419, 623, 635) )

### Status Calculation in UI

The admin interface calculates promotion status for display:

```

```

**Implementation:** [app/routes/admin.py L784-L870](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L784-L870)

| Status | Condition | Visual Treatment |
| --- | --- | --- |
| Activa | fecha_inicio ≤ now ≤ fecha_fin | Success badge |
| Futura | fecha_inicio > now | Info badge |
| Expirada | fecha_fin < now | Warning badge |

**Sources:**

* [app/routes/client.py L59-L68](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L59-L68)
* [app/routes/client.py L81-L90](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L81-L90)
* [app/routes/admin.py L784](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L784-L784)
* [app/routes/admin.py L870](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L870-L870)

---

## Notification System Integration

The promotion system creates `Notificacion` records for administrators when promotions are created, edited, or deleted. All notifications use `tipo='promocion'`.

### Creation Notification

**Implementation:** [app/routes/admin.py L764-L771](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L764-L771)

```

```

### Update Notification

**Implementation:** [app/routes/admin.py L850-L857](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L850-L857)

Message format: `"Promoción '{nombre}' actualizada con éxito..."`

### Deletion Notification

**Implementation:** [app/routes/admin.py L900-L906](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L900-L906)

Message format: `"Promoción '{nombre}' eliminada con éxito..."`

**Sources:**

* [app/routes/admin.py L764-L771](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L764-L771)
* [app/routes/admin.py L850-L857](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L850-L857)
* [app/routes/admin.py L900-L906](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L900-L906)

---

## Transaction and Error Handling

### Database Transaction Management

All AJAX operations use explicit transaction management with rollback on error:

```

```

### Validation Error Responses

| Error Condition | HTTP Status | Response Message |
| --- | --- | --- |
| Missing required fields | 400 | "Todos los campos son obligatorios." |
| Invalid discount range | 400 | "El descuento debe estar entre 0 y 100%." |
| Invalid date range | 400 | "La fecha de inicio debe ser anterior a la fecha de fin." |
| Invalid item_type | 400 | "Tipo de item inválido." |
| Duplicate name | 400 | "Ya existe una promoción con ese nombre." |
| Item not found | 404 | (Automatic via get_or_404) |
| Generic exception | 500 | "Error: {exception_message}" |

**Sources:**

* [app/routes/admin.py L727-L739](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L727-L739)
* [app/routes/admin.py L788-L795](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L788-L795)

---

## Integration Points

### Frontend JavaScript Requirements

The admin promotion management page requires JavaScript to:

1. Open modal forms for create/edit operations
2. Populate form fields with existing data for editing
3. Submit AJAX POST requests with JSON payloads
4. Handle JSON responses and update DOM
5. Display success/error messages

**Expected response structure:**

```

```

### Database Schema Dependencies

Promotions require:

* `Producto` table with `id_producto` primary key
* `Servicio` table with `id_servicio` primary key
* `Usuario` table with `id_usuario` primary key (for notifications)
* `Notificacion` table with `tipo` enum including 'promocion'

**Foreign key constraints:** `Promocion.id_producto` → `Producto.id_producto`, `Promocion.id_servicio` → `Servicio.id_servicio`

**Sources:**

* [app/routes/admin.py L773-L786](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L773-L786)
* [app/routes/admin.py L859-L872](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L859-L872)

---

## Summary Table: Key Routes and Operations

| Route | Method | Authentication | Purpose | Response Type |
| --- | --- | --- | --- | --- |
| `/admin/gestion_promociones` | GET | admin | List all promotions | HTML |
| `/admin/agregar_promocion` | POST | admin | Create new promotion | JSON |
| `/admin/editar_promocion/<id>` | POST | admin | Update existing promotion | JSON |
| `/admin/eliminar_promocion/<id>` | POST | admin | Delete promotion | JSON |
| `/client/productos?filter=promotions` | GET | cliente | Filter products by promotions | HTML |
| `/client/servicios?filter=promotions` | GET | cliente | Filter services by promotions | HTML |

**Sources:**

* [app/routes/admin.py L683-L914](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/admin.py#L683-L914)
* [app/routes/client.py L52-L94](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/client.py#L52-L94)