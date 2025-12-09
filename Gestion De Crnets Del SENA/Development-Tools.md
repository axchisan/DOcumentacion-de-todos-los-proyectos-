# Development Tools

> **Relevant source files**
> * [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)
> * [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
> * [macos/Flutter/GeneratedPluginRegistrant.swift](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/macos/Flutter/GeneratedPluginRegistrant.swift)
> * [pubspec.lock](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock)
> * [pubspec.yaml](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml)

This page documents the development-time dependencies and build tools used in the SENA Digital ID Card application. These tools are not included in the compiled application but are essential for code generation, static analysis, and testing during development.

For runtime dependencies that ship with the application, see [Core Dependencies](/axchisan/AppGestionCarnetsSENA/7.1-core-dependencies) and [UI and Media Dependencies](/axchisan/AppGestionCarnetsSENA/7.2-ui-and-media-dependencies).

---

## Overview

The application uses three primary development tools defined in the `dev_dependencies` section of [pubspec.yaml L24-L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L24-L29)

:

| Tool | Version | Purpose |
| --- | --- | --- |
| `build_runner` | ^2.4.6 | Orchestrates code generation processes |
| `hive_generator` | ^2.0.1 | Generates Hive TypeAdapter classes for data models |
| `flutter_lints` | ^5.0.0 | Enforces Dart/Flutter code quality rules |
| `flutter_test` | SDK | Provides testing framework (built-in) |

These tools enable an automated code generation workflow that produces type-safe serialization code for Hive database integration.

**Sources:** [pubspec.yaml L24-L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L24-L29)

---

## Build Runner Architecture

```

```

**build_runner** serves as the orchestration layer that coordinates multiple code generators. It performs the following functions:

1. **Dependency Resolution**: Scans `pubspec.yaml` to identify all builder packages (e.g., `hive_generator`)
2. **File Discovery**: Locates source files with generator annotations (`@HiveType`, `@HiveField`)
3. **Analyzer Integration**: Parses Dart code using the `analyzer` package to build abstract syntax trees
4. **Generator Execution**: Invokes `hive_generator` with analyzed code structures
5. **Output Management**: Writes generated files with `.g.dart` extension to preserve manual code
6. **Incremental Builds**: Tracks file modifications via `build_daemon` to regenerate only changed files

**Sources:** [pubspec.yaml L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L29-L29)

 [pubspec.lock L113-L128](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L113-L128)

---

## Hive Generator

```

```

The `hive_generator` package is a Dart build system generator that produces Hive TypeAdapter classes. It processes classes annotated with `@HiveType` and generates binary serialization code.

### Generation Process

For each annotated class in [lib/models/models.dart L5-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L5-L68)

:

1. **Class Detection**: Identifies `@HiveType(typeId: 0)` annotation on `Aprendiz` class
2. **Field Mapping**: Maps `@HiveField(0)` through `@HiveField(9)` to class properties
3. **Adapter Generation**: Creates `AprendizAdapter` in [lib/models/models.g.dart L9-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L9-L68)
4. **Binary Serialization**: Generates `read()` method with `BinaryReader` for deserialization
5. **Binary Deserialization**: Generates `write()` method with `BinaryWriter` for serialization

### Generated TypeAdapter Structure

The generated `AprendizAdapter` in [lib/models/models.g.dart L9-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L9-L68)

 contains:

| Method | Purpose | Implementation |
| --- | --- | --- |
| `typeId` | Returns unique identifier `0` | Matches `@HiveType(typeId: 0)` |
| `read(BinaryReader)` | Deserializes binary data to `Aprendiz` | Reads 10 fields sequentially by field ID |
| `write(BinaryWriter, Aprendiz)` | Serializes `Aprendiz` to binary | Writes 10 bytes of field IDs and values |
| `hashCode` | Hash based on `typeId` | Required for Hive internal registry |
| `operator ==` | Equality based on `typeId` | Required for Hive internal registry |

Similarly, `DispositivoAdapter` is generated for the `Dispositivo` class in [lib/models/models.g.dart L70-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L70-L114)

 with `typeId = 1` and 5 fields.

### Part Directive Requirement

The generated file requires a `part` directive in [lib/models/models.dart L3](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L3-L3)

:

```

```

This allows the generated TypeAdapters to access private members of the source classes if needed.

**Sources:** [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

 [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

 [pubspec.yaml L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L28-L28)

 [pubspec.lock L392-L399](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L392-L399)

---

## Flutter Lints Configuration

```

```

The `flutter_lints` package provides a curated set of static analysis rules recommended by the Flutter team. Version 5.0.0 is specified in [pubspec.yaml L27](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L27-L27)

 and resolves to v5.0.0 in [pubspec.lock L326-L333](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L326-L333)

### Lint Rule Categories

The package enforces three categories of rules:

1. **Style Conventions**: Consistent code formatting (const constructors, single quotes)
2. **Error Prevention**: Catches common programming mistakes before runtime
3. **Flutter Best Practices**: Framework-specific patterns (proper use of Keys, BuildContext)

### Transitive Dependencies

The linter system depends on:

* `lints` (v5.1.1): Base Dart language linting rules [pubspec.lock L544-L551](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L544-L551)
* `analyzer` (v6.11.0): Dart static analysis engine [pubspec.lock L17-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L17-L24)

The analyzer package is shared between `flutter_lints` and `build_runner`, ensuring consistent code analysis across development tasks.

**Sources:** [pubspec.yaml L27](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L27-L27)

 [pubspec.lock L326-L333](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L326-L333)

 [pubspec.lock L544-L551](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L544-L551)

 [pubspec.lock L17-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L17-L24)

---

## Flutter Test Framework

The `flutter_test` package (from Flutter SDK) provides testing infrastructure. While not actively used in the current codebase (no test files present), it is included in [pubspec.yaml L25-L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L25-L26)

 as a standard Flutter dependency.

### Test Capabilities

The framework supports:

* **Widget Testing**: Testing UI components in isolation
* **Unit Testing**: Testing business logic and data models
* **Integration Testing**: End-to-end application flows

### Testing Dependencies

The test framework pulls in several transitive packages from [pubspec.lock](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock)

:

* `test_api` (v0.7.4): Core testing primitives [pubspec.lock L845-L852](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L845-L852)
* `matcher` (v0.12.17): Assertion and expectation matching [pubspec.lock L568-L575](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L568-L575)
* `leak_tracker` (v10.0.9): Memory leak detection [pubspec.lock L520-L527](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L520-L527)
* `vm_service` (v15.0.0): Dart VM debugging integration [pubspec.lock L885-L892](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L885-L892)

**Sources:** [pubspec.yaml L25-L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L25-L26)

 [pubspec.lock L342-L346](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L342-L346)

---

## Code Generation Workflow

```

```

### Step-by-Step Generation Process

**Step 1: Annotate Data Models**

Add Hive annotations to classes in [lib/models/models.dart L5-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L5-L40)

:

```

```

**Step 2: Add Part Directive**

Include generated file reference in [lib/models/models.dart L3](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L3-L3)

:

```

```

**Step 3: Run Code Generation**

Execute build command:

```

```

Options:

* `build`: One-time generation
* `watch`: Continuous regeneration on file changes
* `build --delete-conflicting-outputs`: Force regeneration by deleting existing outputs

**Step 4: Register Adapters**

The generated adapters must be registered before Hive initialization. In the main application entry point, adapters are registered via:

```

```

### Watch Mode for Development

For continuous development, use watch mode:

```

```

This monitors source files and automatically regenerates code on save, triggered by the `build_daemon` background process [pubspec.lock L98-L104](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L98-L104)

**Sources:** [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

 [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

 [pubspec.yaml L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L29-L29)

---

## Build System Dependency Tree

```

```

### Shared Dependencies

Several packages are shared across multiple development tools:

| Package | Version | Used By |
| --- | --- | --- |
| `analyzer` | 6.11.0 | `build_runner`, `hive_generator`, `flutter_lints` |
| `source_gen` | 1.5.0 | `hive_generator`, other generators |
| `code_builder` | 4.10.1 | `hive_generator` (implicit via `source_gen`) |
| `watcher` | 1.1.2 | `build_runner` (file system monitoring) |

This shared dependency structure minimizes package duplication and ensures consistent behavior across tools.

**Sources:** [pubspec.lock L1-L952](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.lock#L1-L952)

---

## Command Reference

### Code Generation Commands

| Command | Description | Use Case |
| --- | --- | --- |
| `flutter pub run build_runner build` | One-time code generation | Initial setup, CI/CD pipelines |
| `flutter pub run build_runner build --delete-conflicting-outputs` | Force regeneration | Resolve generation conflicts |
| `flutter pub run build_runner watch` | Continuous regeneration | Active development |
| `flutter pub run build_runner clean` | Delete all generated files | Reset build state |

### Static Analysis Commands

| Command | Description | Output |
| --- | --- | --- |
| `flutter analyze` | Run all lint rules | Console warnings/errors |
| `flutter analyze --no-fatal-infos` | Analyze without treating infos as errors | Exit code 0 on infos |
| `dart analyze` | Dart-only analysis | Skips Flutter-specific rules |

### Testing Commands

| Command | Description | Scope |
| --- | --- | --- |
| `flutter test` | Run all tests | Widget + Unit tests |
| `flutter test test/unit/` | Run unit tests only | Specific directory |
| `flutter test --coverage` | Generate coverage report | All tests with lcov output |

### Dependency Management

| Command | Description | Effect |
| --- | --- | --- |
| `flutter pub get` | Install dependencies | Downloads all packages to `.pub-cache` |
| `flutter pub upgrade` | Update to latest compatible | Updates within version constraints |
| `flutter pub outdated` | Check for updates | Shows available newer versions |
| `flutter pub deps` | Show dependency tree | Visualizes all transitive dependencies |

**Sources:** [pubspec.yaml L1-L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L1-L44)

---

## Generated File Structure

### models.g.dart Anatomy

The generated file [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

 has the following structure:

```
models.g.dart
├── Header Comment (DO NOT MODIFY BY HAND)
├── Part Declaration (part of 'models.dart')
├── AprendizAdapter
│   ├── typeId = 0
│   ├── read(BinaryReader reader)
│   │   ├── Read field count (10)
│   │   ├── Read field map
│   │   └── Construct Aprendiz instance
│   ├── write(BinaryWriter writer, Aprendiz obj)
│   │   ├── Write field count (10)
│   │   └── Write each field with ID
│   ├── hashCode getter
│   └── == operator override
└── DispositivoAdapter
    ├── typeId = 1
    ├── read(BinaryReader reader)
    │   ├── Read field count (5)
    │   ├── Read field map
    │   └── Construct Dispositivo instance
    ├── write(BinaryWriter writer, Dispositivo obj)
    │   ├── Write field count (5)
    │   └── Write each field with ID
    ├── hashCode getter
    └── == operator override
```

### Field Serialization Order

The `write()` method in [lib/models/models.g.dart L34-L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L34-L57)

 serializes fields in strict order:

1. Write total field count (`writeByte(10)` for Aprendiz)
2. For each field: write field ID, then write field value
3. Field IDs correspond to `@HiveField(n)` annotations

The `read()` method in [lib/models/models.g.dart L14-L31](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L14-L31)

 deserializes by:

1. Reading field count
2. Building field ID → value map
3. Constructing object with values from map by field ID

This design allows forward/backward compatibility: if field IDs are stable, older code can ignore new fields, and new code can use default values for missing fields.

**Sources:** [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

---

## Integration with Runtime Application

```

```

The development tools operate exclusively at build time. Their output (generated TypeAdapters) is compiled into the application binary. At runtime:

1. **Initialization**: Application imports generated adapters from `models.g.dart`
2. **Registration**: `Hive.registerAdapter()` calls register each adapter with Hive's internal registry
3. **Serialization**: When `box.put(aprendiz)` is called, Hive uses `AprendizAdapter.write()` to convert the object to binary
4. **Deserialization**: When `box.get(key)` is called, Hive uses `AprendizAdapter.read()` to reconstruct the object from binary

The development tools themselves are not included in the compiled `.apk` or `.ipa` files.

**Sources:** [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

 [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

---

## Troubleshooting

### Common Issues

| Issue | Symptom | Solution |
| --- | --- | --- |
| Conflicting outputs | `Conflict on write` error | Run with `--delete-conflicting-outputs` flag |
| Missing generated file | Import error on `models.g.dart` | Run `flutter pub run build_runner build` |
| Type not registered | `HiveError: Cannot read, unknown typeId` | Verify `Hive.registerAdapter()` is called before box access |
| Outdated generated code | Type mismatch at runtime | Regenerate with `build_runner build` after model changes |
| Build cache corruption | Inconsistent generation results | Run `build_runner clean` then rebuild |
| Lint violations blocking CI | CI pipeline fails on warnings | Fix violations or configure `analysis_options.yaml` |

### Verification Steps

After running code generation:

1. Verify [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)  exists
2. Check that `AprendizAdapter` and `DispositivoAdapter` classes are present
3. Confirm `typeId` values match annotations (0 and 1)
4. Ensure field count in `write()` matches `@HiveField` annotations

**Sources:** [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

 [pubspec.yaml L24-L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L24-L29)