# Distribution and Packaging

> **Relevant source files**
> * [dist/Calculadora.jar](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar)
> * [dist/lib/AbsoluteLayout.jar](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar)
> * [manifest.mf](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/manifest.mf)
> * [nbproject/build-impl.xml](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml)

This page documents how the SimpleCalculator project creates distributable artifacts, specifically the executable JAR file and its dependencies. It covers the JAR packaging process, manifest generation, library copying mechanism, and the final distribution directory structure.

For information about the overall build lifecycle phases, see [Build Lifecycle](/ricardo-alan/SimpleCalculator/5.2-build-lifecycle). For configuration of build properties, see [Build Configuration](/ricardo-alan/SimpleCalculator/5.1-build-configuration).

---

## Overview

The distribution and packaging phase transforms compiled classes and resources into an executable JAR file with all necessary dependencies. The process is orchestrated by Apache Ant using the `copylibs` mechanism, which bundles the application and copies external libraries to maintain a clean separation between application code and dependencies.

**Key Artifacts:**

* `dist/Calculadora.jar` - Main executable JAR with embedded manifest
* `dist/lib/AbsoluteLayout.jar` - External UI layout library
* `META-INF/MANIFEST.MF` - Generated manifest with classpath and main class

**Sources:** [dist/Calculadora.jar L1-L9](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar#L1-L9)

 [dist/lib/AbsoluteLayout.jar L1-L10](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L10)

---

## Distribution Directory Structure

The build system produces a structured distribution directory that separates the application JAR from its external dependencies.

```

```

The `dist/` directory contains the complete distributable application. The JAR file includes all compiled classes and image resources, while external libraries reside in the `lib/` subdirectory. This structure allows the JAR's manifest to reference dependencies using relative paths.

**Sources:** [dist/Calculadora.jar L1-L12](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar#L1-L12)

 [nbproject/build-impl.xml L965-L968](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L965-L968)

---

## JAR Creation Workflow

The JAR creation process follows a multi-stage workflow that handles manifest generation, resource bundling, and library copying.

```

```

The workflow conditionally executes either the `copylibs` task (when `do.mkdist` is set) or the standard `jar` task. For the SimpleCalculator project, `do.mkdist` is enabled because external libraries need to be copied.

**Sources:** [nbproject/build-impl.xml L965-L1032](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L965-L1032)

 [nbproject/build-impl.xml L90-L98](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L90-L98)

---

## Manifest Generation

The manifest file is generated dynamically through a series of conditional build targets that construct the `META-INF/MANIFEST.MF` with appropriate attributes.

### Manifest Targets

| Target | Condition | Purpose | Line Reference |
| --- | --- | --- | --- |
| `-do-jar-create-manifest` | `do.archive && !manifest.available` | Creates temporary blank manifest | [973-976](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/973-976) |
| `-do-jar-copy-manifest` | `do.archive+manifest.available` | Copies from `manifest.mf` template | [977-980](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/977-980) |
| `-do-jar-set-mainclass` | `do.archive+main.class.available` | Adds `Main-Class` attribute | [981-985](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/981-985) |
| `-do-jar-set-profile` | `do.archive+profile.available` | Adds `Profile` attribute | [986-990](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/986-990) |
| `-do-jar-set-splashscreen` | `do.archive+splashscreen.available` | Adds `SplashScreen-Image` | [991-998](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/991-998) |

### Generated Manifest Content

The final manifest embedded in [dist/Calculadora.jar L3-L9](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar#L3-L9)

 contains:

```
Manifest-Version: 1.0
Ant-Version: Apache Ant 1.9.7
Created-By: 1.8.0_131-b11 (Oracle Corporation)
Class-Path: lib/AbsoluteLayout.jar
X-COMMENT: Main-Class will be added automatically by build
Main-Class: calculadora.Calculadora
```

The `Class-Path` attribute is automatically generated by the `copylibs` task to reference dependencies in the `lib/` subdirectory. The `Main-Class` attribute enables direct execution via `java -jar Calculadora.jar`.

**Sources:** [dist/Calculadora.jar L3-L9](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar#L3-L9)

 [manifest.mf L1-L4](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/manifest.mf#L1-L4)

 [nbproject/build-impl.xml L973-L998](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L973-L998)

---

## Library Management and CopyLibs Task

The project uses NetBeans' `CopyLibs` task to handle dependency packaging. This mechanism copies external libraries to `dist/lib/` and updates the JAR's classpath accordingly.

```

```

### CopyLibs Macro Definition

The `copylibs` macro is defined in [nbproject/build-impl.xml L823-L853](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L823-L853)

:

```

```

**Key Operations:**

1. **Path Conversion** [829-842](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/829-842) : Transforms absolute classpaths to relative `lib/*` paths
2. **Task Definition** [843](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/843) : Loads the CopyLibs task from NetBeans libraries
3. **JAR Creation** [844-850](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/844-850) : Packages classes and sets manifest classpath
4. **Library Copying**: External JARs are automatically copied to `dist/lib/`

**Sources:** [nbproject/build-impl.xml L823-L853](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L823-L853)

 [nbproject/build-impl.xml L999-L1004](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L999-L1004)

---

## Build Targets for Distribution

The distribution phase is invoked through several interconnected Ant targets that form a dependency chain.

```

```

### Primary Distribution Targets

| Target Name | Purpose | Dependencies |
| --- | --- | --- |
| `jar` | Main entry point for JAR creation | `init`, `compile`, `-pre-jar`, `-do-jar`, `-post-jar` |
| `-do-jar` | Orchestrates JAR building process | `-do-jar-with-libraries` or `-do-jar-without-libraries` |
| `-do-jar-with-libraries` | Builds JAR with library copying | All manifest and copylibs targets |
| `-do-jar-copylibs` | Executes CopyLibs task | Manifest generation targets |

**Sources:** [nbproject/build-impl.xml L1026-L1033](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L1026-L1033)

 [nbproject/build-impl.xml L999-L1004](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L999-L1004)

---

## Execution Target and Usage

After successful packaging, the build system provides information about executing the application.

### Execution Instructions

The `-do-jar-copylibs` target [nbproject/build-impl.xml L1001-L1003](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L1001-L1003)

 outputs:

```python
To run this application from the command line without Ant, try:
java -jar "${dist.jar.resolved}"
```

This expands to:

```

```

### Runtime Classpath Resolution

When executed, the Java runtime:

1. Reads the `Class-Path` manifest attribute: `lib/AbsoluteLayout.jar`
2. Resolves the path relative to the JAR location: `dist/lib/AbsoluteLayout.jar`
3. Loads the main class: `calculadora.Calculadora`
4. Includes `AbsoluteLayout.jar` on the classpath automatically

```

```

**Sources:** [dist/Calculadora.jar L3-L9](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar#L3-L9)

 [nbproject/build-impl.xml L1001-L1003](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L1001-L1003)

---

## Archive Compression and Indexing

The JAR packaging process supports optional compression and indexing features controlled by build properties.

### Compression and Index Properties

From [nbproject/build-impl.xml L201-L203](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L201-L203)

:

* `jar.compress` - Controls ZIP compression (default: unset, implies false)
* `jar.index` - Creates JAR index for faster class loading (default: false)
* `jar.index.metainf` - Includes META-INF in index (default: false)

The `copylibs` task respects these properties in [nbproject/build-impl.xml L844](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L844-L844)

:

```

```

### Distribution Archive Exclusions

Files matching `dist.archive.excludes` are excluded from the JAR during packaging [nbproject/build-impl.xml L845](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L845-L845)

:

```

```

This prevents development artifacts like `.netbeans_automatic_build` marker files from being packaged.

**Sources:** [nbproject/build-impl.xml L201-L203](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L201-L203)

 [nbproject/build-impl.xml L844-L845](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L844-L845)

 [nbproject/build-impl.xml L856-L859](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L856-L859)

---

## Distribution Artifacts Summary

The complete distribution package consists of:

| Artifact | Path | Size | Content | Purpose |
| --- | --- | --- | --- | --- |
| Main JAR | `dist/Calculadora.jar` | ~19KB | Application classes, images, manifest | Executable application |
| Layout Library | `dist/lib/AbsoluteLayout.jar` | ~2KB | NetBeans AbsoluteLayout classes | UI positioning |

### JAR Internal Structure

The `Calculadora.jar` contains:

* `META-INF/MANIFEST.MF` - Execution metadata
* `calculadora/` - Compiled application classes including: * `Calculadora.class` - Main application class * `Calculadora$*.class` - Inner classes for event handlers
* `images/` - UI assets: * Button images (`btn*.png`) * Theme-specific icons (`darkmode_*.png`)

**Verification Command:**

```

```

**Sources:** [dist/Calculadora.jar L1-L477](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/Calculadora.jar#L1-L477)

 [dist/lib/AbsoluteLayout.jar L1-L45](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L45)