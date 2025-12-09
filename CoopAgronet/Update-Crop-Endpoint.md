# Update Crop Endpoint

> **Relevant source files**
> * [backennd/db_interaction/connection.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php)
> * [backennd/db_interaction/editar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php)

## Purpose and Scope

The Update Crop Endpoint (`editar_cultivo.php`) processes HTTP POST requests to modify existing crop records in the `cultivos` table. This endpoint accepts an `id` parameter identifying the target record and seven data fields to update. It is one of five CRUD endpoints comprising the Crop Management API (see [Crop Management API](/axchisan/CoopAgronet/2.3-crop-management-api) for overview).

For creating new crops, see [Create Crop Endpoint](/axchisan/CoopAgronet/2.3.1-create-crop-endpoint). For retrieving crop data before editing, see [Read Crop Endpoints](/axchisan/CoopAgronet/2.3.2-read-crop-endpoints).

**Sources:** [backennd/db_interaction/editar_cultivo.php L1-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L1-L25)

---

## Endpoint Specification

| Attribute | Value |
| --- | --- |
| **File Path** | `backennd/db_interaction/editar_cultivo.php` |
| **HTTP Method** | POST |
| **Content Type** | `application/x-www-form-urlencoded` or `multipart/form-data` |
| **Authentication** | None (no session or authorization checks) |
| **Response Format** | Plain text (not JSON) |

### Request Parameters

The endpoint expects eight POST parameters:

| Parameter | Type | Description | Database Column |
| --- | --- | --- | --- |
| `id` | Integer | Primary key of crop to update | `id` |
| `tipo` | String | Crop type/variety | `tipo` |
| `fecha_siembra` | Date | Planting date (YYYY-MM-DD) | `fecha_siembra` |
| `cantidad` | Integer | Quantity/amount | `cantidad` |
| `dueno` | String | Owner name | `dueno` |
| `edad` | Integer | Age in days | `edad` |
| `ubicacion` | String | Location/plot identifier | `ubicacion` |
| `notas` | Text | Additional notes | `notas` |

**Sources:** [backennd/db_interaction/editar_cultivo.php L5-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L5-L12)

---

## Request Processing Flow

```

```

**Diagram: Update Crop Request-Response Sequence**

This diagram shows the complete lifecycle of an update request from client to database and back.

**Sources:** [backennd/db_interaction/editar_cultivo.php L1-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L1-L25)

 [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

---

## Database Connection Integration

The endpoint uses the shared connection pattern through `connection.php`:

```

```

**Diagram: Database Connection Dependency Chain**

The `include` statement on line 2 pulls in the `$connection` variable established by `connection.php`. This mysqli object is used for query execution (line 17) and explicitly closed after the operation (line 23).

**Sources:** [backennd/db_interaction/editar_cultivo.php L2-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L2-L23)

 [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

---

## SQL Query Construction

### UPDATE Statement Pattern

The SQL query is constructed via direct string interpolation:

```

```

This pattern is implemented in [backennd/db_interaction/editar_cultivo.php L14-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L14-L15)

 as a single-line string with embedded PHP variables.

### Field Mapping

```

```

**Diagram: Parameter Flow from HTTP Request to Database Columns**

Each POST parameter is extracted into a local PHP variable (lines 5-12), then interpolated directly into the SQL UPDATE string without sanitization.

**Sources:** [backennd/db_interaction/editar_cultivo.php L5-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L5-L15)

---

## Response Format

Unlike other endpoints in the system that return JSON, `editar_cultivo.php` returns plain text responses.

### Success Response

```
Cultivo actualizado con éxito
```

Returned when `mysqli_query()` executes successfully ([backennd/db_interaction/editar_cultivo.php L18](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L18-L18)

).

### Error Response

```
Error al actualizar el cultivo: [MySQL error message]
```

Returned when `mysqli_query()` fails, concatenated with `mysqli_error($connection)` output ([backennd/db_interaction/editar_cultivo.php L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L20-L20)

).

**Response Characteristics:**

| Aspect | Value |
| --- | --- |
| Content-Type | `text/html` (PHP default) |
| Status Code | Always 200 OK (no HTTP error codes) |
| Format | Plain Spanish text string |
| Structured Data | None (not JSON) |

**Sources:** [backennd/db_interaction/editar_cultivo.php L18-L20](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L18-L20)

---

## Query Execution and Error Handling

The endpoint uses `mysqli_query()` with basic conditional error handling:

```

```

**Diagram: Execution Flow and Error Handling Logic**

The connection is closed regardless of query success/failure. No exceptions are thrown; all error communication happens through plain text `echo` statements.

**Sources:** [backennd/db_interaction/editar_cultivo.php L17-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L17-L23)

---

## Security Vulnerabilities

### SQL Injection Risk

The endpoint is **critically vulnerable to SQL injection** due to direct variable interpolation in the SQL string without prepared statements or escaping.

**Vulnerable Code Pattern:**

```

```

**Attack Vector Example:**

A malicious client could send:

```sql
POST /editar_cultivo.php
id=1&tipo=Maiz&notas=test' WHERE 1=1; DROP TABLE cultivos; --
```

This would execute destructive SQL due to the lack of input sanitization.

### Missing Authorization

The endpoint performs no checks to verify:

* User authentication (no session validation)
* Ownership of the crop record (any user can modify any crop)
* User permissions (no role-based access control)

**Comparison with Secure Endpoints:**

The authentication endpoints (`login.php`, `create_acount.php`) use prepared statements with parameterized queries. The Update Crop Endpoint lacks this protection.

**Sources:** [backennd/db_interaction/editar_cultivo.php L14-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L14-L15)

---

## Frontend Integration

The endpoint is called by `acciones_cultivos.js` in the crop update workflow:

```

```

**Diagram: Frontend-Backend Update Flow**

The frontend calls this endpoint after:

1. Fetching crop details to populate the form
2. User modifying the form fields
3. Form submission triggering `updateCultivo()` function

After receiving the response, the page performs a full reload to reflect changes.

**Sources:** [backennd/db_interaction/editar_cultivo.php L1-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L1-L25)

---

## Technical Limitations

| Limitation | Description | Impact |
| --- | --- | --- |
| **No Validation** | No server-side validation of field types, lengths, or formats | Invalid data can be inserted into database |
| **No Prepared Statements** | Direct SQL string interpolation | SQL injection vulnerability |
| **Text Response** | Returns plain text instead of JSON | Frontend cannot programmatically parse success/failure |
| **No Status Codes** | Always returns HTTP 200 | Frontend cannot use standard HTTP error handling |
| **No Logging** | No audit trail of who changed what | Cannot track modifications for compliance |
| **Synchronous Processing** | Blocking query execution | Cannot handle concurrent updates efficiently |
| **No Transaction Support** | Single UPDATE query without BEGIN/COMMIT | Partial failures in complex updates cannot be rolled back |

**Sources:** [backennd/db_interaction/editar_cultivo.php L1-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L1-L25)

---

## Connection Lifecycle

The endpoint explicitly manages the database connection lifecycle:

1. **Acquisition**: [backennd/db_interaction/editar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L2-L2)  includes `connection.php`, which establishes the connection
2. **Usage**: [backennd/db_interaction/editar_cultivo.php L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L17-L17)  executes the UPDATE query
3. **Release**: [backennd/db_interaction/editar_cultivo.php L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L23-L23)  calls `mysqli_close($connection)`

This per-request connection pattern means:

* No connection pooling
* New TCP handshake for each request
* Connection overhead on every update operation

**Sources:** [backennd/db_interaction/editar_cultivo.php L2-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L2-L23)

 [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

---

## Relationship to Other Endpoints

```

```

**Diagram: Update Endpoint in Complete CRUD Workflow**

The Update Endpoint is preceded by Read operations (to fetch current data) and followed by Read operations (to display updated data after reload).

**Sources:** [backennd/db_interaction/editar_cultivo.php L1-L25](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/editar_cultivo.php#L1-L25)