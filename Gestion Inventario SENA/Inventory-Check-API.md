# Inventory Check API

> **Relevant source files**
> * [client/lib/presentation/screens/environment/environment_overview_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart)
> * [client/lib/presentation/screens/environment/manage_schedules_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/manage_schedules_screen.dart)
> * [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart)
> * [client/lib/presentation/screens/inventory/inventory_check_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart)
> * [server/app/routers/inventory_checks.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py)

## Purpose and Scope

This document describes the backend API endpoints for the inventory verification system. The inventory checks router (`inventory_checks.py`) provides RESTful endpoints for creating, updating, and querying inventory verification records, which track the three-stage approval workflow (student → instructor → supervisor).

For information about the client-side UI for conducting checks, see [Inventory Check Screen](/axchisan/GestionInventarioSENA/5.1-inventory-check-screen). For details on the complete verification workflow and status transitions, see [Verification Workflow](/axchisan/GestionInventarioSENA/5.2-verification-workflow).

**Sources:** [server/app/routers/inventory_checks.py L1-L20](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L1-L20)

---

## Router Architecture

The inventory checks router is implemented in FastAPI and mounted at the `/api/inventory-checks/` path. It handles all CRUD operations for inventory verification records and integrates with the Schedule, Environment, InventoryItem, User, and Notification models.

```

```

**Sources:** [server/app/routers/inventory_checks.py L1-L21](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L1-L21)

 [server/app/routers/inventory_checks.py L81-L152](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L81-L152)

---

## Core API Endpoints

The router exposes eight primary endpoints for managing inventory verification workflows:

| HTTP Method | Endpoint | Purpose | Required Role |
| --- | --- | --- | --- |
| POST | `/api/inventory-checks/` | Create new inventory check | student, instructor, supervisor |
| POST | `/api/inventory-checks/by-schedule` | Create/update check by schedule | student, instructor, supervisor |
| PUT | `/api/inventory-checks/{check_id}/confirm` | Confirm check (instructor/supervisor) | instructor, supervisor |
| GET | `/api/inventory-checks/schedule-stats` | Get statistics for schedule/date | Any authenticated |
| PUT | `/api/inventory-checks/{check_id}/supervisor-approve` | Supervisor approval | supervisor |
| PUT | `/api/inventory-checks/{check_id}/assign-role` | Assign verification to role | supervisor, admin |
| GET | `/api/inventory-checks/` | List inventory checks with filters | Any authenticated |
| GET | `/api/inventory-checks/by-schedule` | Get checks for specific schedule/date | Any authenticated |

**Sources:** [server/app/routers/inventory_checks.py L81-L655](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L81-L655)

---

## Verification Totals Calculation

The `calculate_verification_totals()` function is a core utility that computes inventory statistics by querying all `InventoryItem` records in an environment and aggregating their quantity fields.

```

```

The function returns a dictionary containing:

* `total_items`: Count of distinct inventory items
* `items_good`: Sum of available quantities across all items
* `items_damaged`: Sum of damaged quantities
* `items_missing`: Sum of missing quantities

This calculation is invoked every time a check is created or updated to ensure real-time accuracy based on current inventory state.

**Sources:** [server/app/routers/inventory_checks.py L49-L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L49-L79)

---

## Creating Inventory Checks

### Standard Creation Endpoint

The `POST /api/inventory-checks/` endpoint creates a new inventory check record with explicit student, environment, and schedule associations.

**Request Schema:** `InventoryCheckCreateRequest`

* `environment_id`: UUID of the environment being checked
* `student_id`: UUID of the student performing the check
* `schedule_id`: UUID of the associated schedule/shift
* `cleaning_notes`: Optional notes about cleanliness

**Process Flow:**

```

```

**Status Determination Logic:**

1. If `items_damaged > 0` or `items_missing > 0`, status is set to `"issues"`
2. Otherwise, status is set to `"instructor_review"`

The `student_confirmed_at` timestamp is set to Colombian timezone (`get_colombia_time()`).

**Sources:** [server/app/routers/inventory_checks.py L81-L151](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L81-L151)

---

## Schedule-Based Verification

The `POST /api/inventory-checks/by-schedule` endpoint provides a more flexible approach that allows any role (student, instructor, or supervisor) to create or update a verification for a specific schedule and date.

### Request Schema: VerificationByScheduleRequest

```

```

### Behavior by Role

```

```

### Key Logic Highlights

**For Existing Checks (Update Path):**

* **Student:** Updates `cleaning_notes` and `student_confirmed_at` if not already confirmed
* **Instructor:** Sets classroom assessment fields (`is_clean`, `is_organized`, `inventory_complete`), updates status to `"issues"` or `"supervisor_review"` based on totals
* **Supervisor:** Can complete any missing steps (acting as instructor if needed), sets final status to `"complete"` or `"issues"`

**For New Checks (Create Path):**

* Initial status depends on creating role: `"student_pending"`, `"instructor_review"`, or `"supervisor_review"`
* Higher-privilege roles can skip earlier workflow steps by setting confirmation timestamps for all prior roles

**Sources:** [server/app/routers/inventory_checks.py L153-L327](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L153-L327)

---

## Confirmation Endpoint

The `PUT /api/inventory-checks/{check_id}/confirm` endpoint is used by instructors and supervisors to officially confirm a verification and advance it through the workflow.

### Instructor Confirmation Flow

```

```

### Supervisor Confirmation Flow

When a supervisor confirms:

1. If no instructor has confirmed yet, supervisor can act as instructor (sets `instructor_id` and `instructor_confirmed_at`)
2. Sets `supervisor_id`, `supervisor_comments`, and `supervisor_confirmed_at`
3. Determines final status: * `"complete"` if `inventory_complete=True` and no damaged/missing items * `"issues"` otherwise
4. Creates notifications for both the student and instructor (if different from supervisor)

**Sources:** [server/app/routers/inventory_checks.py L329-L430](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L329-L430)

---

## Status Workflow State Machine

The inventory check status field follows a defined state machine with role-based transitions:

```

```

**Status Descriptions:**

| Status | Meaning | Next Steps |
| --- | --- | --- |
| `student_pending` | Student initiated, awaiting data entry | Student completes item verification |
| `instructor_review` | Ready for instructor review | Instructor confirms cleanliness/organization |
| `supervisor_review` | Ready for supervisor approval | Supervisor performs final review |
| `complete` | Verification fully approved | Terminal state |
| `issues` | Problems identified | Requires attention/correction |
| `rejected` | Supervisor rejected the verification | Terminal state |

**Sources:** [server/app/routers/inventory_checks.py L120-L137](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L120-L137)

 [server/app/routers/inventory_checks.py L189-L226](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L189-L226)

 [server/app/routers/inventory_checks.py L363-L385](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L363-L385)

---

## Supervisor Approval Endpoint

The `PUT /api/inventory-checks/{check_id}/supervisor-approve` endpoint provides a dedicated path for supervisors to approve or reject verifications with comments.

**Request Body:**

```

```

**Process:**

1. Validates current user has `"supervisor"` role
2. Loads the inventory check by `check_id`
3. Recalculates verification totals
4. Sets `supervisor_id`, `supervisor_comments`, and `supervisor_confirmed_at`
5. Updates status to `"complete"` (if approved) or `"rejected"` (if not approved)
6. Creates a `SupervisorReview` record for audit trail
7. Sends notifications to student and instructor

**Sources:** [server/app/routers/inventory_checks.py L478-L553](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L478-L553)

---

## Querying Inventory Checks

### List Checks: GET /api/inventory-checks/

This endpoint supports comprehensive filtering for retrieving inventory checks:

**Query Parameters:**

| Parameter | Type | Description |
| --- | --- | --- |
| `environment_id` | UUID | Filter by environment (defaults to user's assigned environment) |
| `date` | string | Filter by check date (format: YYYY-MM-DD) |
| `shift` | string | Filter by time of day: `morning`, `afternoon`, `night` |
| `status` | string | Filter by status value |

**Role-Based Filtering:**

```

```

**Shift Time Ranges:**

* `morning`: 07:00:00 - 12:00:00
* `afternoon`: 13:00:00 - 18:00:00
* `night`: 18:00:00 - 22:00:00

**Sources:** [server/app/routers/inventory_checks.py L580-L633](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L580-L633)

---

### Get Checks by Schedule: GET /api/inventory-checks/by-schedule

This endpoint retrieves all inventory checks for a specific schedule and date combination.

**Query Parameters (all required):**

* `environment_id`: UUID
* `schedule_id`: UUID
* `date`: string (format: YYYY-MM-DD)

**Returns:** List of `InventoryCheckResponse` objects matching the criteria (typically 0 or 1 check per schedule per day).

**Sources:** [server/app/routers/inventory_checks.py L635-L655](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L635-L655)

---

## Schedule Statistics Endpoint

The `GET /api/inventory-checks/schedule-stats` endpoint provides real-time statistics for a schedule/date combination, useful for dashboard displays.

**Query Parameters:**

* `environment_id`: UUID
* `schedule_id`: UUID
* `date`: string (YYYY-MM-DD)

**Response Schema:**

```

```

**Example Response:**

```

```

**Sources:** [server/app/routers/inventory_checks.py L433-L476](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L433-L476)

---

## Timezone Handling

All timestamps in the inventory checks system use Colombian timezone (`America/Bogota`) to ensure consistency across distributed clients.

**Utility Functions:**

```

```

These functions are used when setting:

* `check_time`: Time when check was created
* `student_confirmed_at`: Student confirmation timestamp
* `instructor_confirmed_at`: Instructor confirmation timestamp
* `supervisor_confirmed_at`: Supervisor confirmation timestamp

**Sources:** [server/app/routers/inventory_checks.py L23-L37](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L23-L37)

---

## Notification Integration

The inventory checks router integrates tightly with the notification system to alert users at workflow transition points.

### Notification Trigger Points

```

```

### Notification Creation Pattern

The router creates `Notification` records directly in the database:

```

```

**Notification Priority:** All inventory check notifications use `priority="medium"` by default.

**Sources:** [server/app/routers/inventory_checks.py L140-L149](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L140-L149)

 [server/app/routers/inventory_checks.py L302-L325](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L302-L325)

 [server/app/routers/inventory_checks.py L391-L427](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L391-L427)

 [server/app/routers/inventory_checks.py L524-L544](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L524-L544)

---

## Role-Based Authorization

Each endpoint enforces role-based access control by checking `current_user.role` against allowed roles:

| Endpoint | Allowed Roles | Authorization Logic |
| --- | --- | --- |
| `POST /` | student, instructor, supervisor | Must be one of these roles |
| `POST /by-schedule` | student, instructor, supervisor | Must be one of these roles |
| `PUT /{check_id}/confirm` | instructor, supervisor | Only these roles can confirm checks |
| `GET /schedule-stats` | Any authenticated | No specific role requirement |
| `PUT /{check_id}/supervisor-approve` | supervisor | Must be supervisor |
| `PUT /{check_id}/assign-role` | supervisor, admin | Must be supervisor or admin |
| `GET /` | Any authenticated | Role determines filtering scope |
| `GET /by-schedule` | Any authenticated | No specific role requirement |

**Authorization Pattern:**

```

```

**Sources:** [server/app/routers/inventory_checks.py L87-L88](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L87-L88)

 [server/app/routers/inventory_checks.py L160-L161](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L160-L161)

 [server/app/routers/inventory_checks.py L336-L337](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L336-L337)

 [server/app/routers/inventory_checks.py L486-L487](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L486-L487)

 [server/app/routers/inventory_checks.py L564-L565](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L564-L565)

---

## Client Integration Example

The Flutter client interacts with these endpoints primarily through the `InventoryCheckScreen` and uses the `ApiService` for HTTP communication.

### Creating a Check via Schedule

```

```

**Code Reference:** The client implementation in `_saveCheck()` method constructs the request and handles responses.

**Sources:** [client/lib/presentation/screens/inventory/inventory_check_screen.dart L812-L880](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart#L812-L880)

---

## Error Handling

The API endpoints return standard HTTP error codes with descriptive messages:

| Status Code | Scenario | Example Message |
| --- | --- | --- |
| 400 | Invalid date format | "Formato de fecha inválido. Se espera YYYY-MM-DD" |
| 400 | Duplicate check for today | "Ya se realizó verificación hoy para este turno" |
| 400 | Instructor already confirmed | "Ya confirmada por otro instructor" |
| 403 | Unauthorized role | "Rol no autorizado" |
| 403 | Invalid permission | "Solo instructores y supervisores pueden confirmar verificaciones" |
| 404 | Environment not found | "Ambiente no encontrado" |
| 404 | Student not found | "Estudiante no encontrado" |
| 404 | Schedule not found | "Horario no encontrado" |
| 404 | Check not found | "Verificación no encontrada" |

**Error Response Pattern:**

```

```

**Sources:** [server/app/routers/inventory_checks.py L88-L92](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L88-L92)

 [server/app/routers/inventory_checks.py L96-L100](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L96-L100)

 [server/app/routers/inventory_checks.py L107-L108](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L107-L108)

 [server/app/routers/inventory_checks.py L164-L169](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L164-L169)

 [server/app/routers/inventory_checks.py L337-L341](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L337-L341)

 [server/app/routers/inventory_checks.py L352-L353](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L352-L353)

 [server/app/routers/inventory_checks.py L486-L487](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L486-L487)

 [server/app/routers/inventory_checks.py L564-L565](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L564-L565)

 [server/app/routers/inventory_checks.py L603-L604](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L603-L604)

 [server/app/routers/inventory_checks.py L647-L648](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L647-L648)

---

## Database Transaction Patterns

The router follows consistent patterns for database operations:

1. **Query for validation:** Check that referenced entities (Environment, Schedule, User) exist
2. **Check business rules:** Verify no duplicate checks exist for the same schedule/date
3. **Perform calculation:** Call `calculate_verification_totals()` for current inventory state
4. **Create/update records:** Add or modify the InventoryCheck entity
5. **Set status:** Apply workflow logic to determine appropriate status
6. **Create notifications:** Generate notifications for relevant users
7. **Commit transaction:** Persist all changes atomically
8. **Refresh entities:** Reload the entity to get updated relationships
9. **Return response:** Map to response schema and return to client

**Example Pattern:**

```

```

**Sources:** [server/app/routers/inventory_checks.py L81-L151](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L81-L151)

 [server/app/routers/inventory_checks.py L153-L327](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L153-L327)

---

## Summary Table: Endpoint Quick Reference

| Endpoint | Method | Purpose | Key Parameters | Returns |
| --- | --- | --- | --- | --- |
| `/api/inventory-checks/` | POST | Create check with explicit student | `environment_id`, `student_id`, `schedule_id` | `check_id` |
| `/api/inventory-checks/by-schedule` | POST | Create/update check by schedule | `environment_id`, `schedule_id`, verification fields | `check_id`, `action` |
| `/api/inventory-checks/{check_id}/confirm` | PUT | Instructor/supervisor confirmation | `is_clean`, `is_organized`, `inventory_complete`, `comments` | `InventoryCheckResponse` |
| `/api/inventory-checks/schedule-stats` | GET | Get statistics for schedule/date | `environment_id`, `schedule_id`, `date` | Statistics dict |
| `/api/inventory-checks/{check_id}/supervisor-approve` | PUT | Supervisor approval/rejection | `approved`, `comments` | Status message |
| `/api/inventory-checks/{check_id}/assign-role` | PUT | Assign verification to role | `target_role` | Status message |
| `/api/inventory-checks/` | GET | List checks with filters | `environment_id`, `date`, `shift`, `status` | `List[InventoryCheckResponse]` |
| `/api/inventory-checks/by-schedule` | GET | Get checks for schedule/date | `environment_id`, `schedule_id`, `date` | `List[InventoryCheckResponse]` |

**Sources:** [server/app/routers/inventory_checks.py L81-L655](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py#L81-L655)