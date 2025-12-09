# Overview

> **Relevant source files**
> * [pom.xml](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml)
> * [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java)
> * [src/main/java/com/adso/el_taller_de_adso/login/login.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.form)
> * [src/main/java/com/adso/el_taller_de_adso/login/login.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java)

This document provides a high-level introduction to **El Taller de ADSO**, a desktop mechanical workshop management system. It covers the application's purpose, architectural structure, functional modules, and technology stack. For detailed information about specific subsystems, see the corresponding sections: [Getting Started](/BrayanTirado/Servicio-Mec-nico/2-getting-started) for setup instructions, [Application Architecture](/BrayanTirado/Servicio-Mec-nico/3-application-architecture) for in-depth architectural details, and sections [4](/BrayanTirado/Servicio-Mec-nico/4-service-management-module) through [7](/BrayanTirado/Servicio-Mec-nico/7-client-management-module) for module-specific documentation.

## Purpose and Scope

**El Taller de ADSO** is a comprehensive Java Swing desktop application designed to manage all aspects of a mechanical workshop's operations. The system handles vehicle registration and tracking, service management with product associations, client records, inventory control, and business reporting. It uses a PostgreSQL database backend for persistent storage and implements a Multiple Document Interface (MDI) pattern to allow users to work with multiple functional areas simultaneously.

The application supports two authentication modes: a hardcoded Super Admin account for administrative access and database-authenticated user accounts stored in the `clientes` table with role-based permissions.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L1-L406](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L1-L406)

 [src/main/java/com/adso/el_taller_de_adso/login/login.java L1-L251](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L1-L251)

 [pom.xml L1-L96](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L1-L96)

## System Architecture

### Application Structure

The application follows a hierarchical launch sequence with three distinct phases: authentication, main application initialization, and on-demand module loading.

**Application Initialization Flow**

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L144-L203](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L144-L203)

 [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L34-L36](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L34-L36)

 [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L259-L366](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L259-L366)

### Layered Architecture

The system implements a three-tier architecture with clear separation of concerns:

**Three-Tier Architecture Diagram**

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L8-L22](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L8-L22)

 [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L259-L366](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L259-L366)

**Layer Responsibilities:**

| Layer | Components | Responsibilities |
| --- | --- | --- |
| **Presentation** | `login`, `AplicacionPrincipal`, JInternalFrame modules | User interface, event handling, input validation, form management |
| **Business Logic** | DAO classes, Domain models | Data access abstraction, business rules, transaction management, data transformation |
| **Data** | `ConexionBD`, PostgreSQL database | Connection pooling (basic), SQL execution, data persistence |

The DAO pattern enforces separation: UI components never directly access the database. All database operations route through DAO classes that use `ConexionBD.conectar()` to obtain connections. The `ServicioDAO` class demonstrates advanced transaction management with explicit `setAutoCommit(false)`, `commit()`, and `rollback()` operations for multi-table operations.

**Sources:** [pom.xml L22-L26](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L22-L26)

## Core Functional Modules

The application provides five primary functional areas, each accessible through the menu bar in `AplicacionPrincipal`:

**Module Organization by Menu**

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L97-L240](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L97-L240)

### Module Summary

| Module | Primary Classes | Purpose | Wiki Reference |
| --- | --- | --- | --- |
| **Vehicle Management** | `MarcoVehiculo`, `ConsultaVehiculo` | Register vehicles with client associations, search by license plate, view service history | [Vehicle Management Module](/BrayanTirado/Servicio-Mec-nico/6-vehicle-management-module) |
| **Service Management** | `MarcoServicios`, `HistorialServicios` | Create services with product associations, track service history, manage transactions | [Service Management Module](/BrayanTirado/Servicio-Mec-nico/4-service-management-module) |
| **Client Management** | `MarcoClientes`, `MarcoGestionClientes` | Register new clients with role assignment, update/delete client records | [Client Management Module](/BrayanTirado/Servicio-Mec-nico/7-client-management-module) |
| **Inventory Management** | `FormularioProducto`, `BuscarProducto`, `EditarProducto`, `StockBajo`, `FacturacionProductos` | Add/edit products, search inventory, monitor low stock, process product orders | [Inventory Management Module](/BrayanTirado/Servicio-Mec-nico/5-inventory-management-module) |
| **Reporting** | `reporte_cliente`, `reporte_vehiculo`, `reporte_fecha`, `ingresos_generados` | Generate business intelligence reports using JasperReports | See individual report documentation |

**Sources:** [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L8-L22](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L8-L22)

## Technology Stack

### Core Dependencies

The application is built on Java 22 and uses Maven for dependency management and build automation. The main executable class is `com.adso.el_taller_de_adso.login.login`.

**Technology Stack Overview**

```

```

**Sources:** [pom.xml L14-L67](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L14-L67)

 [pom.xml L69-L94](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L69-L94)

### Key Dependencies Table

| Dependency | Version | Purpose in Application |
| --- | --- | --- |
| `postgresql` | 42.7.1 | JDBC driver for PostgreSQL database connectivity |
| `jcalendar` | 1.4 | Date picker components used in service and vehicle forms |
| `AbsoluteLayout` | RELEASE260 | NetBeans visual GUI builder layout manager |
| `jasperreports` | 6.20.0 | Generates PDF/Excel reports from `.jasper` templates |
| `jasperreports-functions` | 6.20.0 | Extended functions for JasperReports expressions |
| `itext` | 2.1.7 | PDF rendering backend for JasperReports |
| `poi` + `poi-ooxml` | 5.2.5 | Apache POI for Excel file export functionality |
| `jfreechart` | 1.5.4 | Chart generation for visual reports (e.g., income graphs) |

**Sources:** [pom.xml L21-L66](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L21-L66)

## Application Entry Points and Lifecycle

### Main Class and Initialization

The application entry point is defined in the Maven POM as `com.adso.el_taller_de_adso.login.login`. The lifecycle follows this sequence:

**Application Lifecycle Sequence**

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L208-L238](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L208-L238)

 [src/main/java/com/adso/el_taller_de_adso/login/login.java L144-L203](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L144-L203)

 [pom.xml L18](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L18-L18)

 [pom.xml L83](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L83-L83)

### MDI Container Initialization

The `AplicacionPrincipal` constructor initializes the MDI environment through the NetBeans-generated `initComponents()` method. This method constructs:

1. **JDesktopPane** (`desktopPane`): A container with a custom `paintComponent()` override that renders a background image from `/C:/Users/Brayan/Documents/NetBeansProjects/Servicio-Mec-nico/imagen/Imagen_Principal.png`
2. **JMenuBar** with five JMenu instances: Vehículos, Servicios, Clientes, Reportes, Inventarios
3. **ActionListener** registrations for each menu item that instantiate the corresponding JInternalFrame on demand

Menu item action handlers follow a consistent pattern:

```

```

This lazy instantiation approach means frames are only created when the user requests them, reducing initial memory footprint.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L34-L36](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L34-L36)

 [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L45-L256](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L45-L256)

 [src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java L259-L263](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/AplicacionPrincipal.java#L259-L263)

## Database Connection Management

The system uses a centralized connection utility class `ConexionBD` that all DAO classes depend on. This class provides static methods `conectar()` and `cerrar()` for obtaining and closing database connections. The connection parameters (URL, username, password) are configured within this class.

**Note**: The current implementation creates a new connection for each operation rather than using connection pooling, which may impact performance under high load. For database configuration details, see [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L163](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L163-L163)

## Build and Deployment

The Maven configuration specifies:

* **Artifact ID**: `El_Taller_De_ADSO`
* **Version**: `1.0-SNAPSHOT`
* **Packaging**: JAR
* **Main Class**: `com.adso.el_taller_de_adso.login.login`

The `maven-assembly-plugin` is configured to create a JAR with all dependencies bundled (descriptor: `jar-with-dependencies`). This produces a single executable JAR file that can be run with:

```

```

For detailed build instructions and dependency configuration, see [Build Configuration and Dependencies](/BrayanTirado/Servicio-Mec-nico/2.1-build-configuration-and-dependencies).

**Sources:** [pom.xml L7-L19](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L7-L19)

 [pom.xml L69-L94](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L69-L94)