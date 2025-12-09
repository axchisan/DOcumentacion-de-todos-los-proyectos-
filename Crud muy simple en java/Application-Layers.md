# Application Layers

> **Relevant source files**
> * [build/classes/repository/conexionDB.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class)
> * [build/classes/services/alumnoDataChange.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class)
> * [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)
> * [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

## Purpose and Scope

This document describes the three-tier architecture of the crud3 application, detailing each layer's responsibilities, key classes, and how they interact. The application follows a classic layered architecture pattern with clear separation between presentation, business logic, and data access concerns.

For information about the complete data flow through these layers, see [Data Flow](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3.2-data-flow). For details on specific UI components, see [User Interface Layer](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/4-user-interface-layer). For database-specific operations, see [Data Access Layer](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/6-data-access-layer).

---

## Three-Tier Architecture Overview

The crud3 application implements a three-tier architecture where each layer has distinct responsibilities and communicates through well-defined interfaces.

```

```

**Sources:** [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

 [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

---

## Presentation Layer

The presentation layer handles all user interactions and is implemented using Java Swing components. This layer is responsible for rendering the UI, capturing user input, performing client-side validation, and displaying results.

### Key Components

| Class | Package | Primary Responsibility | Key Methods |
| --- | --- | --- | --- |
| `GUI` | `GUI` | Main data entry form | `ValidadarDatos()`, `LimpiarCampos()`, `btnSave1ActionPerformed()` |
| `AdminForm` | `GUI` | Administrative interface | TBD |
| `Paneles` | (default) | Additional UI component | TBD |

### Layer Characteristics

The presentation layer in this application:

* **Extends** `javax.swing.JFrame` for top-level windows
* **Contains** GUI components: `JTextField`, `JButton`, `JLabel`, `JPanel`
* **Handles** user events through `ActionListener` implementations
* **Performs** client-side validation before invoking business logic
* **Displays** success/error messages via `JOptionPane`
* **Does not** directly access the database or contain SQL logic

### Main GUI Structure

The `GUI.GUI` class ([src/GUI/GUI.java L15](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L15-L15)

) serves as the primary entry point for user interaction:

```

```

**Sources:** [src/GUI/GUI.java L37-L142](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L37-L142)

---

## Business Logic Layer

The business logic layer contains the application's core functionality, including data models, validation rules, and service operations. This layer acts as an intermediary between the presentation and data access layers.

### Key Components

| Class | Package | Responsibility | Key Methods/Fields |
| --- | --- | --- | --- |
| `alumno` | `model` | Student data entity | `getNombre()`, `setNombre()`, `getApellido()`, etc. |
| `alumnoDataChange` | `services` | Data persistence service | `guardado(alumno)` |
| `ValidadarDatos` | `GUI.GUI` | Input validation | (method in GUI class) |

### Data Model: alumno

The `alumno` class ([src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

) represents a student entity with five fields:

| Field | Type | Purpose |
| --- | --- | --- |
| `id` | `int` | Primary key (auto-increment) |
| `nombre` | `String` | Student's first name |
| `apellido` | `String` | Student's last name |
| `telefono` | `String` | Phone number |
| `correo` | `String` | Email address |

The class provides:

* Default constructor ([src/model/alumno.java L11-L12](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L11-L12) )
* Parameterized constructor ([src/model/alumno.java L14-L20](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L14-L20) )
* Standard getters and setters for all fields ([src/model/alumno.java L22-L60](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L22-L60) )

### Service Layer: alumnoDataChange

The `alumnoDataChange` class ([src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

) provides the persistence service:

**Static field:**

* `mensaje` (String): Holds the result message for display to the user

**Methods:**

* `guardado(alumno a)`: Accepts an `alumno` object and persists it to the database, returning `boolean` success status

```

```

**Sources:** [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

### Validation Logic

Client-side validation is implemented in `ValidadarDatos()` method ([src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

):

**Validation sequence:**

1. Check `txtNombre` is not empty ([src/GUI/GUI.java L144-L146](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L144-L146) )
2. Check `txtApellido` is not empty ([src/GUI/GUI.java L147-L149](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L147-L149) )
3. Check `txtTelefono` is not empty ([src/GUI/GUI.java L150-L152](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L150-L152) )
4. Check `txtCorreo` is not empty ([src/GUI/GUI.java L153-L155](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L153-L155) )
5. If all valid, create `alumno` object and invoke service ([src/GUI/GUI.java L157-L165](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L165) )

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

---

## Data Access Layer

The data access layer manages all database interactions, providing connection management and abstracting JDBC operations from the business logic.

### Key Components

| Class | Package | Responsibility | Key Methods |
| --- | --- | --- | --- |
| `conexionDB` | `repository` | Database connection management | `conectar()` |

### Connection Management: conexionDB

The `conexionDB` class ([src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

) implements the connection management strategy:

**Method:** `conectar()`

* **Returns:** `java.sql.Connection`
* **Process:** 1. Loads MySQL JDBC driver: `com.mysql.cj.jdbc.Driver` 2. Creates connection with URL: `jdbc:mysql://localhost:3306/colegio2` 3. Uses credentials: username=`root`, password=`""` (empty) 4. Returns active Connection object or `null` on error

```

```

**Sources:** [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

 [build/classes/repository/conexionDB.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class)

### Database Operations

The service layer uses the connection to execute SQL operations:

**SQL Statement** (from `alumnoDataChange`):

```sql
INSERT INTO alumnoss (nombre, apellido, telefono, correo) VALUES (?, ?, ?, ?)
```

**Parameter Binding:**

1. `ps.setString(1, a.getNombre())`
2. `ps.setString(2, a.getApellido())`
3. `ps.setString(3, a.getTelefono())`
4. `ps.setString(4, a.getCorreo())`

**Sources:** [build/classes/services/alumnoDataChange.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class)

---

## Layer Communication Flow

The following diagram illustrates the complete interaction flow when a user saves student data:

```

```

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

---

## Layer Responsibilities Summary

| Layer | Package(s) | Classes | Allowed Dependencies | Prohibited Actions |
| --- | --- | --- | --- | --- |
| **Presentation** | `GUI`, (default) | `GUI`, `AdminForm`, `Paneles` | Business Logic Layer | Direct database access, SQL queries |
| **Business Logic** | `model`, `services` | `alumno`, `alumnoDataChange` | Data Access Layer | UI component manipulation, direct JDBC calls |
| **Data Access** | `repository` | `conexionDB` | JDBC API, MySQL Driver | Business logic decisions, UI operations |

### Design Principles Observed

1. **Separation of Concerns**: Each layer has a single, well-defined responsibility
2. **Dependency Direction**: Layers depend only on lower layers (Presentation → Business → Data Access)
3. **Encapsulation**: Database connection details are hidden in the repository layer
4. **Single Responsibility**: Each class has one primary purpose

### Architectural Benefits

* **Maintainability**: Changes to database configuration only affect `conexionDB`
* **Testability**: Business logic can be tested independently of UI and database
* **Flexibility**: Can replace MySQL with another database by modifying only the data access layer
* **Clear Contract**: Well-defined interfaces between layers

**Sources:** [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

 [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)