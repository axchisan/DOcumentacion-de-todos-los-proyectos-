# File Storage Architecture

> **Relevant source files**
> * [Admin/delete_image.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php)
> * [Admin/generate_informe_pdf.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php)
> * [Admin/informe.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php)

## Purpose and Scope

This document describes the file storage system for medical images in the Consultorio Emily Bernal application. The system manages two types of medical images: radiographs (`radiografia`) and oral photographs (`foto_boca`). The architecture implements a reference-based storage pattern where image files are stored on disk while the database stores only filename references.

For information about uploading images through the web interface, see [Image Upload System](/axchisan/Consultorio_Emily_Bernal/6.1-image-upload-system). For details on the deletion API endpoint, see [Image Deletion API](/axchisan/Consultorio_Emily_Bernal/6.2-image-deletion-api). For PDF generation that includes these images, see [PDF Generation System](/axchisan/Consultorio_Emily_Bernal/3-pdf-generation-system).

---

## Storage Architecture Overview

The file storage system uses a two-tier approach separating physical file storage from metadata storage. Files reside in type-specific directories under `../uploads/`, while the `informe_medico` table stores filename references.

### Directory Structure

```

```

**Directory Paths Used in Code:**

* Radiographs: `../uploads/radiografias/`
* Oral photos: `../uploads/fotos_boca/`

Sources: [Admin/informe.php L193-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L193-L223)

 [Admin/delete_image.php L29-L30](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L29-L30)

 [Admin/generate_informe_pdf.php L293-L311](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L293-L311)

---

## Database Schema for File References

The `informe_medico` table stores filename strings as references to physical files. The table does not store binary image data.

| Field | Type | Description |
| --- | --- | --- |
| `id_informe` | INT | Primary key |
| `id_cita` | INT | Foreign key to `citas` table |
| `id_paciente` | INT | Foreign key to `pacientes` table |
| `radiografia` | VARCHAR | Filename reference to radiograph image |
| `foto_boca` | VARCHAR | Filename reference to oral photo |
| *(other clinical fields)* | TEXT/DECIMAL | Medical examination data |

The fields `radiografia` and `foto_boca` store only the filename (e.g., `"123_radiografia_1634567890.jpg"`), not full paths or binary data.

Sources: [Admin/informe.php L428-L434](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L428-L434)

 [Admin/delete_image.php L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L43-L43)

---

## File Upload Workflow

The upload process in `informe.php` implements a multi-step workflow with validation, unique filename generation, directory management, and database updates.

```

```

### Upload Implementation Details

**Radiograph Upload:**

```sql
Location: Admin/informe.php:192-206
Process:
1. Validate $_FILES['radiografia']['error'] === UPLOAD_ERR_OK
2. Set $radiografia_path = "../uploads/radiografias/"
3. Create directory if needed: mkdir($radiografia_path, 0777, true)
4. Generate filename: $patient_id . "_radiografia_" . time() . ".ext"
5. Execute: move_uploaded_file($_FILES['radiografia']['tmp_name'], $target)
6. Store filename in $radiografia variable for database update
```

**Oral Photo Upload:**

```yaml
Location: Admin/informe.php:209-223
Process: Identical to radiograph with path "../uploads/fotos_boca/"
         and filename pattern $patient_id . "_boca_" . time() . ".ext"
```

**Database Update Logic:**

```sql
Location: Admin/informe.php:234-421
Three conditional branches:
1. Both images uploaded: UPDATE with both radiografia and foto_boca fields
2. Only radiografia uploaded: UPDATE only radiografia field
3. Only foto_boca uploaded: UPDATE only foto_boca field
4. No images uploaded: UPDATE without image fields
```

Sources: [Admin/informe.php L192-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L223)

 [Admin/informe.php L234-L421](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L234-L421)

---

## File Deletion Workflow

The `delete_image.php` endpoint implements a two-phase deletion process: database nullification followed by file system removal. This ordering ensures referential integrity.

```

```

### Deletion Implementation

**Path Determination:**

```

```

**File System Deletion:**

```

```

**Database Nullification:**

```

```

**Note on Ordering:** The file is deleted from disk BEFORE the database field is nullified. If the database update fails, the file is already gone, creating an inconsistency. A more robust approach would reverse this order or use transactions.

Sources: [Admin/delete_image.php L1-L50](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L1-L50)

 [Admin/informe.php L829-L860](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L829-L860)

---

## File Retrieval Mechanisms

The system retrieves stored images through two primary mechanisms: direct HTML rendering and PDF generation.

### HTML Display in Web Interface

```

```

**Implementation in informe.php:**

```

```

Sources: [Admin/informe.php L734-L753](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L734-L753)

### PDF Generation Retrieval

PDF generators construct absolute file paths and use TCPDF's `Image()` method to embed images.

**Radiograph Inclusion in PDF:**

```

```

**Oral Photo Inclusion in PDF:**

```

```

The PDF generator checks for file existence before attempting to embed images, logging errors if files are missing rather than failing the entire PDF generation.

Sources: [Admin/generate_informe_pdf.php L292-L312](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L292-L312)

 [Reportes/descargar_historia.php L293-L322](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Reportes/descargar_historia.php#L293-L322)

---

## Filename Convention and Uniqueness

The system generates unique filenames using a composite pattern to prevent collisions and provide traceability.

### Naming Pattern

```javascript
Format: {patient_id}_{type_identifier}_{unix_timestamp}.{extension}

Examples:
- 123_radiografia_1634567890.jpg
- 456_boca_1634567891.png

Components:
- patient_id: Integer from database (id_paciente)
- type_identifier: "radiografia" or "boca"
- unix_timestamp: time() function result (seconds since epoch)
- extension: Extracted from original filename via pathinfo()
```

### Generation Code

**Radiograph filename:**

```

```

**Oral photo filename:**

```

```

### Uniqueness Guarantee

The `time()` function provides uniqueness with one-second granularity. For the same patient uploading the same image type within the same second, a collision could occur. However, this is unlikely in typical usage patterns where:

1. Form submissions take >1 second to process
2. Multiple simultaneous uploads for the same patient are rare
3. The system updates existing records rather than creating duplicates

Sources: [Admin/informe.php L198](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L198-L198)

 [Admin/informe.php L215](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L215-L215)

---

## Reference-Based Storage Pattern

The architecture separates concerns between file storage and metadata storage through a reference-based pattern.

```

```

### Pattern Characteristics

**Database Stores:**

* Filename strings only (VARCHAR fields)
* NULL when no image exists
* No path information (relative paths constructed at retrieval)
* No binary data (BLOB not used)

**File System Stores:**

* Actual image files (JPG, PNG, etc.)
* Organized in type-specific directories
* Physical file paths: `../uploads/radiografias/filename` or `../uploads/fotos_boca/filename`

**Advantages:**

1. Database remains lightweight (no BLOB storage)
2. Files can be backed up separately from database
3. Direct HTTP access to images via relative URLs
4. Easy to implement file system operations (unlink, file_exists)

**Disadvantages:**

1. Referential integrity not enforced (orphaned files possible)
2. Two-phase operations required (file + database)
3. Manual synchronization between layers needed

Sources: [Admin/informe.php L192-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L223)

 [Admin/delete_image.php L1-L50](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L1-L50)

 [Admin/generate_informe_pdf.php L292-L312](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L292-L312)

---

## File Access Control and Security

The system implements session-based authentication but lacks file-level access controls.

### Authentication Check

```

```

All operations that manipulate files (upload, delete) require an active doctor session. However, the system has security limitations:

### Security Considerations

| Aspect | Implementation | Risk Level |
| --- | --- | --- |
| **Upload validation** | Checks UPLOAD_ERR_OK only | High - no MIME type verification |
| **File extension validation** | Uses pathinfo() without whitelist | High - dangerous extensions allowed |
| **Directory traversal** | No path sanitization | Medium - uses POST data in paths |
| **Direct file access** | Files served via relative URLs | Medium - no authorization on retrieval |
| **File size limits** | Relies on PHP ini settings | Low - configurable |

**Missing Security Measures:**

1. MIME type validation in [Admin/informe.php L192-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L223)
2. File extension whitelist (only jpg, png, gif allowed)
3. Authorization check that doctor owns the patient record
4. Protected storage directory (outside web root)
5. Sanitization of `$_POST['file_name']` in [Admin/delete_image.php L18](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L18-L18)

**SQL Injection Prevention:**
The code uses `mysqli_real_escape_string()` on `$_POST['id_cita']` in [Admin/delete_image.php L19](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L19-L19)

 but the UPDATE query in [Admin/delete_image.php L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L43-L43)

 uses string interpolation for the `$type` field. This is mitigated by validating `$type` against a whitelist in [Admin/delete_image.php L22-L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L22-L26)

Sources: [Admin/delete_image.php L5-L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L5-L26)

 [Admin/informe.php L192-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L223)

---

## Directory Creation and Permissions

The system creates directories dynamically if they don't exist, using recursive directory creation.

```

```

**Permission Mode:** `0777` (read/write/execute for all users)

**Recursive Flag:** Third parameter `true` enables recursive creation

**Implications:**

* Directories are created on-demand during first upload
* Permissive permissions (0777) allow broad access
* Recursive creation would create parent directories if missing
* No explicit verification that directories are writable after creation

Sources: [Admin/informe.php L194-L196](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L194-L196)

 [Admin/informe.php L211-L213](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L211-L213)

---

## Error Handling Patterns

The file storage system implements different error handling strategies across operations.

### Upload Error Handling

```yaml
Pattern: Session flash messages with redirect

Location: Admin/informe.php:195-202
On mkdir failure:
  $_SESSION['MensajeTexto'] = "Error: No se pudo crear el directorio"
  $_SESSION['MensajeTipo'] = "p-3 mb-2 bg-danger text-white"
  
On move_uploaded_file failure:
  $_SESSION['MensajeTexto'] = "Error al subir la radiografía"
  $_SESSION['MensajeTipo'] = "p-3 mb-2 bg-danger text-white"
  
Then continues processing (non-blocking errors)
```

### Deletion Error Handling

```javascript
Pattern: JSON responses for AJAX

Location: Admin/delete_image.php:12-48
Validation errors:
  json_encode(['success' => false, 'message' => 'Datos incompletos'])
  
File not found:
  json_encode(['success' => false, 'message' => 'Archivo no encontrado'])
  
Unlink failure:
  json_encode(['success' => false, 'message' => 'No se pudo eliminar'])
  
Database error:
  json_encode(['success' => false, 'message' => 'Error al actualizar: ' . mysqli_error()])
```

### PDF Generation Error Handling

```yaml
Pattern: Error logging with graceful degradation

Location: Admin/generate_informe_pdf.php:294-296
if (!file_exists($radiografia_path)) {
    error_log("Error: No se encontró la radiografía en '$radiografia_path'");
    // PDF generation continues without image
}
```

The PDF generator logs missing files but doesn't fail the entire document generation, allowing partial reports.

Sources: [Admin/informe.php L195-L202](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L195-L202)

 [Admin/delete_image.php L12-L48](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L12-L48)

 [Admin/generate_informe_pdf.php L294-L296](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L294-L296)

---

## Integration with Medical Report System

The file storage architecture is tightly coupled with the medical report workflow, where images supplement clinical examination text fields.

```

```

### Workflow Integration Points

**1. Report Creation (informe.php):**

* User fills clinical text fields
* User optionally uploads radiograph and/or oral photo
* System processes uploads first, then creates/updates database record
* Both text and image references stored atomically (INSERT or UPDATE)

**2. Report Viewing (ver_historia.php):**

* Query retrieves text fields and image filename references
* HTML displays images inline with clinical data
* Image URLs constructed: `../uploads/TYPE/filename`

**3. Report PDF Export:**

* PDF generator queries both text fields and image references
* Images embedded at specific positions in PDF layout
* Missing images logged but don't prevent PDF generation

**4. Report Editing (informe.php):**

* Existing images displayed with delete option
* New images can be uploaded to replace or add
* Delete operation nullifies database field and removes file
* Update operation preserves existing images unless new ones uploaded

Sources: [Admin/informe.php L173-L424](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L173-L424)

 [Admin/ver_historia.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php)

 [Admin/generate_informe_pdf.php L1-L331](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L1-L331)