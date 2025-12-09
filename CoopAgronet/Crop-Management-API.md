# Crop Management API

> **Relevant source files**
> * [backennd/db_interaction/agregar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php)
> * [backennd/db_interaction/editar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php)
> * [backennd/db_interaction/eliminar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php)
> * [backennd/db_interaction/obtener_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php)
> * [backennd/db_interaction/obtener_cultivos.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php)

## Purpose and Scope

The Crop Management API consists of five PHP endpoints that provide complete CRUD (Create, Read, Update, Delete) functionality for managing crop records in the `cultivos` database table. These endpoints follow a RESTful-style design pattern where each operation is handled by a dedicated PHP file that accepts specific HTTP methods and returns data as either JSON or plain text.

This document provides an architectural overview of the API layer. For detailed documentation of individual endpoints, see:

* [Create Crop Endpoint](/axchisan/CoopAgronet/2.3.1-create-crop-endpoint) - `agregar_cultivo.php`
* [Read Crop Endpoints](/axchisan/CoopAgronet/2.3.2-read-crop-endpoints) - `obtener_cultivos.php` and `obtener_cultivo.php`
* [Update Crop Endpoint](/axchisan/CoopAgronet/2.3.3-update-crop-endpoint) - `editar_cultivo.php`
* [Delete Crop Endpoint](/axchisan/CoopAgronet/2.3.4-delete-crop-endpoint) - `eliminar_cultivo.php`

For database connection details, see [Database Connection Layer](/axchisan/CoopAgronet/2.1-database-connection-layer). For security analysis of these endpoints, see [Security Considerations](/axchisan/CoopAgronet/4-security-considerations).

Sources: All files in `backennd/db_interaction/`

## API Endpoints Overview

The Crop Management API exposes five endpoints, each responsible for a single operation on the `cultivos` table:

| Endpoint File | HTTP Method | Operation | Input | Output Format |
| --- | --- | --- | --- | --- |
| `agregar_cultivo.php` | POST | Create new crop | FormData (7 fields) | Plain text |
| `obtener_cultivos.php` | GET | Retrieve all crops | None | JSON array |
| `obtener_cultivo.php` | GET | Retrieve single crop | Query param `id` | JSON object |
| `editar_cultivo.php` | POST | Update existing crop | FormData (8 fields: id + 7 data) | Plain text |
| `eliminar_cultivo.php` | DELETE | Delete crop | Query param `id` | Plain text |

All endpoints share a common dependency on `connection.php` to obtain the `$connection` mysqli object for database operations.

Sources: [backennd/db_interaction/agregar_cultivo.php L1-L27](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L1-L27)

 [backennd/db_interaction/editar_cultivo.php L1-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L1-L26)

 [backennd/db_interaction/eliminar_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L1-L17)

 [backennd/db_interaction/obtener_cultivo.php L1-L18](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L18)

 [backennd/db_interaction/obtener_cultivos.php L1-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L16)

## API Architecture

**Diagram: Crop Management API Structure**

```

```

Sources: [backennd/db_interaction/agregar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L2-L2)

 [backennd/db_interaction/editar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L2-L2)

 [backennd/db_interaction/eliminar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L2-L2)

 [backennd/db_interaction/obtener_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L2-L2)

 [backennd/db_interaction/obtener_cultivos.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L2-L2)

## Database Schema

The `cultivos` table contains the following fields that all five endpoints operate on:

| Column | Type | Description |
| --- | --- | --- |
| `id` | INT (Primary Key, AUTO_INCREMENT) | Unique identifier for each crop record |
| `tipo` | VARCHAR | Type/species of crop (e.g., "Maíz", "Trigo") |
| `fecha_siembra` | DATE | Planting date |
| `cantidad` | INT | Quantity/amount planted |
| `dueno` | VARCHAR | Owner name |
| `edad` | INT | Age of crop in days |
| `ubicacion` | VARCHAR | Location/plot identifier |
| `notas` | TEXT | Additional notes or comments |

The `id` field is automatically generated on insertion and is used as the identifier for update, delete, and single-read operations.

Sources: [backennd/db_interaction/agregar_cultivo.php L8-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L8-L14)

 [backennd/db_interaction/editar_cultivo.php L5-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L5-L12)

 [backennd/db_interaction/obtener_cultivo.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L6-L6)

 [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4)

## Request and Response Patterns

**Diagram: Data Flow Through Crop Management Endpoints**

```

```

Sources: [backennd/db_interaction/agregar_cultivo.php L7-L26](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L26)

 [backennd/db_interaction/obtener_cultivos.php L4-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L14)

 [backennd/db_interaction/obtener_cultivo.php L4-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L4-L16)

 [backennd/db_interaction/editar_cultivo.php L4-L24](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L4-L24)

 [backennd/db_interaction/eliminar_cultivo.php L4-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L4-L15)

## Common Implementation Patterns

### Request Method Validation

All endpoints validate the HTTP request method before processing:

* **Create endpoint** checks `$_SERVER["REQUEST_METHOD"] == "POST"` at [backennd/db_interaction/agregar_cultivo.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L7)
* **Update endpoint** checks `$_SERVER["REQUEST_METHOD"] == "POST"` at [backennd/db_interaction/editar_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L4-L4)
* **Delete endpoint** checks `$_SERVER["REQUEST_METHOD"] == "DELETE"` at [backennd/db_interaction/eliminar_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L4-L4)
* **Read one endpoint** checks `isset($_GET["id"])` at [backennd/db_interaction/obtener_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L4-L4)
* **Read all endpoint** has no method validation (executes on any request) at [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4)

### Database Connection Pattern

Every endpoint follows an identical connection lifecycle:

1. Include `connection.php` via `include` statement at the top of the file
2. Use the provided `$connection` mysqli object for queries
3. Execute SQL operation with `mysqli_query($connection, $sql)`
4. Close connection with `mysqli_close($connection)` before terminating

Sources: [backennd/db_interaction/agregar_cultivo.php L2-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L2-L25)

 [backennd/db_interaction/editar_cultivo.php L2-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L2-L23)

 [backennd/db_interaction/eliminar_cultivo.php L2-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L2-L14)

### Response Format Inconsistency

The API has inconsistent response formats:

| Endpoint Type | Response Format | Example |
| --- | --- | --- |
| Create | Plain text (Spanish) | `"Cultivo agregado con éxito"` |
| Read all | JSON array | `[{id: 1, tipo: "Maíz", ...}, ...]` |
| Read one | JSON object | `{id: 1, tipo: "Maíz", ...}` |
| Update | Plain text (Spanish) | `"Cultivo actualizado con éxito"` |
| Delete | Plain text (Spanish) | `"Cultivo eliminado correctamente"` |

This inconsistency means frontend code must handle both JSON parsing and plain text responses depending on the operation being performed.

Sources: [backennd/db_interaction/agregar_cultivo.php L20-L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L20-L22)

 [backennd/db_interaction/obtener_cultivos.php L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L12-L12)

 [backennd/db_interaction/obtener_cultivo.php L10-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L10-L12)

 [backennd/db_interaction/editar_cultivo.php L18-L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L18-L20)

 [backennd/db_interaction/eliminar_cultivo.php L9-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L9-L11)

### Error Handling

All endpoints use `mysqli_error($connection)` to report database errors to the client:

```

```

Errors are returned as plain text strings directly to the client, which exposes internal database error messages. No error codes or structured error objects are provided.

Sources: [backennd/db_interaction/agregar_cultivo.php L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L22-L22)

 [backennd/db_interaction/editar_cultivo.php L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L20-L20)

 [backennd/db_interaction/eliminar_cultivo.php L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L11-L11)

## SQL Query Construction

### Direct String Interpolation

All five endpoints construct SQL queries using direct string interpolation of user input without prepared statements:

```

```

This pattern creates **SQL injection vulnerabilities** because user-controlled data from `$_POST` and `$_GET` is directly inserted into SQL statements without escaping or parameterization. See [Security Considerations](/axchisan/CoopAgronet/4-security-considerations) for detailed analysis.

Sources: [backennd/db_interaction/agregar_cultivo.php L16-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L16-L17)

 [backennd/db_interaction/editar_cultivo.php L14-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L14-L15)

 [backennd/db_interaction/eliminar_cultivo.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L7-L7)

 [backennd/db_interaction/obtener_cultivo.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L6-L6)

### No Pagination

The `obtener_cultivos.php` endpoint retrieves all crop records with `SELECT * FROM cultivos` and returns the entire dataset in a single JSON response at [backennd/db_interaction/obtener_cultivos.php L4-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L12)

 As the number of crops grows, this approach will:

* Increase memory usage on the PHP server
* Increase network bandwidth consumption
* Slow down page load times on the frontend

No `LIMIT`, `OFFSET`, or pagination parameters are supported.

Sources: [backennd/db_interaction/obtener_cultivos.php L4-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L12)

## Input Validation

None of the endpoints perform explicit input validation before executing SQL queries:

* No checks for required fields (empty string validation)
* No data type validation (e.g., ensuring `cantidad` is numeric)
* No format validation (e.g., ensuring `fecha_siembra` is a valid date)
* No length restrictions on string fields
* No sanitization of special characters

The only validation is implicit through SQL execution - invalid data types may cause SQL errors which are then reported to the client.

Sources: [backennd/db_interaction/agregar_cultivo.php L8-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L8-L17)

 [backennd/db_interaction/editar_cultivo.php L5-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L5-L15)

## Authorization Model

**No authorization checks are implemented.** The API endpoints do not:

* Verify user authentication before allowing operations
* Check resource ownership before updates/deletes
* Restrict access based on user roles or permissions

Any client that can reach these endpoints can:

* Read all crop data in the database
* Modify any crop record regardless of the `dueno` field
* Delete any crop record

This means a user can modify or delete crops owned by other users. For comparison, see [User Authentication System](/axchisan/CoopAgronet/2.2-user-authentication-system) which demonstrates proper authentication patterns used elsewhere in the application.

Sources: [backennd/db_interaction/eliminar_cultivo.php L4-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L4-L15)

 [backennd/db_interaction/editar_cultivo.php L4-L24](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L4-L24)

## Error Reporting Configuration

The `agregar_cultivo.php` endpoint explicitly enables error reporting with:

```

```

at [backennd/db_interaction/agregar_cultivo.php L3-L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L3-L4)

 This configuration causes PHP errors to be displayed directly in the HTTP response, which is useful for development but should be disabled in production as it exposes internal implementation details and file paths.

The other four endpoints do not have these directives and rely on the PHP installation's default error reporting settings.

Sources: [backennd/db_interaction/agregar_cultivo.php L3-L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L3-L4)