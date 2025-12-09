# Create Crop Endpoint

> **Relevant source files**
> * [backennd/db_interaction/agregar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php)
> * [backennd/db_interaction/connection.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php)

## Purpose and Scope

This document provides a detailed technical specification of the `agregar_cultivo.php` endpoint, which handles HTTP POST requests to insert new crop records into the `cultivos` table. The endpoint accepts seven POST parameters describing a crop's characteristics and returns a text-based success or error message.

For information about retrieving existing crops, see [Read Crop Endpoints](/axchisan/CoopAgronet/2.3.2-read-crop-endpoints). For the overall Crop Management API architecture, see [Crop Management API](/axchisan/CoopAgronet/2.3-crop-management-api).

**Sources:** [backennd/db_interaction/agregar_cultivo.php L1-L27](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L1-L27)

---

## Request Specification

### HTTP Method and Routing

The endpoint accepts only `POST` requests, verified at [backennd/db_interaction/agregar_cultivo.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L7)

 Any non-POST request results in silent rejection with no response body.

| Attribute | Value |
| --- | --- |
| **HTTP Method** | POST |
| **Expected Content-Type** | application/x-www-form-urlencoded or multipart/form-data |
| **Authentication Required** | No (security vulnerability) |
| **Authorization Check** | None |

### Required POST Parameters

The endpoint expects exactly seven POST parameters, extracted at [backennd/db_interaction/agregar_cultivo.php L8-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L8-L14)

:

| Parameter | Type | Description | Example |
| --- | --- | --- | --- |
| `tipo` | string | Crop type/species name | "Tomate", "Maíz" |
| `fecha_siembra` | string | Planting date in YYYY-MM-DD format | "2024-01-15" |
| `cantidad` | numeric | Quantity planted | "100" |
| `dueno` | string | Owner/responsible farmer name | "Juan Pérez" |
| `edad` | numeric | Age in days or growth stage | "45" |
| `ubicacion` | string | Geographic location or plot identifier | "Sector Norte, Lote 3" |
| `notas` | string | Additional observations or notes | "Riego diario necesario" |

**No validation is performed** on these inputs before database insertion. Missing, empty, or malformed values are passed directly to the SQL query.

**Sources:** [backennd/db_interaction/agregar_cultivo.php L8-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L8-L14)

---

## Database Interaction Flow

### Connection Acquisition

```

```

The `$connection` variable is established at [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

 and made available to this script via `include` at [backennd/db_interaction/agregar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L2-L2)

 For details on connection configuration, see [Database Connection Layer](/axchisan/CoopAgronet/2.1-database-connection-layer).

**Sources:** [backennd/db_interaction/agregar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L2-L2)

 [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

### SQL Statement Construction

The INSERT statement is constructed at [backennd/db_interaction/agregar_cultivo.php L16-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L16-L17)

 using direct string interpolation:

```

```

This query maps directly to the `cultivos` table schema:

| POST Parameter | SQL Column | Data Type in DB |
| --- | --- | --- |
| `$_POST["tipo"]` | `tipo` | VARCHAR |
| `$_POST["fecha_siembra"]` | `fecha_siembra` | DATE |
| `$_POST["cantidad"]` | `cantidad` | INT |
| `$_POST["dueno"]` | `dueno` | VARCHAR |
| `$_POST["edad"]` | `edad` | INT |
| `$_POST["ubicacion"]` | `ubicacion` | VARCHAR |
| `$_POST["notas"]` | `notas` | TEXT |

The `id` column (primary key, AUTO_INCREMENT) is omitted from the INSERT, allowing the database to generate it automatically.

**Sources:** [backennd/db_interaction/agregar_cultivo.php L16-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L16-L17)

---

## Execution Flow Diagram

```

```

**Sources:** [backennd/db_interaction/agregar_cultivo.php L1-L27](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L1-L27)

---

## Response Format

### Success Response

When `mysqli_query()` returns `true` at [backennd/db_interaction/agregar_cultivo.php L19](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L19-L19)

 the endpoint outputs:

```
Cultivo agregado con éxito
```

**Content-Type:** `text/html` (default PHP output)
**HTTP Status Code:** 200 OK (implicit)

### Error Response

When the query fails, the response at [backennd/db_interaction/agregar_cultivo.php L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L22-L22)

 includes the MySQL error message:

```
Error al guardar el cultivo: [mysqli_error message]
```

Example error messages include:

* `Duplicate entry '...' for key 'PRIMARY'` (if manual ID provided)
* `Data too long for column 'tipo' at row 1`
* `Incorrect date value: '...' for column 'fecha_siembra'`

**Important:** Unlike other endpoints in the system (e.g., login.php, create_acount.php), this endpoint does **not** return JSON-encoded responses. The frontend must parse text output.

**Sources:** [backennd/db_interaction/agregar_cultivo.php L20-L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L20-L22)

---

## Code Structure Map

```

```

**Sources:** [backennd/db_interaction/agregar_cultivo.php L1-L27](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L1-L27)

---

## Integration with Frontend

The endpoint is called from `agregar_cultivo.js`, which submits the crop form using `FormData`:

```

```

The frontend module is documented in [Crop Creation and Listing](/axchisan/CoopAgronet/3.3.1-crop-creation-and-listing).

**Sources:** [backennd/db_interaction/agregar_cultivo.php L7-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L23)

---

## Security Vulnerabilities

### SQL Injection

The endpoint is **vulnerable to SQL injection** due to direct string interpolation at [backennd/db_interaction/agregar_cultivo.php L16-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L16-L17)

 An attacker can execute arbitrary SQL by injecting malicious values into any POST parameter.

**Example Attack Vector:**

```sql
POST /agregar_cultivo.php
Content-Type: application/x-www-form-urlencoded

tipo=Tomate'; DROP TABLE cultivos; --&fecha_siembra=2024-01-01&cantidad=100&dueno=Attacker&edad=0&ubicacion=Remote&notas=Payload
```

This would construct:

```

```

**Recommended Remediation:**

Use prepared statements with parameterized queries:

```

```

See [enviar_pregunta.php](/axchisan/CoopAgronet/2.4-support-question-system) for an example of proper prepared statement usage in this codebase.

### No Authentication or Authorization

The endpoint does not verify:

* User authentication status (no session check)
* User authorization to create crops
* Rate limiting to prevent abuse

Any client can send unlimited POST requests to create arbitrary crop records.

### Input Validation Gaps

No validation is performed on:

* Required field presence (empty values allowed)
* Data type correctness (`cantidad` and `edad` should be numeric)
* Date format validity for `fecha_siembra`
* String length limits
* Malicious content in text fields

For security recommendations applicable across all endpoints, see [Security Considerations](/axchisan/CoopAgronet/4-security-considerations).

**Sources:** [backennd/db_interaction/agregar_cultivo.php L8-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L8-L17)

---

## Error Handling

### MySQL Connection Errors

If `mysqli_connect()` fails in `connection.php`, the script terminates via `die()` at [backennd/db_interaction/connection.php L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L13-L13)

 before reaching this endpoint's logic. The JSON error response is never returned because the script exits.

### Query Execution Errors

When `mysqli_query()` returns `false`, the error message from `mysqli_error($connection)` is echoed directly to the response. This **exposes internal database details** to clients, including:

* Table structure information
* Column names and types
* SQL syntax (in some error types)
* Database engine version details

### No Exception Handling

The script uses no `try-catch` blocks. PHP errors are displayed due to [backennd/db_interaction/agregar_cultivo.php L3-L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L3-L4)

:

* `error_reporting(E_ALL)` - Report all errors
* `ini_set('display_errors', 1)` - Display errors in output

This is **unsafe for production** as it exposes system internals.

**Sources:** [backennd/db_interaction/agregar_cultivo.php L3-L22](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L3-L22)

 [backennd/db_interaction/connection.php L12-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L12-L14)

---

## Database Transaction Behavior

### Atomicity

Each invocation executes a **single INSERT statement** with no explicit transaction control. MySQL's default autocommit mode applies:

* If the INSERT succeeds, the change is immediately committed
* If the INSERT fails, no partial data is written
* No rollback mechanism is available

### Concurrency

The endpoint has no locking mechanism. Concurrent POST requests can result in:

* Multiple crops with identical data (no UNIQUE constraints on non-PK columns)
* Race conditions if the same `dueno` creates multiple crops simultaneously

### Connection Lifecycle

The connection is closed at [backennd/db_interaction/agregar_cultivo.php L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L25-L25)

 regardless of success or failure. Connection pooling is not implemented.

**Sources:** [backennd/db_interaction/agregar_cultivo.php L19-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L19-L25)

---

## Performance Characteristics

| Aspect | Behavior |
| --- | --- |
| **Query Complexity** | Single INSERT, O(1) time complexity |
| **Indexes Used** | PRIMARY KEY index on `id` (auto-increment) |
| **Network Roundtrips** | 1 (single query execution) |
| **Connection Overhead** | Full TCP handshake + auth per request (no pooling) |
| **Response Size** | ~26 bytes (success message) or variable (error) |

### Bottlenecks

1. **Connection establishment** at [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)  adds ~5-20ms per request
2. **Disk I/O** for INSERT operation depends on table size and storage engine (InnoDB)
3. **Table locks** if MyISAM storage engine is used (not specified in schema)

**Sources:** [backennd/db_interaction/agregar_cultivo.php L19](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L19-L19)

 [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

---

## Data Type Mapping

```

```

**Type Conversion Notes:**

* MySQL performs implicit conversion from string literals to DATE (column `fecha_siembra`) and INT (columns `cantidad`, `edad`)
* Invalid date strings cause query failure
* Non-numeric strings in INT columns are converted to 0
* No type validation occurs in PHP layer

**Sources:** [backennd/db_interaction/agregar_cultivo.php L8-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L8-L17)

---

## Testing Considerations

### Manual Testing

**Valid Request:**

```

```

Expected response: `Cultivo agregado con éxito`

**Invalid Date Format:**

```

```

Expected response: `Error al guardar el cultivo: Incorrect date value: '15-01-2024' for column 'fecha_siembra' at row 1`

### Edge Cases

| Test Case | Input | Expected Behavior | Actual Behavior |
| --- | --- | --- | --- |
| Missing required field | Omit `tipo` parameter | Validation error | Empty string inserted |
| SQL injection in `notas` | `notas="'; DROP TABLE--"` | Sanitized/rejected | SQL injection executed |
| Non-numeric `cantidad` | `cantidad="abc"` | Type error | MySQL converts to 0 |
| GET request | `GET /agregar_cultivo.php` | Method not allowed error | Silent rejection, empty response |
| Duplicate ID (manual) | `id=1&tipo=...` | Duplicate key error | Depends on auto-increment handling |

**Sources:** [backennd/db_interaction/agregar_cultivo.php L7-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L7-L23)

---

## Related Endpoints

| Endpoint | Purpose | Documentation |
| --- | --- | --- |
| `obtener_cultivos.php` | Retrieve all crops | [Read Crop Endpoints](/axchisan/CoopAgronet/2.3.2-read-crop-endpoints) |
| `obtener_cultivo.php` | Retrieve single crop by ID | [Read Crop Endpoints](/axchisan/CoopAgronet/2.3.2-read-crop-endpoints) |
| `editar_cultivo.php` | Update existing crop | [Update Crop Endpoint](/axchisan/CoopAgronet/2.3.3-update-crop-endpoint) |
| `eliminar_cultivo.php` | Delete crop by ID | [Delete Crop Endpoint](/axchisan/CoopAgronet/2.3.4-delete-crop-endpoint) |

For the complete CRUD system architecture, see [Crop Management API](/axchisan/CoopAgronet/2.3-crop-management-api).

**Sources:** [backennd/db_interaction/agregar_cultivo.php L1-L27](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/agregar_cultivo.php#L1-L27)