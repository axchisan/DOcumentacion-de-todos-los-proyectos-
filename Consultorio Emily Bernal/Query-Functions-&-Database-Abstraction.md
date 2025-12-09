# Query Functions & Database Abstraction

> **Relevant source files**
> * [Admin/historia_clinica.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php)
> * [Admin/inicioAdmin.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php)
> * [Admin/realizar_consulta.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/realizar_consulta.php)
> * [Admin/ver_historia.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php)

## Purpose and Scope

This document describes the database abstraction layer provided by [php/consultas.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/consultas.php)

 and [php/conexionDB.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/conexionDB.php)

 which serve as the primary interface between the application's business logic and the MySQL database. These files provide reusable query functions that encapsulate common data retrieval operations, reducing code duplication and centralizing database access patterns.

For information about the underlying database schema and table structures, see [Core Data Model](/axchisan/Consultorio_Emily_Bernal/4.1-core-data-model). For security aspects of database queries including SQL injection prevention, see [SQL Injection Prevention](/axchisan/Consultorio_Emily_Bernal/5.3-sql-injection-prevention).

---

## Architecture Overview

The database abstraction layer follows a two-tier structure: a connection manager and a query function library.

### Database Abstraction Architecture

```

```

**Sources:** [Admin/inicioAdmin.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L4)

 [Admin/historia_clinica.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L3-L4)

 [Admin/ver_historia.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L3-L4)

 [Admin/realizar_consulta.php L6-L7](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/realizar_consulta.php#L6-L7)

---

## Connection Management: conexionDB.php

The [php/conexionDB.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/conexionDB.php)

 file establishes the database connection and makes it available to the entire application through the `$link` variable.

### Connection Pattern

All administrative pages follow this consistent pattern:

```

```

The `conexionDB.php` file:

* Establishes a mysqli connection to the MySQL database
* Exports a `$link` variable containing the connection object
* Handles connection errors (implementation details not visible in provided files)
* Sets character encoding for proper UTF-8 support

This connection object is then passed as the first parameter to all query functions in `consultas.php`.

**Sources:** [Admin/inicioAdmin.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L4)

 [Admin/historia_clinica.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L3-L4)

 [Admin/ver_historia.php L3-L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L3-L4)

---

## Core Query Functions: consultas.php

The [php/consultas.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/consultas.php)

 file provides a library of reusable query functions that encapsulate common database operations. Each function accepts the `$link` connection object and returns either a result set or a single row.

### Function Catalog

| Function Name | Return Type | Purpose | Used In |
| --- | --- | --- | --- |
| `consultarDoctor()` | Associative Array | Retrieve doctor information by ID | All admin pages |
| `consultarPaciente()` | Associative Array | Retrieve patient demographic and medical history | `ver_historia.php`, PDF generators |
| `MostrarCitas()` | mysqli_result | Retrieve pending/active appointments for a doctor | `inicioAdmin.php` |
| `MostrarCitasCompletadas()` | mysqli_result | Retrieve completed appointments for a doctor | `historia_clinica.php` |
| `ConsultarCitas()` | Associative Array | Retrieve specific appointment details by ID | `realizar_consulta.php` |
| `validarToken()` | Boolean | Validate session token against database | All admin pages |

---

## Function Usage Patterns

### consultarDoctor($link, $id_doctor)

This function retrieves comprehensive doctor information for display in the sidebar profile section.

**Function Signature:**

```

```

**Usage Example:**

```

```

**Return Fields:**

* `nombreD`: Doctor's first name
* `apellido`: Doctor's last name
* `sexo`: Gender (used to determine profile image)
* `correo_electronico`: Email address
* Additional fields from the `doctor` table

**Sources:** [Admin/inicioAdmin.php L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L26-L26)

 [Admin/historia_clinica.php L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L26-L26)

 [Admin/ver_historia.php L78](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L78-L78)

 [Admin/realizar_consulta.php L11](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/realizar_consulta.php#L11-L11)

---

### consultarPaciente($link, $id_paciente)

This function retrieves comprehensive patient information including demographics, medical history, and emergency contacts.

**Function Signature:**

```

```

**Usage Example:**

```

```

**Return Fields:** The function returns all fields from the `pacientes` table, including:

* Demographics: `nombre`, `apellido`, `fecha_nacimiento`, `sexo`, `cedula`
* Contact: `correo_electronico`, `telefono`, `lugar_direccion_residencia`
* Medical: `eps`, `tipo_sangre`, `alertas_medicas`, `ocupacion`, `estado_civil`
* Medical History ENUMs: `historia_cardiovasculares`, `historia_hemorragicas`, `historia_dermatologicas`, `historia_mentales`, `historia_diabetes`, `historia_cancer`, `historia_artritis`, `historia_alergias`, `historia_cirugias`, `historia_otros`
* Emergency Contact: `emergencia_nombre`, `emergencia_telefono`
* Minor Information: `menor_acompanante`, `menor_parentesco`, `menor_telefono`

**Sources:** [Admin/ver_historia.php L37-L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L37-L43)

---

### MostrarCitas($link, $id_doctor)

This function retrieves all pending and active appointments for a specific doctor, joining with patient, consultation type, and doctor tables.

**Function Signature:**

```

```

**Usage Example:**

```

```

**Return Fields:** The result set includes fields from multiple tables:

* From `citas`: `id_cita`, `fecha_cita`, `hora_cita`, `estado`, `diagnostico`, `descripcion`, `medicina`
* From `pacientes`: `nombre`, `apellido`, `años` (calculated age)
* From `consultas`: `tipo` (consultation type)
* From `doctor`: Doctor name fields

**Sources:** [Admin/inicioAdmin.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L27-L27)

 [Admin/inicioAdmin.php L119-L132](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L119-L132)

---

### MostrarCitasCompletadas($link, $id_doctor)

Similar to `MostrarCitas()` but filters specifically for completed appointments (estado = 'A').

**Function Signature:**

```

```

**Usage Example:**

```

```

**Sources:** [Admin/historia_clinica.php L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L27-L27)

 [Admin/historia_clinica.php L102-L120](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L102-L120)

---

### ConsultarCitas($link, $id_cita)

This function retrieves detailed information about a specific appointment by its ID.

**Function Signature:**

```

```

**Usage Example:**

```

```

**Sources:** [Admin/realizar_consulta.php L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/realizar_consulta.php#L9-L9)

---

### validarToken($link, $id_usuario, $tipo, $session_token)

This critical security function validates that a session token matches the token stored in the database, preventing concurrent login sessions.

**Function Signature:**

```

```

**Parameters:**

* `$link`: Database connection
* `$id_usuario`: Doctor ID to validate
* `$tipo`: User type ('Doctor' in this system)
* `$session_token`: Token from `$_SESSION['session_token']`

**Return Value:**

* `true`: Token is valid, session is authorized
* `false`: Token mismatch, indicating concurrent login elsewhere

**Usage Example:**

```

```

**Sources:** [Admin/inicioAdmin.php L17-L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L17-L24)

 [Admin/historia_clinica.php L17-L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L17-L24)

 [Admin/ver_historia.php L16-L23](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L16-L23)

---

## Query Function Usage Flow

The following diagram illustrates how a typical administrative page uses the abstraction layer:

```

```

**Sources:** [Admin/inicioAdmin.php L1-L28](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L28)

---

## Hybrid Approach: Abstraction + Direct Queries

While the system provides abstraction functions for common operations, complex or page-specific queries are written directly using prepared statements. This hybrid approach balances code reusability with query flexibility.

### Direct Query Example

The [Admin/ver_historia.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php)

 file demonstrates this pattern:

```

```

**When to Use Each Approach:**

| Use Abstraction Functions | Use Direct Queries |
| --- | --- |
| Retrieving complete entity records | Complex multi-table joins |
| Common queries used across multiple pages | Page-specific query logic |
| Standard CRUD operations | Dynamic WHERE clauses |
| Token validation | Queries requiring special handling |

**Sources:** [Admin/ver_historia.php L37-L66](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L37-L66)

 [Admin/ver_historia.php L69-L75](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L69-L75)

---

## Query Function to Database Table Mapping

This diagram shows how query functions map to database tables and which pages consume them:

```

```

**Sources:** [Admin/inicioAdmin.php L26-L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L26-L27)

 [Admin/historia_clinica.php L26-L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L26-L27)

 [Admin/ver_historia.php L37-L78](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L37-L78)

 [Admin/realizar_consulta.php L9-L11](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/realizar_consulta.php#L9-L11)

---

## Security Through Abstraction

All query functions in `consultas.php` implement security best practices:

### Prepared Statements

Every function uses mysqli prepared statements with parameter binding to prevent SQL injection:

```

```

### Access Control

Functions like `MostrarCitas()` and `MostrarCitasCompletadas()` implicitly filter results by doctor ID, ensuring doctors can only access their own appointments:

```

```

This encapsulation prevents developers from accidentally creating queries that leak data across doctor accounts.

For comprehensive security documentation, see [Security & Authentication](/axchisan/Consultorio_Emily_Bernal/5-security-and-authentication) and [Token Validation System](/axchisan/Consultorio_Emily_Bernal/5.2-token-validation-system).

**Sources:** [Admin/ver_historia.php L51-L60](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L51-L60)

---

## Data Transformation in Query Functions

Query functions perform data transformations beyond simple retrieval:

### Age Calculation

The `MostrarCitas()` and `MostrarCitasCompletadas()` functions calculate patient age from `fecha_nacimiento`:

```

```

This calculation is performed in SQL using date functions, avoiding repetitive PHP date arithmetic.

**Sources:** [Admin/inicioAdmin.php L122](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L122-L122)

 [Admin/historia_clinica.php L105](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L105-L105)

### Multi-Table Joins

Functions automatically join related tables to provide complete data:

```

```

**Sources:** [Admin/inicioAdmin.php L119-L127](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L119-L127)

---

## Error Handling Patterns

### Boolean Return for Validation

The `validarToken()` function returns a clear boolean indicating success or failure:

```

```

### False on Record Not Found

The `consultarPaciente()` function returns `false` when a patient record doesn't exist:

```

```

**Sources:** [Admin/ver_historia.php L37-L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L37-L43)

---

## Integration with Session Management

Query functions integrate seamlessly with the session-based authentication system:

```

```

**Sources:** [Admin/inicioAdmin.php L1-L28](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L28)

---

## Common Usage Pattern Summary

Every administrative page follows this standardized pattern:

1. **Session Initialization** ``` ```
2. **Include Abstraction Layer** ``` ```
3. **Validate Session** ``` ```
4. **Validate Token** ``` ```
5. **Load Required Data** ``` ```
6. **Render Page with Data**

This consistent pattern across all pages demonstrates the effectiveness of the abstraction layer in promoting code standardization and reducing duplication.

**Sources:** [Admin/inicioAdmin.php L1-L28](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L28)

 [Admin/historia_clinica.php L1-L28](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/historia_clinica.php#L1-L28)

 [Admin/ver_historia.php L1-L78](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L1-L78)