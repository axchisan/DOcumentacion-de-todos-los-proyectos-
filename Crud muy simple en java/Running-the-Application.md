# Running the Application

> **Relevant source files**
> * [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)
> * [nbproject/project.properties](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties)
> * [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

This page describes how to execute the `crud3` application after it has been successfully built. It covers the main entry point, various execution methods, the startup sequence, and runtime requirements. For information about building the application from source, see [Building the Project](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.2-building-the-project). For database setup requirements before running, see [Prerequisites and Setup](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.1-prerequisites-and-setup).

---

## Main Entry Point

The application's main entry point is the `GUI.GUI` class, which is specified in the project configuration at [nbproject/project.properties L77](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L77-L77)

 This class contains the `main()` method that initializes and displays the primary user interface.

**Main Class Configuration:**

```
main.class=GUI.GUI
```

The `main()` method is implemented at [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

 and performs the following initialization steps:

1. **Look and Feel Configuration**: Attempts to set the Nimbus Look and Feel for improved visual appearance
2. **Exception Handling**: Catches and logs potential exceptions (`ClassNotFoundException`, `InstantiationException`, `IllegalAccessException`, `UnsupportedLookAndFeelException`)
3. **GUI Instantiation**: Creates a new `GUI` instance on the Event Dispatch Thread using `EventQueue.invokeLater()`
4. **Display**: Makes the window visible with `setVisible(true)`

**Sources:** [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

 [nbproject/project.properties L77](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L77-L77)

---

## Execution Methods

### Running from Distributed JAR

After building the project (see [Building the Project](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.2-building-the-project)), the executable JAR file is located at `dist/crud3.jar`. Execute it from the command line:

```

```

The JAR manifest specifies `GUI.GUI` as the main class, allowing direct execution. The MySQL Connector/J library is included in the classpath configuration.

**Sources:** [nbproject/project.properties L31](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L31-L31)

 [nbproject/project.properties L77](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L77-L77)

---

### Running from NetBeans IDE

Within NetBeans IDE, the application can be executed using the standard Run command (F6 or Run → Run Project). The IDE automatically:

* Compiles any modified source files
* Builds the class files to `build/classes/`
* Constructs the runtime classpath including dependencies
* Executes the `main()` method in `GUI.GUI`

**Sources:** [nbproject/project.properties L77](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L77-L77)

 [nbproject/project.properties L82-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L82-L84)

---

### Running from Command Line with Classpath

To run directly from compiled classes without using the JAR:

```

```

The classpath must include:

* `build/classes/` - compiled application classes
* MySQL Connector/J JAR - database driver
* AbsoluteLayout library - GUI layout manager

**Classpath Configuration:**

| Component | Purpose | Configuration Location |
| --- | --- | --- |
| `build.classes.dir` | Compiled `.class` files | [nbproject/project.properties L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L7-L7) |
| `mysql-connector-j-9.1.0.jar` | JDBC driver | [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36) |
| `libs.absolutelayout.classpath` | Layout library | [nbproject/project.properties L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L41-L41) |

**Sources:** [nbproject/project.properties L7](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L7-L7)

 [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

 [nbproject/project.properties L39-L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L39-L41)

 [nbproject/project.properties L82-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L82-L84)

---

## Application Startup Sequence

The following diagram illustrates the complete startup sequence from `main()` method invocation to the displayed GUI:

```

```

**Key Initialization Steps:**

1. **Lines 104-110**: Look and Feel detection and configuration
2. **Lines 111-119**: Exception handling for Look and Feel failures
3. **Lines 123-127**: Event Dispatch Thread execution
4. **Line 125**: GUI constructor invocation
5. **Line 21**: `initComponents()` method call (from constructor)
6. **Lines 31-84**: Component creation and layout configuration

**Sources:** [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

 [src/GUI/GUI.java L20-L22](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L20-L22)

 [src/GUI/GUI.java L31-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L31-L84)

---

## Runtime Initialization Flow

This diagram maps the code execution from the main method to fully initialized GUI components:

```

```

**Component Initialization Details:**

| Component | Type | Initialization Line | Purpose |
| --- | --- | --- | --- |
| `jPanel1` | `JPanel` | [src/GUI/GUI.java L33](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L33-L33) | Main container panel |
| `jLabel1` | `JLabel` | [src/GUI/GUI.java L34](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L34-L34) | "Correo" field label |
| `jLabel2` | `JLabel` | [src/GUI/GUI.java L35](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L35-L35) | "nombre" field label |
| `jLabel3` | `JLabel` | [src/GUI/GUI.java L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L36-L36) | "apellido" field label |
| `jLabel4` | `JLabel` | [src/GUI/GUI.java L37](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L37-L37) | "telefono" field label |
| `txtCorreo` | `JTextField` | [src/GUI/GUI.java L38](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L38-L38) | Email input field |
| `txtNombre` | `JTextField` | [src/GUI/GUI.java L39](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L39-L39) | Name input field |
| `txtApellido` | `JTextField` | [src/GUI/GUI.java L40](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L40-L40) | Last name input field |
| `txtTelefono` | `JTextField` | [src/GUI/GUI.java L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L41-L41) | Phone input field |
| `btnClean` | `JButton` | [src/GUI/GUI.java L42](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L42-L42) | Clear fields button |
| `btnSave1` | `JButton` | [src/GUI/GUI.java L43](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L43-L43) | Save data button |

**Sources:** [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

 [src/GUI/GUI.java L31-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L31-L84)

 [src/GUI/GUI.java L20-L22](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L20-L22)

---

## Runtime Requirements

### MySQL Database Connection

The application requires a running MySQL server for database operations. The connection parameters are configured in `repository.conexionDB`:

**Connection Requirements:**

| Parameter | Expected Value | Notes |
| --- | --- | --- |
| Database Server | `localhost:3306` | Must be running before application start |
| Database Name | `colegio2` | Must exist with proper schema |
| Table Name | `alumnoss` | Must exist with columns: id, nombre, apellido, telefono, correo |
| Username | `root` | Configured in `conexionDB.conectar()` |
| Password | Empty string (`""`) | Default configuration |

**Connection Timing:**

The database connection is established **lazily** when the user first attempts to save data:

```

```

**Connection Failure Handling:**

If the MySQL server is not running or the database does not exist when the user attempts to save:

1. The `conexionDB.conectar()` method throws an exception
2. The `alumnoDataChange.guardado()` method catches the exception
3. A `JOptionPane` dialog displays the error message stored in `alumnoDataChange.mensaje`
4. The application continues running (does not crash)

For database schema setup instructions, see [Database Schema](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/6.2-database-schema).

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

 [src/GUI/GUI.java L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L162-L162)

---

## Post-Startup Behavior

### Event Loop Execution

After the GUI becomes visible, the application enters the Swing event loop, where it:

1. **Waits for User Input**: Button clicks, text field entries, window events
2. **Dispatches Events**: Routes events to registered `ActionListener` implementations
3. **Processes Actions**: Executes `btnSave1ActionPerformed()` or `btnCleanActionPerformed()` based on user actions

### Main Window Properties

| Property | Value | Configuration Location |
| --- | --- | --- |
| Default Close Operation | `EXIT_ON_CLOSE` | [src/GUI/GUI.java L45](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L45-L45) |
| Window Size | Auto-sized via `pack()` | [src/GUI/GUI.java L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L84-L84) |
| Layout Manager | `AbsoluteLayout` | [src/GUI/GUI.java L46](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L46-L46) |
| Panel Dimensions | 860×480 pixels | [src/GUI/GUI.java L82](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L82-L82) |

**Sources:** [src/GUI/GUI.java L45](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L45-L45)

 [src/GUI/GUI.java L82](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L82-L82)

 [src/GUI/GUI.java L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L84-L84)

---

## Execution Examples

### Example 1: Running the Packaged JAR

```

```

**Expected Output:**

* Main data entry window appears
* Window title displays as configured
* Input fields for nombre, apellido, telefono, correo are visible
* Save and Clean buttons are enabled

---

### Example 2: Running with Verbose Logging

To troubleshoot startup issues, run with verbose output:

```

```

This outputs all class loading operations, useful for diagnosing:

* Missing dependencies
* Classpath configuration issues
* MySQL driver loading failures

---

### Example 3: Running from Build Directory

```

```

**Note:** Adjust the MySQL connector path to match your local installation as configured in [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

**Sources:** [nbproject/project.properties L36](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L36-L36)

 [nbproject/project.properties L82-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/project.properties#L82-L84)

---

## Troubleshooting Runtime Issues

### Application Does Not Start

**Symptom:** No window appears, no error message

**Possible Causes:**

1. Main class not found in classpath
2. Missing Swing libraries (unlikely on standard JDK installations)
3. Display environment issues (if running headless)

**Verification:**

```

```

---

### Database Connection Failures

**Symptom:** Error dialog appears when clicking Save button

**Possible Causes:**

1. MySQL server not running on `localhost:3306`
2. Database `colegio2` does not exist
3. Table `alumnoss` does not exist or has incorrect schema
4. MySQL driver not in classpath

**Verification:**

```

```

For database setup instructions, see [Prerequisites and Setup](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.1-prerequisites-and-setup).

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

---

## Related Pages

* [Building the Project](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.2-building-the-project) - Compilation and packaging process
* [Prerequisites and Setup](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.1-prerequisites-and-setup) - Database and dependency setup
* [Main Data Entry Form (GUI.GUI)](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/4.1-main-data-entry-form-(gui.gui)) - Detailed GUI component documentation
* [Event Handling and Action Listeners](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/4.4-event-handling-and-action-listeners) - Button action implementations
* [Database Connection (conexionDB)](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/6.1-database-connection-(conexiondb)) - Connection management details