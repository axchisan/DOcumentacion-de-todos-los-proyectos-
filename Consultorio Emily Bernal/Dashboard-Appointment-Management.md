# Dashboard - Appointment Management

> **Relevant source files**
> * [Admin/inicioAdmin.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php)

## Purpose and Scope

The Dashboard (implemented in `inicioAdmin.php`) serves as the primary landing page for authenticated doctors in the Consultorio Emily Bernal system. This page displays all appointments (citas) associated with the logged-in doctor, providing a centralized interface for viewing appointment details and performing CRUD operations. The dashboard uses DataTables for interactive data presentation and implements the session-based flash messaging pattern for user feedback.

For information about the session validation and token checking mechanisms used by this page, see [5.1](/axchisan/Consultorio_Emily_Bernal/5.1-session-management) and [5.2](/axchisan/Consultorio_Emily_Bernal/5.2-token-validation-system). For details about the clinical history workflow that this dashboard links to, see [2.3](/axchisan/Consultorio_Emily_Bernal/2.3-clinical-history-management). For consultation editing functionality, see [2.4](/axchisan/Consultorio_Emily_Bernal/2.4-consultation-and-diagnosis-interface).

## Page Initialization and Security

The dashboard implements a multi-layered security check before rendering any content. The initialization sequence validates both session existence and token authenticity to prevent unauthorized access and concurrent logins.

```mermaid
flowchart TD

Start["inicioAdmin.php<br>page load"]
SessionStart["session_start()"]
CheckSession["isset($_SESSION['id_doctor'])<br>AND<br>isset($_SESSION['session_token'])"]
SetError["$_SESSION['MensajeTexto']<br>'Error acceso al sistema'"]
RedirectIndex["header('Location: ../index.php')"]
ExtractUser["$vUsuario = $_SESSION['id_doctor']"]
ValidateToken["validarToken($link, $vUsuario,<br>'Doctor', $_SESSION['session_token'])"]
DestroySession["session_unset()<br>session_destroy()"]
SetConcurrentError["$_SESSION['MensajeTexto']<br>'Tu sesión ha sido cerrada'"]
QueryDoctor["consultarDoctor($link, $vUsuario)"]
QueryCitas["MostrarCitas($link, $vUsuario)"]
RenderPage["Render HTML with<br>sidebar and DataTable"]
End["Exit"]

Start --> SessionStart
SessionStart --> CheckSession
CheckSession --> SetError
SetError --> RedirectIndex
CheckSession --> ExtractUser
ExtractUser --> ValidateToken
ValidateToken --> DestroySession
DestroySession --> SetConcurrentError
SetConcurrentError --> RedirectIndex
ValidateToken --> QueryDoctor
QueryDoctor --> QueryCitas
QueryCitas --> RenderPage
RedirectIndex --> End
RenderPage --> End
```

**Security Check Implementation:**

| Check Type | Code Location | Purpose |
| --- | --- | --- |
| Session existence | [Admin/inicioAdmin.php L7-L12](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L7-L12) | Ensures user has authenticated |
| Token validation | [Admin/inicioAdmin.php L17-L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L17-L24) | Prevents concurrent logins from multiple devices |
| Database connection | [Admin/inicioAdmin.php L3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L3) | Establishes connection via `conexionDB.php` |
| User data retrieval | [Admin/inicioAdmin.php L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L26-L26) | Fetches doctor profile for sidebar display |
| Appointment query | [Admin/inicioAdmin.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L27-L27) | Retrieves appointments specific to this doctor |

The `validarToken()` function call at [Admin/inicioAdmin.php L17](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L17-L17)

 compares the session token against the database-stored token. If tokens mismatch (indicating a new login occurred elsewhere), the current session is destroyed and the user is redirected to the login page with an explanatory message.

**Sources:** [Admin/inicioAdmin.php L1-L28](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L28)

## Data Retrieval and Query Functions

The dashboard retrieves data using two primary query functions from the `consultas.php` abstraction layer. These functions encapsulate the SQL logic and return result sets for rendering.

```mermaid
flowchart TD

ConsultarDoctor["consultarDoctor($link, $vUsuario)"]
MostrarCitas["MostrarCitas($link, $vUsuario)"]
DoctorTable["doctor table"]
CitasTable["citas table"]
PacienteTable["pacientes table"]
ConsultasTable["consultas table"]
Row["$row<br>(doctor profile)"]
ResultadoCitas["$resultadoCitas<br>(mysqli_result)"]
ProfileDisplay["Profile image & name<br>lines 59-64"]
TableRows["Table tbody<br>lines 119-132"]

ConsultarDoctor --> DoctorTable
MostrarCitas --> CitasTable
MostrarCitas --> PacienteTable
MostrarCitas --> ConsultasTable
DoctorTable --> Row
CitasTable --> ResultadoCitas
PacienteTable --> ResultadoCitas
ConsultasTable --> ResultadoCitas
Row --> ProfileDisplay
ResultadoCitas --> TableRows

subgraph subGraph2 ["Page Variables"]
    Row
    ResultadoCitas
end

subgraph subGraph1 ["Database Tables"]
    DoctorTable
    CitasTable
    PacienteTable
    ConsultasTable
end

subgraph subGraph0 ["Query Functions"]
    ConsultarDoctor
    MostrarCitas
end
```

**Query Function Details:**

| Function | Return Value | Usage | Line Reference |
| --- | --- | --- | --- |
| `consultarDoctor()` | Associative array | Populates sidebar with doctor name, gender, and profile image | [Admin/inicioAdmin.php L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L26-L26) |
| `MostrarCitas()` | `mysqli_result` object | Provides appointment data for DataTable rendering | [Admin/inicioAdmin.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L27-L27) |

The `MostrarCitas()` function performs a JOIN query across multiple tables to retrieve:

* Patient name and age (`pacientes.nombre`, `pacientes.apellido`, calculated age)
* Consultation type (`consultas.tipo`)
* Appointment date and time (`citas.fecha_cita`, `citas.hora_cita`)
* Appointment status (`citas.estado`: 'A' = Realizada, 'P' = Pendiente)
* Diagnosis description (`citas.descripcion`)
* Patient and appointment IDs for action links

The query filters results by `id_doctor` to ensure doctors only see their own appointments, implementing data-level access control.

**Sources:** [Admin/inicioAdmin.php L26-L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L26-L27)

 [php/consultas.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/consultas.php)

## User Interface Structure

The dashboard implements a two-column layout using Bootstrap's grid system with a fixed sidebar and scrollable main content area.

### Sidebar Navigation

The sidebar component displays the doctor's profile and navigation menu. It uses conditional rendering to display gender-appropriate profile images.

```mermaid
flowchart TD

Sidebar["aside.sidebar<br>lines 51-77"]
Toggle["Burger menu toggle<br>lines 52-56"]
SideInner["div.side-inner<br>lines 57-76"]
Profile["div.profile<br>lines 58-66"]
NavMenu["div.nav-menu<br>lines 67-75"]
GenderCheck["$row['sexo']<br>== 'Masculino'?"]
MaleImg["img: odontologo.png<br>line 60"]
FemaleImg["img: odontologa.png<br>line 62"]
DoctorName["h3.name<br>utf8_decode(nombreD + apellido)<br>line 64"]
Location["span.country<br>'Barbosa Santander'<br>line 65"]
NavLinks["Navigation Links"]
Link1["Citas (inicioAdmin.php)<br>line 69"]
Link2["Dentistas (doctores.php)<br>line 70"]
Link3["Calendario (calendar.php)<br>line 71"]
Link4["Historia Clínica<br>(historia_clinica.php)<br>line 72"]
Link5["Cerrar sesión<br>(../php/cerrar.php)<br>line 73"]

Sidebar --> Toggle
Sidebar --> SideInner
SideInner --> Profile
SideInner --> NavMenu
Profile --> GenderCheck
GenderCheck --> MaleImg
GenderCheck --> FemaleImg
Profile --> DoctorName
Profile --> Location
NavMenu --> NavLinks
NavLinks --> Link1
NavLinks --> Link2
NavLinks --> Link3
NavLinks --> Link4
NavLinks --> Link5
```

**Navigation Menu Structure:**

| Link Text | Destination | Icon | Purpose |
| --- | --- | --- | --- |
| Citas | `inicioAdmin.php` | `far fa-calendar-check` | View appointments (current page) |
| Dentistas | `doctores.php` | `fas fa-user-md` | Manage doctor records |
| Calendario | `calendar.php` | `far fa-calendar-alt` | View/manage schedule |
| Historia Clínica | `historia_clinica.php` | `far fa-calendar-alt` | Access clinical histories |
| Cerrar sesión | `../php/cerrar.php` | `fas fa-sign-out-alt` | Logout |

The profile image selection logic at [Admin/inicioAdmin.php L59-L63](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L59-L63)

 uses a PHP conditional to display `odontologo.png` for male doctors and `odontologa.png` for female doctors based on the `sexo` field from the database.

**Sources:** [Admin/inicioAdmin.php L51-L77](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L51-L77)

### Main Content Area

The main content area displays appointments in a DataTable with action buttons for each row.

```mermaid
flowchart TD

Main["main.bg.bg-white<br>line 79"]
SiteSection["div.site-section<br>line 80"]
Container["div.container<br>line 81"]
Row["div.row.justify-content-center<br>line 82"]
Col["div.col-md-12<br>line 83"]
ContentBox["div.content-box-large<br>line 84"]
Breadcrumb["ol.breadcrumb<br>'Inicio'<br>lines 85-87"]
PanelBody["div.panel-body<br>line 88"]
InfoRow["div.row<br>line 89"]
ColInfo["div.col-md-12.text-info<br>line 90"]
Header["div.bg-primary<br>'Citas'<br>line 91"]
MessageArea["Flash message display<br>lines 92-102"]
DataTable["table#example<br>lines 103-134"]
CheckMessage["isset($_SESSION['MensajeTexto'])"]
DisplayAlert["div.alert with<br>$_SESSION['MensajeTipo']<br>lines 94-97"]
NoMessage["(no alert shown)"]
TableHead["thead with 10 columns<br>lines 104-117"]
TableBody["tbody with while loop<br>lines 118-133"]

Main --> SiteSection
SiteSection --> Container
Container --> Row
Row --> Col
Col --> ContentBox
ContentBox --> Breadcrumb
ContentBox --> PanelBody
PanelBody --> InfoRow
InfoRow --> ColInfo
ColInfo --> Header
ColInfo --> MessageArea
ColInfo --> DataTable
MessageArea --> CheckMessage
CheckMessage --> DisplayAlert
CheckMessage --> NoMessage
DataTable --> TableHead
DataTable --> TableBody
```

**Sources:** [Admin/inicioAdmin.php L79-L143](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L79-L143)

## DataTable Implementation

The appointments table uses the DataTables jQuery plugin for enhanced interactivity, providing search, sort, and pagination capabilities.

### Table Column Structure

The DataTable at [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 defines 10 columns:

| Column Header | Data Source | Code Location | Notes |
| --- | --- | --- | --- |
| Nombre completo | `$row['nombre'] . ' ' . $row['apellido']` | Line 121 | Concatenated patient name |
| Edad | `$row['años']` | Line 122 | Calculated patient age |
| Consulta | `$row['tipo']` | Line 123 | Consultation type from `consultas` table |
| Fecha | `$row['fecha_cita']` | Line 124 | Appointment date |
| Hora | `$row['hora_cita']` | Line 125 | Appointment time |
| Estado | Conditional: 'Realizada' or 'Pendiente' | Line 126 | Based on `estado` field: 'A' or 'P' |
| Diagnóstico | `$row['descripcion']` | Line 127 | Description/diagnosis text |
| (Action 1) | Edit link icon | Line 128 | Links to `realizar_consulta.php` |
| (Action 2) | Report icon | Line 129 | Links to `informe.php` |
| (Action 3) | Delete icon | Line 130 | Links to `realizar_consultasUPDATE.php` |

### DataTables Initialization

The DataTables plugin is initialized through external JavaScript files loaded at the bottom of the page:

```mermaid
flowchart TD

jQuery["jquery-3.5.1.js<br>line 160"]
DTCore["jquery.dataTables.min.js<br>line 161"]
DTResponsive["dataTables.responsive.min.js<br>line 162"]
DTInit["datatable.js<br>(initialization script)<br>line 163"]
TableElement["table#example<br>line 103"]
Features["DataTable Features"]
Search["Search box"]
Sort["Column sorting"]
Pagination["Pagination controls"]
Responsive["Responsive layout"]

jQuery --> DTCore
DTCore --> DTResponsive
DTResponsive --> DTInit
DTInit --> TableElement
TableElement --> Features
Features --> Search
Features --> Sort
Features --> Pagination
Features --> Responsive
```

The table element uses the ID `example` at [Admin/inicioAdmin.php L103](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L103)

 which is targeted by the initialization script in `datatable.js`. The table includes Bootstrap classes `table table-striped nowrap responsive` for styling and responsiveness.

**Sources:** [Admin/inicioAdmin.php L103-L134](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L103-L134)

 [Admin/inicioAdmin.php L160-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L160-L163)

## CRUD Operations and Action Links

Each row in the DataTable provides three action buttons that enable different operations on appointments.

```mermaid
flowchart TD

Row["DataTable Row<br>(one appointment)"]
EditAction["Edit Action<br>line 128"]
ReportAction["Report Action<br>line 129"]
DeleteAction["Delete Action<br>line 130"]
EditLink["a href='./realizar_consulta.php?<br>accion=UDT&id={id_cita}'"]
EditIcon["i.fas.fa-edit"]
EditTooltip["data-toggle='tooltip'<br>title='Editar'"]
ReportLink["a href='./informe.php?<br>id={id_paciente}'"]
ReportIcon["i.fas.fa-file-alt"]
ReportTooltip["data-toggle='tooltip'<br>title='Ver Informe'"]
DeleteLink["a href='../crud/realizar_consultasUPDATE.php?<br>accion=DLT&id={id_cita}&estado={estado}'"]
DeleteIcon["i.fas.fa-trash"]
DeleteTooltip["data-toggle='tooltip'<br>title='Anular'"]
ConfirmJS["onclick='return confirmation()'"]
ConfirmDialog["JavaScript confirm:<br>'¿Realmente desea eliminar esta cita?'<br>lines 44-48"]

Row --> EditAction
Row --> ReportAction
Row --> DeleteAction
EditAction --> EditLink
EditLink --> EditIcon
EditLink --> EditTooltip
ReportAction --> ReportLink
ReportLink --> ReportIcon
ReportLink --> ReportTooltip
DeleteAction --> DeleteLink
DeleteLink --> DeleteIcon
DeleteLink --> DeleteTooltip
DeleteLink --> ConfirmJS
ConfirmJS --> ConfirmDialog
```

### Action Link Details

**Edit Appointment (Realizar Consulta):**

* Location: [Admin/inicioAdmin.php L128](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L128)
* Target: `realizar_consulta.php` with parameters `accion=UDT` and `id={id_cita}`
* Icon: `fas fa-edit` (pencil icon)
* Purpose: Opens consultation form to edit diagnosis, description, and medications
* See [2.4](/axchisan/Consultorio_Emily_Bernal/2.4-consultation-and-diagnosis-interface) for details on the consultation interface

**View Medical Report:**

* Location: [Admin/inicioAdmin.php L129](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L129-L129)
* Target: `informe.php` with parameter `id={id_paciente}`
* Icon: `fas fa-file-alt` (document icon)
* CSS Class: `btn btn-success` (green button)
* Purpose: Opens medical report editor for the patient
* See [2.3.3](/axchisan/Consultorio_Emily_Bernal/2.3.3-medical-report-editor) for details on the report editor

**Delete Appointment (Anular):**

* Location: [Admin/inicioAdmin.php L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L130-L130)
* Target: `../crud/realizar_consultasUPDATE.php` with parameters `accion=DLT`, `id={id_cita}`, and `estado={estado}`
* Icon: `fas fa-trash` (trash can icon)
* CSS Class: `text-danger` (red text)
* JavaScript Confirmation: Calls `confirmation()` function defined at [Admin/inicioAdmin.php L44-L48](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L44-L48)
* Purpose: Marks appointment as deleted/cancelled (likely soft delete)

The delete action implements a client-side confirmation dialog using vanilla JavaScript. The `confirmation()` function returns the result of `window.confirm()`, which prevents the link navigation if the user clicks "Cancel".

**Sources:** [Admin/inicioAdmin.php L128-L130](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L128-L130)

 [Admin/inicioAdmin.php L44-L48](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L44-L48)

## Flash Messaging System

The dashboard implements the Post-Redirect-Get (PRG) pattern for displaying success and error messages using session variables.

### Message Display Logic

```mermaid
stateDiagram-v2
    [*] --> CheckMessage : "Page loads"
    CheckMessage --> DisplayAlert : "isset($_SESSION['MensajeTexto'])== true"
    CheckMessage --> NoDisplay : "isset($_SESSION['MensajeTexto'])== false"
    DisplayAlert --> RenderDiv : "Render div.alert with$_SESSION['MensajeTipo'] class"
    RenderDiv --> ShowText : "Display $_SESSION['MensajeTexto']"
    ShowText --> AddCloseButton : "Add delete button(X icon)"
    AddCloseButton --> ClearSession : "$_SESSION['MensajeTexto'] = null$_SESSION['MensajeTipo'] = null"
    ClearSession --> PageRendered : "Message cleared from session"
    NoDisplay --> PageRendered
    PageRendered --> UserInteraction : "User sees page"
    UserInteraction --> UserClicksX : "User clicks delete button"
    UserClicksX --> JSRemove : "JavaScript removesalert element"
    JSRemove --> [*] : "JavaScript removesalert element"
    PageRendered --> [*] : "Message not visible"
```

**Message Types:**

| Type Class | Visual Appearance | Used For |
| --- | --- | --- |
| `p-3 mb-2 bg-success text-white` | Green alert | Successful operations |
| `p-3 mb-2 bg-danger text-white` | Red alert | Errors, access denied |
| `p-3 mb-2 bg-warning text-black` | Yellow alert | Warnings |

The message display logic at [Admin/inicioAdmin.php L93-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L101)

 follows this sequence:

1. Check if `$_SESSION['MensajeTexto']` is set
2. If set, render a `<div>` with the alert class from `$_SESSION['MensajeTipo']`
3. Display the message text
4. Add a close button with Font Awesome `fa-times` icon
5. Immediately null out both session variables (ensuring one-time display)

### Client-Side Message Dismissal

The page includes JavaScript at [Admin/inicioAdmin.php L146-L155](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L146-L155)

 that enables manual message dismissal:

```javascript
document.querySelectorAll('.alert .delete').forEach(($delete) => {
    const $notification = $delete.parentNode;
    $delete.addEventListener('click', () => {
        $notification.parentNode.removeChild($notification);
    });
});
```

This script finds all delete buttons within alert elements and attaches click listeners that remove the entire alert from the DOM when clicked.

**Sources:** [Admin/inicioAdmin.php L93-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L101)

 [Admin/inicioAdmin.php L146-L155](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L146-L155)

## CSS and JavaScript Dependencies

The dashboard loads multiple CSS and JavaScript libraries to provide its functionality.

### CSS Dependencies

| Library | File Path | Purpose | Line Reference |
| --- | --- | --- | --- |
| Bootstrap 4 | `../src/css/lib/bootstrap/css/bootstrap.min.css` | Grid system, form styling, buttons | Line 36 |
| Custom Admin CSS | `../src/css/admin.css` | Sidebar and layout styles | Line 37 |
| Custom Styles | `../src/css/custom_styles.css` | Additional custom styling | Line 38 |
| Font Awesome | `../src/css/lib/fontawesome/css/all.css` | Icon fonts for UI elements | Line 39 |
| DataTables CSS | `../src/js/lib/datatable/css/jquery.dataTables.min.css` | DataTables styling | Line 40 |
| DataTables Responsive | `../src/js/lib/datatable/css/responsive.dataTables.min.css` | Responsive table behavior | Line 41 |

### JavaScript Dependencies

| Library | File Path | Purpose | Line Reference |
| --- | --- | --- | --- |
| jQuery | `../src/js/jquery.js` | DOM manipulation and AJAX | Line 157 |
| Bootstrap JS | `../src/css/lib/bootstrap/js/bootstrap.min.js` | Bootstrap components | Line 158 |
| Admin JS | `../src/js/admin.js` | Custom admin functionality | Line 159 |
| jQuery 3.5.1 | `../src/js/lib/datatable/js/jquery-3.5.1.js` | DataTables dependency | Line 160 |
| DataTables Core | `../src/js/lib/datatable/js/jquery.dataTables.min.js` | DataTables plugin | Line 161 |
| DataTables Responsive | `../src/js/lib/datatable/js/dataTables.responsive.min.js` | Responsive tables | Line 162 |
| DataTables Init | `../src/js/lib/datatable/datatable.js` | Table initialization | Line 163 |

The load order is significant: jQuery must load before Bootstrap and DataTables. The DataTables initialization script (`datatable.js`) loads last to ensure all dependencies are available.

**Sources:** [Admin/inicioAdmin.php L36-L41](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L41)

 [Admin/inicioAdmin.php L157-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L163)

## Page Flow Summary

The complete flow from page request to rendered dashboard follows this sequence:

```mermaid
flowchart TD

Request["HTTP GET<br>inicioAdmin.php"]
PHP_Start["PHP Execution Begins"]
SessionStart["session_start()<br>line 2"]
Includes["Include files:<br>conexionDB.php<br>consultas.php<br>lines 3-4"]
SecurityCheck["Security validation<br>lines 7-24"]
RedirectLogin["Redirect to index.php"]
DataQueries["Query doctor profile<br>and appointments<br>lines 26-27"]
HTMLStart["Begin HTML output<br>line 30"]
HTMLHead["Render :<br>- Meta tags<br>- CSS links<br>- JavaScript confirmation<br>lines 32-49"]
HTMLBody["Render <br>line 50"]
RenderSidebar["Render sidebar with:<br>- Profile image<br>- Doctor name<br>- Navigation menu<br>lines 51-77"]
RenderMain["Render main content:<br>- Breadcrumb<br>- Flash messages<br>- DataTable<br>lines 79-143"]
JSScripts["Load JavaScript:<br>- Alert dismissal<br>- jQuery<br>- Bootstrap<br>- DataTables<br>lines 145-164"]
Response["Send HTML response<br>to browser"]
BrowserRender["Browser renders page<br>with interactive DataTable"]
BrowserRedirect["Browser redirects<br>to login page"]

Request --> PHP_Start
PHP_Start --> SessionStart
SessionStart --> Includes
Includes --> SecurityCheck
SecurityCheck --> RedirectLogin
SecurityCheck --> DataQueries
DataQueries --> HTMLStart
HTMLStart --> HTMLHead
HTMLHead --> HTMLBody
HTMLBody --> RenderSidebar
RenderSidebar --> RenderMain
RenderMain --> JSScripts
JSScripts --> Response
Response --> BrowserRender
RedirectLogin --> BrowserRedirect
```

**Sources:** [Admin/inicioAdmin.php L1-L165](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L165)