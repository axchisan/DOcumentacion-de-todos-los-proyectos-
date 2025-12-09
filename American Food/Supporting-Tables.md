# Supporting Tables

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)

## Purpose and Scope

This document describes the auxiliary database tables that support the American Food restaurant management system but are not directly involved in the core order processing workflow. These tables provide infrastructure for system configuration and inventory management.

The two supporting tables documented here are:

* **`configuracion`**: Key-value store for system-wide settings
* **`inventario`**: Stock management for ingredients and supplies

For information about core operational tables like `usuarios`, `pedidos`, `menu`, and `mesas`, see [Core Tables](/axchisan/AmericanFood/2.2-core-tables). For order-related tables including `detalles_pedidos`, `pagos`, `facturas`, and `informacion_entrega`, see [Order Management Tables](/axchisan/AmericanFood/2.3-order-management-tables).

---

## Database Supporting Tables Overview

```

```

**Supporting Tables Relationship Diagram**

These tables operate independently from the main order processing flow but provide critical supporting functionality: `configuracion` stores runtime system settings, while `inventario` tracks ingredient stock levels for menu items.

**Sources**: [database/american_food L27-L156](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L27-L156)

---

## Configuration Table (configuracion)

### Table Structure

The `configuracion` table provides a key-value store for system-wide configuration settings.

| Field | Type | Null | Key | Description |
| --- | --- | --- | --- | --- |
| `id` | int(11) | NO | PRIMARY | Auto-incrementing primary key |
| `clave` | varchar(50) | NO |  | Configuration key (setting name) |
| `valor` | varchar(255) | NO |  | Configuration value (setting data) |

**Table Definition**:

```

```

**Sources**: [database/american_food L30-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L34)

### Purpose and Design Pattern

The `configuracion` table implements a **generic key-value store pattern** that allows runtime configuration without code changes. This design supports:

* Dynamic system settings that can be modified through an admin interface
* Environment-specific configuration (development, staging, production)
* Feature flags and toggles
* Business rules (tax rates, service charges, delivery fees)
* Integration settings (payment gateways, email servers, API keys)

### Current State

The table currently has no data inserted:

```

```

This indicates the system currently relies entirely on hard-coded configuration in [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 rather than database-driven settings.

**Sources**: [database/american_food L30-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L34)

### Relationship to Configuration Files

```

```

**Configuration System Architecture**

The system currently uses a hybrid approach where database connection credentials are defined in PHP constants ([config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

), while the `configuracion` table provides extensibility for runtime settings. The empty table suggests this feature is planned but not yet implemented.

**Sources**: [database/american_food L30-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L34)

 [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

### Potential Use Cases

Based on the table schema and system architecture, the `configuracion` table is designed to store:

| Configuration Key Example | Value Example | Purpose |
| --- | --- | --- |
| `tax_rate` | `0.16` | Sales tax percentage |
| `delivery_fee` | `5000.00` | Standard delivery charge |
| `max_delivery_distance_km` | `10` | Maximum delivery radius |
| `min_order_for_delivery` | `20000.00` | Minimum order value for delivery |
| `restaurant_name` | `American Food` | Display name in receipts/invoices |
| `restaurant_address` | `...` | Address for invoices |
| `email_notifications` | `enabled` | Feature toggle for email alerts |
| `kitchen_display_refresh_ms` | `5000` | Auto-refresh interval for kitchen UI |

**Sources**: [database/american_food L30-L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L30-L34)

### Database Indexes

```

```

The table has only a primary key on `id`. For efficient configuration lookups by key name, an index on `clave` would be recommended but is not currently defined.

**Sources**: [database/american_food L316-L317](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L316-L317)

---

## Inventory Table (inventario)

### Table Structure

The `inventario` table tracks stock levels for ingredients, supplies, and raw materials used in menu preparation.

| Field | Type | Null | Key | Description |
| --- | --- | --- | --- | --- |
| `id` | int(11) | NO | PRIMARY | Auto-incrementing primary key |
| `nombre` | varchar(100) | NO |  | Item name (ingredient/supply) |
| `categoria` | varchar(50) | NO |  | Item category (e.g., "vegetales", "carnes", "bebidas") |
| `cantidad` | decimal(10,2) | NO |  | Current stock quantity |
| `unidad` | enum | NO |  | Unit of measurement: 'kg','g','l','ml','unidades' |
| `precio_unitario` | decimal(10,2) | NO |  | Cost per unit |
| `proveedor` | varchar(100) | YES |  | Supplier/vendor name |
| `cantidad_minima` | decimal(10,2) | NO |  | Minimum stock threshold for reorder alerts |

**Table Definition**:

```

```

**Sources**: [database/american_food L139-L148](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L148)

### Field Details

#### Unit of Measurement (unidad)

The `unidad` field uses an ENUM constraint to enforce standard measurement units:

| Unit | Type | Typical Use |
| --- | --- | --- |
| `kg` | Mass | Large quantities (flour, meat, etc.) |
| `g` | Mass | Small quantities (spices, seasonings) |
| `l` | Volume | Large liquid quantities (oil, milk) |
| `ml` | Volume | Small liquid quantities (vanilla extract) |
| `unidades` | Count | Discrete items (eggs, buns, bottles) |

**Sources**: [database/american_food L144](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L144-L144)

#### Stock Management Fields

```

```

**Stock Level Monitoring Logic**

The system uses `cantidad` (current stock) and `cantidad_minima` (minimum threshold) to trigger reorder alerts when inventory falls below acceptable levels.

**Sources**: [database/american_food L143-L147](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L143-L147)

### Sample Data

The table currently contains one test record:

```

```

**Analysis of Sample Record**:

* **Item**: Pear (pera)
* **Category**: Vegetables (vegetales)
* **Stock**: 233 units
* **Unit Price**: 0.00 (not configured)
* **Supplier**: NULL (not assigned)
* **Minimum Stock**: 0.00 (no alert threshold set)

This record appears to be test data with incomplete configuration.

**Sources**: [database/american_food L154-L155](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L154-L155)

### Inventory-to-Menu Relationship

```

```

**Inventory and Menu Integration**

Currently, the `inventario` table and `menu` table have **no direct foreign key relationship**. The `menu.ingredientes` field ([database/american_food L172](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L172-L172)

) is a text field that could contain ingredient references, but there is no enforced link for automatic stock deduction when orders are placed.

**Sources**: [database/american_food L139-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L173)

### Use Cases

The `inventario` table supports several operational workflows:

#### 1. Stock Level Monitoring

Staff can query current inventory to check stock levels:

```

```

#### 2. Supplier Management

Track which suppliers provide specific ingredients:

```

```

#### 3. Cost Analysis

Calculate total inventory value by category:

```

```

**Sources**: [database/american_food L139-L148](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L148)

### Database Indexes

```

```

The table has only a primary key. Additional indexes could improve performance:

* Index on `categoria` for category-based queries
* Index on `proveedor` for supplier reports
* Composite index on `(cantidad, cantidad_minima)` for low-stock queries

**Sources**: [database/american_food L343-L345](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L343-L345)

### Future Enhancement Opportunities

The current `inventario` implementation provides basic stock tracking but lacks integration with the order system. Potential enhancements:

1. **Automatic Stock Deduction**: Create a junction table linking `menu` items to `inventario` ingredients with quantity ratios, then trigger stock updates when orders are marked as `en_preparacion` or `listo`.
2. **Purchase Order System**: Add a `compras` table to track inventory purchases and automatically update `cantidad` when shipments arrive.
3. **Historical Tracking**: Add an `inventario_movimientos` table to log all stock changes (additions, deductions, adjustments) with timestamps and reasons.
4. **Waste Tracking**: Track ingredient spoilage and waste separately from usage.
5. **Recipe Management**: Implement a proper recipe system with ingredient quantities per dish.

**Sources**: [database/american_food L139-L148](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L139-L148)

---

## Table Relationships Summary

```

```

**Supporting Tables Entity Relationship Diagram**

The two supporting tables operate independently:

* **`configuracion`**: No foreign key relationships (standalone key-value store)
* **`inventario`**: No enforced relationships, though logically related to `menu.ingredientes` field through text references

**Sources**: [database/american_food L27-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L27-L173)

---

## Summary

The supporting tables provide infrastructure capabilities:

| Table | Purpose | Current State | Integration Level |
| --- | --- | --- | --- |
| `configuracion` | System settings storage | Empty (not yet used) | None |
| `inventario` | Ingredient stock tracking | 1 test record | Manual only |

Both tables are defined in the schema but minimally utilized, suggesting they represent planned features that are not yet fully implemented in the application layer. The `inventario` table has basic structure for stock management, while the `configuracion` table provides extensibility for future system-wide settings.

**Sources**: [database/american_food L27-L156](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L27-L156)