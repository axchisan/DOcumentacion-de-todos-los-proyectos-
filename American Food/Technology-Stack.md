# Technology Stack

> **Relevant source files**
> * [config/conexionDB.php](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php)
> * [css/style.css](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css)
> * [database/american_food (6).sql](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql) sql)
> * [img/Cheesecake.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg)

This document catalogs the technologies, languages, frameworks, and standards used in the American Food restaurant management system. It covers the backend server environment, database system, frontend presentation layer, asset management standards, and security mechanisms.

For information about how these technologies are configured and initialized, see [Database Configuration](/axchisan/AmericanFood/3.1-database-configuration) and [Connection Manager](/axchisan/AmericanFood/3.2-connection-manager). For details on the database schema itself, see [Schema Overview](/axchisan/AmericanFood/2.1-schema-overview).

---

## Technology Overview

The American Food system is built as a traditional three-tier web application using a LAMP-like stack with the following core technologies:

| Layer | Technology | Version/Standard | Purpose |
| --- | --- | --- | --- |
| Backend Language | PHP | 8.2.4 | Server-side application logic |
| Database | MariaDB | 10.4.28 | Data persistence and relational storage |
| Database Interface | MySQLi | Native PHP extension | Database connectivity and query execution |
| Frontend Styling | CSS3 | Latest standard | User interface presentation |
| Character Encoding | UTF-8 | Unicode standard | Internationalization support |
| Image Format | JPEG | ISO/IEC 10918 | Menu item photography |
| Metadata Standard | XMP | Adobe XMP specification | Image rights management |

**Sources:** [database/american_food L1-L8](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L8)

 [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

---

## Backend Technology Stack

### PHP Runtime Environment

The application runs on PHP 8.2.4, leveraging modern PHP features for security and performance:

```

```

**PHP Extensions Used:**

* **mysqli**: Native MySQL driver for database operations
* **session**: Session state management across requests
* **filter**: Input sanitization and validation
* **password**: Cryptographic password hashing

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [crud/registro_INSERT.php L38-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L38-L59)

### Database System

The database layer uses MariaDB 10.4.28, a MySQL-compatible relational database management system:

```

```

**Database Features:**

* **Storage Engine**: InnoDB (default) for ACID compliance and foreign key support
* **Character Set**: UTF-8 (`utf8mb4_general_ci`) for Spanish language support
* **Transaction Support**: Implicit for INSERT, UPDATE, DELETE operations
* **Foreign Key Constraints**: Enforced relationships between tables

**Sources:** [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

 [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

 [database/american_food L1-L22](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L22)

### MySQLi Database Interface

Database operations use the MySQLi extension with prepared statements:

```

```

**MySQLi Connection Properties:**

* **Connection Object**: Global variable `$link` available throughout application
* **Error Mode**: Manual error checking via `connect_errno` property
* **Character Encoding**: Set to `utf8` after successful connection
* **Persistent Connections**: Not used (standard connection per request)

**Sources:** [config/conexionDB.php L8-L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L8-L17)

---

## Frontend Technology Stack

### CSS3 Architecture

The presentation layer is implemented entirely in CSS3 without JavaScript frameworks:

| File | Purpose | Lines of Code | Key Features |
| --- | --- | --- | --- |
| `style.css` | Global design system | 704 lines | CSS variables, responsive grid, cart sidebar |
| `login.css` | Authentication interface | 156 lines | Login cards, role selection, password strength |
| `cliente.css` | Customer interface | 295 lines | Menu browsing, cart management, order history |
| `cajera.css` | Cashier interface | 535 lines | Payment processing, sales analytics, invoices |
| `cocina.css` | Kitchen interface | 27 lines | Order cards, status visualization |
| `mesera.css` | Server interface | 535 lines | Table management, order taking |

**CSS3 Features Used:**

* **CSS Custom Properties**: Color palette and theme variables
* **Flexbox Layout**: Flexible component positioning
* **Grid Layout**: Table management and menu displays
* **Media Queries**: Responsive breakpoints at 992px, 768px, 576px
* **Transitions**: Smooth state changes and hover effects
* **Box Shadows**: Depth and elevation effects
* **Pseudo-elements**: Custom list bullets and decorative elements

**Sources:** [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 [css/cliente.css L1-L295](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cliente.css#L1-L295)

 [css/cajera.css L1-L535](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/cajera.css#L1-L535)

### Design System Implementation

```

```

**Sources:** [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

 [css/style.css L43-L83](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L43-L83)

 [css/style.css L575-L693](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L575-L693)

### Typography System

```

```

**Typography Properties:**

* **Primary Typeface**: Poppins (Google Fonts, assumed loaded elsewhere)
* **Font Weight**: 700 (bold) for all headings, 500 for navigation
* **Line Height**: 1.6 for body text
* **Base Font Size**: Browser default (typically 16px)

**Sources:** [css/style.css L21-L41](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L21-L41)

### Responsive Design Breakpoints

The application implements mobile-first responsive design with the following breakpoints:

| Breakpoint | Max Width | Target Devices | Layout Adjustments |
| --- | --- | --- | --- |
| Desktop | 992px+ | Large screens | Full multi-column layout, expanded navigation |
| Tablet | 768px - 991px | iPads, tablets | 2-column grid, compressed carousel (450px height) |
| Mobile | 576px - 767px | Large phones | Single column, stacked navigation, 350px carousel |
| Small Mobile | < 576px | Small phones | Minimal carousel (300px), compact buttons |

**Sources:** [css/style.css L386-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L386-L460)

---

## Image and Asset Technologies

### JPEG Image Format

All menu item images use JPEG format with embedded metadata:

```

```

**JPEG Compression:**

* **Quality Level**: High (minimal artifacts visible)
* **Color Space**: RGB
* **Progressive Encoding**: Not used
* **Dimensions**: Typically 200px height for menu cards (scaled via CSS)

**Sources:** [img/Cheesecake.jpg L1-L20](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L20)

 [database/american_food L163-L173](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L163-L173)

### XMP Metadata Standard

Each image contains extensive XMP metadata for rights management:

**XMP Namespaces Used:**

| Namespace | Prefix | Purpose | Example Values |
| --- | --- | --- | --- |
| Dublin Core | `dc:` | Basic descriptive metadata | `dc:creator`, `dc:description` |
| Adobe Photoshop | `photoshop:` | Production metadata | `photoshop:Credit = "Getty Images"` |
| Getty Images GIFT | `GettyImagesGIFT:` | Asset tracking | `GettyImagesGIFT:AssetID = "1146488934"` |
| XMP Rights | `xmpRights:` | License information | `xmpRights:WebStatement = "https://..."` |
| PLUS License | `plus:` | Usage restrictions | `plus:DataMining = "DMI-PROHIBITED-..."` |

**Example XMP Structure from Cheesecake.jpg:**

```

```

**Sources:** [img/Cheesecake.jpg L2-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L2-L10)

---

## Character Encoding and Internationalization

### UTF-8 Implementation

The system uses UTF-8 encoding throughout for Spanish language support:

```

```

**UTF-8 Encoding Locations:**

* **Database Connection**: Set via `mysqli::set_charset('utf8')` method
* **Database Schema**: All tables use `utf8mb4_general_ci` collation
* **HTML Meta Tags**: Assumed present in HTML templates (not shown in provided files)
* **PHP Source Files**: Saved with UTF-8 encoding

**Character Set Details:**

* **Database**: `utf8mb4` (4-byte UTF-8, full Unicode support including emoji)
* **Connection**: `utf8` (3-byte UTF-8, sufficient for Spanish)
* **Collation**: `general_ci` (case-insensitive comparisons)

**Sources:** [config/conexionDB.php L17](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L17-L17)

 [database/american_food L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L18-L18)

 [database/american_food L34](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L34-L34)

---

## Development Environment

### Technology Versions

The development environment specifications from the database dump:

```

```

**Development Environment Details:**

* **Server Host**: `127.0.0.1` (localhost, local development only)
* **Database User**: `root` with full administrative privileges
* **Authentication**: Empty password (insecure, development only)
* **SQL Mode**: `NO_AUTO_VALUE_ON_ZERO` disables auto-increment for explicit zero values
* **Time Zone**: UTC (`+00:00`) for timestamp storage
* **Generation Time**: April 4, 2025 at 10:09 PM

**Sources:** [database/american_food L1-L12](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L12)

 [config/configuracion.php L3-L7](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/configuracion.php#L3-L7)

---

## Security Technologies

### Password Hashing

The system uses PHP's native password hashing API:

```

```

**Password Hashing Algorithm:**

* **Function**: `password_hash($password, PASSWORD_DEFAULT)`
* **Algorithm**: bcrypt (identified by `$2y$` prefix)
* **Cost Factor**: 12 rounds (visible in hash: `$2y$12$...`)
* **Salt**: Automatically generated, 22-character random salt
* **Output Length**: 60 characters fixed

**Password Storage:**

* **Field Type**: `VARCHAR(255)` to accommodate future algorithm changes
* **Current Length**: 60 characters for bcrypt
* **Format**: `$algorithm$cost$salt$hash`

**Sources:** [database/american_food L295](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L295-L295)

 [database/american_food L303-L307](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L303-L307)

### Input Sanitization

PHP's filter extension provides input sanitization:

```

```

**Sanitization Filters Used:**

* **FILTER_SANITIZE_SPECIAL_CHARS**: Converts special characters to HTML entities
* **FILTER_SANITIZE_EMAIL**: Removes illegal characters from email addresses
* **FILTER_VALIDATE_EMAIL**: Validates email format against RFC 5321

**Validation Patterns:**

* **Role Validation**: Whitelist of allowed values (`admin`, `cajera`, `cocinero`, `cliente`, `mesera`)
* **Email Validation**: Two-step process (sanitize, then validate)
* **Required Fields**: Checked via `empty()` function

**Sources:** [crud/registro_INSERT.php L38-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L38-L59)

### SQL Injection Prevention

Prepared statements with parameter binding prevent SQL injection:

```

```

**Prepared Statement Advantages:**

* **SQL Injection Prevention**: User input never interpolated into SQL string
* **Type Safety**: Parameter types explicitly declared (`s`, `i`, `d`, `b`)
* **Performance**: Statement compiled once, executed multiple times
* **Automatic Escaping**: Database driver handles value escaping

**Parameter Type Specifications:**

* `s` - String type for VARCHAR, TEXT fields
* `i` - Integer type for INT fields
* `d` - Double type for DECIMAL fields
* `b` - Blob type for binary data (not used in current implementation)

**Sources:** [crud/registro_INSERT.php L38-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L38-L59)

---

## Technology Dependencies

### External Dependencies

The system relies on the following external technologies and standards:

| Dependency | Type | Purpose | Licensing |
| --- | --- | --- | --- |
| PHP | Runtime | Server-side execution | PHP License 3.01 |
| MariaDB | Database | Data storage | GPL v2 |
| MySQLi | Extension | Database driver | PHP License |
| Google Fonts (Poppins) | Web Font | Typography | SIL Open Font License |
| Font Awesome 6 Free | Icon Font | UI icons | Font Awesome Free License |
| Getty Images | Asset Source | Menu photography | Commercial license required |
| iStockphoto | Asset Source | Stock photography | License per image |

**Third-Party Assets:**

* All menu images sourced from Getty Images/iStockphoto
* Each image has unique AssetID for license tracking
* Data mining restrictions embedded in XMP metadata
* Commercial use requires valid license agreements

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [css/style.css L22](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L22-L22)

 [css/style.css L190-L196](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L190-L196)

### Browser Compatibility

CSS3 features used indicate modern browser requirements:

**Minimum Browser Versions:**

* Chrome/Edge: 88+ (CSS Grid, Custom Properties, Flexbox)
* Firefox: 85+ (Full CSS3 support)
* Safari: 14+ (Webkit prefix not needed)
* Mobile Safari: iOS 14+
* Chrome Mobile: Android 88+

**Progressive Enhancement:**

* Responsive design via media queries (all modern browsers)
* Flexbox layout (IE11 with prefixes, fully supported modern browsers)
* CSS Grid layout (not supported IE11, graceful degradation needed)
* CSS Custom Properties (not supported IE11, fallback colors needed)

**Sources:** [css/style.css L3-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L3-L18)

 [css/style.css L386-L460](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L386-L460)

---

## Technology Integration Map

This diagram shows how all technologies integrate in the complete stack:

```

```

**Sources:** [config/conexionDB.php L1-L18](https://github.com/axchisan/AmericanFood/blob/67fea4d3/config/conexionDB.php#L1-L18)

 [database/american_food L1-L22](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L1-L22)

 [css/style.css L1-L704](https://github.com/axchisan/AmericanFood/blob/67fea4d3/css/style.css#L1-L704)

 [crud/registro_INSERT.php L1-L59](https://github.com/axchisan/AmericanFood/blob/67fea4d3/crud/registro_INSERT.php#L1-L59)