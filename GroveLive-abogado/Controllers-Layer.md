# Controllers Layer

> **Relevant source files**
> * [controllers/abogadosCtrl.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php)
> * [models/abogadosModel.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php)

## Purpose and Scope

This document details the Controllers layer of the Abogado application, specifically the `AbogadosController` class defined in [controllers/abogadosCtrl.php L4-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L4-L12)

 The Controllers layer implements a thin delegation pattern between the Views layer (see [5.1](/GroveLive/abogado/5.1-views-overview)) and the Models layer (see [4.3](/GroveLive/abogado/4.3-models-layer)), providing a clean interface for views to access lawyer data without directly coupling to the model implementation.

## Overview

The Controllers layer consists of a single controller class responsible for all lawyer-related business logic. The controller acts as a facade that views instantiate to retrieve lawyer data, delegating all data access operations to the underlying model layer.

**Key Characteristics:**

* Single controller class (`AbogadosController`) handles all lawyer operations
* No business logic or data transformation - pure delegation pattern
* Methods mirror model layer methods with identical signatures
* Instantiated directly by views rather than through a front controller or dependency injection

Sources: [controllers/abogadosCtrl.php L1-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L15)

## Class Structure

The `AbogadosController` class is defined in `controllers/abogadosCtrl.php` and depends on the `Abogado` model class for data access.

```

```

**Diagram: Controller Class Structure and Dependencies**

Sources: [controllers/abogadosCtrl.php L4-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L4-L12)

 [models/abogadosModel.php L4-L26](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L4-L26)

## Dependency Management

The controller declares its dependency on the model layer using a `require_once` statement at the file level.

| Dependency Type | Target | Location | Purpose |
| --- | --- | --- | --- |
| `require_once` | `../models/abogadosModel.php` | [controllers/abogadosCtrl.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L2-L2) | Loads `Abogado` model class definition |

**Path Resolution:** The relative path `../models/abogadosModel.php` indicates the controller file expects to be included from a context where its parent directory is accessible. Views accomplish this by including the controller from the `views/` directory using the path `../controllers/abogadosCtrl.php`.

Sources: [controllers/abogadosCtrl.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L2-L2)

## Methods

### obtenerAbogados()

Retrieves all lawyer records from the database.

**Method Signature:**

```

```

**Location:** [controllers/abogadosCtrl.php L5-L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L7)

**Return Value:** Array of associative arrays, each representing a lawyer record with keys: `id_abogado`, `nombre`, `especialidad`, `telefono`, `nacionalidad`, `estudios`, `correo`.

**Implementation:** Delegates directly to `Abogado::getAllAbogados()` with no pre-processing or post-processing of data.

**Usage Example:**

```

```

Sources: [controllers/abogadosCtrl.php L5-L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L7)

 [models/abogadosModel.php L5-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L5-L15)

### obtenerAbogadoPorId()

Retrieves a single lawyer record by its unique identifier.

**Method Signature:**

```

```

**Location:** [controllers/abogadosCtrl.php L9-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L9-L11)

**Parameters:**

| Name | Type | Description |
| --- | --- | --- |
| `$id` | integer | Primary key value (`id_abogado`) of the lawyer to retrieve |

**Return Value:** Associative array representing the lawyer record if found, `null` if no matching record exists.

**Implementation:** Delegates directly to `Abogado::getAbogadoById($id)` with parameter pass-through.

**Usage Example:**

```

```

Sources: [controllers/abogadosCtrl.php L9-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L9-L11)

 [models/abogadosModel.php L17-L25](https://github.com/GroveLive/abogado/blob/8bfc71d0/models/abogadosModel.php#L17-L25)

## Delegation Pattern

The controller implements a pure delegation pattern where each controller method performs a single operation: calling the corresponding static method on the model class.

```

```

**Diagram: Method Call Delegation Flow**

**Pattern Characteristics:**

* No data transformation occurs in the controller layer
* No error handling or validation logic
* Return values are passed through without modification
* Controller methods are instance methods calling static model methods

**Design Trade-offs:**

| Aspect | Current Implementation | Alternative Approach |
| --- | --- | --- |
| Method Type | Instance methods | Static methods (could eliminate need for instantiation) |
| State | Stateless (no properties) | Could maintain connection/config state |
| Return Values | Direct pass-through | Could wrap in response objects or add metadata |
| Error Handling | None (relies on model/DB layer) | Could catch exceptions and provide view-friendly errors |

Sources: [controllers/abogadosCtrl.php L5-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L11)

## Usage in Views

The controller is instantiated directly by both view files using the `new` operator.

### Home View Integration

[views/home.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/home.php)

 instantiates the controller to retrieve all lawyers for the listing page:

```

```

The view instantiates a new controller instance for each page request, calls `obtenerAbogados()` to retrieve all lawyer records, and iterates over the returned array to render the lawyer grid.

### Profile View Integration

[views/abogadosView.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/views/abogadosView.php)

 instantiates the controller to retrieve a specific lawyer:

```

```

The view instantiates a new controller instance, calls `obtenerAbogadoPorId()` with the URL parameter `id`, and conditionally renders the profile or an error message based on whether a lawyer record was found.

Sources: [controllers/abogadosCtrl.php L1-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L15)

## Architectural Role

The controller layer serves several architectural purposes within the MVC pattern:

```

```

**Diagram: Controller Layer Positioning in Architecture**

**Architectural Benefits:**

1. **Decoupling:** Views depend on the controller interface, not the model implementation. If model method signatures change, only the controller needs updating.
2. **Single Point of Change:** Future requirements like caching, logging, or authorization could be added to controller methods without modifying views or models.
3. **Consistent Interface:** Both views use the same controller interface, promoting code consistency.
4. **Testability:** Controller methods can be unit tested independently by mocking the model layer.

**Current Limitations:**

* **No Abstraction Value:** Since methods are pure pass-throughs, the controller adds minimal architectural value beyond potential future extension points.
* **No Shared Logic:** Each view instantiates its own controller instance rather than sharing a single instance, though this is acceptable given the stateless design.
* **No Error Context:** Controller methods don't add context about which operation failed, making debugging harder for views.

Sources: [controllers/abogadosCtrl.php L1-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L15)

## Extension Points

While the current implementation is minimal, the controller layer provides natural extension points for future enhancements:

| Enhancement | Implementation Location | Purpose |
| --- | --- | --- |
| **Request Validation** | Before delegation calls | Validate `$id` parameter is positive integer |
| **Response Caching** | Around delegation calls | Cache `getAllAbogados()` results to reduce DB queries |
| **Logging** | Before/after delegation | Log data access operations for audit trails |
| **Authorization** | Before delegation calls | Check user permissions before allowing data access |
| **Data Transformation** | After model returns | Transform model data to view-specific DTOs |
| **Error Handling** | Try-catch around delegation | Catch database exceptions and return error objects |

None of these enhancements are currently implemented, preserving the controller's role as a thin delegation layer.

Sources: [controllers/abogadosCtrl.php L5-L11](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L5-L11)

## File Structure Summary

| Element | Value |
| --- | --- |
| **File Path** | `controllers/abogadosCtrl.php` |
| **Lines of Code** | 15 (including PHP tags and whitespace) |
| **Class Name** | `AbogadosController` |
| **Public Methods** | 2 (`obtenerAbogados`, `obtenerAbogadoPorId`) |
| **Dependencies** | 1 (`../models/abogadosModel.php`) |
| **Instantiation Sites** | 2 (`views/home.php`, `views/abogadosView.php`) |

The entire controller implementation fits in a single small file with no configuration, state management, or helper methods.

Sources: [controllers/abogadosCtrl.php L1-L15](https://github.com/GroveLive/abogado/blob/8bfc71d0/controllers/abogadosCtrl.php#L1-L15)