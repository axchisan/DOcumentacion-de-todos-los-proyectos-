# Medical Report PDF Generator

> **Relevant source files**
> * [Admin/generate_informe_pdf.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php)

## Purpose and Scope

This document describes the medical report PDF generation system implemented in [Admin/generate_informe_pdf.php L1-L331](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L1-L331)

 This generator creates focused, appointment-specific medical report PDFs containing patient demographics, anamnesis, appointment details, clinical examinations, and associated medical images (radiographs and oral photographs).

This generator differs from the clinical history PDF generator (see [3.1](/axchisan/Consultorio_Emily_Bernal/3.1-clinical-history-pdf-generator)) in that it produces a single comprehensive report for one appointment rather than a complete patient history. For details on TCPDF library methods and configuration, see [3.3](/axchisan/Consultorio_Emily_Bernal/3.3-tcpdf-library-integration).

**Sources:** [Admin/generate_informe_pdf.php L1-L331](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L1-L331)

---

## System Overview

The medical report PDF generator is a POST-endpoint PHP script that requires an authenticated doctor session. It retrieves data from multiple database tables, combines it with uploaded medical images, and produces a downloadable PDF using the TCPDF library.

### High-Level Data Flow

```

```

**Sources:** [Admin/generate_informe_pdf.php L1-L331](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L1-L331)

---

## Request Processing and Validation

The generator performs multi-layered validation before processing:

### Input Validation Sequence

```

```

**Validation Steps:**

| Step | Line Range | Check | Error Handling |
| --- | --- | --- | --- |
| Buffer Initialization | [3-3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/3-3) | `ob_start()` | Required for header control |
| Database Connection | [10-13](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/10-13) | `if (!$link)` | `die()` with mysqli_connect_error |
| POST Parameter | [16-18](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/16-18) | `isset($_POST['patient_id'])` | `die()` with message |
| SQL Injection Prevention | [20-20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/20-20) | `mysqli_real_escape_string()` | Sanitizes input |
| Session Validation | [24-26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/24-26) | `isset($_SESSION['id_doctor'])` | `die()` with message |
| Patient Data | [29-32](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/29-32) | `consultarPaciente()` result | `die()` if NULL |
| Appointment Data | [54-56](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/54-56) | `mysqli_num_rows()` check | `die()` if 0 rows |

**Sources:** [Admin/generate_informe_pdf.php L3-L58](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L3-L58)

---

## Data Retrieval Architecture

The generator retrieves data from five database tables using both a query abstraction function and prepared statements.

### Database Query Mapping

```

```

### Query Implementation Details

**Patient Data Query:**

* **Function:** `consultarPaciente($link, $patient_id)` at [29-29](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/29-29)
* **Source File:** `php/consultas.php`
* **Returns:** Associative array with all patient fields
* **Used For:** Patient demographics and anamnesis sections

**Appointment Data Query:**

```

```

* **Implementation:** Prepared statement at [44-58](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/44-58)
* **Security:** Uses `mysqli_stmt_bind_param()` with parameter types `"ii"`
* **Returns:** Most recent appointment for patient-doctor pair
* **Fields Used:** `fecha_cita`, `hora_cita`, `nombreD`, `tipo`, `estado`

**Medical Report Query:**

```

```

* **Implementation:** Prepared statement at [61-67](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/61-67)
* **Security:** Uses `mysqli_stmt_bind_param()` with parameter type `"i"`
* **Returns:** All medical report fields or empty array if none exists
* **Handles Missing Data:** Ternary operator returns `[]` if no rows

**Sources:** [Admin/generate_informe_pdf.php L29-L67](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L29-L67)

---

## TCPDF Initialization and Configuration

The generator configures TCPDF with specific document metadata, margins, and header/footer settings.

### TCPDF Setup Sequence

```

```

### Configuration Parameters

| Method | Parameters | Purpose | Line |
| --- | --- | --- | --- |
| `new TCPDF()` | Orientation: `PDF_PAGE_ORIENTATION`Unit: `PDF_UNIT`Format: `PDF_PAGE_FORMAT`Unicode: `true`Encoding: `'UTF-8'`Disk cache: `false` | Initialize TCPDF instance | [76](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/76) |
| `SetCreator()` | `PDF_CREATOR` | Set document creator metadata | [79](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/79) |
| `SetAuthor()` | `'Sistema Odontológico'` | Set author name | [80](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/80) |
| `SetTitle()` | `'Informe Médico'` | Set PDF title | [81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/81) |
| `SetSubject()` | `'Informe Médico del Paciente'` | Set subject metadata | [82](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/82) |
| `SetKeywords()` | `'Informe, Médico, Paciente, Odontología'` | Set searchable keywords | [83](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/83) |
| `SetMargins()` | Left: `15`, Top: `30`, Right: `15` | Set page margins in mm | [85](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/85) |
| `SetHeaderMargin()` | `15` | Set header top margin | [86](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/86) |
| `SetFooterMargin()` | `15` | Set footer bottom margin | [87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/87) |
| `setHeaderFont()` | `['helvetica', '', 10]` | Font family, style, size | [89](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/89) |
| `setFooterFont()` | `['helvetica', '', 8]` | Font family, style, size | [90](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/90) |
| `SetHeaderData()` | Logo path, width, title, subtitle | Configure header appearance | [94-96](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/94-96) |
| `AddPage()` | None (uses defaults) | Add first page | [101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/101) |

**Logo Handling:**

* Primary method: `SetHeaderData()` at [93-96](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/93-96)
* Backup method: Manual `Image()` at [105-109](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/105-109)  to force logo on first page
* Conditional rendering based on `file_exists('../src/img/logo.png')`

**Sources:** [Admin/generate_informe_pdf.php L69-L109](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L69-L109)

---

## Document Structure and Content Sections

The PDF is organized into four primary sections, each with specific formatting and data sources.

### PDF Content Structure Map

```

```

### Section-Specific Formatting

**1. Title Section ([112-119](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/112-119)

)**

* Font: `helvetica`, Bold, 18pt
* Centered cell with title "Informe Médico"
* Horizontal separator line from x=15 to x=195

**2. Patient Information Section ([122-179](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/122-179)

)**

* Header: Bold 12pt, "Información del Paciente"
* Body: Regular 10pt
* Layout: Label-value pairs using `Cell()` with widths 40-60
* Special handling: * Age calculation using `DateTime` diff at [35-41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/35-41) * Conditional minor fields (age < 18) at [162-172](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/162-172) * `MultiCell()` for `alertas_medicas` (wrapping text) at [178](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/178)

**3. Anamnesis Section ([182-219](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/182-219)

)**

* Header: Bold 12pt, "Anamnesis (Historia Familiar o Personal)"
* Body: Regular 10pt
* Layout: Label-value pairs with widths 60-40
* Data Source: ENUM fields from `pacientes` table (Sí/No values)
* `MultiCell()` for `historia_otros` (free text) at [218](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/218)

**4. Appointment Information Section ([222-241](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/222-241)

)**

* Header: Bold 12pt, "Información de la Cita"
* Body: Regular 10pt
* Layout: Label-value pairs with widths 40-60
* Estado mapping: `'A'` → `'Realizada'`, else `'Pendiente'` at [240](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/240)

**5. Medical Report Section ([244-322](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/244-322)

)**

* Header: Bold 12pt, "Informe Médico"
* Body: Regular 10pt
* Layout: Label-value pairs with widths 55-135 or 85-105
* All fields use `MultiCell()` for text wrapping
* Image insertion at current Y position, width 100mm
* Vertical space after images: `Ln(90)` at [299, 310](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/299, 310)

**Sources:** [Admin/generate_informe_pdf.php L112-L322](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L112-L322)

---

## Medical Image Inclusion Logic

The generator conditionally includes two types of medical images: radiographs and oral photographs. Images are inserted only if the filename exists in the database and the physical file is accessible.

### Image Processing Workflow

```

```

### Image Rendering Parameters

**Radiograph Image:**

* **Database Field:** `informe_medico.radiografia` (filename only)
* **File Path Construction:** `'../uploads/radiografias/' . $medical_report['radiografia']` at [293](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/293)
* **Validation:** `isset()` and `!empty()` at [292](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/292)
* **File Existence Check:** `file_exists($radiografia_path)` at [294](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/294)
* **Error Handling:** `error_log()` if file not found at [295](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/295)
* **TCPDF Method:** `Image($radiografia_path, 50, $pdf->GetY(), 100)` at [298](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/298) * X position: 50mm (centered) * Y position: Current Y coordinate (`GetY()`) * Width: 100mm (height auto-calculated to maintain aspect ratio)
* **Vertical Spacing:** `Ln(90)` advances cursor 90mm at [299](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/299)

**Oral Photograph:**

* **Database Field:** `informe_medico.foto_boca` (filename only)
* **File Path Construction:** `'../uploads/fotos_boca/' . $medical_report['foto_boca']` at [304](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/304)
* **Validation:** `isset()` and `!empty()` at [303](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/303)
* **File Existence Check:** `file_exists($foto_boca_path)` at [305](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/305)
* **Error Handling:** `error_log()` if file not found at [306](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/306)
* **TCPDF Method:** `Image($foto_boca_path, 50, $pdf->GetY(), 100)` at [309](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/309)
* **Vertical Spacing:** `Ln(90)` advances cursor 90mm at [310](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/310)

### Image Storage Architecture

```

```

**Key Design Decisions:**

1. **Reference-based storage:** Only filenames stored in database, not binary data
2. **Defensive programming:** Multiple validation layers (isset, !empty, file_exists)
3. **Graceful degradation:** Missing images logged but don't halt PDF generation
4. **Consistent positioning:** Both images use same X coordinate (50mm) and width (100mm)
5. **Fixed vertical spacing:** 90mm Ln ensures adequate space regardless of actual image height

**Sources:** [Admin/generate_informe_pdf.php L292-L312](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L292-L312)

---

## Output Generation and HTTP Response

The generator uses output buffering control and TCPDF's download mode to deliver the PDF as a file download.

### Output Buffer Management

```

```

### Output Method Implementation

**Buffer Control:**

* **Initialization:** `ob_start()` at [3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/3)  captures any unintended output (whitespace, warnings)
* **Cleanup:** `ob_end_clean()` at [325](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/325)  discards buffer content before sending headers
* **Purpose:** Prevents "headers already sent" errors that would corrupt the PDF

**HTTP Headers:**

```

```

* **Line:** [327-328](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/327-328)
* **Content-Type:** Declares MIME type as PDF
* **Content-Disposition:** `attachment` forces download dialog (vs. inline display)
* **Filename:** Dynamic based on `$patient_id` variable

**TCPDF Output Method:**

```

```

* **Line:** [329](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/329)
* **First Parameter:** Filename used if second parameter is 'F' or 'I'
* **Second Parameter:** `'D'` = Force download with specified filename
* **Alternative Modes:** * `'I'`: Inline display in browser * `'F'`: Save to server file * `'S'`: Return as string (used for email attachments)

**Script Termination:**

* `exit()` at [330](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/330)  prevents any further output after PDF is sent

### Output Modes Comparison

| Mode | Description | Use Case | Line Reference |
| --- | --- | --- | --- |
| `'D'` | Download (force) | Current implementation | [329](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/329) |
| `'I'` | Inline (browser) | Preview mode (not used) | N/A |
| `'F'` | File (save to disk) | Server-side storage (not used) | N/A |
| `'S'` | String (return) | Email attachment (see descargar_historia.php) | N/A |

**Sources:** [Admin/generate_informe_pdf.php L3-L331](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L3-L331)

---

## Error Handling and Edge Cases

The generator implements defensive error handling at multiple layers to ensure robust operation.

### Error Handling Matrix

| Error Condition | Detection Method | Line | Response | User Impact |
| --- | --- | --- | --- | --- |
| Database connection failure | `if (!$link)` | [11-13](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/11-13) | `die()` with mysqli_connect_error | Immediate termination |
| Missing patient_id parameter | `!isset($_POST['patient_id'])` | [16-18](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/16-18) | `die()` with message | Immediate termination |
| No active doctor session | `!isset($_SESSION['id_doctor'])` | [24-26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/24-26) | `die()` with message | Immediate termination |
| Patient not found | `consultarPaciente()` returns NULL | [30-32](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/30-32) | `die()` with message | Immediate termination |
| No appointments found | `mysqli_num_rows() == 0` | [54-56](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/54-56) | `die()` with message | Immediate termination |
| TCPDF library missing | `!file_exists()` | [70-72](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/70-72) | `die()` with path message | Immediate termination |
| Logo file missing | `!file_exists('logo.png')` | [93-97](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/93-97) | Fallback text header | PDF generated without logo |
| Radiograph file missing | `!file_exists($radiografia_path)` | [294](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/294) | `error_log()` | PDF generated without image |
| Oral photo file missing | `!file_exists($foto_boca_path)` | [305](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/305) | `error_log()` | PDF generated without image |
| Medical report missing | `mysqli_num_rows() == 0` | [66](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/66) | Empty array `[]` | "N/A" displayed in PDF |
| NULL database fields | Various | Throughout | Null coalescing `?? 'N/A'` | "N/A" displayed |

### Null Coalescing Pattern

The generator extensively uses the null coalescing operator (`??`) to handle missing data gracefully:

```

```

**Applied to 25+ fields:**

* Patient fields: `telefono`, `eps`, `ocupacion`, `estado_civil`, `cedula`, `sexo`
* Emergency contact fields: `emergencia_nombre`, `emergencia_telefono`
* Minor fields: `menor_acompanante`, `menor_parentesco`, `menor_telefono`
* Anamnesis fields: All `historia_*` fields
* Medical report fields: All examination and diagnosis fields

**Alternative to `isset()` checks:** Reduces verbosity and provides default value in single expression.

### SQL Injection Prevention

All database queries use prepared statements with parameter binding:

**Patient Query:**

* Uses abstraction function `consultarPaciente()` at [29](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/29)
* Assumes function uses prepared statements internally

**Appointment Query:**

```

```

* Type specification: `"ii"` = two integers at [51](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/51)
* Prevents injection even if `patient_id` contains malicious input

**Medical Report Query:**

```

```

* Type specification: `"i"` = single integer at [63](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/63)

**Additional Sanitization:**

* `mysqli_real_escape_string()` applied to POST input at [20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/20)
* Provides defense-in-depth even though prepared statements used

**Sources:** [Admin/generate_informe_pdf.php L11-L72](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L11-L72)

 [Admin/generate_informe_pdf.php L140-L321](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L140-L321)

---

## Code Entity Reference

### Key Functions and Methods

| Entity | Type | Purpose | Line |
| --- | --- | --- | --- |
| `ob_start()` | PHP function | Initialize output buffer | [3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/3) |
| `session_start()` | PHP function | Start session | [5](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/5) |
| `mysqli_real_escape_string()` | PHP function | Sanitize POST input | [20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/20) |
| `consultarPaciente()` | Custom function | Query patient data | [29](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/29) |
| `mysqli_prepare()` | PHP function | Create prepared statement | [50, 62](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/50, 62) |
| `mysqli_stmt_bind_param()` | PHP function | Bind query parameters | [51, 63](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/51, 63) |
| `mysqli_stmt_execute()` | PHP function | Execute prepared query | [52, 64](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/52, 64) |
| `mysqli_stmt_get_result()` | PHP function | Get result set | [53, 65](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/53, 65) |
| `mysqli_fetch_assoc()` | PHP function | Fetch row as associative array | [57, 66](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/57, 66) |
| `DateTime::diff()` | PHP method | Calculate age | [38](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/38) |
| `new TCPDF()` | Constructor | Initialize PDF object | [76](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/76) |
| `SetCreator()` | TCPDF method | Set creator metadata | [79](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/79) |
| `SetMargins()` | TCPDF method | Set page margins | [85](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/85) |
| `SetHeaderData()` | TCPDF method | Configure header | [94](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/94) |
| `AddPage()` | TCPDF method | Add new page | [101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/101) |
| `Image()` | TCPDF method | Insert image | [105, 298, 309](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/105, 298, 309) |
| `SetFont()` | TCPDF method | Set font style | [112, 122, 125](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/112, 122, 125) |
| `Cell()` | TCPDF method | Render single-line cell | [114, 128, etc.](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/114, 128, etc.) |
| `MultiCell()` | TCPDF method | Render multi-line cell | [178, 218, 256](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/178, 218, 256) |
| `Line()` | TCPDF method | Draw horizontal line | [118](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/118) |
| `GetY()` | TCPDF method | Get current Y position | [118, 298, 309](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/118, 298, 309) |
| `Ln()` | TCPDF method | Line break with spacing | [113, 129, 299, 310](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/113, 129, 299, 310) |
| `ob_end_clean()` | PHP function | Discard output buffer | [325](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/325) |
| `header()` | PHP function | Send HTTP header | [327-328](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/327-328) |
| `Output()` | TCPDF method | Generate and output PDF | [329](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/329) |
| `exit()` | PHP function | Terminate script | [330](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/330) |

### Variables

| Variable | Type | Source | Usage |
| --- | --- | --- | --- |
| `$patient_id` | int | `$_POST['patient_id']` | Patient identifier |
| `$doctor_id` | int | `$_SESSION['id_doctor']` | Current doctor session |
| `$patient` | array | `consultarPaciente()` | Patient demographics |
| `$age` | int/string | Calculated from birthdate | Patient age |
| `$appointment` | array | Database query | Latest appointment data |
| `$medical_report` | array | Database query | Medical examination data |
| `$pdf` | TCPDF | `new TCPDF()` | PDF document object |
| `$radiografia_path` | string | Concatenated path | Radiograph file path |
| `$foto_boca_path` | string | Concatenated path | Oral photo file path |

### File Paths Referenced

| Path | Purpose | Lines |
| --- | --- | --- |
| `../php/conexionDB.php` | Database connection | [7](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/7) |
| `../php/consultas.php` | Query abstraction functions | [8](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/8) |
| `../Reportes/tcpdf/tcpdf.php` | TCPDF library | [73](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/73) |
| `../src/img/logo.png` | Clinic logo | [93, 105](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/93, 105) |
| `../uploads/radiografias/` | Radiograph storage | [293](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/293) |
| `../uploads/fotos_boca/` | Oral photo storage | [304](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/304) |

**Sources:** [Admin/generate_informe_pdf.php L1-L331](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/generate_informe_pdf.php#L1-L331)