# Code Quality and Linting

> **Relevant source files**
> * [.editorconfig](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig)
> * [.eslintrc.json](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json)
> * [package.json](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json)

## Purpose and Scope

This document covers the code quality enforcement mechanisms in the MusicApp codebase, specifically focusing on ESLint configuration for TypeScript and HTML template linting, EditorConfig for consistent formatting across editors, and the npm scripts used to execute quality checks. This page documents the static analysis tools that enforce code standards before runtime.

For information about runtime testing and quality assurance, see [Testing Infrastructure](/axchisan/MusicApp-Ionic/6.2-testing-infrastructure). For editor-specific development setup, see [VS Code Integration](/axchisan/MusicApp-Ionic/6.3-vs-code-integration).

## Overview

The MusicApp enforces code quality through two complementary mechanisms: **ESLint** for static code analysis and **EditorConfig** for consistent formatting. ESLint validates TypeScript and HTML templates against Angular-specific rules, while EditorConfig ensures consistent whitespace and formatting across different editors.

### Code Quality Tool Stack

```

```

**Sources:** [.eslintrc.json L1-L46](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L1-L46)

 [.editorconfig L1-L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L1-L17)

 [package.json L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L12-L12)

## ESLint Configuration Architecture

The linting system is configured in `.eslintrc.json` and uses the Angular ESLint plugin suite to enforce Angular-specific patterns. The configuration uses ESLint's "overrides" pattern to apply different rule sets to TypeScript files versus HTML templates.

### Configuration File Structure

```

```

**Sources:** [.eslintrc.json L1-L46](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L1-L46)

### TypeScript Linting Rules

The TypeScript override section applies strict Angular conventions for component and directive naming. The configuration extends the recommended Angular ESLint plugin and adds custom rules.

| Rule | Setting | Purpose |
| --- | --- | --- |
| `@angular-eslint/component-class-suffix` | `["error", {"suffixes": ["Page", "Component"]}]` | Enforces that component classes end with either "Page" or "Component" suffix |
| `@angular-eslint/component-selector` | `["error", {"type": "element", "prefix": "app", "style": "kebab-case"}]` | Requires element selectors to use "app-" prefix and kebab-case style |
| `@angular-eslint/directive-selector` | `["error", {"type": "attribute", "prefix": "app", "style": "camelCase"}]` | Requires attribute selectors to use "app" prefix and camelCase style |

**TypeScript Configuration Details:**

The TypeScript linting configuration is defined in [.eslintrc.json L6-L38](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L6-L38)

:

* **Parser Options** [.eslintrc.json L7-L10](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L7-L10) : Points to `tsconfig.json` for type-aware linting and enables `createDefaultProgram` for fallback type information
* **Extended Configurations** [.eslintrc.json L11-L14](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L11-L14) : Extends both `plugin:@angular-eslint/recommended` (core Angular rules) and `plugin:@angular-eslint/template/process-inline-templates` (inline template support)
* **Custom Rules** [.eslintrc.json L15-L37](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L15-L37) : Three Angular-specific rules configured as "error" level

**Sources:** [.eslintrc.json L5-L39](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L5-L39)

### HTML Template Linting

The HTML override section handles Angular template files with minimal custom configuration, relying on the recommended template rules from the Angular ESLint plugin.

**HTML Configuration Details:**

Defined in [.eslintrc.json L40-L44](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L40-L44)

:

* **File Pattern**: Targets `*.html` files only
* **Extended Configuration**: Uses `plugin:@angular-eslint/template/recommended` which includes rules for: * Template syntax validation * Accessibility checks * Angular-specific template patterns * Structural directive usage
* **Custom Rules**: Empty object, meaning no overrides to recommended rules

**Sources:** [.eslintrc.json L40-L44](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L40-L44)

## EditorConfig Standards

EditorConfig provides editor-agnostic formatting rules that work across IDEs (VS Code, WebStorm, etc.). These rules are automatically applied by supported editors when saving files.

### Formatting Rules by File Type

```

```

**Sources:** [.editorconfig L1-L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L1-L17)

### EditorConfig Rule Details

The configuration is defined in [.editorconfig L1-L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L1-L17)

 with three sections:

**Global Rules** [.editorconfig L4-L9](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L4-L9)

:

* `charset = utf-8`: Ensures all files use UTF-8 encoding
* `indent_style = space`: Uses spaces instead of tabs
* `indent_size = 2`: Two spaces per indentation level (standard for Angular/Ionic)
* `insert_final_newline = true`: Adds newline at end of file
* `trim_trailing_whitespace = true`: Removes trailing spaces

**TypeScript-Specific Rules** [.editorconfig L11-L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L11-L12)

:

* `quote_type = single`: Enforces single quotes in TypeScript files (matches ESLint conventions)

**Markdown-Specific Rules** [.editorconfig L14-L16](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L14-L16)

:

* `max_line_length = off`: No line length limit for documentation
* `trim_trailing_whitespace = false`: Preserves trailing spaces (meaningful in Markdown)

**Sources:** [.editorconfig L1-L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L1-L17)

## Linting Commands and Workflow

The linting system integrates with npm scripts and the Angular CLI, providing both manual and automated quality checks.

### Command Execution Flow

```

```

**Sources:** [package.json L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L12-L12)

 [.eslintrc.json L1-L46](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L1-L46)

### Available Linting Scripts

The `package.json` defines the following linting command at [package.json L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L12-L12)

:

```
"lint": "ng lint"
```

**Usage:**

* **`npm run lint`**: Executes full codebase linting
* **`ng lint --fix`**: Automatically fixes auto-fixable issues
* **`ng lint --format stylish`**: Uses different output format
* **`ng lint --max-warnings 0`**: Treats warnings as errors in CI

**Sources:** [package.json L6-L13](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L6-L13)

## Linting Dependencies

The linting infrastructure depends on multiple npm packages that work together to provide comprehensive code quality checking.

### ESLint Core Packages

| Package | Version | Purpose |
| --- | --- | --- |
| `eslint` | `^9.16.0` | Core ESLint engine |
| `@typescript-eslint/eslint-plugin` | `^8.18.0` | TypeScript-specific linting rules |
| `@typescript-eslint/parser` | `^8.18.0` | Parses TypeScript for ESLint |

**Sources:** [package.json L48-L50](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L48-L50)

### Angular ESLint Packages

| Package | Version | Purpose |
| --- | --- | --- |
| `@angular-eslint/builder` | `^20.0.0` | Angular CLI integration for `ng lint` |
| `@angular-eslint/eslint-plugin` | `^20.0.0` | Angular-specific linting rules |
| `@angular-eslint/eslint-plugin-template` | `^20.0.0` | Angular template linting rules |
| `@angular-eslint/schematics` | `^20.0.0` | Scaffolding for ESLint config |
| `@angular-eslint/template-parser` | `^20.0.0` | Parses Angular templates for ESLint |

**Sources:** [package.json L37-L41](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L37-L41)

### Additional ESLint Plugins

| Package | Version | Purpose |
| --- | --- | --- |
| `eslint-plugin-import` | `^2.29.1` | Validates ES6+ import/export syntax |
| `eslint-plugin-jsdoc` | `^48.2.1` | JSDoc comment linting |
| `eslint-plugin-prefer-arrow` | `1.2.2` | Enforces arrow function usage |

**Sources:** [package.json L51-L53](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L51-L53)

### Package Dependency Graph

```

```

**Sources:** [package.json L35-L61](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L35-L61)

## Rule Enforcement Levels

ESLint rules can be configured at different severity levels. The MusicApp configuration uses strict enforcement for Angular conventions.

### Severity Levels in Configuration

All custom rules in [.eslintrc.json L15-L37](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L15-L37)

 are configured as `"error"` level:

* **`"error"`**: Violations cause linting to fail (exit code 1)
* **`"warn"`**: Violations are reported but don't fail the build
* **`"off"`**: Rule is disabled

The MusicApp uses `"error"` exclusively for its custom rules, ensuring strict compliance with Angular naming conventions.

### Example Rule Application

For the `@angular-eslint/component-class-suffix` rule [.eslintrc.json L16-L21](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L16-L21)

:

**Valid Examples:**

* `export class HomePage implements OnInit {}`
* `export class GroupCardComponent {}`

**Invalid Examples:**

* `export class HomeView implements OnInit {}` ❌ (must end with "Page" or "Component")
* `export class Group {}` ❌ (must end with "Page" or "Component")

**Sources:** [.eslintrc.json L15-L37](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L15-L37)

## Integration with Build Pipeline

The linting system integrates with the overall development and CI/CD workflow, providing quality gates at multiple stages.

### Linting Execution Points

```

```

**Sources:** [package.json L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L12-L12)

 [.eslintrc.json L1-L46](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L1-L46)

 [.editorconfig L1-L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.editorconfig#L1-L17)

## Best Practices and Usage Patterns

### Running Linting Locally

Before committing code, developers should run:

```

```

To automatically fix auto-fixable issues:

```

```

### Component and Directive Naming

Based on the rules in [.eslintrc.json L16-L37](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L16-L37)

:

**Component Class Names:**

* Use `Page` suffix for routable pages: `HomePage`, `SettingsPage`
* Use `Component` suffix for reusable components: `HeaderComponent`, `CardComponent`

**Component Selectors:**

* Must use `app-` prefix: `<app-header>`, `<app-card>`
* Must use kebab-case: `<app-user-profile>`, not `<app-userProfile>`

**Directive Selectors:**

* Must use `app` prefix: `appHighlight`, `appTooltip`
* Must use camelCase: `appUserPermission`, not `app-user-permission`

**Sources:** [.eslintrc.json L16-L37](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L16-L37)

### Ignoring Specific Files

The configuration ignores files matching `projects/**/*` pattern [.eslintrc.json L3](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L3-L3)

 To ignore additional files or directories, add patterns to the `ignorePatterns` array.

**Sources:** [.eslintrc.json L3](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/.eslintrc.json#L3-L3)