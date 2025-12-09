# Data Model and Schema

> **Relevant source files**
> * [ddbb/DBConexion.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php)
> * [models/abogadosModel.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php)
> * [views/abogadosView.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php)
> * [views/home.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php)

This document describes the database schema for the Abogado application, including the structure of the `tablaRelacional` database, the `abogado` table, field definitions, and data types. It explains how the database structure maps to the model layer and the data access patterns used throughout the application.

For information about how this data is accessed and manipulated through the application layers, see [Backend Components](/GroveLive/abogado/4-backend-components). For details on the database connection mechanism, see [Database Connection Management](/GroveLive/abogado/4.4-database-connection-management).

---

## Database Overview

The application uses a MySQL database named `tablaRelacional` that stores all lawyer information in a single table structure. The database connection is established through the `DBConexion` class with the following configuration:

| Configuration | Value |
| --- | --- |
| **Database Name** | `tablaRelacional` |
| **Host** | `localhost` |
| **User** | `root` |
| **Password** | (empty string) |
| **Character Encoding** | UTF-8 |

The database is accessed exclusively through the `Abogado` model class using the mysqli PHP extension. All queries are executed against a single table named `abogado`.

**Sources:** [ddbb/DBConexion.php L7-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L12)

---

## Schema Structure

The database implements a flat, single-table architecture with no foreign key relationships or table joins. This design reflects a simple information browsing system focused on lawyer profile display.

**Database Schema Diagram**

```

```

**Sources:** [models/abogadosModel.php L7-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L7-L24)

 [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

---

## Table: abogado

The `abogado` table is the sole data storage entity in the system. It stores comprehensive information about lawyers including their personal details, contact information, and professional credentials.

### Table Characteristics

| Characteristic | Details |
| --- | --- |
| **Table Name** | `abogado` |
| **Primary Key** | `id_abogado` |
| **Access Pattern** | Read-only (SELECT operations only) |
| **Record Retrieval** | Full table scan or single record by ID |

### SQL Access Patterns

The table supports two query patterns implemented in the `Abogado` model:

**Pattern 1: Full Table Retrieval**

```

```

Used by: `Abogado::getAllAbogados()` - [models/abogadosModel.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L7-L7)

**Pattern 2: Single Record Retrieval by ID**

```

```

Used by: `Abogado::getAbogadoById($id)` - [models/abogadosModel.php L19](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L19-L19)

**Sources:** [models/abogadosModel.php L5-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L25)

---

## Field Definitions

The following table documents each field in the `abogado` table based on usage patterns observed in the codebase. Data types are inferred from SQL parameter binding and PHP variable handling.

| Field Name | Data Type | Constraints | Description | Usage in Views |
| --- | --- | --- | --- | --- |
| `id_abogado` | `INT` | PRIMARY KEY, NOT NULL | Unique identifier for each lawyer record | URL parameter, navigation links |
| `nombre` | `VARCHAR` | NOT NULL | Full name of the lawyer | Displayed in listing and profile views |
| `especialidad` | `VARCHAR` | - | Legal practice area or specialty | Displayed in listing and profile views |
| `telefono` | `VARCHAR` | - | Contact phone number | Displayed in profile view only |
| `nacionalidad` | `VARCHAR` | - | Lawyer's nationality or country of origin | Displayed in profile view only |
| `estudios` | `VARCHAR` | - | Educational background and credentials | Displayed in profile view only |
| `correo` | `VARCHAR` | - | Email address for contact | Displayed in profile view only |

### Field Usage Matrix

```

```

**Sources:** [views/home.php L20-L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L20-L22)

 [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

---

## Data Type Analysis

### Primary Key: id_abogado

The `id_abogado` field serves as the primary key and is treated as an integer throughout the codebase:

* **Parameter Binding:** Uses `"i"` type specifier in prepared statement - [models/abogadosModel.php L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L21-L21)
* **URL Transmission:** Passed via GET parameter without type casting - [views/home.php L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L22-L22)
* **Method Signature:** Type-hinted as integer in `getAbogadoById($id)` - [models/abogadosModel.php L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L17-L17)

### String Fields

All other fields (`nombre`, `especialidad`, `telefono`, `nacionalidad`, `estudios`, `correo`) are treated as string values:

* **Fetching:** Retrieved using `fetch_assoc()` which returns strings - [models/abogadosModel.php L11-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L11-L24)
* **Output Sanitization:** All fields processed through `htmlspecialchars()` - [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)
* **Storage:** Likely `VARCHAR` type based on textual content and PHP string handling

**Sources:** [models/abogadosModel.php L17-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L17-L24)

 [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

---

## Model-to-Schema Mapping

The following diagram illustrates how the database schema maps to PHP code entities in the model layer:

```

```

### Data Structure Format

**getAllAbogados() Return Format:**

```

```

**getAbogadoById() Return Format:**

```

```

**Sources:** [models/abogadosModel.php L5-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L25)

---

## Schema Access Patterns

### Read-Only Operations

The application implements only `SELECT` operations. No `INSERT`, `UPDATE`, or `DELETE` operations are present in the codebase, indicating a read-only browsing system.

| Operation | SQL Statement | Prepared Statement | Security Measure |
| --- | --- | --- | --- |
| List All | `SELECT * FROM abogado` | No | None (no user input) |
| Get By ID | `SELECT * FROM abogado WHERE id_abogado = ?` | Yes | Parameter binding with type "i" |

### Query Execution Flow

```

```

**Sources:** [models/abogadosModel.php L5-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L25)

 [ddbb/DBConexion.php L5-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L16)

---

## Schema Constraints and Considerations

### Inferred Constraints

While the actual DDL (Data Definition Language) is not present in the codebase, the following constraints can be inferred from usage patterns:

**Primary Key Constraint:**

* `id_abogado` must be unique and not null
* Used as the sole identifier for record retrieval

**NOT NULL Constraints (Inferred):**

* `nombre` and `especialidad` - Always displayed without null checks in the listing view
* Other fields - May allow NULL as they are only displayed in the detail view

### Missing Schema Elements

The following schema elements are not visible in the codebase:

| Element | Status | Impact |
| --- | --- | --- |
| **CREATE TABLE statement** | Not present | Data types must be inferred |
| **Indexes** | Unknown | Performance optimization unclear |
| **Foreign keys** | None | Single-table design |
| **Constraints** | Unknown | Data validation at schema level unclear |
| **Default values** | Unknown | Field initialization behavior unknown |

### Character Encoding

The connection explicitly sets UTF-8 encoding to ensure proper handling of international characters in lawyer names and other text fields:

```

```

This is critical for supporting names and text in Spanish and other languages with special characters.

**Sources:** [ddbb/DBConexion.php L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L12-L12)

---

## Data Access Security

### SQL Injection Prevention

The schema is accessed using two different security approaches:

**getAllAbogados() - No Parameterization Required:**

* Uses a static query with no user input
* No SQL injection risk as the query contains no variables

**getAbogadoById() - Prepared Statements:**

* Uses parameterized query with `?` placeholder - [models/abogadosModel.php L19](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L19-L19)
* Binds parameter as integer type `"i"` - [models/abogadosModel.php L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L21-L21)
* Prevents SQL injection on the `id_abogado` field

### Output Sanitization

All field values are sanitized at the view layer using `htmlspecialchars()` before display, preventing XSS attacks. This occurs independently of the schema layer but is essential for secure data presentation.

For detailed security analysis, see [Security Considerations](/GroveLive/abogado/7-security-considerations).

**Sources:** [models/abogadosModel.php L17-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L17-L24)

 [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

---

## Schema Evolution Considerations

The current schema represents a simple, single-table design suitable for a basic lawyer directory application. Future enhancements might require:

* **Additional Tables:** Practice areas, client reviews, case history
* **Relationships:** Many-to-many relationship for specializations, one-to-many for case records
* **Indexes:** On `especialidad` for filtered searches, on `nombre` for alphabetical sorting
* **Full-Text Search:** On multiple fields for keyword searching
* **Audit Fields:** `created_at`, `updated_at`, `deleted_at` for record tracking

However, such changes would require modifications to the model layer documented in [Models Layer](/GroveLive/abogado/4.3-models-layer) and potentially the controller layer documented in [Controllers Layer](/GroveLive/abogado/4.2-controllers-layer).

**Sources:** [models/abogadosModel.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L1-L27)