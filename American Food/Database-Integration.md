# Database Integration

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)
> * [img/alitas.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas.jpg)
> * [img/hamburguesa.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/hamburguesa.jpg)

## Purpose and Scope

This page documents how menu item images are integrated with the database layer of the American Food system. It covers the `menu` table's `imagen` field, file naming conventions, storage patterns, and the relationship between static image assets in the filesystem and their corresponding database records. For information about the catalog of available images and their organization, see [Menu Item Images](/axchisan/AmericanFood/6.1-menu-item-images). For details about image metadata and licensing, see [Image Metadata and Licensing](/axchisan/AmericanFood/6.2-image-metadata-and-licensing).

## The menu Table Structure

The `menu` table serves as the central repository for all menu item data, including the critical link to image assets. The table is defined in [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

| Field | Type | Constraints | Purpose |
| --- | --- | --- | --- |
| `id` | int(11) | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| `nombre` | varchar(100) | NOT NULL | Item name |
| `descripcion` | text | NULL | Item description |
| `categoria` | enum | NOT NULL | 'entradas', 'principales', 'postres', 'bebidas' |
| `precio` | decimal(10,2) | NOT NULL | Item price |
| `imagen` | varchar(255) | NULL | **Filename of associated image** |
| `disponible` | tinyint(1) | DEFAULT 1 | Availability flag |
| `fecha_actualizacion` | datetime | AUTO UPDATE | Last modified timestamp |
| `ingredientes` | text | NULL | Ingredients list |

The `imagen` field is the integration point between the database and filesystem. It stores only the filename (not a full path), allowing the application to construct complete paths at runtime.

**Sources:** [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

## Image Filename Convention

### Naming Pattern

All image filenames in the `menu` table follow a strict timestamp-prefixed pattern:

```
{unix_timestamp}_{descriptive_name}.{extension}
```

### Examples from Database

| Menu Item | imagen Field Value | Pattern Breakdown |
| --- | --- | --- |
| Alitas Bufalo | `1743391251_Alitas bufalo.webp` | timestamp: 1743391251, name: "Alitas bufalo", format: webp |
| Hamburguesa BBQ | `1743391294_hamburguesa_bbq.jpg` | timestamp: 1743391294, name: "hamburguesa_bbq", format: jpg |
| Hot Dog Clásico | `1743391334_hotdog_clasico.jpg` | timestamp: 1743391334, name: "hotdog_clasico", format: jpg |
| Limonada | `1743391358_limonadas.jpg` | timestamp: 1743391358, name: "limonadas", format: jpg |
| Tarta de Manzana | `1743391401_tarta_manzana.jpg` | timestamp: 1743391401, name: "tarta_manzana", format: jpg |
| Brownie con Helado | `1743391445_brownie_con_helado.jpg` | timestamp: 1743391445, name: "brownie_con_helado", format: jpg |

### Timestamp Purpose

The Unix timestamp prefix (seconds since epoch) serves multiple purposes:

* **Uniqueness**: Prevents filename collisions when multiple items have similar names
* **Chronological Ordering**: Files can be sorted by upload time
* **Version Control**: Updating an item's image generates a new timestamp, preserving old versions
* **Cache Busting**: Browser caches are invalidated when image filenames change

**Sources:** [database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

## Image Storage and Retrieval Architecture

### System Integration Flow

```

```

**Sources:** [database/american_food L163-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L185)

## Database Relationships Involving Images

### Foreign Key Dependencies

The `menu` table participates in critical relationships that propagate image access throughout the order system:

```

```

### Cascade Implications

1. **detalles_pedidos.plato_id → menu.id**: References menu items including their images. If a menu item is deleted, the foreign key constraint prevents deletion (no CASCADE), preserving order history.
2. **Image Availability**: The `menu.disponible` field controls whether items appear in customer-facing interfaces, but the `imagen` field remains intact regardless of availability status.

**Sources:** [database/american_food L455-L457](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L455-L457)

 [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

## File Path Resolution Pattern

### Path Construction Logic

The application uses a consistent pattern for constructing image URLs:

```
Base Path: img/
Stored Value: {timestamp}_{name}.{ext}
Resolved URL: img/{timestamp}_{name}.{ext}
```

### Example Resolution

For menu item ID 4 (Alitas Bufalo):

| Database Query Result | Path Construction | Final HTML |
| --- | --- | --- |
| `imagen = '1743391251_Alitas bufalo.webp'` | `'img/' + '1743391251_Alitas bufalo.webp'` | `<img src="img/1743391251_Alitas bufalo.webp">` |

### Character Encoding Considerations

* The database connection uses UTF-8 encoding ([config/conexionDB.php L15](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L15-L15) )
* Image filenames may contain non-ASCII characters (e.g., "Alitas bufalo" with space)
* The `varchar(255)` field accommodates international characters and spaces
* URL encoding is handled by the browser when requesting image resources

**Sources:** [database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

## Integration with Order System

### Image Access Through Orders

When orders are processed, images are accessed indirectly through the `detalles_pedidos` table:

```

```

### Order Details Example

Order ID 1 from [database/american_food L273](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L273-L273)

 contains items referencing menu images:

| detalles_pedidos.id | pedido_id | plato_id | Menu Item | imagen Field |
| --- | --- | --- | --- | --- |
| 1 | 1 | 5 | Hamburguesa | `1743391294_hamburguesa_bbq.jpg` |
| 2 | 1 | 4 | Alitas Bufalo | `1743391251_Alitas bufalo.webp` |
| 3 | 1 | 6 | Hot Dog | `1743391334_hotdog_clasico.jpg` |
| 4 | 1 | 9 | Brownie con Helado | `1743391445_brownie_con_helado.jpg` |
| 5 | 1 | 8 | Tarta de Manzana | `1743391401_tarta_manzana.jpg` |
| 6 | 1 | 7 | Limonada | `1743391358_limonadas.jpg` |

**Sources:** [database/american_food L54-L61](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L54-L61)

 [database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

## Database Query Patterns

### Retrieving Menu Items with Images

Typical query pattern for fetching menu items with their associated images:

```

```

Result includes `imagen` field containing filenames like `1743391294_hamburguesa_bbq.jpg`.

### Filtering by Image Availability

Check for menu items missing images:

```

```

All current menu items have images populated ([database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

), indicating complete image integration.

### Join Pattern for Order Display

Retrieving order details with item images:

```

```

This pattern is used by kitchen and cashier interfaces to display order items with visual references.

**Sources:** [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

 [database/american_food L42-L48](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L42-L48)

## Index Optimization

### Relevant Indexes

The `menu` table includes an index optimized for category-based image retrieval:

```

```

Defined at [database/american_food L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L352)

This index accelerates queries filtering by category (e.g., fetching all "entradas" with images for the customer menu interface), which is a common operation when displaying categorized menu sections with images.

### Query Performance Impact

* **Without Index**: Full table scan to find items by category
* **With Index**: Direct lookup of category members, critical when loading image-heavy menu grids
* **Typical Use Case**: Customer interface (`cliente.css`) displays menu items grouped by category with thumbnail images

**Sources:** [database/american_food L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L352)

## Data Integrity Constraints

### NULL Handling

The `imagen` field allows NULL values ([database/american_food L169](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L169-L169)

), providing flexibility:

* **Newly Created Items**: Can be saved without images initially
* **Discontinued Items**: Images can be removed while preserving the menu record
* **Import/Migration**: Allows batch insertion before image upload

### Field Length

`varchar(255)` length constraint:

* Maximum filename length: 255 characters
* Typical usage: 40-60 characters (timestamp + descriptive name + extension)
* Headroom: Sufficient for long descriptive names and paths

### Character Set

The table uses `utf8mb4` character set ([database/american_food L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L173-L173)

):

* Supports Unicode characters in filenames
* Enables Spanish characters (e.g., "tarta_manzana")
* Compatible with international menu items

**Sources:** [database/american_food L169](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L169-L169)

 [database/american_food L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L173-L173)

## Image Update Workflow

### Modification Timestamp Tracking

The `fecha_actualizacion` field automatically updates when records change:

```

```

This provides audit trail for image updates:

1. New image uploaded → generates new timestamp-prefixed filename
2. `imagen` field updated with new filename
3. `fecha_actualizacion` automatically records modification time
4. Old image file may remain in filesystem (manual cleanup required)

### Update Example

```

```

**Sources:** [database/american_food L171](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L171-L171)

## Security Considerations

### File Path Sanitization

The `imagen` field stores only filenames, not full paths:

* **Prevention**: Directory traversal attacks (e.g., `../../etc/passwd`) are prevented since paths are constructed by prepending `img/`
* **Validation**: Application should validate that `imagen` values contain only allowed characters
* **Storage**: No user-controlled path components exist in database

### SQL Injection Protection

When querying menu items by image criteria:

```

```

The system uses prepared statements for database operations, as demonstrated in [crud/registro_INSERT.php L61-L67](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L61-L67)

**Sources:** [database/american_food L169](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L169-L169)

## Integration Summary

The database integration for menu images implements a robust, scalable pattern:

| Aspect | Implementation | Benefits |
| --- | --- | --- |
| **Storage** | Filename only in `imagen` field | Allows path changes without DB updates |
| **Naming** | Timestamp prefix convention | Uniqueness, versioning, cache busting |
| **Relationships** | Foreign keys through `detalles_pedidos` | Images accessible in order processing |
| **Character Support** | UTF-8 encoding | International menu items supported |
| **Integrity** | NULL allowed, max 255 chars | Flexibility with reasonable constraints |
| **Indexing** | Category index | Fast filtered queries with images |
| **Audit Trail** | Auto-updating timestamp | Track image modifications |

This architecture enables efficient image management while maintaining data integrity and supporting the application's multi-role workflow requirements documented in [User Roles and Access Control](/axchisan/AmericanFood/7-user-roles-and-access-control).

**Sources:** [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

 [database/american_food L179-L185](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L185)

 [database/american_food L352](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L352-L352)