# Building the Project

> **Relevant source files**
> * [build.xml](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml)
> * [nbproject/build-impl.xml](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml)
> * [nbproject/genfiles.properties](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/genfiles.properties)

This page explains how to build the crud3 application using Apache Ant, covering compilation, testing, and JAR packaging. For information about setting up prerequisites and dependencies, see [Prerequisites and Setup](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.1-prerequisites-and-setup). For information about running the compiled application, see [Running the Application](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/2.3-running-the-application).

## Overview

The crud3 project uses Apache Ant as its build system, with configuration managed by NetBeans IDE. The build process is controlled by two primary files:

* `build.xml` - Main build configuration file that can be customized
* `nbproject/build-impl.xml` - Generated implementation file containing the actual build logic

The build system compiles Java sources, executes tests, and packages the application into an executable JAR file with dependencies.

**Sources:** [build.xml L1-L74](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml#L1-L74)

 [nbproject/build-impl.xml L1-L30](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1-L30)

## Build System Architecture

```

```

**Sources:** [build.xml L10-L12](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml#L10-L12)

 [nbproject/build-impl.xml L22-L30](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L22-L30)

 [nbproject/build-impl.xml L1049](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1049-L1049)

## Available Build Targets

The build system provides several targets that can be invoked directly. The following table lists the most commonly used targets:

| Target | Description | Dependencies |
| --- | --- | --- |
| `default` | Builds and tests the entire project | `test`, `jar`, `javadoc` |
| `init` | Initializes build properties and directories | Multiple initialization targets |
| `compile` | Compiles Java source files to `.class` files | `init`, `deps-jar` |
| `test` | Runs JUnit tests | `compile` |
| `jar` | Creates the executable JAR file | `compile` |
| `clean` | Removes all build artifacts | None |
| `javadoc` | Generates API documentation | `init`, `compile` |

**Sources:** [nbproject/build-impl.xml L30](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L30-L30)

 [nbproject/build-impl.xml L1111](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1111-L1111)

 [nbproject/build-impl.xml L1258](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1258-L1258)

 [nbproject/build-impl.xml L1649](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1649-L1649)

## Compilation Process

### Compilation Flow

```

```

**Sources:** [nbproject/build-impl.xml L1111](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1111-L1111)

 [nbproject/build-impl.xml L1080-L1110](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1080-L1110)

### Compilation Configuration

The compilation process uses the following key properties and settings:

| Property | Value/Purpose |
| --- | --- |
| `src.dir` | Source directory (typically `src/`) |
| `build.classes.dir` | Output directory for compiled classes (typically `build/classes/`) |
| `javac.classpath` | Classpath including MySQL Connector/J |
| `javac.source` | Java source version (configured in project) |
| `javac.target` | Java target version (configured in project) |
| `javac.debug` | Enable debug information (default: `true`) |

The actual `javac` compilation is executed through a macro defined in the build implementation:

**Sources:** [nbproject/build-impl.xml L1095-L1100](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1095-L1100)

 [nbproject/build-impl.xml L311-L377](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L311-L377)

### Customization Hooks

The build system provides empty placeholder targets that can be overridden in `build.xml` for customization:

* `-pre-compile` - Executes before compilation starts
* `-post-compile` - Executes after compilation completes

These targets are documented in `build.xml` with examples:

**Sources:** [build.xml L13-L42](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml#L13-L42)

 [nbproject/build-impl.xml L1083-L1110](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1083-L1110)

## Testing

### Test Execution Flow

```

```

**Sources:** [nbproject/build-impl.xml L1649](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1649-L1649)

 [nbproject/build-impl.xml L1638-L1646](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1638-L1646)

### Test Configuration

The test execution uses macros defined for both JUnit and TestNG frameworks:

| Component | Description |
| --- | --- |
| `test.src.dir` | Test source directory |
| `build.test.classes.dir` | Compiled test classes output |
| `build.test.results.dir` | Test execution results |
| `javac.test.classpath` | Classpath for test compilation |
| `run.test.classpath` | Classpath for test execution |

**Sources:** [nbproject/build-impl.xml L490-L604](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L490-L604)

 [nbproject/build-impl.xml L1641-L1643](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1641-L1643)

## JAR Packaging

### JAR Build Process

```

```

**Sources:** [nbproject/build-impl.xml L1258](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1258-L1258)

 [nbproject/build-impl.xml L1131-L1257](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1131-L1257)

### JAR Manifest Configuration

The generated JAR file includes a manifest with the following key attributes:

| Attribute | Value | Set By |
| --- | --- | --- |
| `Main-Class` | `GUI.GUI` | `-do-jar-set-mainclass` target |
| `Class-Path` | `lib/mysql-connector-j-*.jar` | `copylibs` macro |

The manifest is generated from a template and updated during the build process:

**Sources:** [nbproject/build-impl.xml L1166-L1170](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1166-L1170)

 [nbproject/build-impl.xml L987-L1016](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L987-L1016)

### Library Packaging

The `copylibs` task handles packaging external dependencies:

```

```

**Sources:** [nbproject/build-impl.xml L1198-L1203](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1198-L1203)

 [nbproject/build-impl.xml L1008-L1014](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1008-L1014)

## Common Build Commands

### Building from Command Line

To build the project from the command line, navigate to the project root directory and execute:

```

```

**Sources:** [build.xml L10-L12](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml#L10-L12)

 [nbproject/build-impl.xml L30](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L30-L30)

### Building from NetBeans IDE

When building from NetBeans, the IDE executes the same Ant targets but provides additional integration:

1. **Clean and Build** - Executes `clean` then `jar` targets
2. **Build** - Executes the `jar` target (incremental)
3. **Test** - Executes the `test` target
4. **Run** - Builds if necessary, then executes the main class

The IDE also supports automatic compilation on save (Compile on Save feature), which can bypass the full Ant build for faster iteration.

**Sources:** [build.xml L5-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml#L5-L9)

 [nbproject/build-impl.xml L1071-L1079](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1071-L1079)

## Build Output Structure

The build process generates the following directory structure:

| Directory | Contents | Created By |
| --- | --- | --- |
| `build/classes/` | Compiled application `.class` files | `compile` target |
| `build/test/classes/` | Compiled test `.class` files | `compile-test` target |
| `build/test/results/` | JUnit test results (XML) | `test` target |
| `dist/crud3.jar` | Executable JAR with manifest | `jar` target |
| `dist/lib/` | External library dependencies | `jar` target (copylibs) |
| `dist/javadoc/` | Generated API documentation | `javadoc` target |

**Sources:** [nbproject/build-impl.xml L1081](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1081-L1081)

 [nbproject/build-impl.xml L1133](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1133-L1133)

 [nbproject/build-impl.xml L1639](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1639-L1639)

 [nbproject/build-impl.xml L1489](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1489-L1489)

## Build Lifecycle Dependencies

```

```

**Sources:** [nbproject/build-impl.xml L30](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L30-L30)

 [nbproject/build-impl.xml L1049](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1049-L1049)

 [nbproject/build-impl.xml L1111](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1111-L1111)

 [nbproject/build-impl.xml L1258](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1258-L1258)

 [nbproject/build-impl.xml L1323](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1323-L1323)

 [nbproject/build-impl.xml L1649](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L1649-L1649)

## Build Properties

Key build properties are loaded from multiple sources in the following order:

1. `nbproject/private/private.properties` - User-specific settings
2. `nbproject/project.properties` - Project-wide settings
3. `build.xml` - Custom overrides

Common properties that may need configuration:

| Property | Purpose | Default Location |
| --- | --- | --- |
| `javac.classpath` | Compilation classpath | `project.properties` |
| `main.class` | Main class for execution | `project.properties` |
| `dist.jar` | Output JAR path | `project.properties` |
| `manifest.file` | Manifest template | `manifest.mf` |

**Sources:** [nbproject/build-impl.xml L40-L54](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/nbproject/build-impl.xml#L40-L54)

 [build.xml L43-L44](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build.xml#L43-L44)