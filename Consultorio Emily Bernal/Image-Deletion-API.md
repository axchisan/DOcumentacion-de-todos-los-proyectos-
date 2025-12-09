# Image Deletion API

> **Relevant source files**
> * [Admin/delete_image.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php)
> * [Admin/informe.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php)

## Purpose and Overview

The Image Deletion API is a server-side AJAX endpoint that enables authenticated doctors to delete medical images (radiographs and oral photos) associated with patient medical reports. The API implements a two-step deletion process: first removing the physical file from the filesystem, then updating the database to clear the file reference. This endpoint is invoked asynchronously from the Medical Report Editor interface.

For information about uploading images, see [Image Upload System](/axchisan/Consultorio_Emily_Bernal/6.1-image-upload-system). For details on file storage organization, see [File Storage Architecture](/axchisan/Consultorio_Emily_Bernal/6.3-file-storage-architecture).

**Sources:** [Admin/delete_image.php L1-L50](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L1-L50)

---

## Endpoint Specification

### Access Point

| Property | Value |
| --- | --- |
| File Path | `Admin/delete_image.php` |
| HTTP Method | POST |
| Content Type | `application/x-www-form-urlencoded` |
| Response Type | `application/json` |
| Authentication | Session-based (requires `$_SESSION['id_doctor']`) |

### Request Parameters

| Parameter | Type | Required | Valid Values | Description |
| --- | --- | --- | --- | --- |
| `type` | string | Yes | `radiografia`, `foto_boca` | Specifies which image type to delete |
| `file_name` | string | Yes | Any string | Name of the file to delete (e.g., `123_radiografia_1234567890.jpg`) |
| `id_cita` | integer | Yes | Positive integer | Appointment ID associated with the image |

**Sources:** [Admin/delete_image.php L12-L19](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L12-L19)

---

## Complete Request-Response Flow

```

```

**Diagram: Complete Image Deletion Flow from User Action to Database Update**

**Sources:** [Admin/delete_image.php L1-L50](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L1-L50)

 [Admin/informe.php L829-L860](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L829-L860)

---

## Authentication and Authorization

### Session Validation

The API enforces session-based authentication by checking for the presence of `$_SESSION['id_doctor']` at the beginning of the request lifecycle. If this session variable is not set, the API immediately returns an unauthorized response and terminates execution.

```

```

**Diagram: Session Authentication Check at API Entry Point**

**Implementation Details:**

| Code Location | Purpose |
| --- | --- |
| [Admin/delete_image.php L2](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L2-L2) | `session_start()` initializes or resumes session |
| [Admin/delete_image.php L6-L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L6-L9) | Checks `$_SESSION['id_doctor']` existence |

**Note:** The API does not perform additional authorization checks to verify that the requesting doctor has access to the specific appointment. This assumes that the frontend (informe.php) only presents deletion options for images belonging to appointments where `id_doctor` matches the session doctor.

**Sources:** [Admin/delete_image.php L2-L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L2-L9)

---

## Validation Process

### Multi-Stage Validation Pipeline

The API implements a four-stage validation process before attempting file deletion:

```

```

**Diagram: Four-Stage Validation Pipeline Before File Deletion**

### Validation Stage Details

| Stage | Code Location | Validation | Error Message |
| --- | --- | --- | --- |
| 1. Parameter Presence | [Admin/delete_image.php L12-L15](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L12-L15) | Checks `isset()` for all three POST parameters | "Datos incompletos" |
| 2. Type Whitelist | [Admin/delete_image.php L22-L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L22-L26) | Validates `type` using `in_array()` against `['radiografia', 'foto_boca']` | "Tipo de imagen no válido" |
| 3. File Existence | [Admin/delete_image.php L33-L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L33-L36) | Calls `file_exists()` on constructed path | "Archivo no encontrado" |
| 4. SQL Escaping | [Admin/delete_image.php L19](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L19-L19) | Applies `mysqli_real_escape_string()` to `id_cita` | N/A (prevents SQL injection) |

**Variable Assignment and Path Construction:**

```

```

**Sources:** [Admin/delete_image.php L12-L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L12-L36)

---

## Two-Step Deletion Workflow

### Critical Ordering: File-First, Database-Second

The deletion process follows a specific sequence: physical file deletion precedes database update. This ordering is intentional to prevent database inconsistency.

```

```

**Diagram: Two-Step Deletion Process with Error Handling**

### Rationale for Ordering

The file-first, database-second approach prevents the following scenario:

1. Database updated to NULL
2. File deletion fails
3. Result: Database shows no image, but file remains orphaned on disk

With the current ordering:

1. File deletion fails → database unchanged → consistent state
2. File deletion succeeds, database update fails → file deleted but reference remains (less critical issue)

### Code Implementation

**Step 1 - File Deletion:**

```

```

**Step 2 - Database Update:**

```

```

**Database Query Pattern:**

The query uses variable column name substitution (`$type`) to update either the `radiografia` or `foto_boca` column in the `informe_medico` table, setting it to NULL based on the deletion type.

**Sources:** [Admin/delete_image.php L37-L48](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L37-L48)

---

## Response Format

### JSON Response Structure

All responses from the API follow a consistent JSON structure with two fields:

```

```

### Response Catalog

| Scenario | `success` | `message` | HTTP Status | Code Location |
| --- | --- | --- | --- | --- |
| No session | `false` | "Acceso no autorizado" | 200 | [Admin/delete_image.php L7](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L7-L7) |
| Missing parameters | `false` | "Datos incompletos" | 200 | [Admin/delete_image.php L13](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L13-L13) |
| Invalid type | `false` | "Tipo de imagen no válido" | 200 | [Admin/delete_image.php L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L24-L24) |
| File not found | `false` | "Archivo no encontrado" | 200 | [Admin/delete_image.php L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L34-L34) |
| File deletion failed | `false` | "No se pudo eliminar el archivo" | 200 | [Admin/delete_image.php L38](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L38-L38) |
| Database error | `false` | "Error al actualizar la base de datos: {mysqli_error}" | 200 | [Admin/delete_image.php L47](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L47-L47) |
| Success | `true` | "Imagen eliminada correctamente" | 200 | [Admin/delete_image.php L45](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L45-L45) |

**Note:** All responses return HTTP 200 status code. Error differentiation occurs through the `success` boolean field rather than HTTP status codes.

### Error Message Internationalization

All messages are in Spanish, consistent with the application's user interface language. The database error message includes the MySQL error text from `mysqli_error($link)` for debugging purposes.

**Sources:** [Admin/delete_image.php L7-L48](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L7-L48)

---

## Frontend Integration

### JavaScript Implementation in informe.php

The Medical Report Editor page (`informe.php`) implements the client-side deletion logic using the Fetch API within a `DOMContentLoaded` event listener.

```

```

**Diagram: Frontend Deletion Flow with Fetch API**

### Key Code Elements

**Event Listener Attachment:**

[Admin/informe.php L830-L832](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L830-L832)

 - Selects all buttons with class `remove-image` and attaches click event listeners.

**Data Extraction:**

```

```

**Fetch Request Configuration:**

```

```

**Response Handling:**

| Response Condition | Action | Code Location |
| --- | --- | --- |
| `data.success === true` | Remove image preview element, show success alert | [Admin/informe.php L848-L850](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L848-L850) |
| `data.success === false` | Show error message from API | [Admin/informe.php L851-L852](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L851-L852) |
| Fetch exception | Log error to console, show generic alert | [Admin/informe.php L854-L857](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L854-L857) |

### HTML Structure Requirements

The JavaScript expects the following DOM structure for proper operation:

```

```

This structure is rendered conditionally in [Admin/informe.php L734-L739](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L734-L739)

 for radiographs and [Admin/informe.php L748-L753](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L748-L753)

 for oral photos.

**Sources:** [Admin/informe.php L829-L860](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L829-L860)

 [Admin/informe.php L734-L753](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L734-L753)

---

## Error Handling Strategy

### Defensive Programming Patterns

The API implements multiple defensive programming techniques:

| Pattern | Implementation | Purpose |
| --- | --- | --- |
| Early returns | `exit()` after each error response | Prevents execution after error conditions |
| Whitelist validation | `in_array($type, $valid_types)` | Prevents path traversal attacks |
| Existence checks | `file_exists()` before `unlink()` | Prevents PHP warnings on missing files |
| SQL escaping | `mysqli_real_escape_string()` | Prevents SQL injection |
| JSON encoding | `json_encode()` for all responses | Ensures consistent response format |

### Security Considerations

**1. Path Traversal Prevention:**

The API validates `type` against a hardcoded whitelist (`['radiografia', 'foto_boca']`) before path construction, preventing directory traversal attacks via malicious `type` values.

**2. File Name Validation Gap:**

The `file_name` parameter is **not validated** beyond checking if the file exists. This assumes that:

* Filenames are generated by the upload system using a controlled pattern
* The frontend only sends legitimate filenames extracted from the database
* The `file_exists()` check provides implicit validation

**Potential Vulnerability:** A malicious actor with session access could potentially delete arbitrary files within the `../uploads/radiografias/` or `../uploads/fotos_boca/` directories by crafting requests with arbitrary `file_name` values.

**3. Authorization Gap:**

The API does not verify that the authenticated doctor has permission to delete images for the specified `id_cita`. It relies on frontend access controls in `informe.php` which only displays delete buttons for appointments matching the session doctor's ID.

**Sources:** [Admin/delete_image.php L22-L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L22-L26)

 [Admin/delete_image.php L33-L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L33-L36)

---

## Integration with Database Schema

### Affected Table and Columns

The deletion API modifies the `informe_medico` table in the MySQL database:

**Table:** `informe_medico`

**Modified Columns:**

* `radiografia` (varchar/text) - Stores filename of radiograph image
* `foto_boca` (varchar/text) - Stores filename of oral photo

**Update Query Pattern:**

```

```

The column name is dynamically substituted based on the `type` parameter, allowing a single query template to handle both image types.

### Relationship to Other Tables

```

```

**Diagram: Database Schema Context for Image Deletion**

The API uses `id_cita` as the WHERE clause constraint because:

1. `informe_medico` has a one-to-one relationship with `citas`
2. Each appointment (`id_cita`) has at most one medical report
3. This ensures the deletion affects only the intended report

**Sources:** [Admin/delete_image.php L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L43-L43)

---

## Common Usage Patterns

### Typical Request-Response Cycle

**1. Successful Deletion:**

```yaml
POST /Admin/delete_image.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=...

type=radiografia&file_name=123_radiografia_1705432100.jpg&id_cita=456
```

**Response:**

```

```

**2. Authentication Failure:**

```yaml
POST /Admin/delete_image.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=expired_or_invalid

type=radiografia&file_name=123_radiografia_1705432100.jpg&id_cita=456
```

**Response:**

```

```

**3. File Not Found:**

```yaml
POST /Admin/delete_image.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=valid

type=foto_boca&file_name=nonexistent_file.jpg&id_cita=456
```

**Response:**

```

```

### Client-Side Response Handling

The frontend implements optimistic UI updates by removing the image preview element immediately upon successful deletion, without requiring a page refresh. This provides instant feedback to the user while maintaining data consistency through the AJAX pattern.

**Sources:** [Admin/informe.php L838-L860](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L838-L860)

---

## Dependencies and Required Components

### PHP Dependencies

| Component | Purpose | Code Reference |
| --- | --- | --- |
| `conexionDB.php` | Database connection via `$link` variable | [Admin/delete_image.php L3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L3-L3) |
| `session_start()` | PHP session mechanism for authentication | [Admin/delete_image.php L2](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L2-L2) |
| `mysqli_real_escape_string()` | SQL injection prevention | [Admin/delete_image.php L19](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L19-L19) |
| `mysqli_query()` | Database query execution | [Admin/delete_image.php L44](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L44-L44) |
| `mysqli_error()` | Error message retrieval | [Admin/delete_image.php L47](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L47-L47) |

### Frontend Dependencies

| Component | Purpose | Code Reference |
| --- | --- | --- |
| Fetch API | AJAX request mechanism | [Admin/informe.php L838](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L838-L838) |
| `querySelector()` | DOM element selection | [Admin/informe.php L830](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L830-L830) |
| `addEventListener()` | Event handling | [Admin/informe.php L831](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L831-L831) |
| `confirm()` | User confirmation dialog | [Admin/informe.php L837](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L837-L837) |
| `alert()` | User notification | [Admin/informe.php L849-L851](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L849-L851) |

### Filesystem Dependencies

The API requires read/write access to the following directories:

* `../uploads/radiografias/`
* `../uploads/fotos_boca/`

**Sources:** [Admin/delete_image.php L3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L3-L3)

 [Admin/informe.php L830-L860](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L830-L860)

---

## Testing and Debugging Considerations

### Manual Testing Checklist

| Test Case | Expected Behavior | Validation Method |
| --- | --- | --- |
| Delete with valid session | Image removed from filesystem and database | Check file system and query database |
| Delete without session | Returns "Acceso no autorizado" | Check JSON response |
| Delete with invalid type | Returns "Tipo de imagen no válido" | Check JSON response |
| Delete non-existent file | Returns "Archivo no encontrado" | Check JSON response |
| Delete with missing parameters | Returns "Datos incompletos" | Check JSON response |
| Database update failure | Returns database error message | Temporarily corrupt database connection |
| File deletion permission issue | Returns "No se pudo eliminar el archivo" | Set file permissions to read-only |

### Common Issues and Resolutions

**Issue 1: Session expiration during long editing sessions**

* Symptom: "Acceso no autorizado" response despite being logged in
* Resolution: Refresh page to reinitialize session

**Issue 2: File permissions prevent deletion**

* Symptom: "No se pudo eliminar el archivo" despite file existing
* Resolution: Verify web server has write permissions on upload directories

**Issue 3: Database connection lost**

* Symptom: "Error al actualizar la base de datos" with connection error
* Resolution: Check `conexionDB.php` configuration and MySQL service status

### Debug Information Sources

For troubleshooting failed deletions, examine:

1. PHP error logs for filesystem permission issues
2. MySQL error log for database connectivity problems
3. Browser console for JavaScript errors in frontend
4. Network tab in browser DevTools for request/response inspection

**Sources:** [Admin/delete_image.php L1-L50](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L1-L50)