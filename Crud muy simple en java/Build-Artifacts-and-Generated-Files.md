# Build Artifacts and Generated Files

> **Relevant source files**
> * [build/classes/.netbeans_automatic_build](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_automatic_build)
> * [build/classes/.netbeans_update_resources](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_update_resources)
> * [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)
> * [build/classes/model/alumno.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class)

## Purpose and Scope

This document details the build artifacts and generated files produced during the compilation process of the crud3 application. These artifacts are created by the Ant build system and stored in the `build/` directory hierarchy. This page covers compiled bytecode (`.class` files), NetBeans marker files, and the directory structure of build outputs.

For information about the build system configuration itself, see [Ant Build Configuration](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/7.1-ant-build-configuration). For details about the final packaged JAR file, see [JAR Packaging and Manifest](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/7.5-jar-packaging-and-manifest).

---

## Build Directory Structure

The `build/` directory contains all intermediate build artifacts generated during compilation. The structure mirrors the source package hierarchy and includes additional marker files for IDE integration.

```

```

**Sources:** [build/classes/.netbeans_automatic_build](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_automatic_build)

 [build/classes/.netbeans_update_resources](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_update_resources)

 [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)

 [build/classes/model/alumno.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class)

---

## NetBeans Marker Files

The `build/classes/` directory contains two marker files used by NetBeans IDE to track build state and resource updates.

### .netbeans_automatic_build

| Property | Value |
| --- | --- |
| **File Path** | `build/classes/.netbeans_automatic_build` |
| **Size** | Empty (0 bytes) |
| **Purpose** | Signals that the project uses NetBeans automatic build |
| **Created By** | NetBeans IDE during first build |
| **Used By** | NetBeans build system to determine build mode |

This marker file indicates that the project is configured for automatic compilation. When this file is present, NetBeans continuously compiles modified source files in the background rather than requiring explicit build commands.

### .netbeans_update_resources

| Property | Value |
| --- | --- |
| **File Path** | `build/classes/.netbeans_update_resources` |
| **Size** | Empty (0 bytes) |
| **Purpose** | Signals that resources need to be copied to build output |
| **Created By** | NetBeans IDE build system |
| **Used By** | Resource management during incremental builds |

This marker tracks whether non-Java resources (such as `.form` files, images, or property files) need to be synchronized with the build output directory during incremental builds.

**Sources:** [build/classes/.netbeans_automatic_build](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_automatic_build)

 [build/classes/.netbeans_update_resources](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_update_resources)

---

## Compiled Class Files

The build system generates `.class` files containing Java bytecode for each compiled source file. These files are organized in a package hierarchy that mirrors the source tree structure.

```

```

**Sources:** [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)

 [build/classes/model/alumno.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class)

---

## Class File Contents and Structure

### GUI.class Structure

The `build/classes/GUI/GUI.class` file contains the compiled bytecode for the main application window. The class file includes:

| Component | Description |
| --- | --- |
| **Class Metadata** | Version 61 (Java 17), access flags for public class |
| **Constant Pool** | String constants, method references, field descriptors |
| **Fields** | Component references: `jPanel1`, `jLabel1-4`, `txtNombre`, `txtApellido`, `txtTelefono`, `txtCorreo`, `btnSave1`, `btnClean` |
| **Methods** | `<init>()`, `initComponents()`, `ValidadarDatos()`, `LimpiarCampos()`, `main()`, event handlers |
| **Inner Classes** | References to `GUI$1`, `GUI$2`, `GUI$3` (anonymous inner classes) |
| **Dependencies** | `javax.swing.*`, `java.awt.*`, `org.netbeans.lib.awtextra.*`, `model.alumno`, `services.alumnoDataChange` |

The class file structure can be observed in the bytecode:

```

```

Key bytecode patterns visible in [build/classes/GUI/GUI.class L1-L50](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class#L1-L50)

:

* Constructor invocation: `invokespecial javax/swing/JFrame.<init>`
* Component initialization: calls to `new` for `javax.swing.JPanel`, `javax.swing.JLabel`, etc.
* Event listener registration: `addActionListener` method calls
* Field access: `getfield`/`putfield` for component references

**Sources:** [build/classes/GUI/GUI.class L1-L50](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class#L1-L50)

### alumno.class Structure

The `build/classes/model/alumno.class` file contains the compiled student data model. The class file includes:

| Component | Description |
| --- | --- |
| **Class Metadata** | Version 61 (Java 17), public class |
| **Fields** | `id` (I), `nombre` (Ljava/lang/String;), `apellido`, `telefono`, `correo` |
| **Constructors** | Default constructor `<init>()`, parameterized constructor with 5 arguments |
| **Methods** | Getters: `getId()`, `getNombre()`, `getApellido()`, `getTelefono()`, `getCorreo()` |
|  | Setters: `setId(I)`, `setNombre(Ljava/lang/String;)`, etc. |

The field descriptors in the constant pool ([build/classes/model/alumno.class L1-L10](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class#L1-L10)

):

* `I` - int type for `id` field
* `Ljava/lang/String;` - String type for text fields

**Sources:** [build/classes/model/alumno.class L1-L10](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class#L1-L10)

---

## Anonymous Inner Class Files

Event listeners in the GUI generate anonymous inner class files. Each action listener creates a separate `.class` file with a numeric suffix.

```

```

| Class File | Source Location | Purpose |
| --- | --- | --- |
| `GUI$1.class` | Anonymous class in `initComponents()` | ActionListener for `btnSave1` - triggers `ValidadarDatos()` |
| `GUI$2.class` | Anonymous class in `initComponents()` | ActionListener for `btnClean` - triggers `LimpiarCampos()` |
| `GUI$3.class` | Anonymous class in `main()` | Runnable for `EventQueue.invokeLater()` - creates GUI instance |

For more information about anonymous inner classes, see [Anonymous Inner Classes](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/8.3-anonymous-inner-classes).

**Sources:** [build/classes/GUI/GUI.class L17-L35](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class#L17-L35)

---

## Build Artifact Lifecycle

The following diagram shows how build artifacts are created, used, and cleaned during the build lifecycle:

```

```

### Build Target Flow

| Target | Input | Output | Artifacts Created |
| --- | --- | --- | --- |
| `init` | Project properties | Configured build environment | None (sets properties only) |
| `compile` | `src/**/*.java` | Compiled classes | All `.class` files in `build/classes/` |
| `compile-single` | Single modified source file | Updated class file | Individual `.class` file |
| `jar` | `build/classes/**/*.class` | Executable JAR | `dist/crud3.jar` |
| `clean` | `build/` directory | Deleted artifacts | None (removes all) |

**Sources:** [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)

 [build/classes/model/alumno.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class)

---

## Class File Dependencies

The dependency graph shows relationships between compiled class files based on import statements and method calls:

```

```

**Sources:** [build/classes/GUI/GUI.class L1-L50](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class#L1-L50)

 [build/classes/model/alumno.class L1-L10](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class#L1-L10)

---

## Bytecode Verification and Metadata

### Class File Format Version

All class files in the crud3 application are compiled with version 61.0 (0x003D), corresponding to Java 17:

| Java Version | Class File Version | Hex Value |
| --- | --- | --- |
| Java 17 | 61.0 | 0x003D |
| Java 11 | 55.0 | 0x0037 |
| Java 8 | 52.0 | 0x0034 |

This can be verified from the magic number and version bytes at the beginning of each `.class` file: `CA FE BA BE 00 00 00 3D`

### Constant Pool References

The constant pool in `GUI.class` contains references to all dependencies used by the class. Key constant pool entries include:

* **Class References:** `javax/swing/JFrame`, `javax/swing/JPanel`, `model/alumno`, `services/alumnoDataChange`
* **Method References:** `javax/swing/UIManager.getInstalledLookAndFeels()`, `setDefaultCloseOperation(I)`
* **Field References:** `jPanel1`, `txtNombre`, `btnSave1`
* **String Constants:** `"Correo"`, `"nombre"`, `"apellido"`, `"Clean"`, `"Save"`

**Sources:** [build/classes/GUI/GUI.class L1-L50](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class#L1-L50)

 [build/classes/model/alumno.class L1-L10](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class#L1-L10)

---

## Build Output Summary

| Category | Location | File Count | Purpose |
| --- | --- | --- | --- |
| **Marker Files** | `build/classes/` | 2 | NetBeans build state tracking |
| **GUI Classes** | `build/classes/GUI/` | 4+ | Main window and anonymous listeners |
| **Model Classes** | `build/classes/model/` | 1 | Data entity (alumno) |
| **Service Classes** | `build/classes/services/` | 1+ | Business logic (alumnoDataChange) |
| **Repository Classes** | `build/classes/repository/` | 1+ | Database connectivity (conexionDB) |
| **Total** | `build/classes/` | ~10+ | Complete application bytecode |

The exact file count depends on the number of anonymous inner classes and additional source files in each package.

**Sources:** [build/classes/.netbeans_automatic_build](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_automatic_build)

 [build/classes/.netbeans_update_resources](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/.netbeans_update_resources)

 [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)

 [build/classes/model/alumno.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/model/alumno.class)