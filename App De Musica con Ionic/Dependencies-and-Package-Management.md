# Dependencies and Package Management

> **Relevant source files**
> * [package-lock.json](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json)
> * [package.json](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json)

This document describes the package management configuration for the MusicApp application, including all production and development dependencies, NPM scripts, and version locking mechanisms. The application uses npm as its package manager to handle JavaScript/TypeScript dependencies across the Angular, Ionic, and Capacitor frameworks.

For information about how these dependencies are used in the build process, see [Angular Build Configuration](/axchisan/MusicApp-Ionic/5.1-angular-build-configuration). For environment-specific configuration, see [Environment Configuration](/axchisan/MusicApp-Ionic/5.3-environment-configuration). For native platform integration details, see [Capacitor and Native Integration](/axchisan/MusicApp-Ionic/5.4-capacitor-and-native-integration).

## Package Management Overview

The application's dependency configuration is defined in [package.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L1-L64)

 and locked to specific versions in [package-lock.json

1](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L1-LNaN)

 The project is configured as a private package and uses npm (or yarn) as the package manager.

```

```

**Sources:** [package.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L1-L64)

 [package-lock.json L1-L10](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L1-L10)

## Package Metadata

The application is identified with the following metadata in [package.json L2-L5](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L2-L5)

:

| Property | Value |
| --- | --- |
| `name` | `"MusicApp"` |
| `version` | `"0.0.1"` |
| `author` | `"Ionic Framework"` |
| `homepage` | `"https://ionicframework.com/"` |
| `private` | `true` |

The `private: true` flag prevents accidental publication to npm registries.

**Sources:** [package.json L2-L5](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L2-L5)

 [package.json L14](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L14-L14)

## NPM Scripts

The application defines six NPM scripts for common development tasks in [package.json L6-L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L6-L12)

:

```

```

| Script | Command | Purpose |
| --- | --- | --- |
| `ng` | `ng` | Direct access to Angular CLI |
| `start` | `ng serve` | Start development server with hot reload |
| `build` | `ng build` | Build application for production |
| `watch` | `ng build --watch --configuration development` | Build with file watching for development |
| `test` | `ng test` | Execute unit tests via Karma |
| `lint` | `ng lint` | Run ESLint code quality checks |

**Sources:** [package.json L6-L12](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L6-L12)

## Production Dependencies

The application includes 13 production dependencies that are bundled with the application at runtime, defined in [package.json L15-L33](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L15-L33)

:

### Angular Framework Dependencies

```

```

| Package | Version | Purpose |
| --- | --- | --- |
| `@angular/core` | ^20.0.0 | Core Angular framework with component model |
| `@angular/common` | ^20.0.0 | Common directives, pipes, and HTTP client |
| `@angular/compiler` | ^20.0.0 | Template compiler for runtime compilation |
| `@angular/forms` | ^20.0.0 | Reactive and template-driven forms |
| `@angular/router` | ^20.0.0 | Client-side routing and navigation |
| `@angular/platform-browser` | ^20.0.0 | Browser-specific rendering |
| `@angular/platform-browser-dynamic` | ^20.0.0 | JIT compilation for browser |
| `@angular/animations` | ^20.0.0 | Animation framework |
| `rxjs` | ~7.8.0 | Reactive extensions for observables |
| `tslib` | ^2.3.0 | TypeScript runtime helpers |
| `zone.js` | ~0.15.0 | Execution context tracking for change detection |

**Sources:** [package.json L16-L23](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L16-L23)

 [package.json L31-L33](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L31-L33)

### Ionic Framework Dependencies

```

```

| Package | Version | Purpose |
| --- | --- | --- |
| `@ionic/angular` | ^8.0.0 | Ionic Framework UI components for Angular |
| `ionicons` | ^7.0.0 | Icon library for Ionic applications |

**Sources:** [package.json L29-L30](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L29-L30)

### Capacitor Native Bridge Dependencies

```

```

| Package | Version | Purpose |
| --- | --- | --- |
| `@capacitor/core` | 7.4.4 | Core Capacitor runtime for native bridge |
| `@capacitor/app` | 7.1.0 | App lifecycle and metadata APIs |
| `@capacitor/haptics` | 7.0.2 | Haptic feedback and vibration |
| `@capacitor/keyboard` | 7.0.3 | Keyboard display and event handling |
| `@capacitor/status-bar` | 7.0.3 | Status bar styling and visibility |

**Sources:** [package.json L24-L28](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L24-L28)

## Development Dependencies

The application includes 26 development dependencies used during build, test, and development workflows, defined in [package.json L35-L61](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L35-L61)

:

### Angular Development Tools

```

```

| Package | Version | Purpose |
| --- | --- | --- |
| `@angular/cli` | ^20.0.0 | Angular command-line interface |
| `@angular-devkit/build-angular` | ^20.0.0 | Angular build tools and Webpack configuration |
| `@angular/compiler-cli` | ^20.0.0 | Ahead-of-Time (AOT) template compiler |
| `@angular/language-service` | ^20.0.0 | TypeScript language service for Angular templates |

**Sources:** [package.json L36](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L36-L36)

 [package.json L42-L44](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L42-L44)

### ESLint and Code Quality Tools

```

```

| Package | Version | Purpose |
| --- | --- | --- |
| `eslint` | ^9.16.0 | Core ESLint linting engine |
| `@typescript-eslint/parser` | ^8.18.0 | TypeScript parser for ESLint |
| `@typescript-eslint/eslint-plugin` | ^8.18.0 | TypeScript-specific ESLint rules |
| `@angular-eslint/builder` | ^20.0.0 | Angular CLI builder for ESLint |
| `@angular-eslint/eslint-plugin` | ^20.0.0 | Angular-specific ESLint rules |
| `@angular-eslint/eslint-plugin-template` | ^20.0.0 | Template linting rules |
| `@angular-eslint/template-parser` | ^20.0.0 | Parser for Angular templates |
| `@angular-eslint/schematics` | ^20.0.0 | Schematics for ESLint configuration |
| `eslint-plugin-import` | ^2.29.1 | ES module import/export rules |
| `eslint-plugin-jsdoc` | ^48.2.1 | JSDoc comment validation |
| `eslint-plugin-prefer-arrow` | 1.2.2 | Enforce arrow function usage |

**Sources:** [package.json L37-L41](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L37-L41)

 [package.json L48-L53](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L48-L53)

### Testing Infrastructure

```

```

| Package | Version | Purpose |
| --- | --- | --- |
| `jasmine-core` | ~5.1.0 | Jasmine testing framework core |
| `@types/jasmine` | ~5.1.0 | TypeScript type definitions for Jasmine |
| `jasmine-spec-reporter` | ~5.0.0 | Console reporter for Jasmine tests |
| `karma` | ~6.4.0 | Test runner for executing unit tests |
| `karma-jasmine` | ~5.1.0 | Karma adapter for Jasmine framework |
| `karma-jasmine-html-reporter` | ~2.1.0 | HTML reporter for browser test results |
| `karma-chrome-launcher` | ~3.2.0 | Chrome browser launcher for Karma |
| `karma-coverage` | ~2.2.0 | Code coverage reporting |

**Sources:** [package.json L47](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L47-L47)

 [package.json L54-L60](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L54-L60)

### Build and Platform Tools

| Package | Version | Purpose |
| --- | --- | --- |
| `@capacitor/cli` | 7.4.4 | Capacitor command-line interface |
| `@ionic/angular-toolkit` | ^12.0.0 | Ionic-specific builders and schematics |
| `typescript` | ~5.9.0 | TypeScript compiler |

**Sources:** [package.json L45-L46](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L45-L46)

 [package.json L61](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L61-L61)

## Dependency Version Ranges

The application uses semantic versioning with different version range specifiers:

```

```

### Version Range Patterns

* **Caret (`^`)**: Allows updates that do not change the leftmost non-zero digit * Example: `^20.0.0` accepts `20.x.x` but not `21.0.0` * Used for: Angular packages, Ionic, ESLint tools
* **Tilde (`~`)**: Allows updates to patch version only * Example: `~7.8.0` accepts `7.8.x` but not `7.9.0` * Used for: RxJS, Zone.js, testing tools
* **Exact**: No automatic updates allowed * Example: `7.4.4` only accepts exactly `7.4.4` * Used for: Capacitor core and plugins

**Sources:** [package.json L15-L61](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L15-L61)

## Version Locking with package-lock.json

The [package-lock.json

1](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L1-LNaN)

 file provides deterministic dependency resolution by locking all packages to exact versions. This file is automatically generated and updated by npm.

### Lockfile Structure

```

```

Key properties in [package-lock.json L2-L5](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L2-L5)

:

* `lockfileVersion: 3`: npm lockfile format version
* `requires: true`: All dependencies are required
* Package entries include: * `version`: Exact installed version * `resolved`: npm registry URL * `integrity`: SHA-512 checksum for verification * `dependencies` or `requires`: Sub-dependencies

**Sources:** [package-lock.json L1-L58](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L1-L58)

## Dependency Categories by Framework

### Angular Ecosystem Dependencies

Production:

* `@angular/animations` [package.json L16](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L16-L16)
* `@angular/common` [package.json L17](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L17-L17)
* `@angular/compiler` [package.json L18](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L18-L18)
* `@angular/core` [package.json L19](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L19-L19)
* `@angular/forms` [package.json L20](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L20-L20)
* `@angular/platform-browser` [package.json L21](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L21-L21)
* `@angular/platform-browser-dynamic` [package.json L22](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L22-L22)
* `@angular/router` [package.json L23](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L23-L23)

Development:

* `@angular-devkit/build-angular` [package.json L36](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L36-L36)
* `@angular-eslint/*` (6 packages) [package.json L37-L41](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L37-L41)
* `@angular/cli` [package.json L42](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L42-L42)
* `@angular/compiler-cli` [package.json L43](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L43-L43)
* `@angular/language-service` [package.json L44](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L44-L44)

### Ionic Ecosystem Dependencies

Production:

* `@ionic/angular` [package.json L29](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L29-L29)
* `ionicons` [package.json L30](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L30-L30)

Development:

* `@ionic/angular-toolkit` [package.json L46](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L46-L46)

### Capacitor Ecosystem Dependencies

Production:

* `@capacitor/core` [package.json L25](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L25-L25)
* `@capacitor/app` [package.json L24](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L24-L24)
* `@capacitor/haptics` [package.json L26](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L26-L26)
* `@capacitor/keyboard` [package.json L27](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L27-L27)
* `@capacitor/status-bar` [package.json L28](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L28-L28)

Development:

* `@capacitor/cli` [package.json L45](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L45-L45)

### Testing and Quality Tools

Development only:

* `eslint` and 8 related plugins [package.json L50-L53](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L50-L53)  [package.json L48-L49](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L48-L49)
* `jasmine-core` and related packages [package.json L54-L55](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L54-L55)
* `karma` and 4 related packages [package.json L56-L60](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L56-L60)
* `@types/jasmine` [package.json L47](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L47-L47)

### Core JavaScript/TypeScript Libraries

Production:

* `rxjs` [package.json L31](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L31-L31)
* `tslib` [package.json L32](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L32-L32)
* `zone.js` [package.json L33](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L33-L33)

Development:

* `typescript` [package.json L61](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L61-L61)

**Sources:** [package.json L15-L61](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L15-L61)

## Package Installation Workflow

```

```

Installation commands:

* `npm install`: Install all dependencies
* `npm install --production`: Install only production dependencies
* `npm ci`: Clean install from lockfile (used in CI/CD)
* `npm update`: Update packages within version ranges
* `npm outdated`: Check for outdated packages

**Sources:** [package.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L1-L64)

 [package-lock.json L1-L10](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L1-L10)

## Peer Dependencies

Several development packages declare peer dependencies that must be satisfied:

```

```

Peer dependencies are automatically satisfied when both packages are listed in the same `dependencies` or `devDependencies` section.

**Sources:** [package-lock.json L368-L429](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L368-L429)

 [package-lock.json L508-L535](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L508-L535)

## Summary

The MusicApp package configuration provides:

1. **13 production dependencies** for runtime functionality: * 8 Angular framework packages * 2 Ionic framework packages * 5 Capacitor native bridge packages * 3 supporting libraries (RxJS, tslib, zone.js)
2. **26 development dependencies** for build and test workflows: * 4 Angular CLI and build tools * 11 ESLint and code quality tools * 8 Jasmine/Karma testing tools * 3 platform tools (Capacitor CLI, Ionic toolkit, TypeScript)
3. **Semantic versioning** with three range strategies: * Caret (`^`) for major frameworks * Tilde (`~`) for supporting libraries * Exact versions for Capacitor packages
4. **Deterministic builds** via package-lock.json with integrity checksums
5. **Six NPM scripts** for common development tasks

All dependencies are managed through npm and locked to specific versions to ensure reproducible builds across development, CI/CD, and production environments.

**Sources:** [package.json L1-L64](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package.json#L1-L64)

 [package-lock.json L1-L58](https://github.com/axchisan/MusicApp-Ionic/blob/0a2b054f/package-lock.json#L1-L58)