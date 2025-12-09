# Overview

> **Relevant source files**
> * [README.md](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md)
> * [lib/main.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart)
> * [pubspec.yaml](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml)

## Purpose and Scope

This document provides a high-level introduction to the SENA Digital ID Card application (`sena_carnet_digital`), a Flutter-based mobile system that enables SENA (Servicio Nacional de Aprendizaje) apprentices to register, generate, and display virtual identification cards on their mobile devices. The application implements a hybrid offline-first architecture, allowing apprentices to access their digital credentials and manage registered devices without requiring constant internet connectivity.

This overview covers the system's purpose, architecture, technology stack, and core capabilities. For detailed information about specific subsystems, refer to:

* Getting started and environment setup: [Getting Started](/axchisan/AppGestionCarnetsSENA/2-getting-started)
* Detailed architecture and design patterns: [System Architecture](/axchisan/AppGestionCarnetsSENA/3-system-architecture)
* Authentication implementation: [Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system)
* Feature-level documentation: [Core Features](/axchisan/AppGestionCarnetsSENA/5-core-features)

**Sources:** [README.md L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md#L1-L75)

 [lib/main.dart L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L1-L75)

 [pubspec.yaml L1-L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L1-L44)

---

## System Context

The SENA Digital ID Card application operates within an institutional ecosystem that includes a pre-existing desktop Java application used by instructors and security personnel for barcode scanning and access validation.

### System Context Diagram

```

```

**Key Integration Points:**

* **Shared Database:** Both mobile and desktop applications connect to the same PostgreSQL database, ensuring data consistency across platforms
* **Barcode Protocol:** The mobile app generates barcodes from `id_identificacion` that the desktop Java application can scan and validate
* **Institutional Registry:** The PostgreSQL database is pre-populated with valid apprentice identification numbers by instructors

**Sources:** [README.md L4-L46](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md#L4-L46)

---

## Application Architecture

### High-Level Component Mapping

The application follows a layered architecture with clear separation of concerns. The following diagram maps conceptual layers to actual code entities:

```

```

**Architectural Layers:**

| Layer | Purpose | Key Files |
| --- | --- | --- |
| **Entry Point** | Application initialization, routing, Hive setup | [lib/main.dart L14-L25](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L25) |
| **Presentation** | UI screens and user interaction logic | [lib/screens/](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/) <br>  directory |
| **Service** | Business logic and data orchestration | [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart) |
| **Data Models** | Domain entities and persistence adapters | [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart) <br>  [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart) |
| **UI Components** | Reusable widgets and styling utilities | [lib/widgets/](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/) <br>  [lib/utils/](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/utils/) <br>  directories |
| **Configuration** | Environment variables and dependencies | [.env](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/.env) <br>  [pubspec.yaml L1-L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L1-L44) |

**Sources:** [lib/main.dart L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L1-L75)

 [pubspec.yaml L1-L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L1-L44)

---

## Technology Stack

### Core Technologies

| Technology | Version/Package | Purpose | Configuration |
| --- | --- | --- | --- |
| **Flutter** | SDK 3.8.1+ | Cross-platform mobile framework | [pubspec.yaml L6-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L6-L7) |
| **Dart** | 3.8.1+ | Programming language | [pubspec.yaml L6-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L6-L7) |
| **Hive** | ^2.2.3 | Local NoSQL storage for offline capability | [pubspec.yaml L18](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L18-L18) <br>  [lib/main.dart L16-L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L16-L19) |
| **PostgreSQL** | postgres ^2.6.2 | Remote relational database | [pubspec.yaml L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L17-L17) |
| **flutter_dotenv** | ^5.1.0 | Environment variable management | [pubspec.yaml L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L22-L22) <br>  [lib/main.dart L4-L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L4-L22) |

### Supporting Packages

| Package | Version | Purpose |
| --- | --- | --- |
| **crypto** | ^3.0.6 | SHA-256 password hashing |
| **qr_flutter** | ^4.1.0 | Barcode generation for ID cards |
| **barcode_widget** | ^2.0.3 | Additional barcode rendering |
| **image_picker** | ^1.0.4 | Profile photo capture/selection |
| **path_provider** | ^2.1.5 | File system access for Hive |
| **hive_generator** | ^2.0.1 | Code generation for type adapters |
| **build_runner** | ^2.4.6 | Build system for code generation |

**Sources:** [pubspec.yaml L9-L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L9-L29)

---

## Core Capabilities

### Primary User Workflows

```

```

### Feature Summary

**Authentication & Registration:**

* Validates apprentice identification numbers against PostgreSQL before account creation ([Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system))
* Implements SHA-256 password hashing for security ([Security Implementation](/axchisan/AppGestionCarnetsSENA/4.4-security-implementation))
* Maintains session state in Hive local storage ([Session Management](/axchisan/AppGestionCarnetsSENA/4.3-session-management))

**Digital ID Card:**

* Generates virtual ID card with profile photo and apprentice details ([Virtual ID Card Display](/axchisan/AppGestionCarnetsSENA/5.2-virtual-id-card-display))
* Creates barcode from `id_identificacion` for scanning by desktop application
* Displays offline once cached locally

**Device Management:**

* Allows registration of up to 10 devices per apprentice ([Device Management](/axchisan/AppGestionCarnetsSENA/5.3-device-management))
* Stores device information in both local cache and PostgreSQL
* Enforces unique device identification within user's device list

**Sources:** [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 [README.md L12-L20](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md#L12-L20)

 [README.md L38-L45](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md#L38-L45)

---

## Key Design Principles

### Hybrid Persistence Strategy

The application implements a **cache-first, write-through** pattern:

1. **Reads:** Attempt local Hive cache first (`getAprendizFromLocal`), fall back to PostgreSQL if needed
2. **Writes:** Write to Hive immediately, then synchronize to PostgreSQL asynchronously (`_syncToPostgres`)
3. **Authentication:** Always validates against PostgreSQL (`getAprendizFromPostgres`), caches on success

This strategy enables offline functionality while maintaining data consistency. See [Data Persistence Layer](/axchisan/AppGestionCarnetsSENA/3.1-data-persistence-layer) for implementation details.

### Route-Based Navigation

The application uses Flutter's named routes system [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 with argument passing:

| Route | Screen | Required Argument |
| --- | --- | --- |
| `/splash` | `SplashScreen` | None |
| `/login` | `LoginScreen` | None |
| `/registro` | `RegistrationScreen` | None |
| `/inicio` | `HomeScreen` | `Aprendiz` (optional) |
| `/carnet` | `IdCardScreen` | `Aprendiz` |
| `/dispositivos` | `DeviceManagementScreen` | `Aprendiz` |

All feature screens receive an `Aprendiz` object containing user data, eliminating the need for global state management. See [Screen Navigation and Routing](/axchisan/AppGestionCarnetsSENA/3.2-screen-navigation-and-routing) for details.

### DatabaseService Abstraction

The `DatabaseService` class acts as the single point of contact for all data operations, abstracting the complexity of dual persistence from UI code. All screens interact with data through `DatabaseService` methods, never directly accessing Hive or PostgreSQL. This encapsulation is detailed in [DatabaseService Reference](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference).

**Sources:** [lib/main.dart L14-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L75)

 [README.md L38-L45](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md#L38-L45)

---

## Project Structure

```markdown
lib/
├── main.dart                          # Application entry point
├── models/
│   ├── models.dart                    # Aprendiz & Dispositivo classes
│   └── models.g.dart                  # Generated Hive adapters
├── screens/
│   ├── splash_screen.dart             # Session detection & routing
│   ├── login_screen.dart              # Authentication UI
│   ├── registration_screen.dart       # Account creation UI
│   ├── home_screen.dart               # Dashboard & navigation hub
│   ├── id_card_screen.dart            # Virtual ID display
│   └── device_management_screen.dart  # Device registry UI
├── services/
│   └── database_service.dart          # Data orchestration layer
├── widgets/
│   ├── custom_textfield.dart          # Reusable text input
│   ├── custom_button.dart             # Reusable button
│   └── sena_logo.dart                 # Branding component
└── utils/
    └── app_colors.dart                # Theme constants
```

**Sources:** [lib/main.dart L5-L12](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L5-L12)

---

## Related Documentation

For detailed information about specific aspects of the system:

* **Setup & Deployment:** [Getting Started](/axchisan/AppGestionCarnetsSENA/2-getting-started)
* **Architecture Deep Dive:** [System Architecture](/axchisan/AppGestionCarnetsSENA/3-system-architecture), [Data Persistence Layer](/axchisan/AppGestionCarnetsSENA/3.1-data-persistence-layer)
* **Security & Auth:** [Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system), [Security Implementation](/axchisan/AppGestionCarnetsSENA/4.4-security-implementation)
* **Features:** [Core Features](/axchisan/AppGestionCarnetsSENA/5-core-features), [Virtual ID Card Display](/axchisan/AppGestionCarnetsSENA/5.2-virtual-id-card-display), [Device Management](/axchisan/AppGestionCarnetsSENA/5.3-device-management)
* **Components:** [UI Components Library](/axchisan/AppGestionCarnetsSENA/6-ui-components-library), [Theme and Styling](/axchisan/AppGestionCarnetsSENA/6.3-theme-and-styling)
* **Dependencies:** [Dependencies and External Packages](/axchisan/AppGestionCarnetsSENA/7-dependencies-and-external-packages)
* **Platform Config:** [Android Platform Configuration](/axchisan/AppGestionCarnetsSENA/8-android-platform-configuration)

**Sources:** [README.md L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/README.md#L1-L75)