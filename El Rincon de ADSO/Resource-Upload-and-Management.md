# Resource Upload and Management

> **Relevant source files**
> * [src/backend/gestionRecursos/get_recent_resources.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/get_recent_resources.php)
> * [src/backend/gestionRecursos/upload_resource.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php)
> * [src/frontend/inicio/index.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php)
> * [src/frontend/panel/panel-usuario.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php)
> * [src/frontend/repositorio/repositorio.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/repositorio.php)

## Purpose and Scope

This document describes the resource upload and management interface within the User Dashboard, specifically the "Mis Aportes" tab functionality. It covers the modal-based upload form, field validation, file handling, and the management operations (edit/delete) for user-contributed resources.

For information about the backend validation logic and database schema, see [Resource Upload and Validation](/axchisan/El-rincon-de-ADSO/5.3-resource-upload-and-validation). For information about viewing and browsing user resources, see [Repository Tab - User Resources](/axchisan/El-rincon-de-ADSO/4.1-repository-tab-user-resources). For general resource browsing functionality available to all users, see [Repository Browser](/axchisan/El-rincon-de-ADSO/5.1-repository-browser).

---

## Upload Interface Access

The resource upload functionality is accessed from the "Mis Aportes" sub-tab within the User Dashboard's "Repositorio" main tab. The interface is modal-based, triggered by clicking the "Nuevo aporte" button.

```

```

**Sources:** [src/frontend/panel/panel-usuario.php L199-L206](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L199-L206)

 [src/frontend/panel/panel-usuario.php L207-L342](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L207-L342)

---

## Upload Modal Structure

The upload modal (`#upload-modal`) is a comprehensive form embedded within the panel page. It remains hidden until triggered and overlays the page content when active.

### Modal Components

| Component | ID/Class | Purpose |
| --- | --- | --- |
| Modal Container | `#upload-modal` | Overlay container with dark background |
| Modal Content | `.modal-content` | White card containing the form |
| Close Button | `#close-upload-modal` | X button to dismiss modal |
| Form | `#upload-resource-form` | Main upload form with multipart encoding |
| Submit Button | `#submit-resource` | Triggers upload process |
| Progress Bar | `#progress-bar` | Visual upload progress indicator |
| Error Display | `#error-message` | Shows validation/upload errors |
| Success Display | `#success-message` | Shows success confirmation |

**Sources:** [src/frontend/panel/panel-usuario.php L207-L342](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L207-L342)

---

## Form Fields and Validation

### Required Fields

The form enforces several required fields marked with asterisks (*):

```

```

### Optional Fields

| Field | ID | Type | Notes |
| --- | --- | --- | --- |
| Description | `#resource-description` | Textarea | Brief description of resource |
| Video URL | `#resource-video-url` | URL input | Required only for video type |
| Video Duration | `#resource-video-duration` | Text (HH:MM:SS) | Optional for videos |
| File Upload | `#resource-file` | File input | Required for non-video types |
| Custom Tags | `#selected-tags` | Hidden (managed by UI) | User-defined tags |
| Publication Date | `#resource-publication-date` | Date input | Defaults to current date if empty |
| Group | `#resource-group` | Select | Required only when visibility is "Group" |

**Sources:** [src/frontend/panel/panel-usuario.php L213-L320](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L213-L320)

---

## Resource Type Configuration

The form adapts dynamically based on the selected resource type. When a user selects a type from the dropdown, JavaScript shows/hides relevant fields.

```

```

### Supported Resource Types

| Type Value | Label | File Requirement | URL Requirement |
| --- | --- | --- | --- |
| `libro` | Libro | PDF, DOCX, PPTX, Images | None |
| `video` | Video | None | YouTube URL required |
| `documento` | Documento | PDF, DOCX, PPTX, Images | None |
| `imagen` | Imagen | Image files only | None |
| `otro` | Otro | Any supported format | None |

**JavaScript handling:** [src/frontend/panel/panel-usuario.php L806-L843](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L806-L843)

**Sources:** [src/frontend/panel/panel-usuario.php L231-L240](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L231-L240)

 [src/frontend/panel/panel-usuario.php L241-L254](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L241-L254)

---

## Category and Tag Management

### Category Selection

Categories are pre-defined in the database and loaded dynamically via AJAX. Users must select at least one category.

```

```

**Category rendering logic:** [src/frontend/panel/panel-usuario.php L851-L891](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L851-L891)

**Backend endpoint:** [src/backend/gestionRecursos/get_categories.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/get_categories.php)

### Custom Tag Management

Users can add custom tags by typing and pressing Enter, comma, or clicking the "Añadir" button. Tags are stored as an array.

**Tag input handler:** [src/frontend/panel/panel-usuario.php L893-L938](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L893-L938)

| Element | Purpose |
| --- | --- |
| `#tag-input` | Text input for typing tag names |
| `#add-tag-button` | Button to add current input as tag |
| `#custom-tags` | Container displaying added tags as removable chips |
| `#selected-tags` | Hidden input storing JSON array of tag names |

**Sources:** [src/frontend/panel/panel-usuario.php L260-L268](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L260-L268)

 [src/frontend/panel/panel-usuario.php L893-L938](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L893-L938)

---

## File Upload and Validation

### Client-Side Validation

Before submission, the form validates file types and sizes in the browser:

```

```

**Image file validation:** [src/backend/gestionRecursos/upload_resource.php L67-L93](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L67-L93)

**Resource file validation:** [src/backend/gestionRecursos/upload_resource.php L118-L154](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L118-L154)

### Server-Side Validation

The backend performs comprehensive validation before accepting uploads:

| Validation | Implementation | Error Response |
| --- | --- | --- |
| Session check | `$_SESSION['usuario_id']` | "Debes iniciar sesión..." |
| HTTP method | `$_SERVER['REQUEST_METHOD'] === 'POST'` | "Método no permitido" |
| Required fields | Empty check on all required fields | "Todos los campos obligatorios..." |
| Image type | Check against allowed MIME types | "Tipo de imagen no permitido..." |
| Image size | Max 5 MB | "La imagen es demasiado grande..." |
| File type | Check against allowed MIME types | "Tipo de archivo no permitido..." |
| File size | Max 10 MB | "El archivo es demasiado grande..." |
| Video URL | YouTube regex validation | "La URL del video no es válida..." |
| Status value | Must be Draft, Pending Review, or Published | "Estado no válido..." |

**Sources:** [src/backend/gestionRecursos/upload_resource.php L7-L154](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L7-L154)

### Allowed File Types

**Images (cover/portada):**

* `image/jpeg`
* `image/png`
* `image/gif`

**Resource files:**

* `application/pdf`
* `application/msword`
* `application/vnd.openxmlformats-officedocument.wordprocessingml.document` (DOCX)
* `application/vnd.ms-powerpoint`
* `application/vnd.openxmlformats-officedocument.presentationml.presentation` (PPTX)
* `image/jpeg`, `image/png`, `image/gif`

**Sources:** [src/backend/gestionRecursos/upload_resource.php L67-L68](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L67-L68)

 [src/backend/gestionRecursos/upload_resource.php L118-L127](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L118-L127)

---

## Upload Processing Flow

```

```

**Sources:** [src/frontend/panel/panel-usuario.php L940-L1037](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L940-L1037)

 [src/backend/gestionRecursos/upload_resource.php L156-L217](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L156-L217)

---

## Database Operations

### Document Insertion

The backend inserts the main resource record into the `documentos` table with all metadata:

```

```

**Implementation:** [src/backend/gestionRecursos/upload_resource.php L161-L180](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L161-L180)

### Junction Table Insertions

After the document is created, the backend creates relationships in junction tables:

**Categories:** [src/backend/gestionRecursos/upload_resource.php L184-L188](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L184-L188)

```

```

**Tags:** [src/backend/gestionRecursos/upload_resource.php L190-L208](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L190-L208)

```

```

All operations occur within a database transaction to ensure atomicity. If any step fails, the transaction rolls back and uploaded files are deleted.

**Transaction handling:** [src/backend/gestionRecursos/upload_resource.php L158-L217](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L158-L217)

**Sources:** [src/backend/gestionRecursos/upload_resource.php L156-L217](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L156-L217)

---

## Preview Functionality

The form includes a live preview card that updates as the user fills in fields. This provides immediate visual feedback.

### Preview Components

```

```

**Preview update logic:** [src/frontend/panel/panel-usuario.php L1042-L1083](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1042-L1083)

The preview updates in real-time as users type or select files, showing exactly how the resource card will appear in the repository.

**Sources:** [src/frontend/panel/panel-usuario.php L321-L331](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L321-L331)

 [src/frontend/panel/panel-usuario.php L1042-L1083](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1042-L1083)

---

## Visibility and Access Control

The form supports three visibility levels that control who can access the uploaded resource:

| Visibility | Value | Description | Additional Requirements |
| --- | --- | --- | --- |
| Public | `Public` | Visible to all users | None |
| Private | `Private` | Only visible to the author | None |
| Group | `Group` | Only visible to specific group members | Must select a group from dropdown |

When "Group" visibility is selected, the form dynamically shows a group selection dropdown (`#resource-group`).

**Visibility field:** [src/frontend/panel/panel-usuario.php L282-L289](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L282-L289)

**Group selection field:** [src/frontend/panel/panel-usuario.php L290-L293](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L290-L293)

**Backend visibility validation:** [src/backend/gestionRecursos/upload_resource.php L56-L59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L56-L59)

**Sources:** [src/frontend/panel/panel-usuario.php L282-L293](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L282-L293)

 [src/backend/gestionRecursos/upload_resource.php L56-L59](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L56-L59)

---

## Resource Status Workflow

Uploaded resources follow a publication workflow controlled by the "Estado" field:

```

```

**Valid status values:**

* `Draft` / `Borrador` - Resource is saved but not published
* `Pending Review` / `Enviar para Revisión` - Resource submitted for review
* `Published` / `Publicado` - Resource is live and visible

**Status field:** [src/frontend/panel/panel-usuario.php L312-L319](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L312-L319)

**Backend status validation:** [src/backend/gestionRecursos/upload_resource.php L34-L49](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L34-L49)

**Sources:** [src/frontend/panel/panel-usuario.php L312-L319](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L312-L319)

 [src/backend/gestionRecursos/upload_resource.php L34-L49](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L34-L49)

---

## Resource Management Operations

After uploading resources, users can manage them from the "Mis Aportes" grid. Each resource card displays management buttons when the user is the author.

### Edit Functionality

```

```

**Edit button logic:** [src/frontend/panel/panel-usuario.php L1292-L1355](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1292-L1355)

When editing:

* The modal title changes to "Editar Recurso"
* A hidden `resource_id` field is populated
* Current file names are displayed (user can keep existing files or upload new ones)
* Form fields are pre-filled with existing values

**Sources:** [src/frontend/panel/panel-usuario.php L1292-L1355](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1292-L1355)

### Delete Functionality

```

```

**Delete button logic:** [src/frontend/panel/panel-usuario.php L1357-L1377](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1357-L1377)

The delete operation:

1. Shows a confirmation dialog
2. Sends POST request to `delete_resource.php`
3. Backend verifies ownership (only author can delete)
4. Deletes database record (cascades to junction tables)
5. Removes uploaded files from filesystem
6. Returns success/error JSON
7. Reloads the resources grid

**Sources:** [src/frontend/panel/panel-usuario.php L1357-L1377](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1357-L1377)

---

## Loading User Resources

The "Mis Aportes" tab loads the user's uploaded resources via AJAX:

```

```

**Loading logic:** [src/frontend/panel/panel-usuario.php L1229-L1290](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1229-L1290)

The grid displays:

* Resource thumbnail/cover image
* Title, author, type
* Categories and tags
* Edit and Delete buttons (since user is the author)
* Publication status indicator

**Sources:** [src/frontend/panel/panel-usuario.php L1229-L1290](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L1229-L1290)

---

## Error Handling

### Client-Side Error Display

The form includes dedicated error and success message containers:

```

```

**Error container:** [src/frontend/panel/panel-usuario.php L338](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L338-L338)

**Success container:** [src/frontend/panel/panel-usuario.php L339](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L339-L339)

### Backend Error Responses

All backend errors return JSON with consistent structure:

```

```

Common error scenarios:

| Scenario | HTTP Status | Message |
| --- | --- | --- |
| Not logged in | 200 (JSON) | "Debes iniciar sesión para subir un recurso" |
| Invalid HTTP method | 200 (JSON) | "Método no permitido" |
| Missing required fields | 200 (JSON) | "Todos los campos obligatorios deben estar completos" |
| Invalid file type | 200 (JSON) | "Tipo de archivo no permitido..." |
| File too large | 200 (JSON) | "El archivo es demasiado grande..." |
| Database error | 200 (JSON) | "Error al guardar en la base de datos..." |

**Error handling:** [src/backend/gestionRecursos/upload_resource.php L7-L216](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L7-L216)

**Sources:** [src/frontend/panel/panel-usuario.php L338-L339](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L338-L339)

 [src/backend/gestionRecursos/upload_resource.php L7-L216](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L7-L216)

---

## File Storage

Uploaded files are stored in the server filesystem with unique names to prevent collisions.

### Storage Location

**Base directory:** `src/uploads/` (relative to project root)

**Path construction:** [src/backend/gestionRecursos/upload_resource.php L81-L84](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L81-L84)

```

```

### File Naming Convention

Files are renamed using `uniqid()` to ensure uniqueness:

**Cover images:** `{uniqid}_cover.{extension}`

**Resource files:** `{uniqid}.{extension}`

**Cover image naming:** [src/backend/gestionRecursos/upload_resource.php L86-L88](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L86-L88)

**Resource file naming:** [src/backend/gestionRecursos/upload_resource.php L143-L145](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L143-L145)

### Database URL Storage

File paths are stored in the database as relative URLs:

**Format:** `../../uploads/{filename}`

This allows the frontend to reference files correctly regardless of the page location.

**URL construction:** [src/backend/gestionRecursos/upload_resource.php L95](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L95-L95)

 [src/backend/gestionRecursos/upload_resource.php L153](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L153-L153)

**Sources:** [src/backend/gestionRecursos/upload_resource.php L81-L95](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L81-L95)

 [src/backend/gestionRecursos/upload_resource.php L143-L153](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/gestionRecursos/upload_resource.php#L143-L153)

---

## Progress Indication

During upload, a progress bar provides visual feedback to the user. The progress bar is updated via XMLHttpRequest events.

```

```

**Progress bar HTML:** [src/frontend/panel/panel-usuario.php L334-L336](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L334-L336)

**Progress update logic:** [src/frontend/panel/panel-usuario.php L980-L1004](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L980-L1004)

The progress bar uses XMLHttpRequest's `upload.onprogress` event to calculate the upload percentage and update the visual indicator in real-time.

**Sources:** [src/frontend/panel/panel-usuario.php L334-L336](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L334-L336)

 [src/frontend/panel/panel-usuario.php L980-L1004](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L980-L1004)

---

## Integration with Dashboard Tabs

The upload modal is part of the larger User Dashboard tab system. It integrates with the repository sub-tabs:

```

```

**Tab structure:** [src/frontend/panel/panel-usuario.php L160-L347](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L160-L347)

**Tab switching logic:** [src/frontend/panel/panel-usuario.php L687-L746](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L687-L746)

After a successful upload, the modal closes and the "Mis Aportes" grid automatically reloads to display the new resource.

**Sources:** [src/frontend/panel/panel-usuario.php L160-L347](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L160-L347)

 [src/frontend/panel/panel-usuario.php L687-L746](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L687-L746)