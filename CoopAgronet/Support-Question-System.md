# Support Question System

> **Relevant source files**
> * [backennd/db_interaction/connection.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php)
> * [backennd/db_interaction/enviar_pregunta.php](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php)

## Purpose and Scope

The Support Question System enables registered users to submit support questions through a web form interface. This system validates user registration status, enforces referential integrity between questions and user accounts, and stores submitted questions in the database for administrative review. The endpoint implementation is located in `backennd/db_interaction/enviar_pregunta.php` and interacts with both the `usuarios` and `preguntas` database tables.

For information about the frontend support form interface, see [Support Form Frontend](/axchisan/CoopAgronet/3.4-support-form-frontend). For authentication and user registration processes, see [User Authentication System](/axchisan/CoopAgronet/2.2-user-authentication-system).

**Sources:** [backennd/db_interaction/enviar_pregunta.php L1-L54](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L1-L54)

---

## System Overview

The Support Question System implements a three-stage validation pipeline that ensures only registered users can submit questions. The system receives POST requests containing user identification and question data, verifies the user's registration status by email lookup, and creates a database record linking the question to the authenticated user through a foreign key relationship.

```

```

**Sources:** [backennd/db_interaction/enviar_pregunta.php L6-L54](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L6-L54)

---

## Database Schema Integration

The system interacts with two database tables to enforce referential integrity and maintain the relationship between users and their submitted questions.

### Table: usuarios

| Column | Type | Description |
| --- | --- | --- |
| `id` | INT PRIMARY KEY AUTO_INCREMENT | User identifier |
| `nombre_completo` | VARCHAR | Full name |
| `correo` | VARCHAR UNIQUE | Email address used for lookup |
| `contrasena` | VARCHAR | Hashed password |

### Table: preguntas

| Column | Type | Description |
| --- | --- | --- |
| `id` | INT PRIMARY KEY AUTO_INCREMENT | Question identifier |
| `usuario_id` | INT FOREIGN KEY | References `usuarios.id` |
| `pregunta` | TEXT | Question content |
| `created_at` | TIMESTAMP | Auto-generated timestamp |

The foreign key relationship `preguntas.usuario_id → usuarios.id` ensures that all questions are associated with valid user accounts and maintains data integrity when users are deleted.

**Sources:** [backennd/db_interaction/enviar_pregunta.php L19-L27](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L19-L27)

 [backennd/db_interaction/enviar_pregunta.php L40-L42](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L40-L42)

---

## Request Processing Flow

### Request Parameters

The endpoint expects three POST parameters:

```
nombre    - User's full name (used for display purposes)
correo    - User's email address (used for authentication lookup)
pregunta  - The support question text content
```

**Sources:** [backennd/db_interaction/enviar_pregunta.php L9-L11](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L9-L11)

### Validation Stage

The initial validation checks that all three required fields are present and non-empty:

```

```

This validation occurs at [backennd/db_interaction/enviar_pregunta.php L13-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L13-L16)

 using PHP's `empty()` function to detect null, empty string, or undefined values.

**Sources:** [backennd/db_interaction/enviar_pregunta.php L13-L16](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L13-L16)

---

## User Verification Logic

The system implements a two-step user verification process using prepared statements to prevent SQL injection vulnerabilities.

### Step 1: Email Lookup Query

```

```

The prepared statement is created at [backennd/db_interaction/enviar_pregunta.php L19-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L19-L23)

:

* **Line 19-20**: Prepares the SELECT query with parameterized placeholder
* **Line 21**: Binds the `$correo` variable as a string parameter (`"s"`)
* **Line 22**: Executes the query against the database
* **Line 23**: Stores the result set for row counting

**Sources:** [backennd/db_interaction/enviar_pregunta.php L19-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L19-L23)

### Step 2: Result Processing

```

```

The result processing logic at [backennd/db_interaction/enviar_pregunta.php L25-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L25-L37)

:

* **Line 25**: Checks if any rows were returned (`$stmt->num_rows > 0`)
* **Lines 26-27**: If found, binds the `id` column to `$usuario_id` variable and fetches the value
* **Lines 30-33**: If not found, returns JSON error with embedded HTML link (`<a href='#' id='activate-register'>`) to trigger registration modal
* **Line 36**: Exits execution to prevent further processing

**Sources:** [backennd/db_interaction/enviar_pregunta.php L25-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L25-L37)

---

## Question Insertion Process

Once user verification succeeds, the system inserts the question into the `preguntas` table with the retrieved `usuario_id` foreign key.

### Insertion Query

```

```

Implementation details at [backennd/db_interaction/enviar_pregunta.php L40-L48](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L40-L48)

:

* **Line 40**: Prepares INSERT statement with two parameterized placeholders
* **Line 41**: Creates new prepared statement object `$stmt_pregunta`
* **Line 42**: Binds parameters with types: * `"i"` - integer for `usuario_id` * `"s"` - string for `pregunta`
* **Line 44**: Executes the insertion
* **Lines 44-47**: Conditional response based on execution success

**Sources:** [backennd/db_interaction/enviar_pregunta.php L40-L48](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L40-L48)

---

## Response Format and Error Handling

The endpoint returns consistent JSON responses across all execution paths, enabling predictable client-side handling.

### Response Schema

```

```

### Response Cases

| Condition | `success` | `message` | Exit Point |
| --- | --- | --- | --- |
| Missing required fields | `false` | `"❌ Todos los campos son obligatorios."` | Line 14 |
| User email not registered | `false` | `"⚠️ El correo ingresado no está registrado. <a href='#' id='activate-register'>Regístrate aquí</a>."` | Line 30-33 |
| Question inserted successfully | `true` | `"✅ Pregunta enviada correctamente."` | Line 45 |
| Database insertion failure | `false` | `"❌ Error al guardar la pregunta."` | Line 47 |

**Sources:** [backennd/db_interaction/enviar_pregunta.php L14](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L14-L14)

 [backennd/db_interaction/enviar_pregunta.php L30-L33](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L30-L33)

 [backennd/db_interaction/enviar_pregunta.php L45-L47](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L45-L47)

---

## Security Implementation

The Support Question System implements several security measures to protect against common vulnerabilities.

### Prepared Statements

Both database queries use prepared statements with parameterized placeholders, protecting against SQL injection attacks:

```

```

**User Lookup:** [backennd/db_interaction/enviar_pregunta.php L19-L23](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L19-L23)

* Query: `"SELECT id FROM usuarios WHERE correo = ?"`
* Binding: `$stmt->bind_param("s", $correo)`

**Question Insertion:** [backennd/db_interaction/enviar_pregunta.php L40-L42](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L40-L42)

* Query: `"INSERT INTO preguntas (usuario_id, pregunta) VALUES (?, ?)"`
* Binding: `$stmt_pregunta->bind_param("is", $usuario_id, $pregunta)`

### User Authentication Requirement

The system enforces that only registered users can submit questions by:

1. Requiring valid email address that exists in `usuarios` table
2. Using the retrieved `usuario_id` as foreign key in `preguntas` table
3. Rejecting submissions from unregistered emails with guidance to register

This pattern ensures accountability and prevents anonymous spam submissions.

**Sources:** [backennd/db_interaction/enviar_pregunta.php L19-L37](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L19-L37)

### Resource Cleanup

The endpoint properly closes database connections and statement resources at [backennd/db_interaction/enviar_pregunta.php L51-L53](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L51-L53)

:

```
$stmt->close();              // Close user lookup statement
$stmt_pregunta->close();     // Close insertion statement  
$connection->close();        // Close database connection
```

**Sources:** [backennd/db_interaction/enviar_pregunta.php L51-L53](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L51-L53)

---

## Database Connection Integration

The endpoint uses the shared connection factory pattern by requiring `connection.php` at [backennd/db_interaction/enviar_pregunta.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L6-L6)

```

```

The `$connection` object provided by [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

 is used throughout the endpoint for:

* Creating prepared statements ([line 20, 41](https://github.com/axchisan/CoopAgronet/blob/e8818744/line 20, 41) )
* Executing queries against the MySQL database

For detailed information about the connection factory, see [Database Connection Layer](/axchisan/CoopAgronet/2.1-database-connection-layer).

**Sources:** [backennd/db_interaction/enviar_pregunta.php L6](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L6-L6)

 [backennd/db_interaction/connection.php L10](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/connection.php#L10-L10)

---

## Complete Data Flow

The following diagram illustrates the complete request-to-response flow with all decision points and database interactions:

```

```

**Sources:** [backennd/db_interaction/enviar_pregunta.php L1-L54](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L1-L54)

---

## Frontend Integration

The backend endpoint integrates with the client-side support form handler located in `enviar_pregunta.js`. The frontend JavaScript:

1. Captures form submission events from `#support-form`
2. Creates FormData object from form inputs
3. Sends POST request to `/enviar_pregunta.php`
4. Displays response message in `#mensaje-form` element
5. Auto-dismisses messages after 5 seconds
6. Clears form fields on successful submission

The embedded HTML in error messages (specifically the `<a href='#' id='activate-register'>` link at [line 32](https://github.com/axchisan/CoopAgronet/blob/e8818744/line 32)

) is designed to be intercepted by frontend JavaScript to trigger the registration modal dialog.

For complete frontend implementation details, see [Support Form Frontend](/axchisan/CoopAgronet/3.4-support-form-frontend).

**Sources:** [backennd/db_interaction/enviar_pregunta.php L32](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L32-L32)

---

## Implementation Characteristics

### Strengths

1. **SQL Injection Protection**: Uses prepared statements consistently across both queries
2. **Referential Integrity**: Foreign key relationship ensures questions link to valid users
3. **User Verification**: Validates registration status before accepting questions
4. **Resource Management**: Properly closes database connections and statements
5. **Consistent Response Format**: Returns uniform JSON structure for all outcomes

### Limitations

1. **No Rate Limiting**: Endpoint accepts unlimited requests from same user
2. **No Question Deduplication**: Allows submission of identical questions multiple times
3. **No Sanitization**: The `$nombre` field is received but not used or validated
4. **HTML in Error Messages**: Embedding HTML (`<a>` tag) in error responses creates potential XSS vector if client doesn't properly escape
5. **No CSRF Protection**: No token validation to prevent cross-site request forgery

**Sources:** [backennd/db_interaction/enviar_pregunta.php L9](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L9-L9)

 [backennd/db_interaction/enviar_pregunta.php L32](https://github.com/axchisan/CoopAgronet/blob/e8818744/backennd/db_interaction/enviar_pregunta.php#L32-L32)