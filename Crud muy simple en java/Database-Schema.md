# Database Schema

> **Relevant source files**
> * [build/classes/repository/conexionDB.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class)
> * [build/classes/services/alumnoDataChange.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class)
> * [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

## Purpose and Scope

This document details the MySQL database schema used by the crud3 application, including database structure, table definitions, column specifications, and the relationship between the database schema and the application's data model. For information about establishing database connections, see [Database Connection (conexionDB)](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/6.1-database-connection-(conexiondb)). For details on CRUD operations performed against this schema, see [CRUD Operations](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/6.3-crud-operations). For the corresponding Java data model, see [Student Model (alumno)](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/5.1-student-model-(alumno)).

## Database Overview

The crud3 application uses a MySQL database to persist student records. The database configuration is hardcoded in the connection layer and uses the following parameters:

| Parameter | Value |
| --- | --- |
| Database Server | `localhost:3306` |
| Database Name | `colegio2` |
| Username | `root` |
| Password | (empty) |
| JDBC URL | `jdbc:mysql://localhost:3306/colegio2` |

**Sources:** [build/classes/repository/conexionDB.class L4](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L4-L4)

## Database Structure Diagram

```

```

**Sources:** [build/classes/services/alumnoDataChange.class L2](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class#L2-L2)

 [src/model/alumno.java L5-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L5-L9)

## Table: alumnoss

The `alumnoss` table is the sole data storage table in the `colegio2` database. It stores student (alumno) records with basic contact information.

### Column Definitions

| Column Name | Data Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `int` | PRIMARY KEY, AUTO_INCREMENT | Unique identifier for each student record |
| `nombre` | `varchar` | NOT NULL (enforced by application) | Student's first name |
| `apellido` | `varchar` | NOT NULL (enforced by application) | Student's last name |
| `telefono` | `varchar` | NOT NULL (enforced by application) | Student's phone number |
| `correo` | `varchar` | NOT NULL (enforced by application) | Student's email address |

### Key Characteristics

* **Primary Key**: The `id` column serves as the primary key and is auto-incremented by the database
* **No Foreign Keys**: The table has no foreign key relationships; it is a standalone entity
* **String Data Types**: All non-id fields are stored as string types (varchar) to accommodate various formatting patterns
* **Application-Level Validation**: Field constraints (NOT NULL, format validation) are enforced in the presentation layer rather than at the database level

**Sources:** [src/model/alumno.java L5-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L5-L9)

 [build/classes/services/alumnoDataChange.class L2](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class#L2-L2)

## Schema-to-Model Mapping

The `alumnoss` table maps directly to the `alumno` Java class in the application's data model layer.

```

```

**Sources:** [src/model/alumno.java L3-L20](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L3-L20)

 [build/classes/services/alumnoDataChange.class L2](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class#L2-L2)

### Field Mapping Details

| Database Column | Java Field | Getter Method | Setter Method |
| --- | --- | --- | --- |
| `id` | `int id` | `getId()` | `setId(int)` |
| `nombre` | `String nombre` | `getNombre()` | `setNombre(String)` |
| `apellido` | `String apellido` | `getApellido()` | `setApellido(String)` |
| `telefono` | `String telefono` | `getTelefono()` | `setTelefono(String)` |
| `correo` | `String correo` | `getCorreo()` | `setCorreo(String)` |

**Sources:** [src/model/alumno.java L22-L60](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L22-L60)

## SQL Operations Against Schema

The application performs INSERT operations against the `alumnoss` table using a PreparedStatement with the following SQL:

```

```

### Key Observations

1. **Auto-Increment Handling**: The `id` field is omitted from the INSERT statement, indicating that the database automatically generates this value via AUTO_INCREMENT
2. **Parameterized Query**: All values are bound as parameters (`?`) to prevent SQL injection
3. **No UPDATE or DELETE**: The current implementation only supports Create operations; Update and Delete operations are not implemented in the codebase

**Sources:** [build/classes/services/alumnoDataChange.class L2](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class#L2-L2)

## Database Schema Flow Diagram

```

```

**Sources:** [build/classes/services/alumnoDataChange.class L2-L12](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class#L2-L12)

 [build/classes/repository/conexionDB.class L4](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L4-L4)

 [src/model/alumno.java L3-L20](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L3-L20)

## Schema Initialization

The database schema must be created manually before the application can run successfully. The application does not include schema migration scripts or automatic table creation logic. Administrators must ensure that:

1. MySQL server is running on `localhost:3306`
2. Database `colegio2` exists
3. Table `alumnoss` is created with the correct column structure
4. The `root` user has appropriate permissions (or connection parameters must be modified in [conexionDB](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/conexionDB) )

**Sources:** [build/classes/repository/conexionDB.class L4](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L4-L4)

## Data Integrity Considerations

### Application-Level Enforcement

The schema relies heavily on application-level validation rather than database constraints:

* **Required Fields**: All fields except `id` are validated in the GUI layer via `ValidadarDatos` before submission
* **No Database-Level Constraints**: The schema does not enforce NOT NULL, UNIQUE, or CHECK constraints at the database level
* **No Referential Integrity**: The single-table design has no foreign key relationships

### Implications

| Aspect | Status | Risk Level | Mitigation |
| --- | --- | --- | --- |
| NULL Values | No DB constraint | Medium | Application validation in GUI layer |
| Duplicate Records | No UNIQUE constraint | Medium | No duplicate detection implemented |
| Data Format | No CHECK constraints | Low | Client-side validation handles basic format checks |
| Connection Credentials | Hardcoded in code | High | Should be externalized to configuration file |

**Sources:** [src/model/alumno.java L5-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L5-L9)

 [build/classes/services/alumnoDataChange.class L2](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class#L2-L2)