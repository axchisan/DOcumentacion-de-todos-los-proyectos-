# Backend API Design

> **Relevant source files**
> * [controllers/AdminController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php)
> * [controllers/AuthController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php)
> * [controllers/CheckinController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php)
> * [controllers/CheckoutController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php)
> * [controllers/ClienteController.php](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/ClienteController.php)

## Purpose and Scope

This document describes the RESTful API patterns, action-based routing system, JSON response formats, and error handling strategies implemented across all backend PHP controllers in the Hotel Benedetti system. It covers the technical architecture of how frontend JavaScript communicates with backend controllers, how requests are routed to specific actions, and how responses are structured.

For information about the specific business logic implemented in each controller, see [Controller Architecture](/GroveLive/hotelBenedetti/6.1-controller-architecture). For details on validation rules and business constraints, see [Data Validation and Business Rules](/GroveLive/hotelBenedetti/6.2-data-validation-and-business-rules). For frontend-backend communication patterns from the client perspective, see [Client-Server Communication](/GroveLive/hotelBenedetti/7-client-server-communication).

---

## API Architecture Overview

All backend controllers in the Hotel Benedetti system follow a unified architectural pattern based on action-based routing and JSON communication. Controllers act as API endpoints that receive HTTP requests with an `action` parameter, dispatch to corresponding methods, and return JSON-formatted responses.

### Controller Initialization Pattern

Each controller follows a consistent initialization sequence:

```

```

**Sources:** [controllers/AdminController.php L1-L16](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L16)

 [controllers/CheckinController.php L1-L13](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L13)

 [controllers/CheckoutController.php L1-L15](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L15)

 [controllers/AuthController.php L1-L28](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L28)

---

## Action-Based Routing System

The API uses an action-based routing mechanism where all requests to a controller include an `action` parameter that determines which operation to execute. This pattern eliminates the need for separate URL endpoints for each operation.

### Routing Flow

```

```

**Sources:** [controllers/AdminController.php L17-L328](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L328)

 [controllers/CheckinController.php L15-L66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L15-L66)

 [controllers/CheckoutController.php L17-L105](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L17-L105)

### Action Mapping Tables

#### AdminController Actions

| HTTP Method | Action Parameter | Purpose | Lines |
| --- | --- | --- | --- |
| POST | `addEmployee` | Create new employee (recepcionista/mucama) | [20-103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/20-103) |
| POST | `updateEmployee` | Update existing employee data | [104-186](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/104-186) |
| POST | `deleteEmployee` | Delete employee record | [187-190](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/187-190) |
| POST | `addRoom` | Create new room | [191-216](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/191-216) |
| POST | `updateRoom` | Update room details | [217-245](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/217-245) |
| POST | `deleteRoom` | Delete room record | [246-249](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/246-249) |
| GET | `getDashboardStats` | Retrieve dashboard statistics | [259-302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/259-302) |
| GET | `getEmployees` | Retrieve all employees | [303-305](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/303-305) |
| GET | `getEmployeeById` | Retrieve single employee | [306-309](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/306-309) |
| GET | `getRooms` | Retrieve all rooms | [310-312](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/310-312) |
| GET | `getRoomById` | Retrieve single room | [313-316](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/313-316) |

**Sources:** [controllers/AdminController.php L17-L322](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L322)

#### CheckinController Actions

| HTTP Method | Action Parameter | Purpose | Lines |
| --- | --- | --- | --- |
| POST | `checkin` | Process guest check-in | [18-54](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/18-54) |

**Sources:** [controllers/CheckinController.php L15-L60](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L15-L60)

#### CheckoutController Actions

| HTTP Method | Action Parameter | Purpose | Lines |
| --- | --- | --- | --- |
| POST | `checkout` | Process guest checkout and payment | [20-93](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/20-93) |

**Sources:** [controllers/CheckoutController.php L17-L99](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L17-L99)

#### AuthController Actions

| HTTP Method | Action Parameter | Purpose | Lines |
| --- | --- | --- | --- |
| POST | `login` | Authenticate user | [41-42](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/41-42) |
| POST | `register` | Register new client | [43-45](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/43-45) |

**Sources:** [controllers/AuthController.php L30-L51](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L30-L51)

---

## Request and Response Format

### Request Structure

All API requests use standard HTTP methods with `application/x-www-form-urlencoded` data:

```

```

**Sources:** [controllers/AdminController.php L17-L18](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L17-L18)

 [controllers/AdminController.php L256-L257](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L256-L257)

### Response Structure

All controllers return JSON responses with a consistent structure:

```

```

**Standard Response Fields:**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `success` | boolean | Yes | Indicates operation success/failure |
| `message` | string | Yes | Human-readable status message |
| `data` | object/array | Conditional | Returned data for successful operations |
| `stats` | object | Conditional | Dashboard statistics (getDashboardStats only) |

**Sources:** [controllers/AdminController.php L30-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L30-L34)

 [controllers/AdminController.php L82-L85](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L82-L85)

 [controllers/AdminController.php L292-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L292-L302)

---

## Input Validation and Sanitization

All controllers implement multi-layer validation before processing requests:

### Validation Flow Diagram

```

```

**Sources:** [controllers/AdminController.php L21-L27](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L21-L27)

 [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 [controllers/AdminController.php L38-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L61)

### Input Sanitization Pattern

```

```

**Example from AdminController:**

```

```

**Sources:** [controllers/AdminController.php L23](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L23-L23)

 [controllers/AdminController.php L108](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L108-L108)

### Role Validation Example

The `addEmployee` and `updateEmployee` actions enforce that only `recepcionista` and `mucama` roles can be created through the admin interface:

```

```

**Sources:** [controllers/AdminController.php L29-L35](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L29-L35)

 [controllers/AdminController.php L115-L121](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L115-L121)

---

## Duplicate Validation Pattern

Controllers implement database-level duplicate checks before creating or updating records:

### Duplicate Check Flow

```

```

**Sources:** [controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48)

 [controllers/AdminController.php L51-L61](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L51-L61)

 [controllers/AdminController.php L124-L135](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L124-L135)

### Email Duplicate Check Example

```

```

**Sources:** [controllers/AdminController.php L38-L48](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L38-L48)

### Update-Specific Duplicate Check

For updates, the duplicate check excludes the current record:

```

```

**Sources:** [controllers/AdminController.php L124-L128](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L124-L128)

---

## Error Handling Strategy

All controllers implement consistent error handling using try-catch blocks and standardized error responses.

### Error Handling Architecture

```

```

**Sources:** [controllers/AdminController.php L10-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L10-L334)

 [controllers/CheckinController.php L9-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L9-L72)

### Error Response Examples

**Generic Server Error:**

```

```

**Business Rule Violation:**

```

```

**Validation Error:**

```

```

**Invalid Action:**

```

```

**Method Not Allowed:**

```

```

**Sources:** [controllers/AdminController.php L329-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L329-L334)

 [controllers/AdminController.php L251-L254](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L251-L254)

 [controllers/AdminController.php L324-L327](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L324-L327)

---

## Model Interaction Pattern

Controllers act as intermediaries between HTTP requests and Model classes, delegating data persistence operations to models:

### Controller-Model Interaction Flow

```

```

**Sources:** [controllers/AdminController.php L64-L103](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L64-L103)

### Property Assignment Pattern

Controllers populate model properties before calling model methods:

```

```

**Sources:** [controllers/AdminController.php L64-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L64-L72)

### Model Method Return Values

All model methods return an associative array with at minimum a `success` key:

```

```

**Sources:** [controllers/AdminController.php L73-L85](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L73-L85)

---

## Transaction-Like Operations

Some operations require multiple database actions. Controllers implement rollback logic when compound operations fail:

### Employee Creation with Rollback

```

```

**Rollback Implementation:**

```

```

**Sources:** [controllers/AdminController.php L72-L97](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L72-L97)

---

## Business Rule Enforcement in Controllers

Controllers enforce business rules before delegating to models:

### Reservation State Validation

The `CheckinController` verifies that reservations are in `confirmada` state before allowing check-in:

```

```

**Sources:** [controllers/CheckinController.php L22-L34](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L22-L34)

### Idempotency Checks

Controllers prevent duplicate operations by checking if records already exist:

**Check-in Idempotency:**

```

```

**Sources:** [controllers/CheckinController.php L37-L47](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L37-L47)

### Checkout Prerequisites

The `CheckoutController` enforces that check-in must occur before checkout:

```

```

**Sources:** [controllers/CheckoutController.php L40-L50](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L40-L50)

---

## Side Effect Orchestration

Controllers orchestrate side effects like status updates and notification creation:

### Checkout Side Effects Flow

```

```

**Implementation:**

```

```

**Sources:** [controllers/CheckoutController.php L69-L92](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L69-L92)

---

## Dashboard Statistics Aggregation

The `AdminController` provides a specialized endpoint for dashboard statistics that performs multiple aggregate queries:

### Statistics Query Pattern

```

```

**Statistics Response Structure:**

```

```

**Sources:** [controllers/AdminController.php L259-L302](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L259-L302)

---

## Password Hashing

All controllers that handle password creation or updates use bcrypt hashing via `password_hash()`:

```

```

For updates, passwords are only hashed if provided:

```

```

**Sources:** [controllers/AdminController.php L26](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L26-L26)

 [controllers/AdminController.php L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L111-L111)

---

## Output Buffer Management in AuthController

The `AuthController` implements special output buffer handling to prevent premature output that would corrupt JSON responses:

```

```

**Sources:** [controllers/AuthController.php L7-L20](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L7-L20)

 [controllers/AuthController.php L60-L66](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L60-L66)

---

## Summary of API Patterns

| Pattern | Implementation | Purpose |
| --- | --- | --- |
| **Action Routing** | `if-elseif-else` on `$_POST['action']` or `$_GET['action']` | Route requests to specific handlers |
| **JSON Responses** | `json_encode(['success' => bool, 'message' => string])` | Consistent API response format |
| **Input Sanitization** | `htmlspecialchars(strip_tags($input))` | Prevent XSS attacks |
| **Duplicate Validation** | `SELECT ... WHERE field = :value` queries before insert/update | Enforce uniqueness constraints |
| **Business Rules** | Query-based validation before operations | Enforce workflow constraints |
| **Error Handling** | `try-catch` blocks with JSON error responses | Graceful error recovery |
| **Model Delegation** | Set model properties, call model methods | Separate concerns between routing and data |
| **Password Security** | `password_hash($password, PASSWORD_BCRYPT)` | Secure password storage |
| **Idempotency** | Check for existing records before creating | Prevent duplicate operations |

**Sources:** [controllers/AdminController.php L1-L334](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AdminController.php#L1-L334)

 [controllers/CheckinController.php L1-L72](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckinController.php#L1-L72)

 [controllers/CheckoutController.php L1-L111](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/CheckoutController.php#L1-L111)

 [controllers/AuthController.php L1-L80](https://github.com/GroveLive/hotelBenedetti/blob/ebdd0186/controllers/AuthController.php#L1-L80)