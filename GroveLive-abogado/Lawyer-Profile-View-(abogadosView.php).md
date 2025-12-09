# Lawyer Profile View (abogadosView.php)

> **Relevant source files**
> * [assets/css/style.css](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css)
> * [assets/js/script.js](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js)
> * [controllers/abogadosCtrl.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php)
> * [views/abogadosView.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php)

## Purpose and Scope

This document details the `abogadosView.php` view, which displays the complete profile information for an individual lawyer. This view renders a detail page showing all available fields from the database for a single lawyer record, including name, specialty, phone number, nationality, education, and email address.

The profile view receives a lawyer identifier via GET parameter, retrieves the corresponding record through the `AbogadosController`, and displays the data with XSS protection. For information about the lawyer listing page that links to this view, see [Lawyer Listing View (home.php)](/GroveLive/abogado/5.1.1-lawyer-listing-view-(home.php)). For details about the controller methods used, see [Controllers Layer](/GroveLive/abogado/4.2-controllers-layer).

**Sources:** [views/abogadosView.php L1-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L40)

---

## File Location and Execution Context

The profile view is located at `views/abogadosView.php` and is accessed via direct URL navigation with a query string parameter. The file is typically reached through JavaScript-enhanced navigation from the home page or via direct URL entry.

| Property | Value |
| --- | --- |
| File Path | `views/abogadosView.php` |
| Access Method | Direct GET request with `id` parameter |
| Parent Directory | `views/` |
| Required Parameter | `id` (lawyer identifier) |
| Dependent Controller | `AbogadosController` |
| Styling Dependency | `assets/css/style.css` |

**Sources:** [views/abogadosView.php L1-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L40)

---

## Request Flow and Parameter Handling

The view follows a strict request processing flow that validates input, retrieves data, and renders output. The process begins with GET parameter validation and proceeds through controller invocation to HTML generation.

### Request Processing Sequence

```mermaid
sequenceDiagram
  participant Browser
  participant abogadosView.php
  participant AbogadosController
  participant AbogadoModel
  participant Database

  Browser->>abogadosView.php: "GET /views/abogadosView.php?id=X"
  abogadosView.php->>abogadosView.php: "Check isset($_GET['id'])"
  loop ["id parameter missing"]
    abogadosView.php->>Browser: "Echo 'Abogado no encontrado.' + exit"
    abogadosView.php->>AbogadosController: "new AbogadosController()"
    abogadosView.php->>AbogadosController: "obtenerAbogadoPorId($_GET['id'])"
    AbogadosController->>AbogadoModel: "getAbogadoById(id)"
    AbogadoModel->>Database: "SELECT * FROM abogado WHERE id_abogado = ?"
    Database-->>AbogadoModel: "Result set (1 row or empty)"
    AbogadoModel-->>AbogadosController: "Array or null"
    AbogadosController-->>abogadosView.php: "$abogado (array or null)"
    abogadosView.php->>Browser: "Echo 'Abogado no encontrado.' + exit"
  end
  abogadosView.php->>abogadosView.php: "Render HTML with data"
  abogadosView.php->>Browser: "Complete HTML page"
```

**Sources:** [views/abogadosView.php L1-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L16)

 [controllers/abogadosCtrl.php L9-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L9-L11)

### GET Parameter Processing

The view expects a single GET parameter named `id` containing the lawyer's unique identifier. The parameter is accessed directly from the `$_GET` superglobal without sanitization, as it is immediately passed to a prepared statement in the model layer.

```mermaid
flowchart TD

Request["HTTP GET Request<br>URL: abogadosView.php?id=X"]
CheckParam["isset($_GET['id'])<br>Line 4"]
MissingParam["Echo error message<br>exit script<br>Lines 5-6"]
InstantiateController["new AbogadosController()<br>Line 9"]
CallMethod["obtenerAbogadoPorId($_GET['id'])<br>Line 10"]
CheckResult["if (!$abogado)<br>Line 12"]
NotFound["Echo error message<br>exit script<br>Lines 13-14"]
RenderHTML["Render profile HTML<br>Lines 18-38"]

Request --> CheckParam
CheckParam --> MissingParam
CheckParam --> InstantiateController
InstantiateController --> CallMethod
CallMethod --> CheckResult
CheckResult --> NotFound
CheckResult --> RenderHTML
```

**Sources:** [views/abogadosView.php L4-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L4-L15)

---

## Error Handling

The view implements two error conditions that result in script termination with user-facing messages. Both error paths output plain text directly to the browser without HTML structure.

### Error Conditions Table

| Error Type | Trigger Condition | Location | Output | HTTP Status |
| --- | --- | --- | --- | --- |
| Missing ID Parameter | `!isset($_GET['id'])` | Lines 4-6 | `"Abogado no encontrado."` | 200 (default) |
| Lawyer Not Found | `!$abogado` (null return) | Lines 12-14 | `"Abogado no encontrado."` | 200 (default) |

### Error Handling Flow

Both error conditions use the same error message text and exit strategy, providing a consistent user experience regardless of whether the error is a missing parameter or a non-existent record.

**Sources:** [views/abogadosView.php L4-L6](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L4-L6)

 [views/abogadosView.php L12-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L12-L14)

---

## Controller Integration

The view depends on the `AbogadosController` class to retrieve lawyer data from the model layer. The controller is loaded via `require_once` and instantiated within the view's execution context.

### Controller Dependency Structure

```mermaid
flowchart TD

ViewFile["abogadosView.php<br>views/"]
RequireStmt["require_once<br>'../controllers/abogadosCtrl.php'<br>Line 2"]
ControllerFile["abogadosCtrl.php<br>controllers/"]
ControllerClass["AbogadosController<br>class"]
InstantiateCtrl["$controller = new AbogadosController()<br>Line 9"]
MethodCall["$controller->obtenerAbogadoPorId($_GET['id'])<br>Line 10"]
ControllerMethod["obtenerAbogadoPorId($id)<br>method"]
ModelCall["Abogado::getAbogadoById($id)<br>static call"]

ViewFile --> RequireStmt
RequireStmt --> ControllerFile
ControllerFile --> ControllerClass
ControllerClass --> InstantiateCtrl
InstantiateCtrl --> MethodCall
MethodCall --> ControllerMethod
ControllerMethod --> ModelCall
```

**Sources:** [views/abogadosView.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L2-L2)

 [views/abogadosView.php L9-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L9-L10)

 [controllers/abogadosCtrl.php L9-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L9-L11)

### Data Retrieval Pattern

The view follows a simple delegation pattern where controller instantiation and method invocation occur inline within the view's PHP execution block. The returned data is stored in the `$abogado` variable for use in the HTML template section.

| Step | Code Location | Operation | Result |
| --- | --- | --- | --- |
| 1. Include Controller | Line 2 | `require_once "../controllers/abogadosCtrl.php"` | Class available |
| 2. Instantiate | Line 9 | `$controller = new AbogadosController()` | Controller object |
| 3. Invoke Method | Line 10 | `$controller->obtenerAbogadoPorId($_GET['id'])` | Array or null |
| 4. Store Result | Line 10 | Assignment to `$abogado` | Data available for view |

**Sources:** [views/abogadosView.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L2-L2)

 [views/abogadosView.php L9-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L9-L10)

---

## HTML Structure and Document Template

After successful data retrieval, the view generates a complete HTML5 document structure with metadata, styling, and content sections.

### Document Structure

```mermaid
flowchart TD

DOCTYPE["<br>Line 18"]
HTMLTag["<br>Line 19"]
HeadSection["<br>Lines 20-25"]
MetaCharset["<br>Line 21"]
MetaViewport["<br>Line 22"]
TitleTag[""]
LinkCSS["href='../assets/css/style.css'><br>Line 24"]
BodySection["<br>Lines 26-37"]
H1Header["Perfil del AbogadoLine 27"]
AbogadoContainer["Lines 28-35"]
FieldsDisplay["Lawyer fields with htmlspecialchars<br>Lines 29-34"]
BackLink["Line 36"]

DOCTYPE --> HTMLTag
HTMLTag --> HeadSection
HTMLTag --> BodySection
HeadSection --> MetaCharset
HeadSection --> MetaViewport
HeadSection --> TitleTag
HeadSection --> LinkCSS
BodySection --> H1Header
BodySection --> AbogadoContainer
BodySection --> BackLink
AbogadoContainer --> FieldsDisplay
```

**Sources:** [views/abogadosView.php L18-L38](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L18-L38)

### Head Section Elements

| Element | Purpose | Content |
| --- | --- | --- |
| `charset` | Character encoding | UTF-8 |
| `viewport` | Responsive design | `width=device-width, initial-scale=1.0` |
| `title` | Browser tab title | Lawyer's name (sanitized) |
| `link` | Stylesheet | Relative path to `style.css` |

**Sources:** [views/abogadosView.php L20-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L20-L25)

---

## Data Display and XSS Protection

The view displays six database fields for each lawyer, applying `htmlspecialchars()` sanitization to all output values to prevent cross-site scripting attacks.

### Displayed Fields

```mermaid
flowchart TD

DBNombre["nombre"]
DBEspecialidad["especialidad"]
DBTelefono["telefono"]
DBNacionalidad["nacionalidad"]
DBEstudios["estudios"]
DBCorreo["correo"]
SanitizeFunc["htmlspecialchars()"]
OutH4["with name"]
OutEspec["Especialidad"]
OutTel["Teléfono"]
OutNac["Nacionalidad"]
OutEst["Estudios"]
OutEmail["Correo"]

DBNombre --> SanitizeFunc
DBEspecialidad --> SanitizeFunc
DBTelefono --> SanitizeFunc
DBNacionalidad --> SanitizeFunc
DBEstudios --> SanitizeFunc
DBCorreo --> SanitizeFunc
SanitizeFunc --> OutH4
SanitizeFunc --> OutEspec
SanitizeFunc --> OutTel
SanitizeFunc --> OutNac
SanitizeFunc --> OutEst
SanitizeFunc --> OutEmail

subgraph subGraph2 ["HTML Output"]
    OutH4
    OutEspec
    OutTel
    OutNac
    OutEst
    OutEmail
end

subgraph subGraph1 ["Sanitization Layer"]
    SanitizeFunc
end

subgraph subGraph0 ["Database Fields"]
    DBNombre
    DBEspecialidad
    DBTelefono
    DBNacionalidad
    DBEstudios
    DBCorreo
end
```

**Sources:** [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

### Field Display Table

| Field Name | Database Column | Display Label | HTML Element | Line Number |
| --- | --- | --- | --- | --- |
| Name | `nombre` | (none - used as heading) | `<h4>` | 29 |
| Specialty | `especialidad` | "Especialidad:" | `<p><strong>` | 30 |
| Phone | `telefono` | "Teléfono:" | `<p><strong>` | 31 |
| Nationality | `nacionalidad` | "Nacionalidad:" | `<p><strong>` | 32 |
| Education | `estudios` | "Estudios:" | `<p><strong>` | 33 |
| Email | `correo` | "Correo:" | `<p><strong>` | 34 |

### XSS Protection Implementation

Every field output uses the short echo tag syntax (`<?= ?>`) with `htmlspecialchars()` function, which converts special HTML characters to their entity equivalents. This prevents malicious script injection through database values.

**Example Pattern:**

```
<?= htmlspecialchars($abogado['campo']); ?>
```

This pattern is applied consistently across all field outputs at lines 23, 29, 30, 31, 32, 33, and 34.

**Sources:** [views/abogadosView.php L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L23-L23)

 [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

---

## CSS Styling Integration

The view applies specific CSS classes that define the visual presentation of the profile container and navigation elements.

### CSS Class Mapping

```mermaid
flowchart TD

StyleSheet["style.css<br>assets/css/"]
AbogadoContainerClass[".abogado-container<br>Lines 98-105"]
VolverClass[".volver<br>Lines 107-120"]
ContainerDiv["Line 28"]
BackLink["Line 36"]
ContainerStyles["background-color: var(--light-bg)<br>padding: 20px<br>border-radius: 8px<br>box-shadow: var(--shadow)<br>border-left: 4px solid<br>margin-bottom: 20px"]
LinkStyles["display: inline-block<br>background-color: var(--secondary-color)<br>color: white<br>padding: 10px 20px<br>border-radius: 6px<br>margin-top: 20px<br>transition: var(--transition)<br>font-weight: bold"]

StyleSheet --> AbogadoContainerClass
StyleSheet --> VolverClass
AbogadoContainerClass --> ContainerDiv
VolverClass --> BackLink
ContainerDiv --> ContainerStyles
BackLink --> LinkStyles
```

**Sources:** [views/abogadosView.php L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L24-L24)

 [views/abogadosView.php L28](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L28-L28)

 [views/abogadosView.php L36](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L36-L36)

 [assets/css/style.css L98-L120](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L98-L120)

### Styling Classes Used

| Class Name | Applied To | Purpose | CSS Lines |
| --- | --- | --- | --- |
| `.abogado-container` | `<div>` wrapper | Profile card styling with shadow and border | 98-105 |
| `.volver` | Back link `<a>` | Button-style navigation link | 107-120 |

### Visual Design Properties

The `.abogado-container` class provides:

* Dark background matching the theme (`var(--light-bg)`)
* Rounded corners (8px border-radius)
* Drop shadow for depth
* Left accent border (4px purple)
* 20px internal padding

The `.volver` class styles the back link as:

* Purple button with white text
* 10px vertical, 20px horizontal padding
* Hover state changing to red accent color
* Smooth transition animation

**Sources:** [assets/css/style.css L98-L105](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L98-L105)

 [assets/css/style.css L107-L120](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L107-L120)

---

## Navigation Elements

The view provides a single navigation element allowing users to return to the lawyer listing page.

### Back Navigation Structure

```mermaid
flowchart TD

BackLink["Line 36"]
LinkText["Text: 'Volver'<br>(Return)"]
TargetPage["home.php<br>Lawyer listing view"]
HoverEffect["Hover: background changes<br>to var(--accent-color)"]

BackLink --> LinkText
BackLink --> TargetPage
BackLink --> HoverEffect
```

**Sources:** [views/abogadosView.php L36](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L36-L36)

### Navigation Properties

| Property | Value |
| --- | --- |
| Link Text | "Volver" (Spanish for "Return") |
| Target URL | `home.php` (relative path) |
| CSS Class | `volver` |
| Position | Below profile container |
| Styling | Button appearance with purple background |

The link uses a relative path (`home.php`) which resolves to `views/home.php` since both files are in the same `views/` directory. This navigation completes the master-detail browsing pattern, allowing users to return from detail view to list view.

**Sources:** [views/abogadosView.php L36](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L36-L36)

---

## Complete View Execution Flow

The following diagram maps the complete execution flow from request to response, showing all code entities involved in the process.

```mermaid
flowchart TD

Start["HTTP GET Request<br>URL: /views/abogadosView.php?id=X"]
RequireController["require_once '../controllers/abogadosCtrl.php'<br>Line 2"]
CheckID["isset($_GET['id'])<br>Line 4"]
ErrorNoID["echo 'Abogado no encontrado.'<br>exit;<br>Lines 5-6"]
NewController["$controller = new AbogadosController()<br>Line 9"]
CallObtener["$abogado = $controller->obtenerAbogadoPorId($_GET['id'])<br>Line 10"]
CtrlMethod["AbogadosController::obtenerAbogadoPorId($id)<br>controllers/abogadosCtrl.php:9-11"]
ModelCall["return Abogado::getAbogadoById($id)<br>Model layer delegation"]
CheckAbogado["if (!$abogado)<br>Line 12"]
ErrorNotFound["echo 'Abogado no encontrado.'<br>exit;<br>Lines 13-14"]
RenderDoctype["<br>Line 18"]
RenderHead["section<br>charset, viewport, title, CSS link<br>Lines 20-25"]
RenderBody["<br>Line 26"]
RenderH1["Perfil del AbogadoLine 27"]
RenderContainer["Line 28"]
RenderFields["Display 6 fields with htmlspecialchars()<br>nombre, especialidad, telefono,<br>nacionalidad, estudios, correo<br>Lines 29-34"]
RenderBack["Volver<br>Line 36"]
End["Complete HTML sent to browser"]

Start --> RequireController
RequireController --> CheckID
CheckID --> ErrorNoID
CheckID --> NewController
NewController --> CallObtener
CallObtener --> CtrlMethod
CtrlMethod --> ModelCall
ModelCall --> CheckAbogado
CheckAbogado --> ErrorNotFound
CheckAbogado --> RenderDoctype
RenderDoctype --> RenderHead
RenderHead --> RenderBody
RenderBody --> RenderH1
RenderH1 --> RenderContainer
RenderContainer --> RenderFields
RenderFields --> RenderBack
RenderBack --> End
ErrorNoID --> End
ErrorNotFound --> End
```

**Sources:** [views/abogadosView.php L1-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L40)

 [controllers/abogadosCtrl.php L9-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L9-L11)

---

## Key Implementation Characteristics

### Architectural Pattern

The view implements a **detail view pattern** in the master-detail relationship, where the master is the lawyer listing (`home.php`) and this file serves as the detail view. The view is responsible for:

* Parameter validation
* Controller instantiation and invocation
* Error handling
* HTML generation with data sanitization

### Security Measures

* **XSS Protection:** All output uses `htmlspecialchars()` function
* **SQL Injection Prevention:** Indirect protection through controller/model prepared statements
* **Input Validation:** Checks for parameter existence before processing

### Limitations

* **No HTTP Status Codes:** Errors return 200 OK instead of 404 Not Found
* **Plain Text Errors:** Error messages lack HTML structure
* **No Input Sanitization:** GET parameter passed directly to controller (relies on model layer security)
* **Single Parameter:** Only supports retrieval by ID, no alternative lookup methods

**Sources:** [views/abogadosView.php L1-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L40)