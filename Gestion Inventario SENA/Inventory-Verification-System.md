# Inventory Verification System

> **Relevant source files**
> * [client/lib/presentation/screens/environment/environment_overview_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart)
> * [client/lib/presentation/screens/environment/manage_schedules_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/manage_schedules_screen.dart)
> * [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart)
> * [client/lib/presentation/screens/inventory/inventory_check_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart)
> * [server/app/routers/inventory_checks.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py)

## Purpose and Scope

The Inventory Verification System is the core workflow feature of the SENA Inventory Management System, responsible for conducting periodic inventory checks to verify the condition, location, and completeness of equipment across educational environments. This system implements a three-stage approval workflow (student → instructor → supervisor) with automatic status transitions, real-time totals calculation, and comprehensive audit trails.

This document covers the complete verification workflow, including the client UI components, backend API logic, data models, status management, and integration with schedules and notifications. For information about viewing completed verification history and statistics, see [Reporting & Analytics](/axchisan/GestionInventarioSENA/9-reporting-and-analytics). For details about the underlying inventory item management, see [Inventory Management](/axchisan/GestionInventarioSENA/6-inventory-management).

---

## System Overview

The inventory verification system enables users to conduct scheduled checks of inventory items within an environment, recording the condition of items (good, damaged, missing) and the cleanliness/organization state of the physical space. The system enforces a hierarchical approval chain where each role validates specific aspects before the verification can be marked complete.

### Key Components

| Component | Location | Purpose |
| --- | --- | --- |
| **InventoryCheckScreen** | `client/lib/presentation/screens/inventory/inventory_check_screen.dart` | Main UI for conducting verifications |
| **inventory_checks router** | `server/app/routers/inventory_checks.py` | Backend API handling all verification operations |
| **InventoryCheck model** | Database via SQLAlchemy ORM | Stores verification records with approval timestamps |
| **calculate_verification_totals** | `server/app/routers/inventory_checks.py:49-79` | Calculates items_good, items_damaged, items_missing |
| **VerificationByScheduleRequest** | `server/app/routers/inventory_checks.py:39-47` | Request schema for creating/updating verifications |

**Sources:**

* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:1-1086`
* `server/app/routers/inventory_checks.py:1-656`
* High-level system architecture diagrams

---

## Verification Workflow Architecture

```

```

**Sources:**

* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:17-159`
* `server/app/routers/inventory_checks.py:153-327`
* `server/app/routers/inventory_checks.py:329-430`

---

## Three-Stage Approval Process

The verification workflow implements a strict three-stage approval chain. Each stage validates different aspects of the inventory check and can identify issues that require resolution.

### Stage 1: Student Verification

Students initiate verifications by selecting a schedule/shift and conducting the physical inventory count. They record:

* Items found in good condition
* Items that are damaged
* Items that are missing
* General cleaning notes

The system automatically calculates verification totals by calling `calculate_verification_totals()` which queries all `InventoryItem` records for the environment and aggregates their `quantity_damaged` and `quantity_missing` fields.

**Code Flow:**

1. Student selects schedule via dropdown: [`inventory_check_screen.dart L1011-L1119](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L1011-L1119)
2. Student saves verification via dialog: [`inventory_check_screen.dart L812-L880](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L812-L880)
3. Client sends POST to `/api/inventory-checks/by-schedule`: [`inventory_check_screen.dart L836-L851](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L836-L851)
4. Backend creates `InventoryCheck` with status `student_pending`: [`inventory_checks.py L247-L280](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L247-L280)
5. Status transitions to `issues` if damaged/missing items detected, otherwise `instructor_review`: [`inventory_checks.py L294-L297](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L294-L297)

### Stage 2: Instructor Confirmation

Instructors review student-initiated verifications and validate:

* Cleanliness of the environment (`is_clean` boolean)
* Organization of the environment (`is_organized` boolean)
* Inventory completeness (`inventory_complete` boolean)
* Additional instructor comments

Instructors can only confirm checks in `instructor_review` status. Upon confirmation, the system sets `instructor_confirmed_at` timestamp and transitions status to either `issues` or `supervisor_review`.

**Code Flow:**

1. Instructor views pending checks: [`inventory_check_screen.dart L919-L946](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L919-L946)
2. Instructor confirms via PUT to `/api/inventory-checks/{check_id}/confirm`: [`inventory_checks.py L329-L430](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L329-L430)
3. Backend validates instructor role: [`inventory_checks.py L336-L337](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L336-L337)
4. Backend updates status based on verification results: [`inventory_checks.py L363-L366](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L363-L366)
5. Notifications sent to supervisors: [`inventory_checks.py L394-L404](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L394-L404)

### Stage 3: Supervisor Final Approval

Supervisors perform final validation and can:

* Take over instructor responsibilities if instructor hasn't confirmed
* Complete all verification steps in one action
* Approve or reject the verification
* Add final supervisor comments

Supervisors have elevated privileges and can complete verifications even if earlier stages weren't properly completed by setting all required IDs and timestamps.

**Code Flow:**

1. Supervisor reviews via supervisor approval endpoint: [`inventory_checks.py L478-L553](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L478-L553)
2. Backend validates supervisor role: [`inventory_checks.py L486-L487](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L486-L487)
3. Backend can act as instructor if needed: [`inventory_checks.py L369-L372](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L369-L372)
4. Final status set to `complete` or `rejected`: [`inventory_checks.py L509-L512](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L509-L512)
5. Notifications sent to student and instructor: [`inventory_checks.py L524-L544](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L524-L544)

**Sources:**

* `server/app/routers/inventory_checks.py:153-327`
* `server/app/routers/inventory_checks.py:329-430`
* `server/app/routers/inventory_checks.py:478-553`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:812-898`

---

## Status State Machine

The verification workflow implements a deterministic state machine that governs status transitions based on role actions and data validation results.

```

```

### Status Definitions

| Status | Description | Can Transition To | Triggered By |
| --- | --- | --- | --- |
| `student_pending` | Initial state after student creation | `issues`, `instructor_review` | Student creates check via POST |
| `instructor_review` | Awaiting instructor confirmation | `issues`, `supervisor_review` | Auto-transition or instructor initiates |
| `supervisor_review` | Awaiting supervisor approval | `complete`, `issues`, `rejected` | Instructor confirms successfully |
| `issues` | Problems identified requiring resolution | `supervisor_review` | Damaged/missing items OR failed validation |
| `complete` | Verification approved by all stages | Terminal state | Supervisor approves with all items good |
| `rejected` | Verification rejected by supervisor | Terminal state | Supervisor explicitly rejects |

### Status Determination Logic

The backend automatically determines the appropriate status based on multiple factors:

1. **Verification Totals:** If `items_damaged > 0` or `items_missing > 0`, status is forced to `issues` regardless of other factors: [`inventory_checks.py L203-L204](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L203-L204)
2. **Inventory Completeness:** If `inventory_complete` is `false`, status transitions to `issues`: [`inventory_checks.py L363-L364](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L363-L364)
3. **Role-Based Progression:** Different roles can move the verification through different stages: * Students can only create checks in `student_pending`: [`inventory_checks.py L268-L269](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L268-L269) * Instructors can move to `instructor_review` or `supervisor_review`: [`inventory_checks.py L242-L243](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L242-L243) * Supervisors can complete all stages and reach `complete`: [`inventory_checks.py L244-L245](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L244-L245)

**Sources:**

* `server/app/routers/inventory_checks.py:133-137`
* `server/app/routers/inventory_checks.py:203-206`
* `server/app/routers/inventory_checks.py:222-225`
* `server/app/routers/inventory_checks.py:284-298`
* `server/app/routers/inventory_checks.py:363-366`
* `server/app/routers/inventory_checks.py:381-385`

---

## Inventory Check Screen UI

The `InventoryCheckScreen` widget provides the complete user interface for conducting inventory verifications. It is a stateful widget with extensive state management for schedules, items, checks, and UI filters.

### Screen Structure

```

```

### Key State Variables

| State Variable | Type | Purpose |
| --- | --- | --- |
| `_selectedScheduleId` | `String?` | Currently selected schedule for verification |
| `_selectedDate` | `DateTime` | Date for which verification is being conducted |
| `_currentScheduleCheck` | `Map<String, dynamic>?` | Existing verification record for selected schedule/date |
| `_currentStatus` | `String?` | Current status of the verification |
| `_items` | `List<dynamic>` | All inventory items in the environment |
| `_checks` | `List<dynamic>` | All verification records filtered by date/schedule |
| `_pendingChecks` | `List<dynamic>` | Checks awaiting current user's action |

**State Management Methods:**

* **`_fetchData()`**: Loads environment, items, schedules, checks, and notifications from API: [`inventory_check_screen.dart L86-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L86-L159)
* **`_fetchScheduleCheck()`**: Retrieves specific check for selected schedule and date: [`inventory_check_screen.dart L183-L206](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L183-L206)
* **`_fetchScheduleStats()`**: Retrieves statistics for selected schedule: [`inventory_check_screen.dart L160-L182](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L160-L182)
* **`_saveCheck()`**: Submits verification data to backend: [`inventory_check_screen.dart L812-L880](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L812-L880)

### Verification Dialog

When users tap the "Verificar Inventario" button, a comprehensive dialog appears collecting verification data:

**Dialog Fields:**

1. **Cleanliness Checkbox** (`is_clean`): Whether the environment is clean
2. **Organization Checkbox** (`is_organized`): Whether items are organized
3. **Inventory Complete Checkbox** (`inventory_complete`): Whether all items were verified
4. **Cleaning Notes TextField**: Notes about cleanliness issues
5. **Comments TextField**: General verification comments

The dialog validates that a schedule is selected before allowing submission: [`inventory_check_screen.dart L819-L824](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L819-L824)

**Sources:**

* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:17-159`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:882-1086`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:812-880`

---

## Verification Data Model

The `InventoryCheck` model is the central database entity for storing verification records. It maintains comprehensive audit trails with timestamps for each approval stage.

### Core Fields

| Field | Type | Description | Required |
| --- | --- | --- | --- |
| `id` | UUID | Primary key | Yes |
| `environment_id` | UUID | Foreign key to Environment | Yes |
| `schedule_id` | UUID | Foreign key to Schedule | Yes |
| `student_id` | UUID | Foreign key to User (student) | Yes |
| `instructor_id` | UUID | Foreign key to User (instructor) | No |
| `supervisor_id` | UUID | Foreign key to User (supervisor) | No |
| `check_date` | Date | Date of verification | Yes |
| `check_time` | Time | Time verification started | Yes |
| `status` | String | Current workflow status | Yes |

### Verification Metrics

| Field | Type | Description |
| --- | --- | --- |
| `total_items` | Integer | Total inventory items in environment |
| `items_good` | Integer | Count of items in good condition |
| `items_damaged` | Integer | Count of damaged items |
| `items_missing` | Integer | Count of missing items |

### Validation Fields

| Field | Type | Description |
| --- | --- | --- |
| `is_clean` | Boolean | Environment cleanliness status |
| `is_organized` | Boolean | Environment organization status |
| `inventory_complete` | Boolean | Whether all items were verified |

### Comments and Notes

| Field | Type | Description |
| --- | --- | --- |
| `cleaning_notes` | String | Notes about cleaning/organization |
| `comments` | String | General student comments |
| `instructor_comments` | String | Instructor observations |
| `supervisor_comments` | String | Supervisor final notes |

### Confirmation Timestamps

| Field | Type | Description | Set By |
| --- | --- | --- | --- |
| `student_confirmed_at` | DateTime | When student completed initial check | `get_colombia_time()` |
| `instructor_confirmed_at` | DateTime | When instructor confirmed | `get_colombia_time()` |
| `supervisor_confirmed_at` | DateTime | When supervisor approved | `get_colombia_time()` |
| `created_at` | DateTime | Record creation timestamp | Auto |
| `updated_at` | DateTime | Last update timestamp | Auto |

All timestamps use Colombian timezone via `pytz.timezone('America/Bogota')`: [`inventory_checks.py L23-L27](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L23-L27)

**Sources:**

* `server/app/routers/inventory_checks.py:1-20` (imports and model references)
* Diagram 3 from high-level system architecture (ER diagram)

---

## Backend API Endpoints

The `inventory_checks` router exposes several endpoints for managing the verification workflow.

### POST /api/inventory-checks/by-schedule

Creates or updates a verification record for a specific schedule and date. This endpoint supports all three roles and intelligently determines the appropriate action based on whether a check already exists.

**Request Schema:** `VerificationByScheduleRequest`

```

```

**Logic Flow:**

1. Validate role is student, instructor, or supervisor: [`inventory_checks.py L160-L161](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L160-L161)
2. Verify environment and schedule exist: [`inventory_checks.py L163-L169](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L163-L169)
3. Check for existing verification: [`inventory_checks.py L174-L178](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L174-L178)
4. If exists, update based on role: [`inventory_checks.py L180-L229](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L180-L229)
5. If new, create with role-appropriate fields: [`inventory_checks.py L232-L327](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L232-L327)
6. Calculate verification totals: [`inventory_checks.py L49-L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L49-L79)
7. Generate notifications: [`inventory_checks.py L302-L325](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L302-L325)

**Response:**

```

```

### PUT /api/inventory-checks/{check_id}/confirm

Allows instructors and supervisors to confirm/approve a verification. This endpoint enforces role-based validation rules.

**Request Schema:** `InventoryCheckInstructorConfirmRequest`

* Contains: `is_clean`, `is_organized`, `inventory_complete`, `comments`

**Logic:**

1. Validate instructor or supervisor role: [`inventory_checks.py L336-L337](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L336-L337)
2. Fetch and validate check exists: [`inventory_checks.py L339-L341](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L339-L341)
3. Recalculate totals from current inventory state: [`inventory_checks.py L343-L347](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L343-L347)
4. Apply role-specific updates: * Instructors: Set instructor fields and move to `supervisor_review`: [`inventory_checks.py L351-L366](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L351-L366) * Supervisors: Can complete instructor step, set final status: [`inventory_checks.py L368-L385](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L368-L385)
5. Generate role-appropriate notifications: [`inventory_checks.py L391-L428](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L391-L428)

**Response:** Full `InventoryCheckResponse` object

### GET /api/inventory-checks/

Retrieves inventory checks with extensive filtering and role-based access control.

**Query Parameters:**

* `environment_id` (UUID): Filter by environment
* `date` (YYYY-MM-DD): Filter by specific date
* `shift` (morning/afternoon/night): Filter by time of day
* `status` (String): Filter by verification status

**Role-Based Filtering:**

* Students: Only see their own checks: [`inventory_checks.py L619-L620](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L619-L620)
* Instructors: See their checks + checks awaiting instructor review: [`inventory_checks.py L621-L625](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L621-L625)
* Supervisors: See their checks + checks awaiting supervisor action: [`inventory_checks.py L626-L630](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L626-L630)

**Response:** Array of `InventoryCheckResponse` objects

### GET /api/inventory-checks/schedule-stats

Returns aggregated statistics for a specific schedule and date combination.

**Query Parameters:**

* `environment_id` (UUID)
* `schedule_id` (UUID)
* `date` (YYYY-MM-DD)

**Response:**

```

```

If no check exists, returns totals calculated from current inventory with status `not_started`: [`inventory_checks.py L454-L463](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L454-L463)

**Sources:**

* `server/app/routers/inventory_checks.py:153-327`
* `server/app/routers/inventory_checks.py:329-430`
* `server/app/routers/inventory_checks.py:580-633`
* `server/app/routers/inventory_checks.py:433-476`

---

## Verification Totals Calculation

The `calculate_verification_totals()` function is the critical business logic that aggregates inventory item quantities into verification metrics. This function is called every time a verification is created or updated to ensure totals reflect the current inventory state.

### Function Logic

```

```

### Implementation Details

**Source Code:** [`inventory_checks.py L49-L79](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L49-L79)

The function performs the following steps:

1. **Query All Items:** Retrieves all `InventoryItem` records for the specified environment
2. **Count Total Items:** Uses `len(inventory_items)` to count distinct inventory items (not individual units)
3. **Aggregate Quantities:** For each item: * Extracts `quantity_damaged` (defaults to 0 if None) * Extracts `quantity_missing` (defaults to 0 if None) * Extracts `quantity` (defaults to 1 if None for individual items) * Calculates available: `quantity - quantity_damaged - quantity_missing`
4. **Accumulate Totals:** Sums up the quantities across all items
5. **Return Dictionary:** Returns structured data with four metrics

**Key Behavior:**

* **Total Items Count:** Represents the number of distinct inventory records, not the sum of all quantities
* **Quantity-Based Metrics:** `items_good`, `items_damaged`, and `items_missing` represent actual unit counts, which can exceed `total_items` for group items
* **Current State Calculation:** Always recalculates from the current database state, not historical snapshots

**Usage Points:**

* Called in POST `/api/inventory-checks/`: [`inventory_checks.py L110](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L110-L110)
* Called in POST `/api/inventory-checks/by-schedule`: [`inventory_checks.py L181](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L181-L181)  [`inventory_checks.py L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L233-L233)
* Called in PUT `/api/inventory-checks/{check_id}/confirm`: [`inventory_checks.py L343](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L343-L343)
* Called in PUT `/api/inventory-checks/{check_id}/supervisor-approve`: [`inventory_checks.py L493](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L493-L493)
* Called in GET `/api/inventory-checks/schedule-stats` (when no check exists): [`inventory_checks.py L455](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L455-L455)

**Sources:**

* `server/app/routers/inventory_checks.py:49-79`
* `server/app/routers/inventory_checks.py:110-137`

---

## Schedule Integration

Inventory verifications are tightly coupled with the `Schedule` entity, which defines the time blocks and programs for which verifications must be conducted. Each verification must be associated with a schedule, creating a one-to-many relationship.

### Schedule Selection Flow

```

```

### Schedule Model Fields (Relevant)

| Field | Type | Purpose in Verification |
| --- | --- | --- |
| `id` | UUID | Links check to schedule via `schedule_id` FK |
| `environment_id` | UUID | Determines which items to verify |
| `instructor_id` | UUID | Identifies instructor responsible for review |
| `program` | String | Displayed in schedule selector dropdown |
| `start_time` / `end_time` | Time | Used for shift filtering (morning/afternoon/night) |
| `student_count` | Integer | Contextual information for verification scope |

### Uniqueness Constraint

The system enforces a uniqueness constraint on verifications: only one `InventoryCheck` can exist per combination of `(environment_id, schedule_id, check_date)`. This prevents duplicate verifications for the same schedule on the same day.

**Enforcement Logic:**

1. Before creating a new check, query existing: [`inventory_checks.py L174-L178](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L174-L178)
2. If found, update existing check instead of creating new: [`inventory_checks.py L180-L229](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L180-L229)
3. If creating via legacy endpoint, raise HTTP 400 error: [`inventory_checks.py L102-L108](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L102-L108)

### Schedule-Based Filtering

The UI provides schedule-based filtering in two locations:

1. **Schedule Dropdown:** Filters all checks to show only those for selected schedule: [`inventory_check_screen.dart L123-L127](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L123-L127)
2. **Pending Checks Badge:** Shows checks awaiting current user's action filtered by schedule
3. **History Modal:** Can filter historical checks by schedule

**Sources:**

* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:108-118`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:183-206`
* `server/app/routers/inventory_checks.py:174-178`
* `server/app/routers/inventory_checks.py:635-655`

---

## Notification System Integration

The verification workflow generates notifications at key transition points to alert stakeholders of actions requiring their attention. Notifications are created asynchronously and stored in the `Notification` model.

### Notification Generation Points

| Trigger | Recipients | Notification Type | Message |
| --- | --- | --- | --- |
| Student creates check | Instructor assigned to schedule | `verification_pending` | "Nueva Verificación Pendiente" |
| Instructor confirms check | All supervisors in environment | `verification_pending` | "Verificación Lista para Supervisión" |
| Supervisor completes/rejects | Student + Instructor | `verification_update` | Status-dependent message |

### Notification Creation Logic

**Student Creates Check:**

```

```

Source: [`inventory_checks.py L302-L311](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L302-L311)

**Instructor Confirms Check:**

```

```

Source: [`inventory_checks.py L313-L324](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L313-L324)

**Supervisor Completes Check:**

```

```

Source: [`inventory_checks.py L407-L416](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L407-L416)

### UI Notification Display

The `InventoryCheckScreen` displays notifications via the `NotificationBadge` widget in the app bar: [`inventory_check_screen.dart L905-L911](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L905-L911)

The notification badge shows an unread count and opens a modal when tapped, allowing users to see all pending notifications related to inventory verifications.

**Sources:**

* `server/app/routers/inventory_checks.py:140-149`
* `server/app/routers/inventory_checks.py:302-325`
* `server/app/routers/inventory_checks.py:391-428`
* `server/app/routers/inventory_checks.py:524-544`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:905-911`

---

## Role-Based Access Control

The verification system implements strict role-based access control (RBAC) to ensure users can only perform actions appropriate to their role in the approval workflow.

### Permission Matrix

| Action | Student | Instructor | Supervisor |
| --- | --- | --- | --- |
| Create initial verification | ✓ | ✓ | ✓ |
| Update own pending verification | ✓ | — | — |
| Confirm verification (instructor step) | — | ✓ | ✓ |
| Approve/reject verification (final) | — | — | ✓ |
| View own verifications | ✓ | ✓ | ✓ |
| View pending verifications awaiting action | ✓ | ✓ | ✓ |
| View all verifications in environment | — | — | ✓ |

### Client-Side Permission Checks

The UI dynamically enables/disables features based on role and verification state:

**Verify Button Visibility:**

```

```

Source: [`inventory_check_screen.dart L887-L896](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L887-L896)

**Pending Checks Filtering:**

```

```

Source: [`inventory_check_screen.dart L129-L130](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L129-L130)

### Backend Role Validation

All API endpoints validate user roles before allowing operations:

**Endpoint-Level Validation:**

```

```

Source: [`inventory_checks.py L87-L88](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L87-L88)

 [`inventory_checks.py L160-L161](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L160-L161)

**Operation-Specific Validation:**

```

```

Source: [`inventory_checks.py L336-L337](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L336-L337)

**Query Filtering by Role:**

```

```

Source: [`inventory_checks.py L619-L630](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L619-L630)

### Supervisor Privilege Elevation

Supervisors have special privileges that allow them to:

1. **Take Over Instructor Duties:** If an instructor hasn't confirmed, supervisor can complete that step: [`inventory_checks.py L369-L372](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L369-L372)
2. **Complete All Steps:** Supervisor can create a verification and immediately mark it complete: [`inventory_checks.py L244-L246](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L244-L246)
3. **Override Status:** Supervisor approval can transition from any non-terminal status to `complete` or `rejected`: [`inventory_checks.py L509-L512](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_checks.py#L509-L512)

**Sources:**

* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:887-896`
* `server/app/routers/inventory_checks.py:87-88`
* `server/app/routers/inventory_checks.py:160-161`
* `server/app/routers/inventory_checks.py:336-337`
* `server/app/routers/inventory_checks.py:619-630`
* `server/app/routers/inventory_checks.py:369-372`

---

## Item Verification UI Components

The inventory check screen displays filtered lists of inventory items with real-time status indicators and detailed information dialogs.

### Item Filtering System

The screen provides three-dimensional filtering:

1. **Text Search:** Filters by item name or ID using `_searchController`: [`inventory_check_screen.dart L209-L210](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L209-L210)
2. **Category Filter:** Dropdown with translated category names: [`inventory_check_screen.dart L211](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L211-L211)
3. **Status Filter:** Dropdown with translated status values: [`inventory_check_screen.dart L212](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L212-L212)

**Filter Implementation:**

```

```

Source: [`inventory_check_screen.dart L207-L215](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L207-L215)

### Item Details Dialog

When users tap an item, a comprehensive details dialog appears showing:

* **Basic Information:** Name, internal code, serial number, category, type, status
* **Quantity Information:** Total, damaged, missing, expected, found
* **Additional Details:** Brand, model, description, notes
* **Important Dates:** Purchase date, warranty expiry, last/next maintenance

The dialog uses a structured layout with color-coded sections and the `_buildDetailRow()` helper method for consistent formatting: [`inventory_check_screen.dart L515-L534](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L515-L534)

### Status Color Coding

The system uses consistent color mapping for status visualization:

```

```

Source: [`inventory_check_screen.dart L268-L293](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`inventory_check_screen.dart#L268-L293)

**Sources:**

* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:207-215`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:539-693`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:268-293`
* `client/lib/presentation/screens/inventory/inventory_check_screen.dart:515-534`

---

## Integration with Environment Overview

The verification system integrates with the environment overview screen to display inventory statistics and verification history. The `EnvironmentOverviewScreen` provides a comprehensive view of the environment's inventory and verification status.

### Statistics Display

The environment header shows aggregated totals calculated from current inventory state:

* **Total Items:** `_calculateTotalEnvironmentItems()` - Sum of all item quantities
* **Available Items:** `_calculateAvailableEnvironmentItems()` - Total minus damaged and missing
* **Damaged Items:** `_calculateDamagedEnvironmentItems()` - Sum of `quantity_damaged`
* **Missing Items:** `_calculateMissingEnvironmentItems()` - Sum of `quantity_missing`

**Calculation Methods:**

```

```

Source: [`environment_overview_screen.dart L479-L505](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`environment_overview_screen.dart#L479-L505)

### Completed Checks Metric

The overview screen calculates the number of completed verifications:

```

```

Source: [`environment_overview_screen.dart L507-L509](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/`environment_overview_screen.dart#L507-L509)

This metric appears in the statistics tab, providing visibility into verification compliance rates.

**Sources:**

* `client/lib/presentation/screens/environment/environment_overview_screen.dart:479-509`
* `client/lib/presentation/screens/environment/environment_overview_screen.dart:350-477`
* Diagram 2 from high-level system architecture (Role-Based Access Control and User Flows)