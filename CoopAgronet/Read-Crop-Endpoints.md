# Read Crop Endpoints

> **Relevant source files**
> * [backennd/db_interaction/connection.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php)
> * [backennd/db_interaction/obtener_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php)
> * [backennd/db_interaction/obtener_cultivos.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php)

## Purpose and Scope

This document covers the two read-only endpoints in the Crop Management API that retrieve crop data from the database: `obtener_cultivos.php` (retrieves all crops) and `obtener_cultivo.php` (retrieves a single crop by ID). Both endpoints handle HTTP GET requests and return JSON-formatted responses.

For information about creating crops, see [Create Crop Endpoint](/axchisan/CoopAgronet/2.3.1-create-crop-endpoint). For updating crops, see [Update Crop Endpoint](/axchisan/CoopAgronet/2.3.3-update-crop-endpoint). For deleting crops, see [Delete Crop Endpoint](/axchisan/CoopAgronet/2.3.4-delete-crop-endpoint).

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

 [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

## Endpoint Overview

The read operations are split into two separate PHP files that serve different retrieval needs:

| Endpoint | File | HTTP Method | Parameters | Purpose |
| --- | --- | --- | --- | --- |
| `obtener_cultivos.php` | [backennd/db_interaction/obtener_cultivos.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php) | GET | None | Retrieves all crop records from database |
| `obtener_cultivo.php` | [backennd/db_interaction/obtener_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php) | GET | `id` (query string) | Retrieves single crop record by ID |

Both endpoints share the same architectural pattern: include the database connection, execute a SQL query, format results as JSON, and close the connection.

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

 [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

## Request-Response Flow

```

```

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

 [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

 [backennd/db_interaction/connection.php L1-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L1-L14)

## List All Crops Endpoint

### File Location and Structure

The `obtener_cultivos.php` endpoint is located at [backennd/db_interaction/obtener_cultivos.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php)

 and implements a simple SELECT-ALL pattern with no filtering, pagination, or sorting capabilities.

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

### Implementation Details

The endpoint follows this execution sequence:

1. **Database Connection**: Includes `connection.php` to obtain the `$connection` mysqli object [backennd/db_interaction/obtener_cultivos.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L2-L2)
2. **Query Execution**: Executes `SELECT * FROM cultivos` to retrieve all columns from all rows [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4)
3. **Result Processing**: Iterates through result set using `mysqli_fetch_assoc()` in a while loop [backennd/db_interaction/obtener_cultivos.php L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L8-L8)
4. **Array Building**: Appends each row to the `$cultivos` array [backennd/db_interaction/obtener_cultivos.php L9](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L9-L9)
5. **JSON Response**: Converts the array to JSON using `json_encode()` [backennd/db_interaction/obtener_cultivos.php L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L12-L12)
6. **Connection Cleanup**: Closes database connection with `mysqli_close()` [backennd/db_interaction/obtener_cultivos.php L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L14-L14)

```

```

Sources: [backennd/db_interaction/obtener_cultivos.php L2-L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L2-L14)

### Response Format

The endpoint returns a JSON array containing zero or more crop objects. Each object represents a row from the `cultivos` table with all columns included.

**Example Response (Multiple Crops):**

```

```

**Example Response (No Crops):**

```

```

Sources: [backennd/db_interaction/obtener_cultivos.php L7-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L7-L12)

### Limitations

The `obtener_cultivos.php` endpoint has several architectural limitations:

| Limitation | Impact | Location |
| --- | --- | --- |
| No pagination | All records returned in single response, performance degrades with table growth | [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4) |
| No filtering | Cannot query by crop type, owner, date range, or other criteria | [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4) |
| No sorting | Results ordered by database default (typically insertion order) | [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4) |
| No error handling | Query failure produces empty array, indistinguishable from "no crops" | [backennd/db_interaction/obtener_cultivos.php L5-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L5-L10) |

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

## Get Single Crop Endpoint

### File Location and Structure

The `obtener_cultivo.php` endpoint is located at [backennd/db_interaction/obtener_cultivo.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php)

 and retrieves a single crop record by ID passed as a query string parameter.

Sources: [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

### Implementation Details

The endpoint implements a conditional execution flow:

1. **Parameter Validation**: Checks if `$_GET["id"]` is set using `isset()` [backennd/db_interaction/obtener_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L4-L4)
2. **ID Extraction**: Assigns query parameter to `$id` variable [backennd/db_interaction/obtener_cultivo.php L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L5-L5)
3. **Database Connection**: Includes `connection.php` [backennd/db_interaction/obtener_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L2-L2)
4. **Query Construction**: Builds SQL with direct string interpolation: `SELECT * FROM cultivos WHERE id = $id` [backennd/db_interaction/obtener_cultivo.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L6-L6)
5. **Query Execution**: Executes query with `mysqli_query()` [backennd/db_interaction/obtener_cultivo.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L7-L7)
6. **Result Handling**: Attempts to fetch single row with `mysqli_fetch_assoc()` [backennd/db_interaction/obtener_cultivo.php L9](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L9-L9)
7. **Response Generation**: Returns crop object if found, error object otherwise [backennd/db_interaction/obtener_cultivo.php L10-L12](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L10-L12)
8. **Connection Cleanup**: Closes database connection [backennd/db_interaction/obtener_cultivo.php L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L15-L15)

```

```

Sources: [backennd/db_interaction/obtener_cultivo.php L4-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L4-L16)

### Request Parameters

The endpoint requires a single query string parameter:

| Parameter | Type | Required | Location | Example |
| --- | --- | --- | --- | --- |
| `id` | Integer (passed as string) | Yes | Query string `$_GET["id"]` | `/obtener_cultivo.php?id=5` |

**Important**: If the `id` parameter is not provided, the endpoint produces no output and returns a blank response. There is no explicit error handling for missing parameters [backennd/db_interaction/obtener_cultivo.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L4-L4)

Sources: [backennd/db_interaction/obtener_cultivo.php L4-L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L4-L5)

### Response Formats

The endpoint returns one of two JSON response formats depending on whether the crop is found:

**Success Response (Crop Found):**

```

```

**Error Response (Crop Not Found):**

```

```

**Silent Failure (Missing ID Parameter):**

```
(empty response body)
```

Sources: [backennd/db_interaction/obtener_cultivo.php L9-L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L9-L13)

## Database Connection Pattern

Both endpoints use the same database connection pattern by including `connection.php`, which provides a shared `$connection` mysqli object.

```

```

The connection parameters are hardcoded in `connection.php` [backennd/db_interaction/connection.php L5-L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L8)

:

| Parameter | Value | Location |
| --- | --- | --- |
| `$host` | `"localhost:3306"` | [backennd/db_interaction/connection.php L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L5) |
| `$user` | `"root"` | [backennd/db_interaction/connection.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L6-L6) |
| `$password` | `""` (empty string) | [backennd/db_interaction/connection.php L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L7-L7) |
| `$database` | `"CoopAgroNet"` | [backennd/db_interaction/connection.php L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L8-L8) |

If the connection fails, `connection.php` terminates execution with a JSON error message [backennd/db_interaction/connection.php L12-L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L12-L13)

Sources: [backennd/db_interaction/connection.php L5-L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L5-L13)

 [backennd/db_interaction/obtener_cultivos.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L2-L2)

 [backennd/db_interaction/obtener_cultivo.php L2](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L2-L2)

## SQL Query Patterns

Both endpoints use direct SQL queries without prepared statements, which creates SQL injection vulnerabilities.

### obtener_cultivos.php Query

The query retrieves all columns and all rows from the `cultivos` table:

```

```

This query is executed at [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4)

 using `mysqli_query($connection, $sql)` [backennd/db_interaction/obtener_cultivos.php L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L5-L5)

Sources: [backennd/db_interaction/obtener_cultivos.php L4-L5](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L5)

### obtener_cultivo.php Query

The query filters by ID using direct string interpolation:

```

```

This query is constructed at [backennd/db_interaction/obtener_cultivo.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L6-L6)

 by embedding the `$id` variable directly into the SQL string without sanitization or parameterization.

```

```

Sources: [backennd/db_interaction/obtener_cultivo.php L5-L7](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L5-L7)

## Field Mapping

The endpoints return all columns from the `cultivos` table. The database schema uses Spanish field names:

| Database Column | Data Type | Description | Example Value |
| --- | --- | --- | --- |
| `id` | INT (Primary Key, Auto-increment) | Unique crop identifier | `"5"` |
| `tipo` | VARCHAR | Crop type/name | `"Tomate"` |
| `fecha_siembra` | DATE | Planting date | `"2024-01-15"` |
| `cantidad` | INT | Quantity (plants or area) | `"500"` |
| `dueno` | VARCHAR | Owner name | `"Juan Pérez"` |
| `edad` | INT | Owner age | `"45"` |
| `ubicacion` | VARCHAR | Location/plot identifier | `"Parcela A-12"` |
| `notas` | TEXT | Additional notes | `"Variedad Cherry"` |

All values are returned as strings in JSON, including numeric fields, due to `mysqli_fetch_assoc()` behavior [backennd/db_interaction/obtener_cultivos.php L8](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L8-L8)

 [backennd/db_interaction/obtener_cultivo.php L9](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L9-L9)

Sources: [backennd/db_interaction/obtener_cultivos.php L8-L9](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L8-L9)

 [backennd/db_interaction/obtener_cultivo.php L9-L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L9-L10)

## Security Vulnerabilities

Both endpoints have critical security issues that should be addressed before production deployment.

### SQL Injection in obtener_cultivo.php

The most severe vulnerability exists in `obtener_cultivo.php` where the `$id` parameter is interpolated directly into the SQL query without sanitization [backennd/db_interaction/obtener_cultivo.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L6-L6)

**Vulnerable Code:**

```

```

**Attack Example:**

```
GET /obtener_cultivo.php?id=1 OR 1=1
```

This would execute:

```

```

Returning all crops instead of a single record, bypassing the WHERE clause entirely.

**More Severe Attack:**

```sql
GET /obtener_cultivo.php?id=1; DROP TABLE cultivos;--
```

Sources: [backennd/db_interaction/obtener_cultivo.php L5-L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L5-L6)

### Lack of Authorization

Neither endpoint verifies user authentication or authorization. Any client can retrieve crop data by making direct HTTP requests to these endpoints without providing credentials.

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

 [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

### Information Disclosure

The endpoints return all database columns, including potentially sensitive information like owner names (`dueno`), ages (`edad`), and locations (`ubicacion`) without any access control or field filtering.

Sources: [backennd/db_interaction/obtener_cultivos.php L4](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L4-L4)

 [backennd/db_interaction/obtener_cultivo.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L6-L6)

## Frontend Integration

These endpoints are called by frontend JavaScript modules:

| Calling Module | Endpoint Used | Purpose |
| --- | --- | --- |
| `agregar_cultivo.js` | `obtener_cultivos.php` | Fetches all crops to populate table on page load and after create/update operations |
| `acciones_cultivos.js` | `obtener_cultivo.php` | Retrieves single crop data when edit button is clicked to populate form |

The frontend expects JSON responses and uses `fetch()` API to make GET requests. For details on frontend implementation, see [Crop Creation and Listing](/axchisan/CoopAgronet/3.3.1-crop-creation-and-listing) and [Crop Edit and Delete Actions](/axchisan/CoopAgronet/3.3.2-crop-edit-and-delete-actions).

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

 [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

## Error Handling Comparison

The two endpoints handle errors differently:

| Scenario | obtener_cultivos.php | obtener_cultivo.php |
| --- | --- | --- |
| Query succeeds, results found | Returns JSON array of objects | Returns JSON object with crop data |
| Query succeeds, no results | Returns empty array `[]` | Returns `{"error": "Cultivo no encontrado"}` |
| Query fails | Returns empty array `[]` (query failure indistinguishable from no results) | Likely returns empty response or PHP error |
| Missing parameters | N/A (no parameters required) | Returns empty response (silent failure) |
| Database connection fails | Script terminates via `die()` in `connection.php` | Script terminates via `die()` in `connection.php` |

Neither endpoint implements comprehensive error logging or structured error responses for database query failures.

Sources: [backennd/db_interaction/obtener_cultivos.php L1-L15](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivos.php#L1-L15)

 [backennd/db_interaction/obtener_cultivo.php L1-L17](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/obtener_cultivo.php#L1-L17)

 [backennd/db_interaction/connection.php L12-L13](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L12-L13)