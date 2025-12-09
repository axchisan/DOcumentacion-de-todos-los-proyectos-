# Clinical History PDF Generator

> **Relevant source files**
> * [Admin/descargar_historia.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php)

## Purpose and Scope

The Clinical History PDF Generator (`Admin/descargar_historia.php`) creates comprehensive PDF documents containing complete patient medical histories, including demographic information, anamnesis data, appointment details, and medical reports. This generator produces standardized clinical history documents suitable for archival and patient distribution.

This document covers the complete PDF generation workflow, from session validation through data retrieval to final PDF output. For the alternative medical report PDF generator focused on specific appointment findings, see [Medical Report PDF Generator](/axchisan/Consultorio_Emily_Bernal/3.2-medical-report-pdf-generator). For TCPDF library methods and configuration, see [TCPDF Library Integration](/axchisan/Consultorio_Emily_Bernal/3.3-tcpdf-library-integration).

**Sources:** [Admin/descargar_historia.php L1-L293](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L1-L293)

## System Overview

The clinical history PDF generator operates as a POST endpoint that receives appointment and patient identifiers, validates authentication, retrieves data from multiple database tables, and generates a multi-section PDF document using the TCPDF library.

### Request-Response Flow

```

```

**Sources:** [Admin/descargar_historia.php L1-L293](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L1-L293)

## Security and Session Validation

The generator implements multi-layered security checks before processing any PDF generation request.

### Session Validation Process

| Validation Step | Session Variable | Action on Failure | Line Reference |
| --- | --- | --- | --- |
| Session Existence | `$_SESSION['id_doctor']` | Redirect to login | [8-14](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/8-14) |
| Token Presence | `$_SESSION['session_token']` | Redirect to login | [8-14](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/8-14) |
| Token Validation | Database token match | Destroy session, redirect | [18-25](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/18-25) |
| Parameter Validation | `$_POST['id_cita']`, `$_POST['id_paciente']` | Redirect to history list | [28-33](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/28-33) |

The system uses `validarToken()` from [php/consultas.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/consultas.php)

 to verify that the session token matches the database record, preventing concurrent login exploitation.

**Sources:** [Admin/descargar_historia.php L8-L33](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L8-L33)

## Data Retrieval Architecture

The generator queries multiple database tables to compile a comprehensive patient history document.

### Database Query Structure

```

```

**Sources:** [Admin/descargar_historia.php L35-L82](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L35-L82)

### Patient Data Retrieval

The `consultarPaciente()` function retrieves comprehensive patient demographics and medical history:

```

```

This returns all fields from the `pacientes` table including:

* Personal information: `nombre`, `apellido`, `fecha_nacimiento`, `cedula`, `telefono`
* Medical history: `historia_cardiovasculares`, `historia_diabetes`, `historia_alergias`, etc.
* Emergency contacts: `emergencia_nombre`, `emergencia_telefono`

**Sources:** [Admin/descargar_historia.php L40-L54](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L40-L54)

### Appointment Data Query

The appointment query uses prepared statements to join three tables:

```

```

This retrieves:

* Appointment details from `citas`: `fecha_cita`, `hora_cita`, `diagnostico`
* Consultation type from `consultas`: `tipo`
* Doctor name from `doctor`: `nombreD`

The WHERE clause ensures doctors can only access their own patients' appointments through the `c.id_doctor` constraint.

**Sources:** [Admin/descargar_historia.php L57-L72](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L57-L72)

### Medical Report Data

An optional query retrieves detailed medical findings:

```

```

This may return clinical examination fields like `examen_intraoral`, `examen_extraoral`, `diagnostico_periodontal`, etc., if a medical report has been created for this appointment.

**Sources:** [Admin/descargar_historia.php L76-L82](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L76-L82)

## TCPDF Initialization and Configuration

The generator creates and configures a TCPDF instance with specific document properties.

### TCPDF Instance Creation

```

```

**Sources:** [Admin/descargar_historia.php L84-L105](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L84-L105)

### Document Metadata Configuration

| Property | Value | Method |
| --- | --- | --- |
| Creator | `PDF_CREATOR` | `SetCreator()` |
| Author | "Sistema Odontológico" | `SetAuthor()` |
| Title | "Historia Clínica" | `SetTitle()` |
| Subject | "Historia Clínica del Paciente" | `SetSubject()` |
| Keywords | "Historia, Clínica, Paciente, Odontología" | `SetKeywords()` |

The generator disables default TCPDF headers and footers (`setPrintHeader(false)`, `setPrintFooter(false)`) to implement custom header styling.

**Sources:** [Admin/descargar_historia.php L94-L102](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L94-L102)

## Document Structure and Sections

The PDF document is organized into three main sections with custom styling and layout.

### Document Header

```

```

The header uses a light blue background (`RGB(230, 240, 255)`) and includes:

* Logo image at coordinates (15, 10) with 30mm width
* Clinic information starting at X=50mm
* Blue title text (`RGB(0, 102, 204)`)
* Bordered history number cell aligned right

**Sources:** [Admin/descargar_historia.php L107-L137](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L107-L137)

### Información General Section

This section displays patient demographics in a structured table format.

#### Table Structure

```

```

The table uses alternating cell fills with gray headers (`RGB(245, 245, 245)`) and white values. Each row contains two label-value pairs using `Cell()` method with border=1.

**Field Mappings:**

| PDF Label | Database Field | Processing |
| --- | --- | --- |
| Nombre y apellidos | `$patient['nombre']` + `$patient['apellido']` | Concatenated |
| Edad | Calculated from `$patient['fecha_nacimiento']` | `DateTime::diff()` |
| Motivo de consulta | `$appointment['tipo']` | From joined `consultas` table |

**Sources:** [Admin/descargar_historia.php L139-L196](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L139-L196)

### Anamnesis Section

The anamnesis section displays family medical history as a checklist table with a separate medical alerts box.

#### Layout Design

```

```

#### Checklist Logic

Each medical history field stores "Sí" or "No" as ENUM values. The generator displays an "X" in the corresponding column:

```

```

The medical alerts box uses `MultiCell()` to handle multi-line text content positioned at coordinates calculated with `$alertas_x = 15 + $anamnesis_width + 10`.

**Sources:** [Admin/descargar_historia.php L199-L275](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L199-L275)

### Signature Section

The final section includes a disclaimer statement and spaces for patient signature and fingerprint:

* Italic disclaimer text at font size 9
* Horizontal signature line drawn with `Line(15, y, 80, y)`
* Rectangle box for fingerprint using `Rect(165, y, 30, 20, 'DF')`

**Sources:** [Admin/descargar_historia.php L278-L286](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L278-L286)

## PDF Output and Download

The generator outputs the PDF directly to the browser as a downloadable file.

### Output Process

```

```

### Output Configuration

| Component | Value | Purpose |
| --- | --- | --- |
| Buffer clearing | `ob_end_clean()` | Remove any previous output |
| Content-Type | `application/pdf` | Declare PDF MIME type |
| Content-Disposition | `attachment; filename=...` | Force download dialog |
| Filename pattern | `Historia_Clinica_{id_paciente}_Cita_{id_cita}.pdf` | Unique identifier |
| Output mode | `'D'` | Download mode (force download) |

The output mode `'D'` instructs TCPDF to send the PDF to the browser with download headers, as opposed to:

* `'I'`: Inline display in browser
* `'S'`: Return as string (for email attachments)
* `'F'`: Save to file system

**Sources:** [Admin/descargar_historia.php L289-L293](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L289-L293)

## Error Handling

The generator implements comprehensive error checking throughout the workflow.

### Error Scenarios

| Error Type | Check Location | Redirect Target | Message Type |
| --- | --- | --- | --- |
| No session | Line 9 | `../index.php` | `bg-danger` |
| Invalid token | Line 18 | `../index.php` | `bg-danger` |
| Missing parameters | Line 28 | `historia_clinica.php` | `bg-danger` |
| Patient not found | Line 41 | `historia_clinica.php` | `bg-danger` |
| Appointment not found | Line 66 | `historia_clinica.php` | `bg-danger` |
| TCPDF missing | Line 85 | Die with message | - |

All redirects use the session-based flash messaging pattern with `$_SESSION['MensajeTexto']` and `$_SESSION['MensajeTipo']` for displaying error notifications on the destination page.

**Sources:** [Admin/descargar_historia.php L8-L86](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L8-L86)

## Integration Points

The clinical history PDF generator integrates with several system components:

```

```

### Entry Points

1. **historia_clinica.php**: Download button in the clinical history list DataTable
2. **ver_historia.php**: Download button in the detail view header

Both entry points submit a POST form with:

* `id_cita`: Appointment identifier
* `id_paciente`: Patient identifier

**Sources:** [Admin/descargar_historia.php L5-L113](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L5-L113)