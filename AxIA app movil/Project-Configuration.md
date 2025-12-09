# Project Configuration

> **Relevant source files**
> * [.metadata](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata)
> * [analysis_options.yaml](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml)

## Purpose and Scope

This document explains the Flutter project's core configuration files that define project metadata, SDK version tracking, and code analysis settings. These files are essential for Flutter tooling to properly assess capabilities, perform upgrades, and enforce code quality standards.

For information about environment variables and credentials management, see [Environment Setup](/axchisan/AxIA/2.2-environment-setup). For detailed code analysis rules and best practices, see [Code Analysis](/axchisan/AxIA/10.1-code-analysis). For comprehensive metadata tracking and migration details, see [Flutter Metadata](/axchisan/AxIA/10.2-flutter-metadata).

## Configuration Files Overview

The AxIA Flutter project uses two primary configuration files to manage project settings and code quality:

| File | Purpose | Version Control | Manual Editing |
| --- | --- | --- | --- |
| `.metadata` | Flutter SDK version tracking and migration metadata | Yes | No |
| `analysis_options.yaml` | Dart analyzer configuration and lint rules | Yes | Yes |

### Configuration Files in Development Workflow

```

```

**Sources:** [.metadata L1-L46](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L1-L46)

 [analysis_options.yaml L1-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L1-L33)

## Flutter Project Metadata

The `.metadata` file is automatically managed by Flutter tooling and tracks essential project information.

### Metadata Structure

```

```

**Sources:** [.metadata L1-L46](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L1-L46)

### Version Tracking

The Flutter SDK version is tracked using a Git commit revision:

| Property | Value | Purpose |
| --- | --- | --- |
| `revision` | `b45fa18946ecc2d9b4009952c636ba7e2ffbb787` | Exact Flutter SDK commit |
| `channel` | `stable` | Flutter release channel |
| `project_type` | `app` | Project classification (app/package/plugin) |

These values ensure consistent Flutter SDK behavior across different development environments.

**Sources:** [.metadata L6-L10](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L6-L10)

### Platform Migration Tracking

Each platform entry records when it was added to the project:

```

```

The project supports seven platforms:

1. **root** - Base Flutter project structure
2. **android** - Android mobile platform
3. **ios** - iOS mobile platform
4. **linux** - Linux desktop platform
5. **macos** - macOS desktop platform
6. **web** - Web browser platform
7. **windows** - Windows desktop platform

All platforms were created with the same revision, indicating they were initialized together during project setup.

**Sources:** [.metadata L13-L35](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L13-L35)

### Unmanaged Files

The metadata specifies files excluded from migration tooling:

```

```

These files contain custom code that should not be automatically modified during Flutter upgrades.

**Sources:** [.metadata L43-L45](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L43-L45)

## Code Analysis Configuration

The `analysis_options.yaml` file configures the Dart analyzer for static code analysis, warnings, and linting.

### Analyzer Configuration Hierarchy

```

```

**Sources:** [analysis_options.yaml L1-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L1-L33)

### Base Lint Package

The project uses the `flutter_lints` package as its foundation:

```

```

This package provides recommended lint rules for Flutter applications, encouraging best practices and consistent code style.

**Sources:** [analysis_options.yaml L14](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L14-L14)

### Error Suppression

Two analyzer errors are explicitly ignored:

| Error Type | Suppression | Rationale |
| --- | --- | --- |
| `unused_import` | `ignore` | Allow imports for future use without warnings |
| `unused_local_variable` | `ignore` | Allow variable declarations during debugging |

```

```

These suppressions reduce noise during development while maintaining code quality enforcement for other issues.

**Sources:** [analysis_options.yaml L10-L13](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L10-L13)

### Lint Rules Customization

The configuration provides a section for custom lint rule overrides:

```

```

Currently, no custom rules are enabled, but the structure allows for:

* **Disabling inherited rules** by setting them to `false`
* **Enabling additional rules** by setting them to `true`
* **File-level suppression** using `// ignore: name_of_lint` comments
* **Line-level suppression** using `// ignore_for_file: name_of_lint` comments

**Sources:** [analysis_options.yaml L16-L30](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L16-L30)

## Integration with Flutter Tooling

### Command-Line Analysis

```

```

**Sources:** [analysis_options.yaml L6](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L6-L6)

 [.metadata L1-L10](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L1-L10)

### IDE Integration

Modern IDEs read `analysis_options.yaml` to provide real-time feedback:

* **VS Code**: Dart extension displays warnings/errors inline
* **Android Studio**: Analysis pane shows lint violations
* **IntelliJ IDEA**: Code inspections use configured rules

The `.metadata` file ensures the IDE uses the correct Flutter SDK version for analysis.

**Sources:** [analysis_options.yaml L4-L5](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L4-L5)

## Configuration File Maintenance

### When to Modify

| File | Modify When | Common Actions |
| --- | --- | --- |
| `.metadata` | Never (auto-managed) | Verify after `flutter upgrade` |
| `analysis_options.yaml` | Code quality standards change | Add/remove lint rules |

### Version Control

Both configuration files must be committed to version control:

```

```

This ensures:

* Consistent Flutter SDK versions across the team
* Uniform code quality standards
* Proper IDE behavior for all developers

**Sources:** [.metadata L4](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L4-L4)

 [analysis_options.yaml L1-L2](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L1-L2)

## Relationship to Other Configuration

The project configuration files work alongside other important files:

```

```

For environment-specific configuration, see [Environment Setup](/axchisan/AxIA/2.2-environment-setup). For build configuration details, see [Android Setup](/axchisan/AxIA/9.1-android-setup). For repository security, see [Gitignore Configuration](/axchisan/AxIA/11.1-gitignore-configuration).

**Sources:** [.metadata L1-L46](https://github.com/axchisan/AxIA/blob/1fe26c44/.metadata#L1-L46)

 [analysis_options.yaml L1-L33](https://github.com/axchisan/AxIA/blob/1fe26c44/analysis_options.yaml#L1-L33)

---

## Summary

The AxIA project uses two configuration files to maintain project consistency:

1. **`.metadata`** - Automatically tracks Flutter SDK version (revision `b45fa18946ecc2d9b4009952c636ba7e2ffbb787` on stable channel), project type (app), and platform support (7 platforms). This file ensures consistent tooling behavior and should never be manually edited.
2. **`analysis_options.yaml`** - Extends `package:flutter_lints/flutter.yaml` with custom error suppressions (`unused_import`, `unused_local_variable`). This file can be modified to adjust code quality standards as the project evolves.

Both files are version-controlled and essential for proper Flutter tooling operation during development, analysis, and migration activities.