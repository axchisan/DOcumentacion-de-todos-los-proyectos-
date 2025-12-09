# Getting Started

> **Relevant source files**
> * [pom.xml](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml)
> * [src/main/java/com/adso/el_taller_de_adso/login/login.form](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.form)
> * [src/main/java/com/adso/el_taller_de_adso/login/login.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java)

This document provides instructions for building, configuring, and launching **El Taller de ADSO** (The ADSO Mechanic Workshop) application for the first time. It covers system requirements, build procedures, database configuration, and initial authentication. For detailed information about the overall application architecture and MDI structure, see [Application Architecture](/BrayanTirado/Servicio-Mec-nico/3-application-architecture). For specifics on the authentication implementation and security model, see [Authentication System](/BrayanTirado/Servicio-Mec-nico/2.2-authentication-system). For database schema and connection management details, see [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer).

---

## System Requirements

The application has specific runtime and build-time requirements that must be satisfied before deployment.

### Required Software

| Component | Version | Purpose |
| --- | --- | --- |
| Java Development Kit (JDK) | 22 | Compilation and runtime environment |
| Apache Maven | 3.6+ | Dependency management and build automation |
| PostgreSQL | 9.5+ | Relational database backend |

### Key Dependencies

The application relies on the following third-party libraries, managed through Maven:

| Library | Version | Usage |
| --- | --- | --- |
| `postgresql` | 42.7.1 | JDBC driver for PostgreSQL connectivity |
| `jcalendar` | 1.4 | Date picker components in service forms |
| `AbsoluteLayout` | RELEASE260 | NetBeans GUI builder layout manager |
| `jasperreports` | 6.20.0 | Report generation engine |
| `jasperreports-functions` | 6.20.0 | Extended reporting functions |
| `itext` | 2.1.7 | PDF document generation |
| `poi` / `poi-ooxml` | 5.2.5 | Excel file manipulation |
| `jfreechart` | 1.5.4 | Data visualization and charting |

**Sources:** [pom.xml L14-L67](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L14-L67)

---

## Build Process

### Build Configuration Overview

The project uses Maven with the assembly plugin to produce a single executable JAR file containing all dependencies. The main entry point is defined as `com.adso.el_taller_de_adso.login.login`.

```

```

**Maven Build Diagram: From Source to Executable**

**Sources:** [pom.xml L1-L95](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L1-L95)

### Building the Application

Execute the following command in the project root directory:

```

```

This command performs the following operations:

1. **Clean**: Removes the `target/` directory from previous builds
2. **Compile**: Compiles all Java source files using Java 22 compiler settings [pom.xml L16-L17](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L16-L17)
3. **Package**: Creates a standard JAR file
4. **Assembly**: Executes the `maven-assembly-plugin` to bundle all dependencies into a single executable JAR [pom.xml L72-L92](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L72-L92)

The resulting artifact is located at:

```
target/El_Taller_De_ADSO-1.0-SNAPSHOT-jar-with-dependencies.jar
```

**Sources:** [pom.xml L69-L94](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L69-L94)

---

## Database Setup

### PostgreSQL Database Creation

Create a new PostgreSQL database for the application. The default connection configuration in `ConexionBD` expects:

* **Database Name**: (Configured in `ConexionBD.conectar()` method)
* **Host**: localhost
* **Port**: 5432 (PostgreSQL default)
* **Encoding**: UTF-8

Execute the following SQL to create the database:

```

```

### Required Database Schema

The application requires the following core tables for operation:

| Table Name | Purpose | Key Relationships |
| --- | --- | --- |
| `roles` | User role definitions | Referenced by `clientes.rol` |
| `clientes` | Customer records and user accounts | References `roles` table |
| `vehiculos` | Vehicle registration data | Foreign key to `clientes` |
| `productos` | Inventory items (parts, supplies) | Referenced by `servicio_productos` |
| `servicios` | Service records | Foreign keys to `clientes`, `vehiculos` |
| `servicio_productos` | Junction table for service-product associations | Foreign keys to `servicios`, `productos` |

### Authentication Table Structure

The authentication system requires specific columns in the `clientes` and `roles` tables:

**`clientes` table must include:**

* `correo` (VARCHAR) - User's email address, used as login username
* `password` (VARCHAR) - Plain text password (security consideration noted)
* `nombre` (VARCHAR) - User's full name
* `rol` (INTEGER) - Foreign key to `roles.id`

**`roles` table must include:**

* `id` (INTEGER, PRIMARY KEY)
* `nombre` (VARCHAR) - Role name (e.g., "Admin", "Mecánico", "Cliente")

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L166-L169](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L166-L169)

### Database Connection Configuration

The application uses the `ConexionBD` class (static utility) to manage database connections. To configure the connection parameters, locate and modify the `ConexionBD.conectar()` method with your PostgreSQL credentials:

* Database URL
* Username
* Password

For details on connection management implementation, see [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer).

**Sources:** Referenced in [src/main/java/com/adso/el_taller_de_adso/login/login.java L163](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L163-L163)

---

## Launching the Application

### Running the Executable JAR

After a successful build, launch the application using:

```

```

The application will display the login window (`login.java` JFrame) as the entry point.

**Sources:** [pom.xml L18](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L18-L18)

 [pom.xml L83](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L83-L83)

### Application Entry Flow

```

```

**Application Launch Sequence**

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L208-L237](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L208-L237)

---

## Authentication and First Login

### Login Window Interface

The login form consists of two input fields:

* **Usuario** (User): Text field for entering username/email [src/main/java/com/adso/el_taller_de_adso/login/login.java L44](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L44-L44)
* **Contraseña** (Password): Password field for entering credentials [src/main/java/com/adso/el_taller_de_adso/login/login.java L45](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L45-L45)

### Authentication Flow

The login system implements a dual authentication strategy: hardcoded Super Admin credentials and database-driven user authentication.

```

```

**Authentication Decision Flow**

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L144-L203](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L144-L203)

### Default Super Admin Credentials

For initial system access and administrative configuration, use the hardcoded Super Admin account:

* **Username**: `Admin`
* **Password**: `super123`

This authentication bypass is implemented at [src/main/java/com/adso/el_taller_de_adso/login/login.java L155-L160](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L155-L160)

 and does **not** require database connectivity.

**Security Consideration**: This hardcoded credential should be removed or secured in production deployments.

### Database User Authentication

For standard users, authentication queries the `clientes` table joined with the `roles` table:

```

```

The system stores passwords in **plain text** in the database [src/main/java/com/adso/el_taller_de_adso/login/login.java L169](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L169-L169)

 which presents a security vulnerability. Production systems should implement password hashing (bcrypt, PBKDF2, or similar).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L166-L173](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L166-L173)

### Post-Authentication Behavior

Upon successful authentication (either Super Admin or database user):

1. The login window is disposed [src/main/java/com/adso/el_taller_de_adso/login/login.java L157](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L157-L157)  or [line 182](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/line 182)
2. A new `AplicacionPrincipal` instance is created and displayed [src/main/java/com/adso/el_taller_de_adso/login/login.java L158](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L158-L158)  or [lines 185-189](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/lines 185-189)
3. For database users, authentication uses `SwingUtilities.invokeLater()` to ensure thread safety
4. The main MDI application window becomes visible with full menu access

For detailed information about the main application window structure, see [Main Application Window](/BrayanTirado/Servicio-Mec-nico/3.1-main-application-window).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L155-L189](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L155-L189)

---

## Initial Configuration Checklist

Before regular operation, verify the following:

* PostgreSQL database created and schema deployed
* Database connection parameters configured in `ConexionBD` class
* Application builds successfully without errors
* Login window launches and displays correctly
* Super Admin credentials authenticate successfully
* At least one database user record exists in `clientes` table with valid `rol` reference
* Database user authentication tested and functional
* Main application window (`AplicacionPrincipal`) launches after login

---

## Troubleshooting Common Issues

### Build Failures

**Symptom**: Maven build fails with dependency resolution errors

**Solution**: Ensure Maven has internet connectivity to download dependencies from Maven Central. Check [pom.xml L21-L67](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/pom.xml#L21-L67)

 for dependency versions.

### Database Connection Errors

**Symptom**: Exception message "Error de conexión o consulta" displayed after login attempt

**Solution**:

* Verify PostgreSQL service is running
* Confirm database exists and is accessible
* Check `ConexionBD.conectar()` configuration matches your PostgreSQL setup
* Review stack trace printed to console [src/main/java/com/adso/el_taller_de_adso/login/login.java L200](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L200-L200)

### Login Authentication Fails

**Symptom**: "Usuario o contraseña incorrectos" message despite correct credentials

**Solution**:

* For Super Admin: Verify exact case-sensitive match: `Admin` / `super123`
* For database users: Query `clientes` table directly to confirm record exists
* Check `roles` table has matching `id` for the user's `rol` value
* Confirm JOIN between `clientes` and `roles` returns results

### Application Window Does Not Appear

**Symptom**: Login window closes but main application does not display

**Solution**:

* Check console for exception stack traces
* Ensure `AplicacionPrincipal` class exists and compiles correctly
* Verify all GUI form files (`.form`) are included in build resources

**Sources:** [src/main/java/com/adso/el_taller_de_adso/login/login.java L198-L201](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/login/login.java#L198-L201)

---

## Next Steps

After successfully launching the application:

1. **Explore the Menu Structure**: The main application provides five primary menus (Vehículos, Servicios, Clientes, Reportes, Inventarios). See [Main Application Window](/BrayanTirado/Servicio-Mec-nico/3.1-main-application-window) for menu navigation details.
2. **Register Initial Data**: Begin by registering clients ([Client Registration](/BrayanTirado/Servicio-Mec-nico/7.1-client-registration)), vehicles ([Vehicle CRUD Operations](/BrayanTirado/Servicio-Mec-nico/6.1-vehicle-crud-operations)), and inventory products ([Adding Products](/BrayanTirado/Servicio-Mec-nico/5.2-adding-products)).
3. **Create Service Records**: Once base data exists, create service records linking clients, vehicles, and products. See [Creating Services](/BrayanTirado/Servicio-Mec-nico/4.1-creating-services).
4. **Generate Reports**: Utilize the reporting functionality powered by JasperReports to analyze workshop operations.