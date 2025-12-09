# Architecture

> **Relevant source files**
> * [build/classes/repository/conexionDB.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class)
> * [build/classes/services/alumnoDataChange.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/services/alumnoDataChange.class)
> * [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)
> * [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

## Purpose and Scope

This document provides a high-level overview of the crud3 application architecture, describing how the major components are organized and how they interact. The application follows a classic three-tier architecture pattern with clear separation between presentation, business logic, and data access concerns.

For detailed information about specific layers, see [Application Layers](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3.1-application-layers). For step-by-step data flow through the system, see [Data Flow](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3.2-data-flow). For a complete list of technologies and frameworks used, see [Technology Stack](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3.3-technology-stack).

## Architectural Pattern

The crud3 application implements a **three-tier architecture** consisting of:

| Layer | Package | Primary Classes | Responsibility |
| --- | --- | --- | --- |
| **Presentation Layer** | `GUI` | `GUI`, `AdminForm`, `Paneles` | User interface components, event handling, input validation |
| **Business Logic Layer** | `services`, `model` | `alumnoDataChange`, `alumno` | Data transformation, business rules, domain model |
| **Data Access Layer** | `repository` | `conexionDB` | Database connectivity, SQL execution, connection management |

This separation ensures that changes to one layer have minimal impact on others. For example, the database implementation can be changed without modifying the GUI code, as long as the service layer interface remains consistent.

**Sources**: [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

 [src/model/alumno.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

## System Component Overview

```

```

**Diagram: High-Level Component Architecture**

This diagram shows how the major components relate to each other. The `GUI.GUI` class is the primary entry point where users interact with the system. It creates `alumno` model objects and invokes the `alumnoDataChange.guardado()` method to persist data. The service layer obtains database connections through `conexionDB.conectar()` and executes SQL statements against the MySQL `colegio2` database.

**Sources**: [src/GUI/GUI.java L15-L179](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L15-L179)

 [src/model/alumno.java L3-L62](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L3-L62)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

## Core Component Responsibilities

### Presentation Layer Components

| Class | Location | Key Methods | Purpose |
| --- | --- | --- | --- |
| `GUI` | `GUI.GUI` | `ValidadarDatos()`, `LimpiarCampos()`, `btnSave1ActionPerformed()`, `btnCleanActionPerformed()` | Main data entry form with input fields for student information |
| `AdminForm` | `GUI.AdminForm` | N/A | Administrative interface for system management |
| `Paneles` | `Paneles` | N/A | Additional user interface component |

The presentation layer is responsible for:

* Rendering user interface components using Java Swing
* Capturing user input through text fields (`txtNombre`, `txtApellido`, `txtTelefono`, `txtCorreo`)
* Client-side validation via `ValidadarDatos()` method
* Displaying feedback messages using `JOptionPane`
* Managing form state through `LimpiarCampos()` method

**Sources**: [src/GUI/GUI.java L15-L179](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L15-L179)

 [src/GUI/AdminForm.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java)

 [src/Paneles.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java)

### Business Logic Layer Components

| Class | Location | Key Elements | Purpose |
| --- | --- | --- | --- |
| `alumno` | `model.alumno` | Fields: `id`, `nombre`, `apellido`, `telefono`, `correo`Getters and setters for all fields | Student data model representing a single student record |
| `alumnoDataChange` | `services.alumnoDataChange` | Static method: `guardado(alumno)`Static field: `mensaje` | Service for persisting student data to the database |

The business logic layer handles:

* Data modeling through the `alumno` class
* Data validation and transformation
* Orchestrating persistence operations
* Maintaining operation status messages in `alumnoDataChange.mensaje`

**Sources**: [src/model/alumno.java L3-L62](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L3-L62)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

### Data Access Layer Components

| Class | Location | Key Methods | Purpose |
| --- | --- | --- | --- |
| `conexionDB` | `repository.conexionDB` | `conectar()` returns `java.sql.Connection` | Database connection factory that loads the MySQL driver and establishes JDBC connections |

The data access layer provides:

* JDBC driver loading (`com.mysql.cj.jdbc.Driver`)
* Connection establishment to `jdbc:mysql://localhost:3306/colegio2`
* Connection resource management
* Abstraction over database-specific connection logic

**Sources**: [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

 [build/classes/repository/conexionDB.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class)

## Component Interaction Flow

```

```

**Diagram: Detailed Component Interaction Sequence**

This sequence diagram illustrates the complete flow of data from user input through validation, model creation, service invocation, database connection establishment, SQL execution, and user feedback. Each step shows the actual method names and class interactions that occur during a save operation.

**Sources**: [src/GUI/GUI.java L87-L89](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L87-L89)

 [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

## Package Structure

The application is organized into the following package hierarchy:

```
src/
├── GUI/
│   ├── GUI.java                    (Main data entry form)
│   └── AdminForm.java              (Administrative interface)
├── model/
│   └── alumno.java                 (Student entity)
├── services/
│   └── alumnoDataChange.java       (Data persistence service)
├── repository/
│   └── conexionDB.java             (Database connection management)
└── Paneles.java                     (Additional UI component)
```

**Package Naming Conventions:**

* `GUI` - Contains all Swing-based user interface components
* `model` - Contains domain entities and data transfer objects
* `services` - Contains business logic and service layer classes
* `repository` - Contains data access and database interaction classes

**Sources**: Project directory structure

## Technology Integration Points

The architecture integrates several technologies at specific boundaries:

| Integration Point | Technology | Implementation Location | Purpose |
| --- | --- | --- | --- |
| **UI Framework** | Java Swing | `GUI.GUI`, `GUI.AdminForm`, `Paneles` | Provides graphical user interface components |
| **JDBC Driver** | MySQL Connector/J | `repository.conexionDB.conectar()` | Enables Java-to-MySQL communication |
| **SQL Execution** | JDBC PreparedStatement | `services.alumnoDataChange.guardado()` | Executes parameterized SQL queries |
| **Build System** | Apache Ant | `build.xml`, `nbproject/build-impl.xml` | Compiles, packages, and deploys the application |

The application uses standard Java APIs (`javax.swing`, `java.sql`) to maintain portability and follows JDBC best practices for database interaction.

**Sources**: [src/GUI/GUI.java L1-L179](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L1-L179)

 [src/repository/conexionDB.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/repository/conexionDB.java)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [build.xml](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml)

## Architectural Characteristics

The crud3 architecture exhibits the following key characteristics:

1. **Separation of Concerns**: Each layer has a distinct responsibility with minimal overlap
2. **Dependency Direction**: Dependencies flow from presentation → business logic → data access → database
3. **Static Service Methods**: `alumnoDataChange.guardado()` is a static method, avoiding the need for service instantiation
4. **Direct Field Access**: The `alumno` model uses package-private fields with public getters/setters
5. **Connection-per-Operation**: Each database operation obtains a new connection via `conexionDB.conectar()`
6. **Swing Event-Driven**: User interactions trigger ActionListener callbacks that invoke business logic

**Sources**: [src/GUI/GUI.java L87-L93](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L87-L93)

 [src/services/alumnoDataChange.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/services/alumnoDataChange.java)

 [src/model/alumno.java L5-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/model/alumno.java#L5-L9)