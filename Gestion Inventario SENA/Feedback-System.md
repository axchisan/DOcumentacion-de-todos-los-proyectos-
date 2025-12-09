# Feedback System

> **Relevant source files**
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)

## Purpose and Scope

The Feedback System provides a mechanism for users to submit feedback, bug reports, feature requests, and suggestions about the SENA Inventory Management System. This system enables users to rate the application, categorize their feedback, and optionally provide detailed information for issue resolution. The system supports priority classification and includes an admin response workflow for feedback management.

This page documents the feedback collection, storage, categorization, and administrative management capabilities. For information about the notification system that alerts users of feedback status changes, see [Notification System](/axchisan/GestionInventarioSENA/11-notification-system). For audit logging of feedback-related actions, see [Audit & Compliance](/axchisan/GestionInventarioSENA/10-audit-and-compliance).

## System Overview

The Feedback System implements a bidirectional communication channel between users and administrators. Users submit categorized feedback with optional ratings and device information, while administrators (with `admin_general` role) can review, prioritize, update status, and provide responses to feedback submissions.

```

```

**Sources:** [server/app/routers/feedback.py L1-L262](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L1-L262)

 [client/lib/presentation/screens/feedback/feedback_form_screen.dart L1-L686](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L1-L686)

## Feedback Data Model

The `Feedback` model stores user-submitted feedback with comprehensive metadata for tracking and resolution.

### Core Fields

| Field | Type | Description |
| --- | --- | --- |
| `id` | UUID | Primary key |
| `user_id` | UUID | Foreign key to User table |
| `type` | String | Category of feedback (bug, suggestion, feature, etc.) |
| `category` | String (Optional) | Human-readable category label |
| `title` | String | Brief summary of feedback |
| `description` | String | Detailed feedback description |
| `steps_to_reproduce` | String (Optional) | Reproduction steps for bugs |
| `priority` | String | Priority level: low, medium, high |
| `rating` | Integer (Optional) | User rating of application (1-5) |
| `status` | String | Current status of feedback |
| `admin_response` | String (Optional) | Administrator's response |
| `include_device_info` | Boolean | Whether device information was included |
| `include_logs` | Boolean | Whether logs were included |
| `allow_follow_up` | Boolean | Whether user allows follow-up contact |
| `created_at` | DateTime | Submission timestamp |
| `updated_at` | DateTime | Last modification timestamp |

**Sources:** [server/app/routers/feedback.py L16-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L16-L48)

## Feedback Categories

The system supports six distinct feedback categories, each with specific use cases and visual representation.

### Category Types

```

```

| Category | ID | Icon | Description | Use Case |
| --- | --- | --- | --- | --- |
| Sugerencia | `suggestion` | lightbulb_outline | Ideas for improvement | Feature enhancements |
| Error/Bug | `bug` | bug_report | Technical problems | Bug reports |
| Nueva Funcionalidad | `feature` | add_circle_outline | New feature requests | Feature requests |
| Usabilidad | `usability` | accessibility | UX/UI feedback | User experience improvements |
| Rendimiento | `performance` | speed | Performance issues | Speed/optimization concerns |
| Otro | `other` | more_horiz | General comments | Miscellaneous feedback |

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L30-L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L30-L73)

 [server/app/routers/feedback.py L63-L68](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L63-L68)

## Priority Levels

Feedback submissions can be assigned priority levels that help administrators triage and respond appropriately.

### Priority Classification

| Priority | Value | Description |
| --- | --- | --- |
| Low | `low` | Non-urgent feedback or minor suggestions |
| Medium | `medium` | Standard priority (default) |
| High | `high` | Critical issues requiring immediate attention |

The priority field is validated at submission time and can be updated by administrators during review.

**Sources:** [server/app/routers/feedback.py L22-L76](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L22-L76)

## Status Workflow

Feedback submissions progress through a defined status lifecycle managed by administrators.

```

```

### Status Definitions

| Status | Description | Actor |
| --- | --- | --- |
| `submitted` | Initial state after user submission | System (automatic) |
| `reviewed` | Admin has reviewed the feedback | admin_general |
| `in_progress` | Admin is actively addressing the feedback | admin_general |
| `completed` | Feedback has been resolved/implemented | admin_general |
| `rejected` | Feedback will not be addressed | admin_general |

Status transitions are restricted to users with the `admin_general` role through the `PUT /api/feedback/{feedback_id}` endpoint.

**Sources:** [server/app/routers/feedback.py L95-L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L95-L233)

 [client/lib/presentation/screens/feedback/feedback_form_screen.dart L129-L144](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L129-L144)

## Backend API Endpoints

The feedback router provides a complete REST API for feedback management at `/api/feedback`.

### Endpoint Overview

```

```

**Sources:** [server/app/routers/feedback.py L14-L262](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L14-L262)

### POST / - Create Feedback

Creates a new feedback submission.

**Request Schema** (`FeedbackCreate`):

* `type` (string, required): One of bug, suggestion, feature, compliment, complaint, other
* `category` (string, optional): Human-readable category
* `title` (string, required): Brief summary
* `description` (string, required): Detailed description
* `steps_to_reproduce` (string, optional): Reproduction steps for bugs
* `priority` (string): low, medium (default), high
* `rating` (integer, optional): 1-5 rating of application
* `include_device_info` (boolean): Whether to include device information
* `include_logs` (boolean): Whether to include logs
* `allow_follow_up` (boolean): Whether to allow follow-up contact

**Validation:**

* Type must be in valid types list [feedback.py L63-L68](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L63-L68)
* Priority must be low, medium, or high [feedback.py L71-L76](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L71-L76)
* Rating must be 1-5 if provided [feedback.py L79-L83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L79-L83)

**Response:** Returns `FeedbackResponse` with status `submitted` and HTTP 201.

**Sources:** [server/app/routers/feedback.py L54-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L54-L107)

### GET / - Get User Feedback

Retrieves feedback submissions for the authenticated user.

**Query Parameters:**

* `skip` (integer, default: 0): Pagination offset
* `limit` (integer, default: 10, max: 100): Number of results
* `type` (string, optional): Filter by feedback type
* `status` (string, optional): Filter by status

**Response:** List of `FeedbackResponse` ordered by `created_at` descending.

**Sources:** [server/app/routers/feedback.py L109-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L109-L134)

### GET /all - Get All Feedback (Admin)

Retrieves all feedback submissions across all users. Restricted to `admin_general` role.

**Query Parameters:**

* `skip` (integer, default: 0): Pagination offset
* `limit` (integer, default: 50, max: 200): Number of results
* `type` (string, optional): Filter by feedback type
* `status` (string, optional): Filter by status
* `priority` (string, optional): Filter by priority

**Ordering:** Results ordered by priority descending, then `created_at` descending.

**Authorization:** HTTP 403 if user is not `admin_general`.

**Sources:** [server/app/routers/feedback.py L136-L174](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L136-L174)

### GET /{feedback_id} - Get Specific Feedback

Retrieves a single feedback submission by ID.

**Authorization Rules:**

* Users can view their own feedback
* `admin_general` can view any feedback
* HTTP 403 for unauthorized access

**Sources:** [server/app/routers/feedback.py L176-L199](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L176-L199)

### PUT /{feedback_id} - Update Feedback (Admin)

Updates feedback status and admin response. Restricted to `admin_general` role.

**Request Schema** (`FeedbackUpdateRequest`):

* `status` (string, optional): New status
* `admin_response` (string, optional): Administrator's response
* `priority` (string, optional): Updated priority

**Side Effects:**

* Updates `updated_at` timestamp to current UTC time

**Sources:** [server/app/routers/feedback.py L201-L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L201-L233)

### DELETE /{feedback_id} - Delete Feedback

Deletes a feedback submission.

**Authorization Rules:**

* Users can delete their own feedback
* `admin_general` can delete any feedback

**Sources:** [server/app/routers/feedback.py L235-L261](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L235-L261)

## Client Interface

The `FeedbackFormScreen` provides a comprehensive UI for submitting and tracking feedback.

### Component Structure

```

```

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L8-L686](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L8-L686)

### Form Fields and Validation

| Field | Controller | Validation | Required |
| --- | --- | --- | --- |
| Category | `selectedCategory` | Must be one of 6 categories | Yes |
| Title | `_titleController` | Non-empty | Yes |
| Description | `_descriptionController` | Non-empty, min 10 characters | Yes |
| Rating | `selectedRating` | 1-5 stars | Yes (default: 5) |
| Email | `_emailController` | Valid email format | Optional |
| Anonymous | `isAnonymous` | Boolean flag | No |

**Validation Implementation:**

* Title: Checks for non-empty value [feedback_form_screen.dart L266-L271](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L266-L271)
* Description: Checks for non-empty and minimum 10 characters [feedback_form_screen.dart L286-L294](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L286-L294)
* Email: Validates email format with regex if provided [feedback_form_screen.dart L321-L328](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L321-L328)

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L258-L348](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L258-L348)

### Category Selection UI

The category selection interface displays six category chips with icons and color coding. Users select a category by tapping the corresponding chip.

**Visual States:**

* Selected: Colored background matching category color, bold text, thicker border
* Unselected: Grey background, normal text weight, thin border

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L396-L446](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L396-L446)

### Rating System

The rating system displays five interactive star icons. Users tap a star to set their rating from 1-5.

**Rating Labels:**

* 1 star: "Muy malo"
* 2 stars: "Malo"
* 3 stars: "Regular"
* 4 stars: "Bueno"
* 5 stars: "Excelente"

**Implementation:** [feedback_form_screen.dart L448-L477](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L448-L477)

 displays stars, [feedback_form_screen.dart L609-L624](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L609-L624)

 provides rating text.

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L448-L624](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L448-L624)

### Submission Flow

```

```

**Submission Process:**

1. Validate form using `_formKey.currentState!.validate()` [feedback_form_screen.dart L627](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L627-L627)
2. Set `isSubmitting = true` to show loading state [feedback_form_screen.dart L631](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L631-L631)
3. Build feedback data object with all fields [feedback_form_screen.dart L634-L644](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L634-L644)
4. POST to `feedbackEndpoint` via `ApiService` [feedback_form_screen.dart L646](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L646-L646)
5. On success: Clear form, reload recent feedback, show success message [feedback_form_screen.dart L648-L671](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L648-L671)
6. On error: Show error SnackBar [feedback_form_screen.dart L672-L684](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L672-L684)

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L626-L685](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L626-L685)

### Recent Feedback Display

The screen displays the user's five most recent feedback submissions below the form.

**Data Loading:**

* Loads on screen initialization [feedback_form_screen.dart L80](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L80-L80)
* Reloads after successful submission [feedback_form_screen.dart L670](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L670-L670)
* Fetches via `GET /api/feedback?limit=5` [feedback_form_screen.dart L83-L119](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback_form_screen.dart#L83-L119)

**Display Format:**
Each feedback item shows:

* Title (bold)
* Status badge with color coding
* Category and submission date
* Star rating visualization

**Status Color Mapping:**

* "Resuelto" (Completed): Green
* "En revisión" (Reviewed): Orange
* "Planificado" (In Progress): Blue
* Other: Grey

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L83-L607](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L83-L607)

## Admin Management Capabilities

Administrators with the `admin_general` role have exclusive access to feedback management features.

### Admin-Only Operations

| Operation | Endpoint | Capability |
| --- | --- | --- |
| View All Feedback | `GET /api/feedback/all` | See all users' feedback |
| Update Status | `PUT /api/feedback/{id}` | Change feedback status |
| Add Response | `PUT /api/feedback/{id}` | Provide admin response |
| Update Priority | `PUT /api/feedback/{id}` | Change priority level |
| Delete Any Feedback | `DELETE /api/feedback/{id}` | Delete any user's feedback |

### Permission Enforcement

```

```

**Authorization Implementation:**

* `GET /all`: Checks `current_user.role != "admin_general"` [feedback.py L148-L152](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L148-L152)
* `PUT /{id}`: Checks `current_user.role != "admin_general"` [feedback.py L210-L214](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L210-L214)
* Non-admin users attempting admin operations receive HTTP 403 Forbidden response

**Sources:** [server/app/routers/feedback.py L148-L214](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L148-L214)

### Response Workflow

When an administrator provides a response to user feedback:

1. Administrator updates feedback with `admin_response` field via `PUT /api/feedback/{id}`
2. Backend updates `updated_at` timestamp [feedback.py L229](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L229-L229)
3. Database commit persists changes [feedback.py L230](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L230-L230)
4. Updated feedback returned in response [feedback.py L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/feedback.py#L233-L233)

The system does not automatically notify users of admin responses; notification integration would need to be implemented separately through the Notification System (see [Notification System](/axchisan/GestionInventarioSENA/11-notification-system)).

**Sources:** [server/app/routers/feedback.py L201-L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L201-L233)

## Integration Points

### API Constants

The feedback endpoint is defined in the client's API constants:

```
feedbackEndpoint = "/api/feedback"
```

**Sources:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L6](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L6-L6)

### Authentication Integration

All feedback endpoints require authentication through the `get_current_user` dependency, which validates JWT tokens and retrieves the current user's information.

**Sources:** [server/app/routers/feedback.py L11-L239](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L11-L239)

### Audit Logging

Feedback-related API calls are automatically logged by the audit middleware, capturing:

* User who submitted/updated feedback
* Endpoint accessed
* Request parameters
* Response status

For details on audit logging, see [Audit & Compliance](/axchisan/GestionInventarioSENA/10-audit-and-compliance).