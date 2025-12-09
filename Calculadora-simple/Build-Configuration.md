# Build Configuration

> **Relevant source files**
> * [build.xml](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml)
> * [nbproject/build-impl.xml](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml)
> * [nbproject/project.properties](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties)

## Purpose and Scope

This document explains the build configuration architecture for the SimpleCalculator project, including the structure of build scripts, property files, and customization mechanisms. The configuration uses Apache Ant with NetBeans-generated build files following a two-file pattern: a user-editable `build.xml` for customization and a generated `nbproject/build-impl.xml` for core build logic.

For information about the build lifecycle phases and execution flow, see [Build Lifecycle](/ricardo-alan/SimpleCalculator/5.2-build-lifecycle). For distribution and JAR packaging details, see [Distribution and Packaging](/ricardo-alan/SimpleCalculator/5.3-distribution-and-packaging). For NetBeans-specific project metadata, see [NetBeans Integration](/ricardo-alan/SimpleCalculator/5.4-netbeans-integration).

**Sources:** [build.xml L1-L73](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L1-L73)

 [nbproject/build-impl.xml L1-L22](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L1-L22)

 [nbproject/project.properties L1-L76](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L1-L76)

## Configuration File Architecture

The build configuration is organized into three primary files that work together to define the build process:

```

```

**Diagram: Build Configuration File Dependencies**

The configuration follows a clear separation of concerns:

* `build.xml` provides customization hooks and target overrides
* `nbproject/build-impl.xml` contains the generated build implementation
* `nbproject/project.properties` defines project-specific build parameters
* Private/user properties provide environment-specific overrides

**Sources:** [build.xml L10-L12](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L10-L12)

 [nbproject/build-impl.xml L36-L55](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L36-L55)

## Build.xml: Custom Build Script

The `build.xml` file is the user-editable entry point for the build system. It is designed to remain stable across IDE regenerations.

### Structure

| Element | Line | Description |
| --- | --- | --- |
| Project declaration | 10 | Names the project "Calculadora" with default target |
| Import statement | 12 | Imports the generated build implementation |
| Documentation | 13-72 | Extensive comments explaining customization |

**Sources:** [build.xml L10-L12](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L10-L12)

### Customization Hook Targets

The build system provides empty placeholder targets that execute before and after major build phases. These can be overridden in `build.xml` to inject custom behavior:

```

```

**Diagram: Customization Hook Execution Order**

Available hook targets include:

| Hook Target | Execution Point | Common Use Cases |
| --- | --- | --- |
| `-pre-init` | Before property initialization | Set custom properties |
| `-post-init` | After property initialization | Validate environment |
| `-pre-compile` | Before Java compilation | Generate source files |
| `-post-compile` | After Java compilation | Bytecode manipulation, obfuscation |
| `-pre-compile-single` | Before single-file compilation | Source preprocessing |
| `-post-compile-single` | After single-file compilation | Individual file processing |
| `-pre-compile-test` | Before test compilation | Test data generation |
| `-post-compile-test` | After test compilation | Test resource preparation |
| `-pre-jar` | Before JAR creation | Add extra resources |
| `-post-jar` | After JAR creation | Sign JAR, create installers |
| `-post-clean` | After cleanup | Remove custom build artifacts |

**Sources:** [build.xml L19-L32](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L19-L32)

### Target Override Examples

The comments in `build.xml` provide examples of common customizations:

**Obfuscation Example** (lines 37-41):

```

```

**Custom Execution Example** (lines 61-65):

```

```

These examples demonstrate how to override specific build phases without modifying the generated `build-impl.xml`.

**Sources:** [build.xml L35-L70](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L35-L70)

### Target Override Patterns

The build system supports two override patterns:

1. **Hook-based customization**: Implement pre/post hooks without changing core targets
2. **Complete target override**: Replace entire targets by redefining them with proper dependencies

Common targets available for override:

| Target | Purpose | Key Dependencies |
| --- | --- | --- |
| `-init-macrodef-javac` | Customize javac compilation macro | Properties loaded |
| `-init-macrodef-junit` | Customize JUnit execution macro | Test properties |
| `-init-macrodef-debug` | Customize debugging macro | Debug properties |
| `-init-macrodef-java` | Customize execution macro | Runtime classpath |
| `-do-jar` | Customize JAR building | Compilation complete |
| `run` | Customize execution | JAR built |
| `-javadoc-build` | Customize Javadoc generation | Sources available |
| `test-report` | Customize test reporting | Tests executed |

**Sources:** [build.xml L47-L70](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L47-L70)

## Build-impl.xml: Generated Build Implementation

The `nbproject/build-impl.xml` file contains the core build logic generated by NetBeans. It should not be edited directly as changes will be overwritten when the project is regenerated.

### File Structure and Sections

```

```

**Diagram: Build-impl.xml Organizational Structure**

**Sources:** [nbproject/build-impl.xml L1-L1420](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L1-L1420)

### Key Requirements and Constraints

The build implementation enforces several requirements:

**Ant Version Requirement** (lines 23-29):

```

```

**Default Target** (line 30):
The default target depends on `test`, `jar`, and `javadoc`, ensuring comprehensive build validation.

**Sources:** [nbproject/build-impl.xml L23-L30](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L23-L30)

### Property Loading Hierarchy

The initialization section loads properties in a specific order, allowing later properties to override earlier ones:

```

```

**Diagram: Property Loading Sequence in -init-project Target**

This hierarchy allows:

* Project-wide defaults in `project.properties`
* User-specific overrides in `~/.netbeans/build.properties`
* Machine-specific overrides in `nbproject/private/private.properties`
* Configuration-specific overrides in `configs/` directories

**Sources:** [nbproject/build-impl.xml L40-L55](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L40-L55)

### Macro Definitions

The initialization section defines several macros used throughout the build:

| Macro | Purpose | Defined At |
| --- | --- | --- |
| `property` | Property resolution | Line 244-251 |
| `javac` | Java compilation | Lines 253-326 |
| `depend` | Dependency checking | Lines 328-339 |
| `force-recompile` | Incremental compilation | Lines 340-359 |
| `junit` | JUnit test execution | Lines 361-434 |
| `testng` | TestNG test execution | Lines 436-463 |
| `test-impl` | Generic test implementation | Lines 464-522 |
| `nbjpdastart` | JPDA debugger start | Lines 729-740 |
| `debug` | Debug execution | Lines 773-796 |
| `java` | Java execution | Lines 799-821 |
| `copylibs` | Library copying for distribution | Lines 823-852 |

**Sources:** [nbproject/build-impl.xml L244-L860](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L244-L860)

## Project Properties Configuration

The `nbproject/project.properties` file defines project-specific build parameters. These properties are used throughout the build process.

### Core Build Properties

| Property | Value | Purpose |
| --- | --- | --- |
| `javac.source` | `1.8` | Java source compatibility version |
| `javac.target` | `1.8` | Java target bytecode version |
| `javac.classpath` | `${libs.absolutelayout.classpath}` | Compilation classpath |
| `main.class` | `calculadora.Calculadora` | Application entry point |
| `source.encoding` | `UTF-8` | Source file encoding |

**Sources:** [nbproject/project.properties L32-L73](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L32-L73)

### Directory Configuration

| Property | Default Value | Description |
| --- | --- | --- |
| `src.dir` | `src` | Source code directory |
| `test.src.dir` | `test` | Test source directory |
| `build.dir` | `build` | Build output directory |
| `build.classes.dir` | `${build.dir}/classes` | Compiled classes location |
| `build.test.classes.dir` | `${build.dir}/test/classes` | Compiled test classes |
| `dist.dir` | `dist` | Distribution directory |
| `dist.jar` | `${dist.dir}/Calculadora.jar` | Output JAR file |
| `dist.javadoc.dir` | `${dist.dir}/javadoc` | Javadoc output |

**Sources:** [nbproject/project.properties L7-L75](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L7-L75)

### Annotation Processing Configuration

The project supports Java annotation processing with the following settings:

| Property | Value | Description |
| --- | --- | --- |
| `annotation.processing.enabled` | `true` | Enable annotation processing |
| `annotation.processing.enabled.in.editor` | `false` | Disable in-editor processing |
| `annotation.processing.processors.list` | (empty) | Specific processors list |
| `annotation.processing.run.all.processors` | `true` | Run all available processors |
| `annotation.processing.source.output` | `${build.generated.sources.dir}/ap-source-output` | Generated source output |

**Sources:** [nbproject/project.properties L1-L6](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L1-L6)

### Distribution Configuration

| Property | Value | Purpose |
| --- | --- | --- |
| `mkdist.disabled` | `false` | Enable library distribution |
| `jar.compress` | `false` | Disable JAR compression |
| `manifest.file` | `manifest.mf` | Custom manifest location |
| `dist.archive.excludes` | (empty) | Files to exclude from JAR |
| `build.classes.excludes` | `**/*.java,**/*.form` | Source files to exclude |

**Sources:** [nbproject/project.properties L8-L61](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L8-L61)

### Classpath Configuration

The build defines several classpaths for different phases:

```

```

**Diagram: Classpath Relationships**

**Sources:** [nbproject/project.properties L19-L72](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L19-L72)

## Customization Patterns

### Adding Custom Build Steps

To add custom build logic, implement the appropriate hook target in `build.xml`:

**Example: Resource Processing After Compilation**

```

```

**Example: Custom JAR Signing**

```

```

### Overriding Build Targets

Complete targets can be overridden by redefining them with appropriate dependencies:

**Example: Custom Run Target**

```

```

The key is ensuring the target has the correct dependencies (e.g., `jar` for `run`) to maintain build integrity.

**Sources:** [build.xml L35-L70](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/build.xml#L35-L70)

### Property Customization

Properties can be customized at multiple levels:

1. **Project-wide**: Edit `nbproject/project.properties`
2. **User-specific**: Create `nbproject/private/private.properties`
3. **Machine-specific**: Edit `~/.netbeans/build.properties`
4. **Configuration-specific**: Create `nbproject/configs/<config>.properties`

Example property override in `nbproject/private/private.properties`:

```

```

### Available Property References

Common properties that can be referenced in custom targets:

| Property | Description | Typical Value |
| --- | --- | --- |
| `${basedir}` | Project root directory | `/path/to/SimpleCalculator` |
| `${build.dir}` | Build output directory | `build` |
| `${build.classes.dir}` | Compiled classes | `build/classes` |
| `${dist.dir}` | Distribution directory | `dist` |
| `${dist.jar}` | Output JAR file | `dist/Calculadora.jar` |
| `${src.dir}` | Source directory | `src` |
| `${javac.classpath}` | Compilation classpath | Library paths |
| `${run.classpath}` | Runtime classpath | Classes + libraries |
| `${main.class}` | Main class name | `calculadora.Calculadora` |

**Sources:** [nbproject/project.properties L1-L76](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/project.properties#L1-L76)

 [nbproject/build-impl.xml L148-L158](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/nbproject/build-impl.xml#L148-L158)