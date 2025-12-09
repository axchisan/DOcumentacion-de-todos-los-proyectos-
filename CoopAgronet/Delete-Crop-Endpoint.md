# Delete Crop Endpoint

> **Relevant source files**
> * [backennd/db_interaction/connection.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php)
> * [backennd/db_interaction/eliminar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php)

## Purpose and Scope

This document describes `eliminar_cultivo.php`, the backend endpoint responsible for deleting crop records from the `cultivos` table. The endpoint processes DELETE HTTP requests, accepts a crop `id` via query parameters, executes a SQL DELETE statement, and returns a plain text response indicating success or failure.

For an overview of all crop management endpoints, see [Crop Management API](/axchisan/CoopAgronet/2.3-crop-management-api). For information about the database connection mechanism, see [Database Connection Layer](/axchisan/CoopAgronet/2.1-database-connection-layer). For frontend integration details, see [Crop Edit and Delete Actions](/axchisan/CoopAgronet/3.3.2-crop-edit-and-delete-actions).

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L1-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L1-L16)

---

## Endpoint Configuration

The delete endpoint is implemented as a standalone PHP script located at [backennd/db_interaction/eliminar_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php)

| Property | Value |
| --- | --- |
| **File Path** | `backennd/db_interaction/eliminar_cultivo.php` |
| **HTTP Method** | DELETE |
| **URL Pattern** | `/backennd/db_interaction/eliminar_cultivo.php?id={crop_id}` |
| **Content Type** | Plain text |
| **Authentication** | None (no session or authorization checks) |
| **Input Validation** | None |

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L1-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L1-L16)

---

## Request Flow Architecture

```

```

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L4-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L4-L15)

 [backennd/db_interaction/connection.php L1-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L1-L14)

---

## Request Method Validation

The endpoint validates that the incoming request uses the DELETE HTTP method via the `$_SERVER["REQUEST_METHOD"]` superglobal:

[backennd/db_interaction/eliminar_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L4-L4)

```

```

If the request method is not DELETE, the script terminates silently without producing any output or error response. This behavior differs from other endpoints like `agregar_cultivo.php` and `editar_cultivo.php` which respond to POST requests.

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L4-L4)

---

## Input Parameter Extraction

The crop ID is extracted directly from the query string using the `$_GET` superglobal:

[backennd/db_interaction/eliminar_cultivo.php L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L5-L5)

```

```

**Key Characteristics:**

* No validation is performed to verify that `id` is present
* No type checking to ensure `id` is numeric
* No sanitization or escaping is applied
* If `id` is missing, `$_GET["id"]` evaluates to `null`, which becomes an empty string in the SQL query

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L5-L5)

---

## Database Connection

The endpoint includes the shared database connection factory:

[backennd/db_interaction/eliminar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L2-L2)

```

```

This provides the `$connection` mysqli object configured with:

* Host: `localhost:3306`
* User: `root`
* Password: (empty string)
* Database: `CoopAgroNet`

For complete connection details, see [Database Connection Layer](/axchisan/CoopAgronet/2.1-database-connection-layer).

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L2-L2)

 [backennd/db_interaction/connection.php L5-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L10)

---

## SQL Delete Operation

```

```

The DELETE query is constructed using direct string interpolation:

[backennd/db_interaction/eliminar_cultivo.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L7-L7)

```

```

**Example Query:**
If `$_GET["id"]` is `"5"`, the resulting SQL is:

```

```

The query is executed using `mysqli_query()`:

[backennd/db_interaction/eliminar_cultivo.php L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L8-L8)

```

```

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L7-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L7-L12)

---

## Response Format

Unlike other endpoints in the Crop Management API that return JSON responses, `eliminar_cultivo.php` returns plain text messages:

| Condition | Response | HTTP Status |
| --- | --- | --- |
| **Successful Deletion** | `"Cultivo eliminado correctamente"` | 200 OK |
| **Database Error** | `"Error al eliminar el cultivo: {mysqli_error}"` | 200 OK |
| **Method Not DELETE** | (empty response) | 200 OK |

[backennd/db_interaction/eliminar_cultivo.php L9-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L9-L11)

**Success Response:**

```

```

**Error Response:**

```

```

**Inconsistency Note:** Other crop endpoints (see [Create Crop Endpoint](/axchisan/CoopAgronet/2.3.1-create-crop-endpoint), [Update Crop Endpoint](/axchisan/CoopAgronet/2.3.3-update-crop-endpoint)) return JSON-encoded responses with `success` and `message` fields. This endpoint uses a simpler text-based format, creating inconsistency in the API contract.

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L9-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L9-L11)

---

## Connection Cleanup

The endpoint explicitly closes the database connection after query execution:

[backennd/db_interaction/eliminar_cultivo.php L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L14-L14)

```

```

This cleanup occurs regardless of whether the deletion succeeded or failed, as it follows both the success and error response paths.

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L14-L14)

---

## Frontend Integration

```

```

The `deleteCultivo()` function in `acciones_cultivos.js` invokes this endpoint:

1. **User Action:** Click delete button in crop table row
2. **Confirmation:** Browser `confirm()` dialog: "¿Estás seguro de que deseas eliminar este cultivo?"
3. **HTTP Request:** DELETE request to `eliminar_cultivo.php?id={cropId}`
4. **Response Handling:** Text response shown in `alert()` dialog
5. **Page Reload:** `location.reload()` refreshes the crop list

For complete frontend integration details, see [Crop Edit and Delete Actions](/axchisan/CoopAgronet/3.3.2-crop-edit-and-delete-actions).

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L1-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L1-L16)

---

## Security Vulnerabilities

### SQL Injection Risk

**Critical Issue:** The endpoint is vulnerable to SQL injection due to direct string interpolation of the `$id` parameter:

[backennd/db_interaction/eliminar_cultivo.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L7-L7)

```

```

**Attack Vector Example:**

```sql
DELETE /eliminar_cultivo.php?id=1 OR 1=1
```

This would construct the SQL:

```

```

This query would delete **all records** from the `cultivos` table, not just the record with `id=1`.

**Remediation:** Use prepared statements with parameter binding:

```

```

### Missing Authorization

The endpoint does not verify:

* Whether the user is authenticated (no session check)
* Whether the user owns the crop being deleted
* Whether the user has permission to delete crops

Any client can delete any crop record by sending a properly formatted DELETE request with any valid crop ID.

### Missing Input Validation

The endpoint does not validate:

* That `$_GET["id"]` is present (could be `null`)
* That `id` is numeric
* That `id` is positive
* That the crop with that `id` exists before attempting deletion

### HTTP Response Status Codes

All responses return HTTP 200 OK, even errors. The client must parse the text response to determine success or failure. Best practice would be to return:

* `200 OK` for successful deletion
* `404 Not Found` if crop ID doesn't exist
* `400 Bad Request` for invalid input
* `401 Unauthorized` if user is not authenticated
* `403 Forbidden` if user lacks permission

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L5-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L5-L11)

---

## Error Handling

The endpoint has minimal error handling:

[backennd/db_interaction/eliminar_cultivo.php L8-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L8-L12)

**Database Query Errors:**

* Caught by checking `mysqli_query()` return value
* Error message includes raw `mysqli_error()` output
* Returns plain text error message to client

**Unhandled Error Cases:**

* Missing `id` parameter → Query executes with empty WHERE clause
* Invalid `id` format → May cause SQL syntax error
* Non-existent crop ID → Query succeeds but affects 0 rows (no indication to client)
* Database connection failure → Error handled by `connection.php`

**No Error Logging:** The endpoint does not log errors to a file or monitoring system, making debugging production issues difficult.

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L8-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L8-L12)

---

## Comparison with Other Crop Endpoints

| Feature | eliminar_cultivo.php | agregar_cultivo.php | editar_cultivo.php |
| --- | --- | --- | --- |
| **HTTP Method** | DELETE | POST | POST |
| **Response Format** | Plain text | JSON | JSON |
| **SQL Method** | Direct interpolation | Direct interpolation | Direct interpolation |
| **Input Validation** | None | None | None |
| **Authorization** | None | None | None |
| **Error Format** | Text string | JSON with `success` field | JSON with `success` field |

**Key Observation:** All three write-operation endpoints share the same security vulnerabilities (SQL injection, no authorization, no input validation), but `eliminar_cultivo.php` differs in its response format, making it inconsistent with the rest of the API.

For details on other crop endpoints, see:

* [Create Crop Endpoint](/axchisan/CoopAgronet/2.3.1-create-crop-endpoint)
* [Read Crop Endpoints](/axchisan/CoopAgronet/2.3.2-read-crop-endpoints)
* [Update Crop Endpoint](/axchisan/CoopAgronet/2.3.3-update-crop-endpoint)

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L1-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L1-L16)

---

## Code Structure Summary

```

```

**Total Lines:** 16 (including PHP tags and blank lines)

**Functional Lines:** 7 (actual logic statements)

**Dependencies:**

* `connection.php` (provides `$connection` mysqli object)
* PHP mysqli extension
* MySQL database with `cultivos` table

**Sources:** [backennd/db_interaction/eliminar_cultivo.php L1-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/eliminar_cultivo.php#L1-L16)

 [backennd/db_interaction/connection.php L1-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L1-L14)