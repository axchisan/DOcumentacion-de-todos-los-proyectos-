# System Architecture

> **Relevant source files**
> * [assets/js/admin.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js)
> * [assets/js/config.js](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/config.js)
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)
> * [controllers/AuthController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php)

## Purpose and Scope

This document explains the three-tier Model-View-Controller (MVC) architecture of the Hotel Benedetti management system, describing how the presentation layer (CSS/JS), application layer (PHP controllers), and data layer (Models/Database) are structured and interact. This page focuses on the architectural separation of concerns and communication patterns between layers.

For detailed information about specific user interfaces and their styling, see [Frontend Layer](/GroveLive/hotelBenedetti/2.1-frontend-layer). For in-depth coverage of controller logic and business rules, see [Backend Controllers](/GroveLive/hotelBenedetti/2.2-backend-controllers). For database schema and entity relationships, see [Data Models and Database Schema](/GroveLive/hotelBenedetti/2.3-data-models-and-database-schema).

---

## Architectural Overview

The Hotel Benedetti system follows a three-tier architecture that cleanly separates concerns across presentation, application, and data layers. The presentation layer consists of HTML pages with associated CSS stylesheets and JavaScript modules. The application layer contains PHP controllers that process HTTP requests, enforce business logic, and coordinate database operations. The data layer comprises PHP model classes that encapsulate database entities and provide CRUD operations.

### Three-Tier Structure

```

```

**Sources:** [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

---

## Presentation Layer

The presentation layer consists of role-specific HTML pages, each paired with dedicated CSS stylesheets and JavaScript modules. All JavaScript files use modern ES6+ syntax with module imports and asynchronous patterns.

### Frontend File Structure

| User Role | HTML Page | CSS Stylesheet | JavaScript Module |
| --- | --- | --- | --- |
| Administrator | `admin.html` | `StyleAdmin.css` | `admin.js` |
| Cliente (Guest) | `cliente.html` | `StyleCliente.css` | `cliente.js` |
| Recepcionista (Receptionist) | `recepcionista.html` | `StyleRecepcionista.css` | `script_recepcionista.js` |
| Mucama (Maid) | `mucama.html` | `StyleMucama.css` | `mucama.js` |
| Authentication | `login.html`, `index.html` | `StyleLogin.css`, `StyleIndex.css` | `login.js` |

Each JavaScript module follows a consistent pattern:

1. Import configuration from `config.js` ([assets/js/config.js L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/config.js#L1-L1) )
2. Attach event listeners on `DOMContentLoaded` ([assets/js/admin.js L8](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L8) )
3. Define functions that make asynchronous `fetch()` requests to controllers
4. Handle JSON responses and update the DOM dynamically
5. Display user feedback via SweetAlert2 library

### Configuration Module

The `config.js` module centralizes the API base URL:

```

```

This constant is imported by all JavaScript modules to construct controller endpoints ([assets/js/admin.js L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L1)

).

**Sources:** [assets/js/config.js L1](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/config.js#L1-L1)

 [assets/js/admin.js L1-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L35)

---

## Application Layer (Controllers)

The application layer consists of PHP controllers that serve as the primary API endpoints. Controllers receive HTTP requests, validate input, invoke model methods, and return JSON responses. All controllers follow an action-based routing pattern where the `action` parameter determines which operation to execute.

### Controller Routing Pattern

```

```

**Sources:** [controllers/AdminController.php L17-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L103)

 [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266)

### Controller Action Dispatching

Controllers use conditional logic to route requests to specific handlers based on the `action` parameter:

**GET Requests** (Query operations):

* `getDashboardStats` - Retrieve room and employee statistics ([controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302) )
* `getEmployees` - Fetch all employee records ([controllers/AdminController.php L303-L305](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L303-L305) )
* `getEmployeeById` - Retrieve single employee details ([controllers/AdminController.php L306-L309](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L306-L309) )
* `getRooms` - Fetch all room records ([controllers/AdminController.php L310-L312](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L310-L312) )
* `getRoomById` - Retrieve single room details ([controllers/AdminController.php L313-L316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L313-L316) )

**POST Requests** (Mutation operations):

* `addEmployee` - Create new employee record ([controllers/AdminController.php L20-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L20-L103) )
* `updateEmployee` - Modify existing employee ([controllers/AdminController.php L104-L186](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L104-L186) )
* `deleteEmployee` - Remove employee record ([controllers/AdminController.php L187-L190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L187-L190) )
* `addRoom` - Create new room record ([controllers/AdminController.php L191-L216](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L191-L216) )
* `updateRoom` - Modify existing room ([controllers/AdminController.php L217-L245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L217-L245) )
* `deleteRoom` - Remove room record ([controllers/AdminController.php L246-L249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L246-L249) )

### Authentication Flow

The `AuthController.php` handles login and registration through the `UsuarioController`:

```

```

The authentication controller initializes sessions ([controllers/AuthController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L2-L2)

), establishes database connections ([controllers/AuthController.php L23-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L23-L27)

), and delegates authentication logic to `UsuarioController`.

**Sources:** [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)

 [controllers/AdminController.php L1-L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L16)

---

## Data Layer

The data layer consists of PHP model classes that encapsulate database entities and provide methods for CRUD operations. All models follow a consistent pattern of accepting a PDO connection object in their constructor and exposing public methods that return associative arrays with `success`, `message`, and optional `data` keys.

### Model-Database Connection Pattern

```

```

### Database Connection Management

The `Database` class ([ddbb/database.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/ddbb/database.php)

) provides centralized connection management. Controllers instantiate it and call `getConnection()` to obtain a PDO object:

```

```

This connection is then passed to model constructors ([controllers/AdminController.php L11-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L11-L15)

), ensuring all models share the same database connection within a request lifecycle.

### Model CRUD Interface

All models implement standard CRUD methods that return structured arrays:

| Method | Return Structure | Example |
| --- | --- | --- |
| `create()` | `["success" => bool, "id" => int, "message" => string]` | Employee creation ([controllers/AdminController.php L72-L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L72-L79) <br> ) |
| `update()` | `["success" => bool, "message" => string]` | Employee update ([controllers/AdminController.php L163-L168](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L163-L168) <br> ) |
| `delete($id)` | `["success" => bool, "message" => string]` | Employee deletion ([controllers/AdminController.php L189](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L189-L189) <br> ) |
| `getById($id)` | `["success" => bool, "user/room" => array, "message" => string]` | Fetch by ID ([controllers/AdminController.php L308-L309](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L308-L309) <br> ) |
| `getAll()` | `["success" => bool, "rooms/employees" => array, "message" => string]` | List all records ([controllers/AdminController.php L311](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L311-L311) <br> ) |

**Sources:** [controllers/AdminController.php L1-L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L16)

 [controllers/AdminController.php L63-L79](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L63-L79)

---

## Communication Patterns

The system uses asynchronous HTTP communication exclusively. No full page reloads occur after initial authentication—all data exchanges happen via AJAX using the Fetch API.

### AJAX Request Flow

```

```

### Request Format

Frontend requests use `URLSearchParams` to encode form data:

```

```

This is sent as the POST body ([assets/js/admin.js L220-L230](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L220-L230)

 [assets/js/admin.js L234](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L234-L234)

).

### Response Format

All controllers return JSON responses with a consistent structure:

```

```

Success responses include a message and optional data object. Error responses set `success: false` and provide an error message ([controllers/AdminController.php L82-L85](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L82-L85)

 [controllers/AdminController.php L93-L96](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L93-L96)

).

**Sources:** [assets/js/admin.js L208-L266](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L208-L266)

 [controllers/AdminController.php L17-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L103)

---

## Request-Response Cycle Example

The following diagram traces a complete request-response cycle for the "Load Dashboard Statistics" operation, demonstrating how all three architectural layers collaborate:

```

```

This cycle demonstrates:

1. **Event-driven architecture**: UI interactions trigger JavaScript event handlers
2. **Asynchronous communication**: `fetch()` makes non-blocking HTTP requests
3. **Stateless controllers**: Each request is self-contained with action parameter
4. **Direct database queries**: Controllers execute SQL via PDO prepared statements
5. **JSON data serialization**: Structured data format for client-server exchange
6. **Dynamic DOM manipulation**: JavaScript updates UI elements without page reload
7. **Visualization layer**: Chart.js integration for dashboard analytics

**Sources:** [assets/js/admin.js L8-L140](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L8-L140)

 [controllers/AdminController.php L256-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L256-L302)

---

## Session Management and Routing

All controllers begin with `session_start()` ([controllers/AdminController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L2-L2)

 [controllers/AuthController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L2-L2)

) to maintain user state across requests. After successful authentication, the `AuthController` sets session variables:

* `$_SESSION['user_id']` - User's database ID
* `$_SESSION['nombre']` - User's first name
* `$_SESSION['apellido']` - User's last name
* `$_SESSION['rol']` - User's role (cliente, administrador, recepcionista, mucama)

These session variables determine which interface the user can access. Frontend pages check the session at page load and redirect unauthorized users to the login page.

**Sources:** [controllers/AuthController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L2-L2)

 [controllers/AdminController.php L2](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L2-L2)

---

## Error Handling and Validation

Controllers implement multi-layered validation:

### Input Sanitization

All POST parameters are sanitized using `htmlspecialchars()` and `strip_tags()` to prevent XSS attacks ([controllers/AdminController.php L21-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L21-L27)

).

### Business Rule Validation

Controllers enforce domain constraints before database operations:

* Email uniqueness checks ([controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48) )
* DNI (national ID) uniqueness checks ([controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61) )
* Role validation (only 'recepcionista' and 'mucama' allowed for employee creation) ([controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35) )
* Room number uniqueness checks ([controllers/AdminController.php L197-L207](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L197-L207) )

### Error Response Pattern

When validation fails, controllers immediately return JSON error responses and exit:

```

```

This pattern prevents further execution and ensures consistent error messaging ([controllers/AdminController.php L43-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L43-L48)

).

### Exception Handling

All controllers wrap logic in try-catch blocks to handle database exceptions and return user-friendly error messages ([controllers/AdminController.php L329-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L329-L334)

 [controllers/AuthController.php L67-L77](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L67-L77)

).

**Sources:** [controllers/AdminController.php L21-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L21-L61)

 [controllers/AdminController.php L329-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L329-L334)

---

## Architectural Benefits

The three-tier separation provides:

1. **Modularity**: Each layer can be modified independently without affecting others
2. **Testability**: Controllers and models can be unit tested in isolation
3. **Security**: Centralized input validation and SQL injection prevention via prepared statements
4. **Maintainability**: Role-specific interfaces share common architectural patterns
5. **Scalability**: AJAX communication eliminates full page reloads, reducing server load
6. **Reusability**: Models encapsulate database logic used by multiple controllers

The consistent action-based routing pattern across all controllers creates a predictable API surface that frontend developers can easily understand and extend.

**Sources:** [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

 [assets/js/admin.js L1-L552](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/assets/js/admin.js#L1-L552)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)