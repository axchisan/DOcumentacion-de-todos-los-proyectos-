# DataTables Implementation

> **Relevant source files**
> * [Admin/historia_clinica.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php)
> * [Admin/inicioAdmin.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php)

## Purpose and Scope

This document describes the DataTables JavaScript library integration used to provide interactive, searchable, sortable tabular displays of appointments and clinical histories in the administrative interfaces. DataTables enhances standard HTML tables with pagination, search functionality, column sorting, and responsive behavior.

The system implements DataTables in two primary locations: the appointment management dashboard and the clinical history list. For information about the overall frontend architecture, see [Frontend Components & UI](/axchisan/Consultorio_Emily_Bernal/7-frontend-components-and-ui). For details about Bootstrap styling applied to these tables, see [Bootstrap Integration & Layout](/axchisan/Consultorio_Emily_Bernal/7.1-bootstrap-integration-and-layout).

---

## DataTables Architecture Overview

**DataTables Library Components**

```

```

**Sources:** [Admin/inicioAdmin.php L40-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L40-L41)

 [Admin/inicioAdmin.php L160-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L160-L163)

 [Admin/historia_clinica.php L40-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L40-L41)

 [Admin/historia_clinica.php L136-L139](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L136-L139)

---

## File Dependencies and Loading Order

DataTables requires a specific loading sequence to function correctly. The system loads dependencies in the following order:

| Load Order | Resource Type | File Path | Purpose |
| --- | --- | --- | --- |
| 1 | CSS | `../src/js/lib/datatable/css/jquery.dataTables.min.css` | Core DataTables styling |
| 2 | CSS | `../src/js/lib/datatable/css/responsive.dataTables.min.css` | Responsive extension styling |
| 3 | JavaScript | `../src/js/jquery.js` | jQuery core library |
| 4 | JavaScript | `../src/js/lib/datatable/js/jquery-3.5.1.js` | jQuery version for DataTables |
| 5 | JavaScript | `../src/js/lib/datatable/js/jquery.dataTables.min.js` | DataTables core library |
| 6 | JavaScript | `../src/js/lib/datatable/js/dataTables.responsive.min.js` | Responsive extension |
| 7 | JavaScript | `../src/js/lib/datatable/datatable.js` | Custom initialization script |

**Sources:** [Admin/inicioAdmin.php L40-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L40-L41)

 [Admin/inicioAdmin.php L157-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L163)

 [Admin/historia_clinica.php L40-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L40-L41)

 [Admin/historia_clinica.php L133-L139](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L133-L139)

---

## Appointments Table Implementation

**Initialization and Structure**

The appointments table on `inicioAdmin.php` displays all pending and completed appointments for the logged-in doctor. The table uses the ID `example` and implements sorting, searching, and pagination.

```

```

**Column Configuration - Appointments Table**

| Column Index | Header | Data Source | Content Type | Actions |
| --- | --- | --- | --- | --- |
| 0 | Nombre completo | `$row['nombre']` + `$row['apellido']` | Text | Searchable, Sortable |
| 1 | Edad | `$row['años']` | Numeric | Searchable, Sortable |
| 2 | Consulta | `$row['tipo']` | Text | Searchable, Sortable |
| 3 | Fecha | `$row['fecha_cita']` | Date | Searchable, Sortable |
| 4 | Hora | `$row['hora_cita']` | Time | Searchable, Sortable |
| 5 | Estado | Conditional: `$row['estado']` | Text (Realizada/Pendiente) | Searchable, Sortable |
| 6 | Diagnóstico | `$row['descripcion']` | Text | Searchable, Sortable |
| 7 | (Actions) | Edit link | Button | Not searchable |
| 8 | (Actions) | View report link | Button | Not searchable |
| 9 | (Actions) | Delete link | Button | Not searchable |

**Sources:** [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

**Table Structure Code Reference**

The table element is defined at [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 with:

* Table ID: `example`
* Classes: `table table-striped nowrap responsive`
* Header definition: [Admin/inicioAdmin.php L104-L117](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L104-L117)
* Body population: [Admin/inicioAdmin.php L118-L132](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L118-L132)  using `mysqli_fetch_array()` loop
* Action buttons: [Admin/inicioAdmin.php L128-L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L130)  (Edit, View Report, Delete)

---

## Clinical History Table Implementation

**Initialization and Structure**

The clinical history table on `historia_clinica.php` displays completed appointments with patient medical records. Unlike the appointments table, this implementation focuses on read-only data with actions to view details or download PDFs.

```

```

**Column Configuration - Clinical History Table**

| Column Index | Header | Data Source | Content Type | Actions |
| --- | --- | --- | --- | --- |
| 0 | Nombre completo | `$row['nombre']` + `$row['apellido']` | Text | Searchable, Sortable |
| 1 | Edad | `$row['años']` ?? 'N/A' | Numeric | Searchable, Sortable |
| 2 | Consulta | `$row['tipo']` ?? 'N/A' | Text | Searchable, Sortable |
| 3 | Fecha | `$row['fecha_cita']` ?? 'N/A' | Date | Searchable, Sortable |
| 4 | Hora | `$row['hora_cita']` ?? 'N/A' | Time | Searchable, Sortable |
| 5 | Estado | Static: "Realizada" | Text | Searchable, Sortable |
| 6 | Diagnóstico | `$row['descripcion']` ?? 'N/A' | Text | Searchable, Sortable |
| 7 | Ver Historia | View link | Button | Not searchable |
| 8 | Descargar PDF | PDF form | Button | Not searchable |

**Sources:** [Admin/historia_clinica.php L87-L122](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L122)

**Table Structure Code Reference**

The table element is defined at [Admin/historia_clinica.php L87-L122](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L122)

 with:

* Table ID: `example` (same as appointments table)
* Classes: `table table-striped nowrap responsive`
* Header definition: [Admin/historia_clinica.php L88-L100](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L88-L100)
* Body population: [Admin/historia_clinica.php L101-L120](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L101-L120)  using `mysqli_fetch_array()` loop
* View action: [Admin/historia_clinica.php L111](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L111-L111)  with parameters `id_cita` and `id_paciente`
* PDF download: [Admin/historia_clinica.php L113-L117](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L113-L117)  using POST form submission

---

## DataTables Initialization

**Initialization Script Location**

Both tables are initialized by the same external JavaScript file: `datatable.js` located at `../src/js/lib/datatable/datatable.js`. This file is included at [Admin/inicioAdmin.php L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L163-L163)

 and [Admin/historia_clinica.php L139](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L139-L139)

**Expected Initialization Pattern**

While the actual `datatable.js` file is not provided in the sources, the standard DataTables initialization for an element with ID `example` would follow this pattern:

```

```

**DataTables Features Enabled**

```

```

**Sources:** [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

---

## Responsive Behavior

**Responsive Extension Configuration**

Both tables include the responsive extension via [Admin/inicioAdmin.php L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L41-L41)

 and [Admin/historia_clinica.php L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L41-L41)

 The responsive extension automatically adjusts column visibility based on screen size.

**CSS Classes for Responsive Tables**

The tables use the following class combination at [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 and [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

:

* `table`: Base Bootstrap table styling
* `table-striped`: Alternating row background colors
* `nowrap`: Prevents text wrapping in cells
* `responsive`: Enables DataTables responsive extension

**Column Priority Behavior**

When the viewport width decreases, the responsive extension collapses columns in reverse order (rightmost first), preserving the most important information. Action columns (buttons) typically collapse first, followed by less critical data columns.

```

```

**Sources:** [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

 [Admin/inicioAdmin.php L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L41-L41)

 [Admin/historia_clinica.php L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L41-L41)

---

## Search and Filter Functionality

**Global Search Implementation**

DataTables automatically generates a global search input that filters across all searchable columns. The search is case-insensitive and searches for partial matches.

**Searchable vs Non-Searchable Columns**

| Table | Searchable Columns | Non-Searchable Columns |
| --- | --- | --- |
| Appointments | Name, Age, Consult, Date, Time, Status, Diagnosis | Action buttons (Edit, View, Delete) |
| Clinical History | Name, Age, Consult, Date, Time, Status, Diagnosis | Action buttons (View, Download PDF) |

**Search Behavior Flow**

```

```

**Sources:** [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 [Admin/historia_clinica.php L87-L122](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L122)

---

## Sorting and Ordering

**Column Sorting Capabilities**

All columns in both tables are sortable by default. Users can click column headers to toggle between ascending, descending, and no sort order.

**Default Sort Order**

While not explicitly configured in the visible code, the typical default sort order would be by appointment date (column index 3) in descending order, showing the most recent appointments first.

**Sort State Indicators**

DataTables displays visual indicators in column headers:

* Unsorted: Two-arrow icon (▲▼)
* Ascending: Up arrow (▲)
* Descending: Down arrow (▼)

**Multi-Column Sorting**

DataTables supports multi-column sorting when users hold Shift and click additional column headers, allowing complex sort operations like:

1. Sort by Date (descending)
2. Then by Time (ascending) for same-date appointments
3. Then by Patient Name (ascending) for same-time appointments

**Sources:** [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 [Admin/historia_clinica.php L87-L122](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L122)

---

## Pagination Configuration

**Pagination Controls**

DataTables automatically generates pagination controls below the table, including:

* Previous/Next buttons
* Page number buttons
* "First" and "Last" page shortcuts
* Page size selector (entries per page)

**Default Pagination Settings**

The expected default configuration (standard DataTables behavior):

* Page length: 10 rows per page
* Page length options: 10, 25, 50, 100, All
* Pagination type: Simple numbers with previous/next

**Information Display**

The table footer displays information about the current view:

* "Showing 1 to 10 of 57 entries" (example)
* "Showing 1 to 10 of 57 entries (filtered from 100 total entries)" when search is active

```

```

**Sources:** [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 [Admin/historia_clinica.php L87-L122](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L122)

---

## Bootstrap Integration

**CSS Class Compatibility**

DataTables seamlessly integrates with Bootstrap styling through the class attributes on the table element at [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 and [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

:

```

```

**Bootstrap Classes Applied**

| Class | Purpose | Visual Effect |
| --- | --- | --- |
| `table` | Base Bootstrap table styling | Border, spacing, typography |
| `table-striped` | Zebra-striping | Alternating row background colors |
| `nowrap` | Prevent text wrapping | Keeps cell content on single line |
| `responsive` | DataTables responsive class | Triggers responsive behavior |

**Button Styling in Action Columns**

Action buttons within table cells use Bootstrap button classes:

* Appointments table: [Admin/inicioAdmin.php L128-L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L130) * Edit: `class="button is-info"` with FontAwesome edit icon * View: `class="btn btn-success"` with FontAwesome file icon * Delete: `class="button text-danger"` with FontAwesome trash icon
* Clinical history table: [Admin/historia_clinica.php L111-L117](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L111-L117) * View: `class="btn btn-info"` * Download: `class="btn btn-success"`

**Sources:** [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 [Admin/inicioAdmin.php L128-L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L130)

 [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

 [Admin/historia_clinica.php L111-L117](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L111-L117)

---

## Data Flow and Server-Side Integration

**PHP to DataTables Data Flow**

```

```

**Server-Side Rendering Pattern**

The system uses server-side rendering (not DataTables server-side processing). The complete table HTML is generated by PHP before page delivery:

1. **Database Query**: [Admin/inicioAdmin.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L27-L27)  calls `MostrarCitas($link, $vUsuario)` or [Admin/historia_clinica.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L27-L27)  calls `MostrarCitasCompletadas($link, $vUsuario)`
2. **Result Iteration**: [Admin/inicioAdmin.php L119-L132](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L119-L132)  or [Admin/historia_clinica.php L102-L120](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L102-L120)  loops through results
3. **HTML Generation**: Each row generates `<tr>` with `<td>` elements containing data
4. **Client Enhancement**: After page load, DataTables enhances the existing HTML table

**No AJAX Data Loading**

Unlike some DataTables implementations, this system does not use AJAX to load table data. All data is rendered server-side during initial page generation. This approach:

* Simplifies implementation
* Works well for moderate data volumes
* Ensures data is available even if JavaScript fails
* Integrates naturally with PHP session-based authentication

**Sources:** [Admin/inicioAdmin.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L27-L27)

 [Admin/inicioAdmin.php L119-L132](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L119-L132)

 [Admin/historia_clinica.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L27-L27)

 [Admin/historia_clinica.php L102-L120](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L102-L120)

---

## Action Buttons and Event Handlers

**Appointments Table Actions**

The appointments table includes three action buttons per row at [Admin/inicioAdmin.php L128-L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L130)

:

1. **Edit Button**: * Icon: `<i class="fas fa-edit"></i>` * Link: `./realizar_consulta.php?accion=UDT&id=<?php echo $row['id_cita']; ?>` * Tooltip: "Editar" * Purpose: Navigate to consultation form for editing
2. **View Report Button**: * Icon: `<i class="fas fa-file-alt"></i>` * Link: `./informe.php?id=<?php echo $row['id_paciente']; ?>` * Tooltip: "Ver Informe" * Purpose: Navigate to medical report editor
3. **Delete Button**: * Icon: `<i class="fas fa-trash"></i>` * Link: `../crud/realizar_consultasUPDATE.php?accion=DLT&id=<?php echo $row['id_cita']; ?>&estado=<?php echo $row['estado']; ?>` * Tooltip: "Anular" * JavaScript: `onclick="return confirmation()"` * Purpose: Cancel appointment with confirmation

**Clinical History Table Actions**

The clinical history table includes two actions per row:

1. **View Button** at [Admin/historia_clinica.php L111](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L111-L111) : * Class: `btn btn-info` * Link: `ver_historia.php?id_cita=<?php echo $row['id_cita']; ?>&id_paciente=<?php echo $row['id_paciente']; ?>` * Purpose: Navigate to detailed history viewer
2. **Download PDF Button** at [Admin/historia_clinica.php L113-L117](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L113-L117) : * Method: POST form submission * Action: `descargar_historia.php` * Parameters: `id_cita`, `id_paciente` (hidden inputs) * Class: `btn btn-success` * Purpose: Generate and download clinical history PDF

**Delete Confirmation Handler**

The confirmation function is defined at [Admin/inicioAdmin.php L44-L47](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L44-L47)

:

```

```

This function displays a browser confirmation dialog before allowing deletion to proceed.

**Sources:** [Admin/inicioAdmin.php L44-L47](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L44-L47)

 [Admin/inicioAdmin.php L128-L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L130)

 [Admin/historia_clinica.php L111](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L111-L111)

 [Admin/historia_clinica.php L113-L117](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L113-L117)

---

## Common Table Identifier Pattern

**Shared Table ID Issue**

Both implementations use the same table ID `example` at [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 and [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

 This works because:

* Each table appears on a separate page
* JavaScript initialization occurs independently per page
* No ID conflicts occur since pages are not loaded simultaneously

**Table Identifier Mapping**

| Page | Table ID | HTML Element | Purpose |
| --- | --- | --- | --- |
| `inicioAdmin.php` | `example` | `<table id="example">` | Appointments management |
| `historia_clinica.php` | `example` | `<table id="example">` | Clinical history records |

**JavaScript Selector**

The initialization script uses jQuery selector `$('#example')` to target the table element. The `datatable.js` file at [Admin/inicioAdmin.php L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L163-L163)

 and [Admin/historia_clinica.php L139](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L139-L139)

 contains this initialization logic.

**Alternative Implementation Pattern**

For a more maintainable codebase, consider using unique IDs:

* `#appointments-table` for appointments
* `#clinical-history-table` for clinical histories

This would require updating both the HTML element IDs and the JavaScript initialization selectors.

**Sources:** [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 [Admin/historia_clinica.php L87](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L87-L87)

 [Admin/inicioAdmin.php L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L163-L163)

 [Admin/historia_clinica.php L139](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L139-L139)

---

## Performance Considerations

**Client-Side Processing Limitations**

The current implementation loads all table data into the DOM at once during server-side rendering. This approach has performance implications:

**Scalability Analysis**

| Data Volume | Performance | User Experience |
| --- | --- | --- |
| < 100 rows | Excellent | Instant response |
| 100-500 rows | Good | Minor delay on page load |
| 500-1000 rows | Acceptable | Noticeable page load time |
| > 1000 rows | Poor | Slow page load, sluggish interactions |

**Current Data Set Characteristics**

Based on the table structure:

* Appointments table: Displays pending and completed appointments for a single doctor
* Clinical history table: Displays only completed appointments with medical reports
* Data scoped by `id_doctor`: Limits result set to one doctor's patients

**Optimization Opportunities**

For larger data sets, consider:

1. **DataTables Server-Side Processing**: Enable AJAX-based data loading with server-side pagination
2. **Date Range Filtering**: Limit initial query to recent appointments (e.g., last 90 days)
3. **Lazy Loading**: Load additional data on demand
4. **Indexing**: Ensure database indexes on `id_doctor`, `fecha_cita`, and `estado` columns

**Memory Usage**

The current pattern generates:

* Full HTML table in PHP memory
* Complete DOM structure in browser memory
* DataTables internal data structures (duplicate data)

For typical clinic usage (one doctor, 50-200 appointments), this overhead is acceptable.

**Sources:** [Admin/inicioAdmin.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L27-L27)

 [Admin/inicioAdmin.php L119-L132](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L119-L132)

 [Admin/historia_clinica.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L27-L27)

 [Admin/historia_clinica.php L102-L120](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L102-L120)