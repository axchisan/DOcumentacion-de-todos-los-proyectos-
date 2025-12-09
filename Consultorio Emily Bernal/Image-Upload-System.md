# Image Upload System

> **Relevant source files**
> * [Admin/informe.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php)

This document describes the medical image upload system implemented in the `informe.php` interface. The system handles two types of images: radiographs (`radiografia`) and oral photographs (`foto_boca`). Images are stored on the filesystem with references maintained in the `informe_medico` database table.

For information about image deletion, see [Image Deletion API](/axchisan/Consultorio_Emily_Bernal/6.2-image-deletion-api). For details on the storage directory structure, see [File Storage Architecture](/axchisan/Consultorio_Emily_Bernal/6.3-file-storage-architecture).

## Upload Workflow Overview

The image upload system operates within the medical report creation/update workflow. Images are uploaded as part of form submissions that also include clinical examination data and treatment information.

```

```

**Sources:** [Admin/informe.php L174-L425](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L174-L425)

## Form Configuration

The upload form requires specific HTML attributes to support file uploads. The form uses `enctype="multipart/form-data"` and POST method.

| Element | Attribute | Value | Purpose |
| --- | --- | --- | --- |
| `<form>` | `method` | `POST` | Submit data to server |
| `<form>` | `enctype` | `multipart/form-data` | Enable file upload |
| `<input>` | `type` | `file` | File selection control |
| `<input>` | `name` | `radiografia` or `foto_boca` | Field identifier |
| `<input>` | `accept` | `image/*` | Restrict to images |

```

```

**Sources:** [Admin/informe.php L690](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L690-L690)

 [Admin/informe.php L728-L732](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L728-L732)

 [Admin/informe.php L742-L746](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L742-L746)

## File Upload Processing

PHP processes uploaded files through the `$_FILES` superglobal array. Each uploaded file entry contains metadata including temporary file path, original filename, size, MIME type, and error code.

### Radiograph Upload Handler

The radiograph upload handler begins at [Admin/informe.php L192-L206](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L206)

 The process:

1. **Validation**: Check `$_FILES['radiografia']['error'] === UPLOAD_ERR_OK`
2. **Directory preparation**: Verify or create `../uploads/radiografias/`
3. **Filename generation**: Create unique name using pattern `{patient_id}_radiografia_{timestamp}.{extension}`
4. **File movement**: Move from temporary location to permanent storage
5. **Variable assignment**: Store filename in `$radiografia` variable for database update

```

```

**Sources:** [Admin/informe.php L192-L206](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L206)

### Oral Photo Upload Handler

The oral photo upload handler at [Admin/informe.php L209-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L209-L223)

 follows an identical pattern with different paths:

* Storage directory: `../uploads/fotos_boca/`
* Filename pattern: `{patient_id}_boca_{timestamp}.{extension}`
* Database field: `foto_boca`

**Sources:** [Admin/informe.php L209-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L209-L223)

## Directory Management

The system ensures upload directories exist before attempting file operations. Directory creation uses recursive mode with full permissions.

```

```

| Directory | Path | Permissions | Recursive |
| --- | --- | --- | --- |
| Radiographs | `../uploads/radiografias/` | `0777` | Yes (`true`) |
| Oral Photos | `../uploads/fotos_boca/` | `0777` | Yes (`true`) |

**Sources:** [Admin/informe.php L193-L197](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L193-L197)

 [Admin/informe.php L210-L214](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L210-L214)

## Filename Generation Strategy

The system generates collision-resistant filenames by combining patient ID, image type, and Unix timestamp. The file extension is preserved from the original upload.

### Filename Components

```

```

### Implementation Details

For radiographs at [Admin/informe.php L198](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L198-L198)

:

```

```

For oral photos at [Admin/informe.php L215](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L215-L215)

:

```

```

| Component | Function | Example Value |
| --- | --- | --- |
| Patient ID | Identifies owner | `123` |
| Type suffix | Image category | `radiografia` or `boca` |
| Timestamp | Uniqueness guarantee | `1638316800` |
| Extension | File format | `jpg`, `png`, `jpeg` |

**Sources:** [Admin/informe.php L198](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L198-L198)

 [Admin/informe.php L215](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L215-L215)

## File Movement and Storage

After filename generation, the system moves files from PHP's temporary upload directory to permanent storage using `move_uploaded_file()`.

### Move Operation

The `move_uploaded_file()` function performs atomic file movement with security validation:

* **Source**: `$_FILES[field]['tmp_name']` (PHP temporary directory)
* **Destination**: Full path combining directory + generated filename
* **Security**: Validates file was uploaded via HTTP POST
* **Atomic**: Single operation, no intermediate states

```

```

### Error Handling During Move

If `move_uploaded_file()` returns `false`, the system sets a session error message and does not update the database:

[Admin/informe.php L199-L202](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L199-L202)

 for radiographs:

```

```

[Admin/informe.php L216-L219](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L216-L219)

 for oral photos uses identical pattern.

**Sources:** [Admin/informe.php L199-L202](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L199-L202)

 [Admin/informe.php L216-L219](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L216-L219)

## Database Integration

After successful file upload, the filename (not the file content) is stored in the `informe_medico` table. The system determines whether to INSERT or UPDATE based on existing records.

### Database Field Mapping

```

```

### Conditional Update Logic

The system checks for existing medical reports at [Admin/informe.php L225-L234](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L225-L234)

:

```

```

Based on `mysqli_num_rows($check_result)`, the system branches to UPDATE or INSERT.

### UPDATE Path with Conditional Fields

For existing reports, the system constructs different UPDATE queries depending on which files were uploaded:

| Condition | Query Fields Updated | Lines |
| --- | --- | --- |
| Both files uploaded | All fields + `radiografia` + `foto_boca` | [Admin/informe.php L238-L273](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L238-L273) |
| Only radiograph | All fields + `radiografia` | [Admin/informe.php L275-L308](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L275-L308) |
| Only oral photo | All fields + `foto_boca` | [Admin/informe.php L310-L343](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L310-L343) |
| No files uploaded | All fields except image fields | [Admin/informe.php L345-L377](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L345-L377) |

Example UPDATE with both images at [Admin/informe.php L238-L253](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L238-L253)

:

```

```

### INSERT Path

For new medical reports, the INSERT statement at [Admin/informe.php L389-L411](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L389-L411)

 includes both image fields:

```

```

**Sources:** [Admin/informe.php L225-L421](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L225-L421)

## Error Handling

The upload system implements multi-stage error detection and user notification through session-based flash messages.

### Error Detection Points

```

```

### Error Message Types

| Error Type | Message | Session Type | Lines |
| --- | --- | --- | --- |
| Directory creation failure (radiograph) | "Error: No se pudo crear el directorio para radiografías." | `bg-danger` | [Admin/informe.php L195-L196](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L195-L196) |
| Directory creation failure (photo) | "Error: No se pudo crear el directorio para fotos de la boca." | `bg-danger` | [Admin/informe.php L212-L213](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L212-L213) |
| File move failure (radiograph) | "Error al subir la radiografía." | `bg-danger` | [Admin/informe.php L200-L201](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L200-L201) |
| File move failure (photo) | "Error al subir la foto de la boca." | `bg-danger` | [Admin/informe.php L217-L218](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L217-L218) |
| Database error | "Error al actualizar/crear el informe médico: {error}" | `bg-danger` | [Admin/informe.php L383](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L383-L383) <br>  [Admin/informe.php L417](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L417-L417) |

### Session Message Pattern

All errors follow the Post-Redirect-Get pattern:

1. **Set message**: Store text and type in `$_SESSION`
2. **Redirect**: `header("Location: informe.php?id=$patient_id")`
3. **Display**: Next page load shows alert
4. **Clear**: Message immediately cleared from session

**Sources:** [Admin/informe.php L195-L201](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L195-L201)

 [Admin/informe.php L212-L218](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L212-L218)

 [Admin/informe.php L383-L384](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L383-L384)

 [Admin/informe.php L417-L418](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L417-L418)

## Security Considerations

The upload system implements multiple security layers to prevent unauthorized file access and malicious uploads.

### Input Validation

```

```

### Security Mechanisms

| Layer | Implementation | Purpose | Location |
| --- | --- | --- | --- |
| Authentication | Session validation | Verify doctor login | [Admin/informe.php L11-L16](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L11-L16) |
| Authorization | Patient ID validation | Verify access rights | [Admin/informe.php L18-L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L18-L36) |
| Upload validation | `UPLOAD_ERR_OK` check | Detect PHP errors | [Admin/informe.php L192](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L192) <br>  [Admin/informe.php L209](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L209-L209) |
| File movement | `move_uploaded_file()` | Verify POST upload | [Admin/informe.php L199](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L199-L199) <br>  [Admin/informe.php L216](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L216-L216) |
| Path traversal prevention | No user-supplied path components | Prevent directory escape | Filename generation |
| SQL injection prevention | Prepared statements | Parameterize queries | [Admin/informe.php L254-L273](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L254-L273) |
| Extension preservation | `pathinfo(PATHINFO_EXTENSION)` | Maintain file type | [Admin/informe.php L198](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L198-L198) <br>  [Admin/informe.php L215](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L215-L215) |

### File Type Restrictions

HTML form restricts selection at [Admin/informe.php L730](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L730-L730)

 and [Admin/informe.php L744](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L744-L744)

:

```

```

This provides client-side filtering but relies on browser enforcement. The system does not perform server-side MIME type validation, trusting the `move_uploaded_file()` function's POST verification.

**Sources:** [Admin/informe.php L11-L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L11-L36)

 [Admin/informe.php L192-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L223)

 [Admin/informe.php L254-L273](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L254-L273)

## Integration with Medical Report Workflow

The upload system operates within the larger medical report creation process. Image uploads are optional additions to comprehensive clinical documentation.

```

```

### Form Structure Dependencies

The medical report form at [Admin/informe.php L690-L772](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L690-L772)

 contains:

* **Text fields**: 12 clinical examination fields (e.g., `examen_intraoral`, `plan_tratamiento`)
* **File uploads**: 2 image upload fields (`radiografia`, `foto_boca`)
* **Numeric field**: 1 cost field (`costo`)
* **Submit button**: Triggers validation and processing
* **PDF generation**: Separate form for exporting completed report

### Execution Flow

1. Form submission triggers POST handler at [Admin/informe.php L174](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L174-L174)
2. Validates `isset($_POST['update_medical'])`
3. Processes all text fields with `mysqli_real_escape_string()`
4. Conditionally processes file uploads if `$_FILES[field]['error'] === UPLOAD_ERR_OK`
5. Checks for existing report with appointment ID
6. Executes conditional UPDATE or INSERT with appropriate field list
7. Redirects with success/error message
8. Form reloads with updated data and message display

**Sources:** [Admin/informe.php L174-L425](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L174-L425)

 [Admin/informe.php L690-L772](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L690-L772)