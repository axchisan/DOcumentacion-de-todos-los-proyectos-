# System Architecture

> **Relevant source files**
> * [lib/main.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart)
> * [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)
> * [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
> * [lib/screens/splash_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)
> * [macos/Flutter/GeneratedPluginRegistrant.swift](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/macos/Flutter/GeneratedPluginRegistrant.swift)

## Purpose and Scope

This document provides a comprehensive overview of the SENA Digital ID Card application's architecture, including its layered structure, component organization, and data flow patterns. This page focuses on the high-level architectural patterns and design decisions that define how the system is organized.

For detailed information about specific architectural components:

* Data persistence implementation and database operations, see [Data Persistence Layer](/axchisan/AppGestionCarnetsSENA/3.1-data-persistence-layer)
* Complete API reference for database operations, see [DatabaseService Reference](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference)
* Navigation routes and screen transitions, see [Screen Navigation and Routing](/axchisan/AppGestionCarnetsSENA/3.2-screen-navigation-and-routing)
* Authentication flows and security, see [Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system)

## Architectural Overview

The application follows a **layered architecture** pattern with clear separation of concerns across four distinct layers:

| Layer | Responsibility | Key Components |
| --- | --- | --- |
| **Presentation Layer** | UI screens and widgets | `SplashScreen`, `LoginScreen`, `RegistrationScreen`, `HomeScreen`, `IdCardScreen`, `DeviceManagementScreen` |
| **Service Layer** | Business logic and data orchestration | `DatabaseService` |
| **Model Layer** | Data structures and serialization | `Aprendiz`, `Dispositivo`, generated Hive adapters |
| **Persistence Layer** | Data storage and retrieval | Hive local storage, PostgreSQL remote database |

The architecture implements a **hybrid persistence strategy** where the `DatabaseService` abstracts dual-database operations, providing transparent offline-first functionality to the UI layer.

### System Architecture Diagram

```

```

**Sources:** [lib/main.dart L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L1-L75)

 [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

## Component Organization

The codebase follows a feature-based organization pattern with clear module boundaries:

### Directory Structure and Module Roles

```

```

**Key Module Characteristics:**

| Module | Files | Dependencies | Responsibility |
| --- | --- | --- | --- |
| `main.dart` | 1 file | All modules | Application bootstrap, Hive initialization, route registration |
| `screens/` | 6 files | `services/`, `widgets/`, `utils/`, `models/` | User interface and interaction handling |
| `services/` | 1 file (`database_service.dart`) | `models/`, external packages | Data orchestration and business logic |
| `models/` | 2 files | Hive package | Data structures and serialization |
| `widgets/` | Multiple files | `utils/` | Reusable UI components |
| `utils/` | Multiple files | None | Constants and helper functions |

**Sources:** [lib/main.dart L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L1-L75)

 [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

## Data Flow Architecture

The application implements a **unidirectional data flow** pattern where all data operations are mediated by `DatabaseService`:

### Data Flow Patterns Diagram

```

```

**Sources:** [lib/services/database_service.dart L21-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L238)

 [lib/screens/login_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart)

 [lib/screens/registration_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart)

 [lib/screens/home_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart)

 [lib/screens/device_management_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/device_management_screen.dart)

### Data Flow Characteristics

The application implements three distinct data flow patterns:

#### 1. Read-Through Pattern (Authentication)

* **Used by:** `LoginScreen`
* **Method:** `getAprendizFromPostgres(id, password)` [lib/services/database_service.dart L129-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L129-L169)
* **Flow:** PostgreSQL → Validate → Cache to Hive → Return to UI
* **Purpose:** Authenticates against authoritative source, populates local cache on success

#### 2. Write-Through Pattern (Data Modification)

* **Used by:** `RegistrationScreen`, `DeviceManagementScreen`
* **Method:** `saveAprendiz(aprendiz)` → `_syncToPostgres(aprendiz)` [lib/services/database_service.dart L42-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L119)
* **Flow:** UI → Hive (immediate) → PostgreSQL (async sync) → Complete
* **Purpose:** Provides immediate local persistence with eventual remote consistency

#### 3. Cache-First Pattern (Offline Access)

* **Used by:** `HomeScreen`, `IdCardScreen`
* **Method:** `getAprendizFromLocal(id)` [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127)
* **Flow:** Hive → Return to UI (no network call)
* **Purpose:** Enables offline functionality for authenticated users

**Sources:** [lib/services/database_service.dart L21-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L238)

## Navigation Architecture

The application uses **Flutter's named route pattern** with centralized route definitions in `main.dart`:

### Route Configuration

```

```

All feature screens that require user data (`HomeScreen`, `IdCardScreen`, `DeviceManagementScreen`) receive an `Aprendiz` object via route arguments:

| Route | Path | Arguments | Implementation |
| --- | --- | --- | --- |
| Splash | `/splash` | None | Entry point screen |
| Login | `/login` | None | Public authentication screen |
| Registration | `/registro` | None | Public registration screen |
| Home | `/inicio` | `Aprendiz` object | Protected screen (requires auth) |
| ID Card | `/carnet` | `Aprendiz` object | Protected screen (requires auth) |
| Device Management | `/dispositivos` | `Aprendiz` object | Protected screen (requires auth) |

**Route argument extraction pattern** (used in [lib/main.dart L50-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L50-L70)

):

```

```

**Sources:** [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)

## Key Architectural Decisions

### 1. Single Service Pattern

The application uses a **single centralized service** (`DatabaseService`) rather than multiple specialized services. This design decision:

* **Simplifies dependency management:** All screens depend on one service
* **Centralizes database logic:** Both Hive and PostgreSQL operations in one place
* **Enables consistent error handling:** All database errors handled uniformly
* **Facilitates synchronization:** The `_syncToPostgres()` method coordinates dual-database writes

**Trade-off:** The `DatabaseService` class has high complexity (239 lines) but provides a clean API to consumers.

**Sources:** [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

### 2. Hive Type Adapters for Serialization

The application uses **code generation** for Hive serialization via `@HiveType` and `@HiveField` annotations:

* **Models defined in:** [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)
* **Adapters generated in:** [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)
* **Registration in:** [lib/main.dart L17-L18](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L17-L18)

This approach ensures type-safe binary serialization without manual serialization code.

**Sources:** [lib/models/models.dart L1-L106](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L1-L106)

 [lib/models/models.g.dart L1-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart#L1-L115)

 [lib/main.dart L17-L18](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L17-L18)

### 3. Environment-Based Configuration

Database credentials are externalized to a `.env` file and loaded via `flutter_dotenv`:

```
Configuration loading sequence:
1. main() → dotenv.load(fileName: ".env")  [line 22]
2. DatabaseService reads: dotenv.env['POSTGRES_HOST'], etc.  [lines 13-17]
3. Connection methods use these values  [lines 23-29, 51-57]
```

This separates sensitive configuration from source code.

**Sources:** [lib/main.dart L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L22-L22)

 [lib/services/database_service.dart L13-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L13-L17)

### 4. Session Management via Hive Persistence

User sessions are **implicit** - the presence of an `Aprendiz` object in the `aprendicesBox` indicates an active session:

* **Session detection:** `SplashScreen` checks Hive box [lib/screens/splash_screen.dart L23-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L24)
* **Session creation:** Login/registration writes to Hive via `saveAprendiz()`
* **Session termination:** Logout clears Hive box (implemented in feature screens)

No explicit session tokens or JWT authentication is used.

**Sources:** [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)

 [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

### 5. Composite Data Model

The `Aprendiz` model contains a **list of `Dispositivo` objects** [lib/models/models.dart L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L26-L26)

 implementing a one-to-many relationship:

* **Advantage:** Simplifies device queries (no separate device lookups)
* **Synchronization:** Both models synced together in `_syncToPostgres()` [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)
* **Device operations:** Modify parent `Aprendiz` object, then save entire graph

This denormalized approach optimizes for the application's read-heavy device access patterns.

**Sources:** [lib/models/models.dart L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L26-L26)

 [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)

 [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

## System Initialization Sequence

The application follows a strict initialization order defined in `main()`:

```

```

**Critical ordering requirements:**

1. `WidgetsFlutterBinding.ensureInitialized()` must precede any async operations [lib/main.dart L15](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L15-L15)
2. Type adapters must be registered before opening boxes [lib/main.dart L17-L18](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L17-L18)
3. Box must be opened before any screen attempts to access it [lib/main.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L19-L19)
4. Environment variables must load before `DatabaseService` is instantiated [lib/main.dart L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L22-L22)

**Sources:** [lib/main.dart L14-L25](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L25)

## Summary

The SENA Digital ID Card application architecture demonstrates several key characteristics:

1. **Clear layered separation** with `DatabaseService` as the architectural keystone
2. **Hybrid persistence strategy** enabling offline-first functionality
3. **Unidirectional data flow** through a single service layer
4. **Route-based navigation** with explicit data passing
5. **Code generation** for type-safe serialization
6. **Environment-based configuration** for deployment flexibility

The architecture prioritizes **offline capability** and **data consistency** through its dual-database approach, while maintaining a simple, maintainable structure for UI developers through the `DatabaseService` abstraction layer.

For implementation details of specific architectural components, refer to the child sections of this document.