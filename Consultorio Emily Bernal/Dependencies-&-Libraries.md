# Dependencies & Libraries

> **Relevant source files**
> * [Admin/calendar.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php)
> * [Admin/descargar_historia.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php)
> * [Admin/inicioAdmin.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php)

## Purpose and Scope

This page documents all external libraries, frameworks, and dependencies used throughout the Consultorio Emily Bernal system. Each dependency is listed with its version, purpose, loading mechanism (CDN or local), and primary usage locations within the codebase. For information about the overall project directory structure, see [Project Structure](/axchisan/Consultorio_Emily_Bernal/8.1-project-structure). For details on the TCPDF library specifically, see the comprehensive reference in [TCPDF Library Reference](/axchisan/Consultorio_Emily_Bernal/9-tcpdf-library-reference).

---

## Core Frontend Framework

### Bootstrap 4.0.0

Bootstrap provides the foundational CSS framework and responsive grid system used throughout all administrative interfaces.

**Version:** 4.0.0
**Loading Method:** CDN (MaxCDN) and Local
**Primary Files:**

* CDN: `https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css`
* Local: `../src/css/lib/bootstrap/css/bootstrap.min.css`
* JS: `https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js` and `../src/css/lib/bootstrap/js/bootstrap.min.js`

**Key Features Used:**

* Responsive grid system (`container`, `row`, `col-md-*`)
* Card components (`card`, `card-header`, `card-body`)
* Form controls and validation styling
* Modal dialogs for confirmations and alerts
* Alert components for flash messages
* Button styling and utilities
* Breadcrumb navigation
* Table styling (`table`, `table-striped`)

**Usage Locations:**

* [Admin/calendar.php L70](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L70-L70)  - CDN CSS
* [Admin/calendar.php L78](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L78-L78)  - CDN JS
* [Admin/inicioAdmin.php L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L36)  - Local CSS
* [Admin/inicioAdmin.php L158](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L158-L158)  - Local JS

Sources: [Admin/calendar.php L70-L78](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L70-L78)

 [Admin/inicioAdmin.php L36-L158](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L158)

---

## JavaScript Foundation

### jQuery

jQuery is the core JavaScript library enabling DOM manipulation, AJAX requests, and event handling.

| Component | Version | Source | Usage |
| --- | --- | --- | --- |
| jQuery Core | 3.2.1 | CDN (code.jquery.com) | Calendar page AJAX, event binding |
| jQuery Core | 3.5.1 | Local | DataTables compatibility |
| jQuery | Unversioned | Local (`../src/js/jquery.js`) | General purpose |

**Primary Functions:**

* AJAX requests for calendar date toggling
* DOM manipulation and event handling
* DataTables initialization
* FullCalendar initialization
* Modal dialog control
* Alert dismissal handlers

**Loading Patterns:**

```xml
<!-- CDN Loading (calendar.php) -->
<script src="https://code.jquery.com/jquery-3.2.1.js"></script>

<!-- Local Loading (inicioAdmin.php) -->
<script src="../src/js/jquery.js"></script>
<script src="../src/js/lib/datatable/js/jquery-3.5.1.js"></script>
```

**Usage Locations:**

* [Admin/calendar.php L76](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L76-L76)  - CDN version 3.2.1
* [Admin/inicioAdmin.php L157](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L157)  - Local version (unspecified)
* [Admin/inicioAdmin.php L160](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L160-L160)  - Local version 3.5.1 (DataTables)

Sources: [Admin/calendar.php L76](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L76-L76)

 [Admin/inicioAdmin.php L157-L160](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L160)

### Popper.js 1.12.9

Popper.js provides tooltip and popover positioning for Bootstrap components.

**Version:** 1.12.9
**Loading Method:** CDN (CloudFlare)
**Source:** `https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js`

**Purpose:**

* Required dependency for Bootstrap 4 dropdowns
* Tooltip positioning engine
* Modal positioning

**Usage Locations:**

* [Admin/calendar.php L77](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L77-L77)

Sources: [Admin/calendar.php L77](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L77-L77)

---

## Calendar System

### FullCalendar

FullCalendar provides the interactive calendar interface for appointment visualization and date availability management.

**Version:** Unspecified (legacy version based on API usage)
**Loading Method:** Local files
**Dependencies:** jQuery, Moment.js

#### FullCalendar File Structure

```

```

**File Paths:**

* CSS: `../src/css/fullcalendar.css`
* Core JS: `../src/js/lib/FullCalendar/fullcalendar.min.js`
* Moment.js: `../src/js/lib/FullCalendar/moment.min.js`
* Locale: `../src/js/lib/FullCalendar/locale/es.js`

**Key Features Used:**

| Feature | Configuration | Purpose |
| --- | --- | --- |
| `header` | Left: prev/next, Center: title, Right: views | Navigation controls |
| `defaultDate` | Current date (yyyy-mm-dd) | Initial display date |
| `editable` | `true` | Allow drag-drop (not actively used) |
| `events` | PHP-generated JSON array | Appointment data source |
| `eventClick` | Modal trigger | Show appointment details |
| `dayRender` | Custom styling | Mark unavailable dates |
| `dayClick` | AJAX handler | Toggle date availability |

**API Methods Used:**

* `$('#calendar').fullCalendar({...})` - Initialize calendar
* `$.fullCalendar.formatDate(date, 'YYYY-MM-DD')` - Format date for comparison
* `$('#calendar').fullCalendar('removeEvents', function)` - Remove events dynamically

**Usage Locations:**

* [Admin/calendar.php L73-L81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L73-L81)  - CSS and JS imports
* [Admin/calendar.php L251-L371](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L251-L371)  - Calendar initialization and configuration

Sources: [Admin/calendar.php L73-L81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L73-L81)

 [Admin/calendar.php L251-L371](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L251-L371)

---

## Data Table Management

### DataTables

DataTables transforms standard HTML tables into feature-rich interactive components with sorting, searching, and pagination.

**Version:** Unspecified (jQuery 3.5.1 compatible)
**Loading Method:** Local files
**Dependencies:** jQuery 3.5.1

#### DataTables File Structure

```

```

**File Paths:**

* CSS: `../src/js/lib/datatable/css/jquery.dataTables.min.css`
* Responsive CSS: `../src/js/lib/datatable/css/responsive.dataTables.min.css`
* jQuery: `../src/js/lib/datatable/js/jquery-3.5.1.js`
* Core JS: `../src/js/lib/datatable/js/jquery.dataTables.min.js`
* Responsive JS: `../src/js/lib/datatable/js/dataTables.responsive.min.js`
* Initialization: `../src/js/lib/datatable/datatable.js`

**Table Configuration:**

* Table ID: `#example` (standardized across pages)
* Classes: `table table-striped nowrap responsive`
* Features: Sorting, searching, pagination, responsive columns

**Columns Displayed (Appointments):**

1. Nombre completo (Full name)
2. Edad (Age)
3. Consulta (Consultation type)
4. Fecha (Date)
5. Hora (Time)
6. Estado (Status: Realizada/Pendiente)
7. Diagnóstico (Diagnosis)
8. Edit button (realizar_consulta.php link)
9. View report button (informe.php link)
10. Delete button (anular action)

**Usage Locations:**

* [Admin/inicioAdmin.php L40-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L40-L41)  - CSS imports
* [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)  - Table HTML structure
* [Admin/inicioAdmin.php L160-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L160-L163)  - JS imports and initialization

Sources: [Admin/inicioAdmin.php L40-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L40-L41)

 [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 [Admin/inicioAdmin.php L160-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L160-L163)

---

## PDF Generation System

### TCPDF

TCPDF is a PHP library for generating PDF documents, used extensively for creating clinical history and medical report PDFs.

**Version:** Unspecified (Version 6.x based on API usage)
**Loading Method:** Local include
**File Path:** `../Reportes/tcpdf/tcpdf.php`

**Class Instantiation:**

```

```

**Core Methods Used:**

| Method | Purpose | Example Usage |
| --- | --- | --- |
| `SetCreator()` | Set PDF creator metadata | `$pdf->SetCreator(PDF_CREATOR);` |
| `SetAuthor()` | Set document author | `$pdf->SetAuthor('Sistema Odontológico');` |
| `SetTitle()` | Set document title | `$pdf->SetTitle('Historia Clínica');` |
| `setPrintHeader()` | Toggle default header | `$pdf->setPrintHeader(false);` |
| `setPrintFooter()` | Toggle default footer | `$pdf->setPrintFooter(false);` |
| `SetMargins()` | Set page margins | `$pdf->SetMargins(15, 15, 15);` |
| `SetAutoPageBreak()` | Enable page breaks | `$pdf->SetAutoPageBreak(true, 15);` |
| `AddPage()` | Add new page | `$pdf->AddPage();` |
| `SetFillColor()` | Set fill color (RGB) | `$pdf->SetFillColor(230, 240, 255);` |
| `SetTextColor()` | Set text color (RGB) | `$pdf->SetTextColor(33, 37, 41);` |
| `SetFont()` | Set font family/style/size | `$pdf->SetFont('helvetica', 'B', 16);` |
| `Cell()` | Output single-line cell | `$pdf->Cell(150, 10, 'HISTORIA CLÍNICA', 0, 1, 'C');` |
| `MultiCell()` | Output multi-line cell | `$pdf->MultiCell(70, 35, $text, 1, 'L', 1);` |
| `Image()` | Insert image | `$pdf->Image('../src/img/logo.png', 15, 10, 30, 0);` |
| `Rect()` | Draw rectangle | `$pdf->Rect(0, 0, 210, 35, 'F');` |
| `Line()` | Draw line | `$pdf->Line(15, 35, 195, 35);` |
| `Output()` | Generate PDF output | `$pdf->Output('filename.pdf', 'D');` |

**Output Modes:**

* `'D'` - Download (force browser download dialog)
* `'I'` - Inline (display in browser)
* `'S'` - String (return as string for email attachments)

**Document Types Generated:**

1. **Clinical History PDF** (`descargar_historia.php`) * Patient demographics and anamnesis * Medical history checklist * Appointment details * Medical alerts * Signature/fingerprint area
2. **Medical Report PDF** (`generate_informe_pdf.php`) * Clinical examinations (intraoral, extraoral, ATM) * Radiographic descriptions * Treatment plans * Diagnosis and prognosis * Medical images (radiographs, oral photos)

**Usage Locations:**

* [Admin/descargar_historia.php L85-L88](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L85-L88)  - Library verification and include
* [Admin/descargar_historia.php L91-L292](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L91-L292)  - PDF generation implementation

**Note:** For comprehensive TCPDF API documentation, see [TCPDF Library Reference](/axchisan/Consultorio_Emily_Bernal/9-tcpdf-library-reference).

Sources: [Admin/descargar_historia.php L85-L292](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L85-L292)

---

## Icon System

### FontAwesome

FontAwesome provides vector icon fonts used throughout the administrative interface for visual elements and action buttons.

**Version:** Unspecified (local installation)
**Loading Method:** Local CSS
**File Path:** `../src/css/lib/fontawesome/css/all.css`

#### Icons Used by Category

**Navigation Icons:**

* `far fa-calendar-check` - Appointments/Citas menu item
* `fas fa-user-md` - Doctors/Dentistas menu item
* `far fa-calendar-alt` - Calendar and Clinical History menu items
* `fas fa-sign-out-alt` - Logout button

**Action Icons:**

* `fas fa-edit` - Edit appointment button
* `fas fa-file-alt` - View medical report button
* `fas fa-trash` - Delete/anular appointment button
* `fa fa-times` - Close alert notification button

**Icon Syntax Examples:**

```

```

**Usage Locations:**

* [Admin/calendar.php L72](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L72-L72)  - CSS import
* [Admin/inicioAdmin.php L39](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L39-L39)  - CSS import
* Navigation menus, action buttons throughout all admin pages

Sources: [Admin/calendar.php L72](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L72-L72)

 [Admin/inicioAdmin.php L39](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L39-L39)

---

## Custom JavaScript Modules

### admin.js

Custom JavaScript for sidebar toggle functionality and general administrative interface behaviors.

**File Path:** `../src/js/admin.js`
**Primary Functions:**

* Sidebar collapse/expand toggle
* Menu navigation state management
* General UI enhancements

**Usage Locations:**

* [Admin/calendar.php L228](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L228-L228)
* [Admin/inicioAdmin.php L159](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L159-L159)

### datatable.js

Custom initialization script for DataTables configuration specific to the system's needs.

**File Path:** `../src/js/lib/datatable/datatable.js`
**Purpose:**

* Standardized DataTables initialization
* Custom configuration options
* Responsive behavior settings

**Usage Locations:**

* [Admin/inicioAdmin.php L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L163-L163)

Sources: [Admin/calendar.php L228](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L228-L228)

 [Admin/inicioAdmin.php L159-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L159-L163)

---

## Dependency Hierarchy and Loading Order

The following diagram illustrates the loading order and dependencies between libraries across the administrative pages:

### Calendar Page Dependencies

```

```

### Dashboard Page Dependencies

```

```

Sources: [Admin/calendar.php L70-L81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L70-L81)

 [Admin/inicioAdmin.php L36-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L163)

---

## PHP Server-Side Dependencies

### conexionDB.php

Database connection abstraction module providing MySQL connectivity.

**File Path:** `../php/conexionDB.php`
**Purpose:** Establishes and manages database connections using MySQLi
**Global Variable:** `$link` (database connection handle)

**Usage Pattern:**

```

```

### consultas.php

Query abstraction layer providing reusable database functions.

**File Path:** `../php/consultas.php`
**Key Functions:**

* `consultarDoctor($link, $id_doctor)` - Retrieve doctor information
* `consultarPaciente($link, $id_paciente)` - Retrieve patient information
* `MostrarCitas($link, $id_doctor)` - Retrieve appointments for doctor
* `validarToken($link, $id, $tipo, $token)` - Validate session tokens

**Usage Pattern:**

```

```

**Dependencies:** Requires `conexionDB.php` to be included first

**Usage Locations:**

* [Admin/calendar.php L5-L6](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L5-L6)  - Includes for database operations
* [Admin/descargar_historia.php L5-L6](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L5-L6)  - Includes for PDF data retrieval
* [Admin/inicioAdmin.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L4)  - Includes for dashboard data

Sources: [Admin/calendar.php L5-L6](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L5-L6)

 [Admin/descargar_historia.php L5-L6](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L5-L6)

 [Admin/inicioAdmin.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L4)

---

## CDN vs Local Resource Strategy

The system employs a mixed strategy for resource loading:

### CDN Resources (calendar.php)

* **Advantages:** * Faster initial load from geographically distributed servers * Browser caching across sites * Reduced server bandwidth
* **Libraries:** * Bootstrap 4.0.0 CSS/JS * jQuery 3.2.1 * Popper.js 1.12.9

### Local Resources (inicioAdmin.php and others)

* **Advantages:** * No external dependencies * Works offline * Version control * Consistent performance
* **Libraries:** * Bootstrap CSS/JS (local copy) * jQuery (multiple versions) * DataTables (complete) * FullCalendar (complete) * FontAwesome (complete) * TCPDF (complete)

### Resource Loading Pattern

```

```

Sources: [Admin/calendar.php L70-L81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L70-L81)

 [Admin/inicioAdmin.php L36-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L163)

 [Admin/descargar_historia.php L85-L88](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L85-L88)

---

## Version Compatibility Matrix

| Library | Version | Requires | Compatible With | Notes |
| --- | --- | --- | --- | --- |
| Bootstrap CSS | 4.0.0 | - | jQuery 3.2.1+, Popper.js 1.12.9 | CDN and local |
| Bootstrap JS | 4.0.0 | jQuery 3.2.1+, Popper.js 1.12.9 | - | CDN and local |
| jQuery | 3.2.1 | - | Bootstrap 4.0.0, FullCalendar | Calendar page |
| jQuery | 3.5.1 | - | DataTables | Dashboard page |
| Popper.js | 1.12.9 | - | Bootstrap 4.0.0 | Bootstrap tooltips |
| FullCalendar | Legacy | jQuery 3.2.1, Moment.js | - | Spanish locale |
| Moment.js | Unspecified | - | FullCalendar | Date manipulation |
| DataTables | Modern | jQuery 3.5.1 | Responsive extension | Local files |
| DataTables Responsive | Modern | DataTables core | - | Mobile support |
| FontAwesome | Unspecified | - | - | All CSS (all.css) |
| TCPDF | 6.x | PHP 5.3+ | - | UTF-8 support |

**Known Issues:**

* jQuery version mismatch between pages (3.2.1 vs 3.5.1 vs unversioned)
* FullCalendar using legacy API (older version)
* No explicit version pinning for some local libraries

**Recommendations:**

* Standardize jQuery version across all pages
* Consider upgrading FullCalendar to v5+
* Document specific TCPDF version in use
* Add version comments in library files

Sources: [Admin/calendar.php L70-L81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L70-L81)

 [Admin/inicioAdmin.php L36-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L163)

---

## Library File Locations

Complete directory structure for external dependencies:

```
src/
├── css/
│   ├── lib/
│   │   ├── bootstrap/
│   │   │   └── css/
│   │   │       └── bootstrap.min.css
│   │   └── fontawesome/
│   │       └── css/
│   │           └── all.css
│   ├── fullcalendar.css
│   ├── admin.css
│   └── custom_styles.css
│
├── js/
│   ├── lib/
│   │   ├── bootstrap/
│   │   │   └── js/
│   │   │       └── bootstrap.min.js
│   │   ├── datatable/
│   │   │   ├── css/
│   │   │   │   ├── jquery.dataTables.min.css
│   │   │   │   └── responsive.dataTables.min.css
│   │   │   └── js/
│   │   │       ├── jquery-3.5.1.js
│   │   │       ├── jquery.dataTables.min.js
│   │   │       └── dataTables.responsive.min.js
│   │   │   └── datatable.js
│   │   └── FullCalendar/
│   │       ├── moment.min.js
│   │       ├── fullcalendar.min.js
│   │       └── locale/
│   │           └── es.js
│   ├── jquery.js
│   └── admin.js
│
Reportes/
└── tcpdf/
    └── tcpdf.php
```

Sources: [Admin/calendar.php L70-L81](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/calendar.php#L70-L81)

 [Admin/inicioAdmin.php L36-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L163)

 [Admin/descargar_historia.php L85-L88](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/descargar_historia.php#L85-L88)