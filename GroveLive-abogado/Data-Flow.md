# Data Flow

> **Relevant source files**
> * [controllers/abogadosCtrl.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php)
> * [ddbb/DBConexion.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php)
> * [models/abogadosModel.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php)
> * [views/abogadosView.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php)
> * [views/home.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php)

## Purpose and Scope

This document explains how lawyer data flows through the Abogado system, from its storage in the MySQL database through the various application layers to final display in the browser. It details the data transformations, sanitization steps, and method calls that occur at each layer, and describes the two distinct retrieval pathways: bulk listing and individual profile lookup.

For information about the complete HTTP request/response cycle, see [Request Lifecycle](/GroveLive/abogado/3.1-request-lifecycle). For details about specific layer implementations, see [Controllers Layer](/GroveLive/abogado/4.2-controllers-layer), [Models Layer](/GroveLive/abogado/4.3-models-layer), and [Views Overview](/GroveLive/abogado/5.1-views-overview).

---

## Data Flow Layers

The Abogado system moves data through five distinct layers, each with specific transformation responsibilities:

| Layer | Component | Primary Responsibility | Data Format |
| --- | --- | --- | --- |
| **Database** | MySQL `abogado` table | Persistent storage | Relational rows |
| **Connection** | `DBConexion` class | Connection management | mysqli object |
| **Model** | `Abogado` class | SQL execution and result mapping | PHP associative arrays |
| **Controller** | `AbogadosController` class | Method delegation | PHP associative arrays (pass-through) |
| **View** | `home.php`, `abogadosView.php` | HTML rendering and XSS protection | Sanitized HTML strings |

**Sources:** [controllers/abogadosCtrl.php L1-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L14)

 [models/abogadosModel.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L1-L27)

 [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

 [views/home.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L27)

 [views/abogadosView.php L1-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L40)

---

## Complete Data Flow Diagram

### Diagram: End-to-End Data Flow with Code Entities

```

```

**Sources:** [models/abogadosModel.php L5-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L25)

 [controllers/abogadosCtrl.php L5-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L11)

 [views/home.php L2-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L2-L24)

 [views/abogadosView.php L9-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L9-L34)

 [ddbb/DBConexion.php L5-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L16)

---

## Database to Model Layer

### Connection Establishment

Data flow begins with `DBConexion::connection()`, which creates a new `mysqli` connection object for each request:

1. **Connection Creation**: [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7)  instantiates `mysqli` with hardcoded credentials
2. **Character Encoding**: [ddbb/DBConexion.php L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L12-L12)  executes `SET NAMES 'utf8'` to ensure proper character handling
3. **Error Handling**: [ddbb/DBConexion.php L9-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L9-L10)  checks `$connection->errno` and outputs error messages on failure

Each call to a model method triggers a new connection; there is no connection pooling or reuse mechanism.

### SQL Execution and Result Retrieval

The `Abogado` class provides two static methods for data retrieval:

#### Bulk Retrieval: getAllAbogados()

[models/abogadosModel.php L5-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L15)

 executes an unfiltered SELECT query:

```sql
Flow: DBConexion::connection() 
      → $conexion->query("SELECT * FROM abogado") 
      → while loop with fetch_assoc() 
      → return array of associative arrays
```

Key characteristics:

* No WHERE clause, no LIMIT clause - retrieves all rows
* No prepared statement (not required; no user input)
* Result accumulation via array push inside while loop [models/abogadosModel.php L11-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L11-L12)
* Returns empty array if no results

#### Targeted Retrieval: getAbogadoById($id)

[models/abogadosModel.php L17-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L17-L25)

 uses prepared statements for secure parameterized queries:

```sql
Flow: DBConexion::connection() 
      → $conexion->prepare("SELECT * FROM abogado WHERE id_abogado = ?")
      → $stmt->bind_param("i", $id)
      → $stmt->execute()
      → $stmt->get_result()
      → return single fetch_assoc() or null
```

Key characteristics:

* Prepared statement with integer type binding [models/abogadosModel.php L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L21-L21)
* Single `fetch_assoc()` call [models/abogadosModel.php L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L24-L24)  - returns null if no match
* No explicit error handling; relies on mysqli default behavior

### Data Format Output

Both model methods return PHP associative arrays with keys matching database column names:

| Database Column | Array Key | Data Type |
| --- | --- | --- |
| `id_abogado` | `$row['id_abogado']` | Integer |
| `nombre` | `$row['nombre']` | String |
| `especialidad` | `$row['especialidad']` | String |
| `telefono` | `$row['telefono']` | String |
| `nacionalidad` | `$row['nacionalidad']` | String |
| `estudios` | `$row['estudios']` | String |
| `correo` | `$row['correo']` | String |

**Sources:** [models/abogadosModel.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L1-L27)

 [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

---

## Model to Controller Layer

The `AbogadosController` class acts as a thin delegation layer with no data transformation logic.

### Method Mapping

```

```

### Controller Implementation Details

**`obtenerAbogados()`** [controllers/abogadosCtrl.php L5-L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L7)

* Direct `return` statement with model call
* No validation, filtering, or transformation
* Returns raw array from model layer

**`obtenerAbogadoPorId($id)`** [controllers/abogadosCtrl.php L9-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L9-L11)

* Passes `$id` parameter directly to model
* No input sanitization (performed by prepared statement in model)
* Returns single array or null

The controller layer serves as an architectural placeholder, maintaining MVC pattern separation even though it performs no business logic.

**Sources:** [controllers/abogadosCtrl.php L1-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L14)

---

## Controller to View Layer

### Data Retrieval in Views

Both views instantiate `AbogadosController` and invoke its methods during the initial PHP execution phase (before HTML output begins).

#### Home View Data Retrieval

[views/home.php L2-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L2-L4)

 executes synchronously at page load:

```
require_once "../controllers/abogadosCtrl.php"
→ new AbogadosController()
→ $controller->obtenerAbogados()
→ $abogados = [...array of all lawyers...]
```

The `$abogados` variable remains in scope for the HTML rendering phase.

#### Profile View Data Retrieval

[views/abogadosView.php L4-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L4-L15)

 includes parameter validation before data retrieval:

```
1. Check $_GET['id'] exists [line 4-7]
2. Instantiate controller [line 9]
3. Call obtenerAbogadoPorId($_GET['id']) [line 10]
4. Check $abogado is not null [line 12-15]
5. Exit with error message if validation fails
```

Unlike the home view, the profile view includes explicit error handling for missing IDs and non-existent lawyers.

**Sources:** [views/home.php L2-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L2-L4)

 [views/abogadosView.php L4-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L4-L15)

---

## View to Browser Layer

### Data Projection and Selective Display

The two views display different subsets of the available data fields:

| Field | Home View | Profile View |
| --- | --- | --- |
| `id_abogado` | Used in URL, not displayed | Used in URL, not displayed |
| `nombre` | ✓ Displayed | ✓ Displayed |
| `especialidad` | ✓ Displayed | ✓ Displayed |
| `telefono` | ✗ Not displayed | ✓ Displayed |
| `nacionalidad` | ✗ Not displayed | ✓ Displayed |
| `estudios` | ✗ Not displayed | ✓ Displayed |
| `correo` | ✗ Not displayed | ✓ Displayed |

### HTML Rendering Patterns

#### Home View: Iteration Pattern

[views/home.php L18-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L18-L24)

 uses a `foreach` loop to render each lawyer:

```

```

Key observations:

* Short tag syntax `<?=` for inline output
* `htmlspecialchars()` wraps every displayed string value
* `id_abogado` is NOT sanitized because it is used in href (integer from database)
* CSS class `menu-item` structures each card

#### Profile View: Direct Output Pattern

[views/abogadosView.php L28-L35](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L28-L35)

 outputs a single lawyer's details:

```

```

Every string field is wrapped with `htmlspecialchars()` to prevent XSS attacks.

**Sources:** [views/home.php L18-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L18-L24)

 [views/abogadosView.php L28-L35](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L28-L35)

---

## Data Transformation Pipeline

### Diagram: Data Format Transformations

```

```

### Transformation Details by Stage

#### Stage 1: Database → mysqli_result

* Encoding: UTF-8 (set via `SET NAMES 'utf8'`)
* Format: Internal mysqli cursor with binary data
* Location: [models/abogadosModel.php L8](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L8-L8)  (getAllAbogados), [models/abogadosModel.php L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L23-L23)  (getAbogadoById)

#### Stage 2: mysqli_result → PHP Array

* Method: `fetch_assoc()`
* Transformation: Column names become array keys, values become PHP strings/integers
* Type casting: Database integers become PHP integers, VARCHAR becomes PHP strings
* Location: [models/abogadosModel.php L11-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L11-L12)  (loop), [models/abogadosModel.php L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L24-L24)  (single fetch)

#### Stage 3: PHP Array → HTML String

* Method: `htmlspecialchars()`
* Transformation: Special characters converted to HTML entities * `<` → `&lt;` * `>` → `&gt;` * `&` → `&amp;` * `"` → `&quot;` * `'` → `&#039;` (when using ENT_QUOTES)
* Location: [views/home.php L20-L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L20-L21)  [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

#### Stage 4: HTML String → Browser DOM

* Parser: Browser HTML parser
* Transformation: Escaped entities rendered as visible text
* Enhancement: CSS styling applied from `style.css`, JavaScript interactions from `script.js`

**Sources:** [models/abogadosModel.php L8-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L8-L24)

 [views/home.php L20-L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L20-L21)

 [views/abogadosView.php L29-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L34)

 [ddbb/DBConexion.php L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L12-L12)

---

## Two Retrieval Pathways

### Diagram: Request-Specific Data Flows

```

```

### Pathway Comparison

| Characteristic | List All Pathway | Individual Lookup Pathway |
| --- | --- | --- |
| **Entry Point** | [views/home.php L3-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L3-L4) | [views/abogadosView.php L9-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L9-L10) |
| **Controller Method** | `obtenerAbogados()` | `obtenerAbogadoPorId($id)` |
| **Model Method** | `getAllAbogados()` | `getAbogadoById($id)` |
| **SQL Type** | Direct query | Prepared statement |
| **Input Validation** | None required | `$_GET['id']` existence check |
| **Return Type** | Array of arrays (always) | Single array or null |
| **Result Iteration** | `foreach` loop required | Direct field access |
| **Error Handling** | None (empty array on no results) | Explicit null check with exit |
| **Security Mechanism** | N/A (no user input) | Prepared statement with bind_param |

**Sources:** [views/home.php L3-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L3-L4)

 [views/abogadosView.php L4-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L4-L15)

 [controllers/abogadosCtrl.php L5-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L11)

 [models/abogadosModel.php L5-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L25)

---

## Security and Sanitization Points

### XSS Prevention Layer

All data sanitization occurs at the **view layer** using `htmlspecialchars()`:

**Home View Sanitization:**

* [views/home.php L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L20-L20)  - Sanitizes `$abogado['nombre']`
* [views/home.php L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L21-L21)  - Sanitizes `$abogado['especialidad']`

**Profile View Sanitization:**

* [views/abogadosView.php L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L23-L23)  - Sanitizes page title
* [views/abogadosView.php L29](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L29-L29)  - Sanitizes displayed nombre
* [views/abogadosView.php L30-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L30-L34)  - Sanitizes all displayed fields (especialidad, telefono, nacionalidad, estudios, correo)

**Unsanitized Output:**

* [views/home.php L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L22-L22)  - `$abogado['id_abogado']` in href attribute (integer from database, assumed safe)
* This is acceptable because `id_abogado` is an integer column and is never user-supplied in this context

### SQL Injection Prevention Layer

SQL injection protection occurs at the **model layer**:

* `getAllAbogados()`: No parameterization needed - hardcoded query with no user input [models/abogadosModel.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L7-L7)
* `getAbogadoById($id)`: Prepared statement with integer type binding [models/abogadosModel.php L19-L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L19-L21)

The `bind_param("i", $id)` call [models/abogadosModel.php L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L21-L21)

 enforces integer type, preventing injection even if `$_GET['id']` contains malicious strings.

### Character Encoding Protection

UTF-8 encoding is enforced at the **connection layer**:

* [ddbb/DBConexion.php L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L12-L12)  executes `SET NAMES 'utf8'` immediately after connection
* Ensures proper handling of special characters in Spanish text (á, é, í, ó, ú, ñ, ¿, ¡)
* Prevents encoding-related injection attacks

**Sources:** [views/home.php L20-L22](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L20-L22)

 [views/abogadosView.php L23-L34](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L23-L34)

 [models/abogadosModel.php L19-L21](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L19-L21)

 [ddbb/DBConexion.php L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L12-L12)

---

## Data Flow Summary Table

| Stage | Input Format | Process | Output Format | File Location |
| --- | --- | --- | --- | --- |
| **Database Query** | User request | SQL execution | mysqli_result | [models/abogadosModel.php L8-L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L8-L23) |
| **Result Fetching** | mysqli_result | `fetch_assoc()` | PHP array(s) | [models/abogadosModel.php L11-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L11-L24) |
| **Controller Delegation** | Model call | Pass-through return | PHP array(s) | [controllers/abogadosCtrl.php L6-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L6-L10) |
| **View Retrieval** | Controller instantiation | Method invocation | `$abogados` or `$abogado` variable | [views/home.php L3-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L3-L4) <br>  [views/abogadosView.php L9-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L9-L10) |
| **HTML Generation** | PHP array(s) | Iteration/output with sanitization | HTML strings | [views/home.php L18-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L18-L24) <br>  [views/abogadosView.php L28-L35](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L28-L35) |
| **Browser Rendering** | HTML document | DOM parsing | Visible webpage | Client-side (not server code) |

**Sources:** [models/abogadosModel.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L1-L27)

 [controllers/abogadosCtrl.php L1-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L14)

 [views/home.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php#L1-L27)

 [views/abogadosView.php L1-L40](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php#L1-L40)