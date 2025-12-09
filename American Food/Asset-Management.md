# Asset Management

> **Relevant source files**
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)
> * [img/Cheesecake.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg)
> * [img/alitas.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas.jpg)
> * [img/hamburguesa.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/hamburguesa.jpg)
> * [img/hotdog.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/hotdog.jpg)

## Purpose and Scope

This document describes the image asset management system used throughout the American Food application. It covers how menu item images are stored, organized, linked to database records, and managed for licensing compliance. For information about how these assets are displayed in the user interface, see the Frontend Architecture documentation ([5](/axchisan/AmericanFood/5-frontend-architecture)).

The asset management system handles 26+ image files representing menu items across four categories: entradas (appetizers), principales (main dishes), postres (desserts), and bebidas (beverages).

---

## System Overview

The asset management system consists of three main components: physical image files stored in the `img/` directory, database references in the `menu` table, and embedded metadata for licensing compliance.

```

```

**Sources**:

* [database/american_food L160-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L160-L186)
* Diagram 7 from high-level architecture

---

## Database Integration

### Menu Table Schema

The `menu` table contains the `imagen` field that stores the filename of each menu item's image. This field allows null values, making images optional for menu items.

| Field | Type | Description |
| --- | --- | --- |
| `id` | INT(11) | Primary key |
| `nombre` | VARCHAR(100) | Menu item name |
| `categoria` | ENUM | Category: entradas, principales, postres, bebidas |
| `precio` | DECIMAL(10,2) | Item price |
| `imagen` | VARCHAR(255) | **Image filename** |
| `disponible` | TINYINT(1) | Availability flag |
| `fecha_actualizacion` | DATETIME | Last update timestamp |

```

```

**Sources**:

* [database/american_food L160-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L160-L173)
* [database/american_food L179-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L186)

### File Reference Pattern

Images are referenced using a timestamp prefix combined with a descriptive name:

```
[unix_timestamp]_[descriptive_name].[extension]
```

Examples from the database:

* `1743391251_Alitas bufalo.webp`
* `1743391294_hamburguesa_bbq.jpg`
* `1743391334_hotdog_clasico.jpg`
* `1743391358_limonadas.jpg`
* `1743391401_tarta_manzana.jpg`
* `1743391445_brownie_con_helado.jpg`

The timestamp prefix (Unix epoch time) ensures unique filenames and provides chronological ordering.

**Sources**:

* [database/american_food L179-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L186)

---

## Image Organization by Category

The system organizes images according to the four menu categories defined in the database schema.

```

```

### Current Asset Inventory

| Category | Items | Sample Images |
| --- | --- | --- |
| **entradas** | Hamburgers, Hot Dogs | `hamburguesa_bbq.jpg`, `hotdog_clasico.jpg` |
| **principales** | Wings | `Alitas bufalo.webp` |
| **postres** | Desserts | `tarta_manzana.jpg`, `brownie_con_helado.jpg` |
| **bebidas** | Beverages | `limonadas.jpg` |

**Sources**:

* [database/american_food L179-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L186)
* Diagram 7 from high-level architecture

---

## Image Metadata and Licensing

All image assets contain embedded metadata for rights management and tracking. The metadata follows industry standards including EXIF, XMP, and Getty Images GIFT (Getty Images Framework for Image Transmission).

### Metadata Architecture

```

```

**Sources**:

* [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

### Metadata Fields

The embedded XMP metadata includes the following key fields:

| Namespace | Field | Purpose | Example Value |
| --- | --- | --- | --- |
| `photoshop` | `Credit` | Source attribution | "Getty Images" |
| `GettyImagesGIFT` | `AssetID` | Unique asset identifier | "1146488934" |
| `xmpRights` | `WebStatement` | License agreement URL | istockphoto.com/legal/license-agreement |
| `dc` | `creator` | Photographer/creator | "emrah_oztas" |
| `dc` | `description` | Image description | "Stawberry Cheesecake" |
| `plus` | `DataMining` | Usage restrictions | DMI-PROHIBITED-EXCEPTSEARCHENGINEINDEXING |
| `plus` | `LicensorURL` | License purchase URL | istockphoto.com/photo/license-gm... |

**Sources**:

* [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

### Licensing Restrictions

All images contain the following data mining restriction:

```
DMI-PROHIBITED-EXCEPTSEARCHENGINEINDEXING
```

This restriction prohibits automated scraping and data mining of the images except for search engine indexing purposes. The license agreements are tracked through embedded URLs pointing to iStockphoto terms.

**Asset ID Tracking Example:**

```

```

**Sources**:

* [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)
* Diagram 7 from high-level architecture

---

## File Naming Conventions

The system uses two distinct naming patterns for image files:

### Pattern 1: Timestamped Files (Database-Referenced)

Format: `[unix_timestamp]_[descriptive_name].[extension]`

These files are referenced in the `menu.imagen` field and represent uploaded menu item images.

**Examples:**

* `1743391251_Alitas bufalo.webp` - Wings with buffalo sauce
* `1743391294_hamburguesa_bbq.jpg` - BBQ hamburger
* `1743391334_hotdog_clasico.jpg` - Classic hot dog
* `1743391401_tarta_manzana.jpg` - Apple tart
* `1743391445_brownie_con_helado.jpg` - Brownie with ice cream

### Pattern 2: Descriptive Files (Stock Assets)

Format: `[descriptive_name].[extension]`

These are stock photography assets from Getty Images/iStockphoto.

**Examples:**

* `Cheesecake.jpg` - Getty Asset ID 1146488934
* `alitas.jpg` - Wings stock photo
* `hamburguesa_bbq.jpg` - BBQ hamburger stock photo

**Sources**:

* [database/american_food L179-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L186)
* [img/Cheesecake.jpg L1](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L1)

---

## Image Format Support

The system supports multiple image formats:

| Format | Usage | Example |
| --- | --- | --- |
| `.jpg` | Primary format for stock photography | `hamburguesa_bbq.jpg`, `Cheesecake.jpg` |
| `.webp` | Modern format for uploaded images | `Alitas bufalo.webp` |

The `menu.imagen` field (VARCHAR 255) can store filenames of any length up to 255 characters, accommodating both formats and descriptive naming.

**Sources**:

* [database/american_food L169](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L169-L169)
* [database/american_food L180](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L180-L180)

---

## Integration with Menu Display

The asset management system integrates with the frontend through direct file path references in the UI components.

```

```

The menu display components reference images using relative paths:

```

```

**Sources**:

* [database/american_food L160-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L160-L186)
* Diagram 5 from high-level architecture (cliente.css menu system)

---

## Availability Management

The `menu` table includes a `disponible` flag (TINYINT) that controls whether menu items are shown to users. This flag works in conjunction with the image assets:

| Value | Status | Behavior |
| --- | --- | --- |
| `1` | Available | Menu item and image displayed |
| `0` | Unavailable | Menu item hidden from customer view |

Example from database:

```

```

The system maintains the image files even when items are marked unavailable, allowing quick re-activation without re-uploading assets.

**Sources**:

* [database/american_food L170](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L170-L170)
* [database/american_food L179-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L186)

---

## Storage Location and Access

All image assets are stored in a single directory structure:

```
AmericanFood/
├── img/
│   ├── Cheesecake.jpg
│   ├── alitas.jpg
│   ├── hamburguesa_bbq.jpg
│   ├── 1743391251_Alitas bufalo.webp
│   ├── 1743391294_hamburguesa_bbq.jpg
│   ├── 1743391334_hotdog_clasico.jpg
│   ├── 1743391358_limonadas.jpg
│   ├── 1743391401_tarta_manzana.jpg
│   └── 1743391445_brownie_con_helado.jpg
└── [other application files]
```

Images are served as static files directly from the `img/` directory without additional processing or CDN integration. The web server must be configured to serve this directory with appropriate MIME types:

* `image/jpeg` for `.jpg` files
* `image/webp` for `.webp` files

**Sources**:

* File structure from provided file paths
* Diagram 1 from high-level architecture (Asset Layer)

---

## Licensing Compliance Requirements

The Getty Images/iStockphoto assets impose specific requirements for production deployment:

### Required Actions

1. **License Verification**: Each image with an embedded `GettyImagesGIFT:AssetID` must have a valid license agreement
2. **Attribution**: The `photoshop:Credit` field ("Getty Images") must be respected per license terms
3. **Usage Tracking**: Asset IDs enable verification of legitimate usage rights
4. **Data Mining Restrictions**: Automated scraping systems must respect the `DMI-PROHIBITED-EXCEPTSEARCHENGINEINDEXING` flag

### Asset ID Examples

The following Asset IDs are embedded in the current image inventory:

* Cheesecake.jpg: `1146488934`
* Additional images have similar tracking embedded in their XMP metadata

These IDs should be cross-referenced with purchase records before production deployment.

**Sources**:

* [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)
* Diagram 7 from high-level architecture

---

## Character Encoding

The system uses UTF-8 encoding throughout, supporting Spanish-language menu item names with special characters:

* File names support: `Alitas bufalo`, `tarta_manzana`
* Database storage: `CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci`
* Metadata descriptions: "Stawberry Cheesecake" (UTF-8 encoded)

This encoding ensures proper handling of accented characters common in Spanish menu terminology (e.g., "bebidas", "tarta").

**Sources**:

* [database/american_food L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L173-L173)
* [database/american_food L179-L186](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L179-L186)