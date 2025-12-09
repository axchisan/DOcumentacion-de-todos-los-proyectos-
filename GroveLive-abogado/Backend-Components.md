# Backend Components

> **Relevant source files**
> * [controllers/abogadosCtrl.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php)
> * [ddbb/DBConexion.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php)
> * [index.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php)
> * [models/abogadosModel.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php)

## Purpose and Scope

This document provides an overview of the server-side PHP components that implement the business logic and data access layers of the Abogado application. The backend consists of four primary components: the entry point redirector, the controller layer, the model layer, and the database connection manager.

For information about the presentation layer (views and client-side assets), see [Frontend Components](/GroveLive/abogado/5-frontend-components). For architectural concepts and request flow patterns, see [System Architecture](/GroveLive/abogado/3-system-architecture).

---

## Backend Architecture Overview

The backend implements a three-layer architecture following the Model-View-Controller pattern, with an additional routing component at the entry point. Each component has a specific responsibility and maintains clear separation of concerns.

| Component | Location | Primary Responsibility | Key Classes/Functions |
| --- | --- | --- | --- |
| Entry Point | `index.php` | HTTP routing and initial redirect | Redirect header |
| Controller Layer | `controllers/abogadosCtrl.php` | Business logic delegation | `AbogadosController` |
| Model Layer | `models/abogadosModel.php` | Data access and retrieval | `Abogado` |
| Database Layer | `ddbb/DBConexion.php` | Connection management | `DBConexion` |

Sources: [index.php L1-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L4)

 [controllers/abogadosCtrl.php L1-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L14)

 [models/abogadosModel.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L1-L27)

 [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

---

## Backend Component Relationships

The following diagram illustrates the dependency structure and method invocations between backend components, using actual class names and method signatures from the codebase.

**Diagram: Backend Component Structure with Code Entities**

```

```

Sources: [index.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L2-L2)

 [controllers/abogadosCtrl.php L2-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L2-L11)

 [models/abogadosModel.php L2-L18](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L2-L18)

 [ddbb/DBConexion.php L5-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L12)

---

## Data Access Method Flow

This diagram traces the execution path from controller methods through model methods to database queries, showing the specific SQL operations and return types at each stage.

**Diagram: Method Invocation Chain with SQL Operations**

```

```

Sources: [controllers/abogadosCtrl.php L5-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L11)

 [models/abogadosModel.php L5-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L25)

 [ddbb/DBConexion.php L5-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L14)

---

## Component Characteristics

### Stateless Architecture

All backend components are stateless, with no session management or persistent state between requests. Each HTTP request instantiates new controller objects and establishes fresh database connections.

### Static Method Pattern

The `Abogado` model and `DBConexion` class exclusively use static methods [models/abogadosModel.php L5-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L17)

 [ddbb/DBConexion.php L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L5)

 This design eliminates the need for object instantiation at the model and database layers, though controllers still use instance methods.

### Thin Controller Layer

The `AbogadosController` implements a pure delegation pattern with no business logic, validation, or data transformation [controllers/abogadosCtrl.php L5-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L11)

 Each controller method contains a single `return` statement that forwards calls to the corresponding model method.

### SQL Injection Protection

The model layer implements two distinct query patterns:

| Method | Query Type | Protection Mechanism | Location |
| --- | --- | --- | --- |
| `getAllAbogados()` | Standard query | None (no user input) | [models/abogadosModel.php L7-L8](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L7-L8) |
| `getAbogadoById($id)` | Parameterized query | Prepared statements with `bind_param("i", $id)` | [models/abogadosModel.php L19-L23](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L19-L23) |

### Connection Management

Database connections are created on-demand via `DBConexion::connection()` [ddbb/DBConexion.php L5-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L16)

 with no connection pooling or reuse. Each model method invokes `DBConexion::connection()`, resulting in multiple connection attempts per request.

Sources: [controllers/abogadosCtrl.php L4-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L4-L12)

 [models/abogadosModel.php L4-L26](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L4-L26)

 [ddbb/DBConexion.php L3-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L3-L17)

---

## Backend Component Summary

The following table provides a detailed breakdown of each backend component's technical characteristics:

| Component | File Path | Class/Type | Methods | Dependencies | Return Types |
| --- | --- | --- | --- | --- | --- |
| Entry Point | `index.php` | Procedural | N/A | None | HTTP 302 redirect |
| Controller | `controllers/abogadosCtrl.php` | `AbogadosController` | `obtenerAbogados()`, `obtenerAbogadoPorId($id)` | `abogadosModel.php` | Arrays |
| Model | `models/abogadosModel.php` | `Abogado` | `getAllAbogados()`, `getAbogadoById($id)` | `DBConexion.php` | Arrays, null |
| Database | `ddbb/DBConexion.php` | `DBConexion` | `connection()` | mysqli extension | mysqli object |

Sources: [index.php L1-L4](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L4)

 [controllers/abogadosCtrl.php L1-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L14)

 [models/abogadosModel.php L1-L27](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L1-L27)

 [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

---

## Implementation Details

### File Structure

Backend components are organized into three directories:

```
controllers/
  └── abogadosCtrl.php     (AbogadosController class)
models/
  └── abogadosModel.php    (Abogado class)
ddbb/
  └── DBConexion.php       (DBConexion class)
index.php                  (Entry point redirector)
```

### Dependency Loading

The backend uses `require_once` for dependency resolution with relative paths:

* Controllers require models: `require_once "../models/abogadosModel.php"` [controllers/abogadosCtrl.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L2-L2)
* Models require database connection: `require_once "../ddbb/DBConexion.php"` [models/abogadosModel.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L2-L2)

This creates a linear dependency chain: Controller → Model → Database Connection → MySQL.

### Error Handling

Error handling is minimal across backend components:

* Database connection errors output a message via `echo` [ddbb/DBConexion.php L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L10-L10)
* Missing lawyer records return `null` from `getAbogadoById()` [models/abogadosModel.php L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L24-L24)
* No exception throwing or try-catch blocks are present

Sources: [controllers/abogadosCtrl.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L2-L2)

 [models/abogadosModel.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L2-L2)

 [ddbb/DBConexion.php L9-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L9-L14)

---

## Related Documentation

For detailed documentation of individual backend components:

* Entry point routing mechanism: [Entry Point and Routing](/GroveLive/abogado/4.1-entry-point-and-routing)
* Controller implementation: [Controllers Layer](/GroveLive/abogado/4.2-controllers-layer)
* Model and data access patterns: [Models Layer](/GroveLive/abogado/4.3-models-layer)
* Database connection configuration: [Database Connection Management](/GroveLive/abogado/4.4-database-connection-management)

For information about how backend components integrate with the presentation layer:

* View interaction patterns: [Views Overview](/GroveLive/abogado/5.1-views-overview)
* Complete request lifecycle: [Request Lifecycle](/GroveLive/abogado/3.1-request-lifecycle)
* Security considerations: [Security Considerations](/GroveLive/abogado/7-security-considerations)