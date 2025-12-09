# Prerequisites and Setup

> **Relevant source files**
> * [build/classes/repository/conexionDB.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class)
> * [manifest.mf](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/manifest.mf)
> * [nbproject/project.properties](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties)

## Purpose and Scope

This document details the software prerequisites, database configuration, and dependency setup required to build and run the crud3 student management application. It covers the installation and configuration of Java 21, MySQL database server, the colegio2 database schema, and the MySQL Connector/J JDBC driver.

For information about building the application after completing this setup, see [Building the Project](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.2-building-the-project). For running the compiled application, see [Running the Application](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.3-running-the-application).

---

## Software Requirements Overview

The following table summarizes all required and optional software components:

| Component | Version | Required | Purpose |
| --- | --- | --- | --- |
| Java Development Kit (JDK) | 21 | Yes | Compilation and runtime environment |
| MySQL Server | 5.7+ / 8.0+ | Yes | Database server for student records |
| MySQL Connector/J | 9.1.0 | Yes | JDBC driver for database connectivity |
| Apache Ant | 1.9+ | Yes | Build automation (bundled with NetBeans) |
| NetBeans IDE | 8.2+ | Optional | Integrated development environment |

**Sources:** [nbproject/project.properties L50-L51](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L50-L51)

 [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

---

## Java Development Kit (JDK) 21

### Installation Requirements

The crud3 application requires **Java SE Development Kit version 21** for both compilation and execution. This is explicitly configured in the project's build settings.

The project is configured with:

* **Source compatibility:** Java 21 ([nbproject/project.properties L50](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L50-L50) )
* **Target compatibility:** Java 21 ([nbproject/project.properties L51](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L51-L51) )

### Verification

Verify your Java installation by running:

```

```

Expected output should indicate version 21 or higher:

```
java version "21.x.x"
Java(TM) SE Runtime Environment
```

### Environment Configuration

Ensure the `JAVA_HOME` environment variable points to your JDK 21 installation directory. The build system requires this for proper compilation.

**Sources:** [nbproject/project.properties L50-L51](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L50-L51)

---

## MySQL Database Server

### Installation

A MySQL server instance is required to persist student records. The application is compatible with MySQL 5.7 or later, with MySQL 8.0 being recommended.

### Connection Configuration

The application connects to MySQL using the following default parameters:

| Parameter | Value | Location in Code |
| --- | --- | --- |
| Host | `localhost` | [repository/conexionDB](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/repository/conexionDB) |
| Port | `3306` | [repository/conexionDB](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/repository/conexionDB) |
| Database | `colegio2` | [repository/conexionDB](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/repository/conexionDB) |
| Username | `root` | [repository/conexionDB](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/repository/conexionDB) |
| Password | *(empty)* | [repository/conexionDB](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/repository/conexionDB) |

### JDBC Connection String

The full connection string used by the application:

```yaml
jdbc:mysql://localhost:3306/colegio2
```

**Sources:** [build/classes/repository/conexionDB.class L1-L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L1-L7)

---

## Database Setup

### Creating the Database

Execute the following SQL statement to create the required database:

```

```

### Database Schema: alumnoss Table

The application uses a single table named `alumnoss` to store student records. Create this table with the following schema:

```

```

### Table Structure

| Column | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | INT | PRIMARY KEY, AUTO_INCREMENT | Unique student identifier |
| `nombre` | VARCHAR(100) | NOT NULL | Student's first name |
| `apellido` | VARCHAR(100) | NOT NULL | Student's last name |
| `telefono` | VARCHAR(20) | NOT NULL | Contact phone number |
| `correo` | VARCHAR(150) | NOT NULL | Email address |

### Database Connection Flow

```

```

**Diagram: Database Connection Architecture**

This diagram maps the code entities responsible for database connectivity to the physical MySQL database structure.

**Sources:** [build/classes/repository/conexionDB.class L1-L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L1-L7)

---

## MySQL Connector/J JDBC Driver

### Driver Dependency

The application requires **MySQL Connector/J version 9.1.0** for JDBC connectivity. This JAR file must be present in the classpath during both compilation and runtime.

### Configuration in Project

The driver is configured as a file reference in the project properties:

**Driver Path Configuration:**

```

```

([nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

)

**Classpath Configuration:**

```

```

([nbproject/project.properties L39-L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L39-L41)

)

### Installation Steps

1. **Download** MySQL Connector/J 9.1.0 from the MySQL official website
2. **Extract** the archive to a permanent location on your system
3. **Update** the file reference in [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)  to match your local path
4. **Verify** the path exists and the JAR file is accessible

### Driver Loading

The application loads the MySQL JDBC driver at runtime using reflection:

```

```

This occurs in the `conexionDB.conectar()` method within the repository layer.

**Sources:** [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

 [nbproject/project.properties L39-L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L39-L41)

 [build/classes/repository/conexionDB.class L2-L3](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L2-L3)

---

## Dependency Resolution Diagram

```

```

**Diagram: MySQL Connector/J Dependency Configuration**

This diagram shows how the MySQL JDBC driver dependency is configured, referenced, and loaded in the application.

**Sources:** [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

 [nbproject/project.properties L39-L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L39-L41)

 [nbproject/project.properties L82-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L82-L84)

 [build/classes/repository/conexionDB.class L2-L3](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L2-L3)

---

## NetBeans IDE (Optional)

### Purpose

While not strictly required, NetBeans IDE provides an integrated environment for:

* Managing the project structure and `.form` GUI definitions
* Running the Ant build system
* Debugging the application
* Editing NetBeans-specific project files

### Version Compatibility

The project was developed using NetBeans and includes NetBeans-specific configurations:

* Project metadata in `nbproject/` directory
* GUI form definitions (`.form` files) for Swing components
* NetBeans-generated `build-impl.xml` for Ant integration

### Alternative: Command-Line Build

If you prefer not to use NetBeans, the application can be built using Apache Ant directly from the command line. See [Building the Project](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.2-building-the-project) for details.

**Sources:** [nbproject/project.properties L1-L99](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L1-L99)

---

## Environment Setup Verification

### Verification Checklist

Before proceeding to build the application, verify your environment meets all requirements:

| Item | Verification Command | Expected Result |
| --- | --- | --- |
| Java 21 installed | `java -version` | Version 21.x.x |
| Java compiler available | `javac -version` | Version 21.x.x |
| MySQL server running | `mysql --version` | MySQL 5.7+ or 8.0+ |
| Database created | `mysql -u root -e "SHOW DATABASES LIKE 'colegio2';"` | colegio2 database exists |
| Table created | `mysql -u root colegio2 -e "SHOW TABLES;"` | alumnoss table exists |
| MySQL Connector/J present | `ls [path-to-jar]` | mysql-connector-j-9.1.0.jar exists |

### Testing Database Connection

You can manually test the database connection using the MySQL client:

```

```

Then verify the table structure:

```

```

Expected output:

```
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int          | NO   | PRI | NULL    | auto_increment |
| nombre    | varchar(100) | NO   |     | NULL    |                |
| apellido  | varchar(100) | NO   |     | NULL    |                |
| telefono  | varchar(20)  | NO   |     | NULL    |                |
| correo    | varchar(150) | NO   |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
```

**Sources:** [build/classes/repository/conexionDB.class L1-L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L1-L7)

---

## Configuration Summary

### Required Configuration Files

The following files must be properly configured before building:

| File | Configuration Item | Description |
| --- | --- | --- |
| [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36) | MySQL Connector path | Must point to valid JAR location |
| [nbproject/project.properties L50-L51](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L50-L51) | Java version | Confirms Java 21 requirement |
| [nbproject/project.properties L77](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L77-L77) | Main class | Specifies `GUI.GUI` as entry point |

### Required Database Objects

| Object Type | Name | Requirement |
| --- | --- | --- |
| Database | `colegio2` | Must exist before running application |
| Table | `alumnoss` | Must exist with correct schema |
| User | `root` | Must have permissions on `colegio2` database |

### File System Requirements

```

```

**Diagram: File System Structure and External Dependencies**

This diagram shows the required directory structure and the external MySQL Connector/J JAR dependency that must be accessible.

**Sources:** [nbproject/project.properties L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L7-L7)

 [nbproject/project.properties L30](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L30-L30)

 [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

 [nbproject/project.properties L97](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L97-L97)

---

## Next Steps

Once all prerequisites are installed and configured:

1. **Build the project** - See [Building the Project](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.2-building-the-project) for compilation instructions
2. **Run the application** - See [Running the Application](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.3-running-the-application) for execution details
3. **Explore the architecture** - See [Architecture](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3-architecture) for understanding system design

**Sources:** [nbproject/project.properties L1-L99](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L1-L99)

 [build/classes/repository/conexionDB.class L1-L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/repository/conexionDB.class#L1-L7)