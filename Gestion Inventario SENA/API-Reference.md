# API Reference

> **Relevant source files**
> * [client/lib/core/constants/api_constants.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart)
> * [client/lib/core/services/profile_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/profile_service.dart)
> * [client/lib/presentation/screens/feedback/feedback_form_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart)
> * [server/app/main.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py)
> * [server/app/routers/auth.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py)
> * [server/app/routers/feedback.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py)
> * [server/app/routers/inventory.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py)
> * [server/app/routers/maintenance_requests.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py)
> * [server/app/routers/notifications.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py)
> * [server/app/schemas/user.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/user.py)
> * [server/app/services/auth_service.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/services/auth_service.py)

This document provides a comprehensive reference for all REST API endpoints in the SENA Inventory Management System. It covers request/response schemas, authentication requirements, role-based access control, and usage examples for each endpoint.

For information about the authentication and authorization mechanisms, see [Authentication & Authorization](/axchisan/GestionInventarioSENA/3-authentication-and-authorization). For details about data models and database schemas, see [Data Models Reference](/axchisan/GestionInventarioSENA/16-data-models-reference).

---

## Overview

The backend API is built with FastAPI and organized into 18 specialized routers, each handling a specific domain of functionality. All endpoints follow REST conventions and return JSON responses. The API enforces role-based access control through JWT token authentication.

**Base URL:** `https://senainventario.axchisan.com`

**API Prefix:** All endpoints are prefixed with `/api/`

### Router Organization

The API is divided into the following functional routers:

| Router | Prefix | Purpose | Tags |
| --- | --- | --- | --- |
| Authentication | `/api/auth` | User authentication and profile management | `auth` |
| Environments | `/api/environments` | Physical location management | `environments` |
| Inventory | `/api/inventory` | Inventory item CRUD operations | `inventory` |
| Inventory Checks | `/api/inventory-checks` | Verification workflow management | `inventory-checks` |
| QR Codes | `/api/qr` | QR code generation and linking | `qr` |
| Schedules | `/api/schedules` | Schedule management | N/A |
| Users | `/api/users` | User management and role assignment | `users` |
| Supervisor Reviews | `/api/supervisor-reviews` | Supervisor approval workflow | `supervisor-reviews` |
| Inventory Check Items | `/api/inventory-check-items` | Individual item verification | `inventory-check-items` |
| System Alerts | `/api/system-alerts` | Critical inventory alerts | `system-alerts` |
| Notifications | `/api/notifications` | Event-driven user notifications | `notifications` |
| Maintenance Requests | `/api/maintenance-requests` | Equipment repair tracking | `maintenance-requests` |
| Maintenance History | `/api/maintenance-history` | Maintenance activity log | `maintenance-history` |
| Statistics | `/api/stats` | Analytics and metrics | `stats` |
| Loans | `/api/loans` | Equipment borrowing system | `loans` |
| Alert Settings | `/api/alert-settings` | Alert threshold configuration | `alert-settings` |
| Reports | `/api/reports` | PDF/Excel report generation | `reports` |
| Audit Logs | `/api/audit-logs` | Compliance and action logging | `audit-logs` |
| Feedback | `/api/feedback` | User feedback collection | `feedback` |

**Sources:** [server/app/main.py L1-L44](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L1-L44)

 [client/lib/core/constants/api_constants.dart L1-L19](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart#L1-L19)

---

## API Architecture Diagram

```

```

**Sources:** [server/app/main.py L1-L44](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L1-L44)

---

## Authentication & Token Management

### Authentication Flow

```

```

**Sources:** [server/app/routers/auth.py L20-L22](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L20-L22)

 [server/app/services/auth_service.py L9-L55](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/services/auth_service.py#L9-L55)

### Authentication Endpoints

#### POST /api/auth/login

Authenticates a user and returns a JWT access token.

**Request Body:**

```

```

**Response (200 OK):**

```

```

**Error Responses:**

* `401 Unauthorized`: Invalid credentials
* `403 Forbidden`: Account inactive

**Implementation:** [server/app/routers/auth.py L20-L22](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L20-L22)

 [server/app/services/auth_service.py L9-L55](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/services/auth_service.py#L9-L55)

**Sources:** [server/app/routers/auth.py L20-L22](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L20-L22)

 [server/app/schemas/user.py L30-L37](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/user.py#L30-L37)

 [server/app/services/auth_service.py L9-L55](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/services/auth_service.py#L9-L55)

---

#### POST /api/auth/register

Creates a new user account.

**Request Body:**

```

```

**Response (200 OK):**

```

```

**Error Responses:**

* `400 Bad Request`: Email already registered

**Implementation:** [server/app/routers/auth.py L24-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L24-L51)

**Sources:** [server/app/routers/auth.py L24-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L24-L51)

 [server/app/schemas/user.py L16-L17](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/user.py#L16-L17)

---

#### GET /api/auth/me

Retrieves the current authenticated user's profile.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Response (200 OK):**

```

```

**Error Responses:**

* `401 Unauthorized`: Invalid or expired token
* `404 Not Found`: User not found

**Implementation:** [server/app/routers/auth.py L53-L76](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L53-L76)

**Client Usage:** [client/lib/core/services/profile_service.dart L11-L30](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/profile_service.dart#L11-L30)

**Sources:** [server/app/routers/auth.py L53-L76](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L53-L76)

 [client/lib/core/services/profile_service.dart L11-L30](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/profile_service.dart#L11-L30)

---

#### PUT /api/auth/me

Updates the current user's profile information.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Request Body (all fields optional):**

```

```

**Response (200 OK):** Returns updated `UserResponse`

**Implementation:** [server/app/routers/auth.py L78-L114](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L78-L114)

**Client Usage:** [client/lib/core/services/profile_service.dart L32-L68](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/profile_service.dart#L32-L68)

**Sources:** [server/app/routers/auth.py L78-L114](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L78-L114)

 [server/app/schemas/user.py L42-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/user.py#L42-L48)

---

#### POST /api/auth/me/change-password

Changes the current user's password.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Request Body:**

```

```

**Response (200 OK):**

```

```

**Error Responses:**

* `400 Bad Request`: Incorrect current password
* `401 Unauthorized`: Invalid token

**Implementation:** [server/app/routers/auth.py L116-L156](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L116-L156)

**Client Usage:** [client/lib/core/services/profile_service.dart L70-L94](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/profile_service.dart#L70-L94)

**Sources:** [server/app/routers/auth.py L116-L156](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L116-L156)

 [server/app/schemas/user.py L50-L52](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/user.py#L50-L52)

---

## Inventory Management Endpoints

### Inventory Item Operations

#### GET /api/inventory/

Retrieves inventory items with optional filtering.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Query Parameters:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `search` | string | No | Search by name, internal_code, or category |
| `environment_id` | uuid | No | Filter by environment |
| `system_wide` | boolean | No | Admin_general: view all environments |
| `admin_access` | boolean | No | Admin_general: bypass environment filter |

**Response (200 OK):**

```

```

**Role-Based Filtering:**

* `student`, `instructor`, `admin`, `supervisor`: Only see items in their assigned environment
* `admin_general`: Can see all items across all environments when `system_wide=true`

**Implementation:** [server/app/routers/inventory.py L14-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L48)

**Sources:** [server/app/routers/inventory.py L14-L48](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L14-L48)

---

#### GET /api/inventory/{item_id}

Retrieves a specific inventory item by ID.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Response (200 OK):** Returns single `InventoryItemResponse`

**Error Responses:**

* `404 Not Found`: Item not found

**Implementation:** [server/app/routers/inventory.py L50-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L50-L58)

**Sources:** [server/app/routers/inventory.py L50-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L50-L58)

---

#### POST /api/inventory/

Creates a new inventory item.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Roles:** `supervisor`, `admin`, `admin_general`

**Request Body:**

```

```

**Response (200 OK):** Returns created `InventoryItemResponse`

**Error Responses:**

* `403 Forbidden`: Unauthorized role

**Implementation:** [server/app/routers/inventory.py L60-L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L60-L73)

**Sources:** [server/app/routers/inventory.py L60-L73](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L60-L73)

---

#### PUT /api/inventory/{item_id}

Updates an existing inventory item.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Roles:** `supervisor`, `admin`, `admin_general`

**Request Body:** All fields optional, same structure as POST

**Status Auto-Update Logic:**

* If `quantity_missing > 0`: status → `missing`
* Else if `quantity_damaged > 0`: status → `damaged`
* Else if both are `0`: status → `available`

**Response (200 OK):** Returns updated `InventoryItemResponse`

**Implementation:** [server/app/routers/inventory.py L75-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L75-L103)

**Sources:** [server/app/routers/inventory.py L75-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L75-L103)

---

#### PUT /api/inventory/{item_id}/verification

Lightweight update endpoint for inventory verification workflow. Accepts only quantity and status fields.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Roles:** All authenticated users

**Request Body:**

```

```

**Implementation:** [server/app/routers/inventory.py L105-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L105-L138)

**Sources:** [server/app/routers/inventory.py L105-L138](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L105-L138)

---

#### DELETE /api/inventory/{item_id}

Deletes an inventory item.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Roles:** `supervisor`, `admin`, `admin_general`

**Response (200 OK):**

```

```

**Implementation:** [server/app/routers/inventory.py L140-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L140-L159)

**Sources:** [server/app/routers/inventory.py L140-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L140-L159)

---

## Maintenance Request Endpoints

### Request Management

#### GET /api/maintenance-requests/

Retrieves maintenance requests with role-based filtering.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Query Parameters:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `item_id` | uuid | No | Filter by specific item |
| `status` | string | No | Filter by status |
| `environment_id` | uuid | No | Filter by environment |
| `system_wide` | boolean | No | Admin_general: view all |
| `admin_access` | boolean | No | Admin_general: bypass filter |

**Response (200 OK):**

```

```

**Role-Based Access:**

* `student`, `instructor`: Only see requests in their environment
* `supervisor`, `admin`: See all requests in their environment or specify `environment_id`
* `admin_general`: See all requests system-wide

**Implementation:** [server/app/routers/maintenance_requests.py L16-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L16-L51)

**Sources:** [server/app/routers/maintenance_requests.py L16-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L16-L51)

---

#### POST /api/maintenance-requests/

Creates a new maintenance request and notifies supervisors.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Request Body:**

```

```

**Notification Logic:**

* Creates notifications for all supervisors in the same environment
* Also notifies supervisors with no specific environment (general supervisors)
* Priority `urgent` creates high-priority notifications

**Response (201 Created):** Returns created `MaintenanceRequestResponse`

**Implementation:** [server/app/routers/maintenance_requests.py L53-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L107)

**Sources:** [server/app/routers/maintenance_requests.py L53-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L53-L107)

---

#### PUT /api/maintenance-requests/{request_id}

Updates a maintenance request and notifies the original requester.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Roles:** `admin`, `supervisor`

**Request Body:** All fields optional

```

```

**Status Change Notifications:**
When status changes, the system creates a notification for the original requester with contextual messages:

* `in_progress`: "Su solicitud de mantenimiento está siendo procesada"
* `completed`: "Su solicitud de mantenimiento ha sido completada"
* `cancelled`: "Su solicitud de mantenimiento ha sido cancelada"
* `on_hold`: "Su solicitud de mantenimiento está en espera"

**Implementation:** [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)

**Sources:** [server/app/routers/maintenance_requests.py L109-L155](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L109-L155)

---

#### DELETE /api/maintenance-requests/{request_id}

Deletes a maintenance request.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Roles:** `admin`, `supervisor`

**Response (200 OK):**

```

```

**Implementation:** [server/app/routers/maintenance_requests.py L157-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L157-L173)

**Sources:** [server/app/routers/maintenance_requests.py L157-L173](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L157-L173)

---

## Notification Endpoints

### User Notifications

#### GET /api/notifications/

Retrieves all notifications for the current user.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Response (200 OK):**

```

```

**Implementation:** [server/app/routers/notifications.py L14-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L14-L24)

**Sources:** [server/app/routers/notifications.py L14-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L14-L24)

---

#### POST /api/notifications/

Creates a new notification (typically used internally by other endpoints).

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Request Body:**

```

```

**Response (201 Created):** Returns created `NotificationResponse`

**Implementation:** [server/app/routers/notifications.py L26-L41](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L26-L41)

**Sources:** [server/app/routers/notifications.py L26-L41](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L26-L41)

---

#### PUT /api/notifications/{notif_id}

Updates a notification (typically to mark as read).

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Request Body:**

```

```

**Security:** Users can only update their own notifications

**Response (200 OK):** Returns updated `NotificationResponse`

**Implementation:** [server/app/routers/notifications.py L43-L70](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L43-L70)

**Sources:** [server/app/routers/notifications.py L43-L70](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/notifications.py#L43-L70)

---

## Feedback System Endpoints

### Feedback Submission & Management

#### POST /api/feedback/

Submits user feedback.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Request Body:**

```

```

**Validation:**

* `type` must be one of: `bug`, `suggestion`, `feature`, `compliment`, `complaint`, `other`
* `priority` must be one of: `low`, `medium`, `high`
* `rating` must be between 1 and 5 if provided

**Response (201 Created):**

```

```

**Implementation:** [server/app/routers/feedback.py L54-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L54-L107)

**Client Usage:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L626-L685](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L626-L685)

**Sources:** [server/app/routers/feedback.py L54-L107](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L54-L107)

 [client/lib/presentation/screens/feedback/feedback_form_screen.dart L626-L685](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L626-L685)

---

#### GET /api/feedback/

Retrieves feedback submissions for the current user.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Query Parameters:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `skip` | integer | No | Pagination offset (default: 0) |
| `limit` | integer | No | Max results (1-100, default: 10) |
| `type` | string | No | Filter by feedback type |
| `status` | string | No | Filter by status |

**Response (200 OK):** Returns array of `FeedbackResponse` objects ordered by most recent first

**Implementation:** [server/app/routers/feedback.py L109-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L109-L134)

**Client Usage:** [client/lib/presentation/screens/feedback/feedback_form_screen.dart L83-L119](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/feedback/feedback_form_screen.dart#L83-L119)

**Sources:** [server/app/routers/feedback.py L109-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L109-L134)

---

#### GET /api/feedback/all

Retrieves all feedback submissions across all users (admin_general only).

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Role:** `admin_general`

**Query Parameters:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `skip` | integer | No | Pagination offset (default: 0) |
| `limit` | integer | No | Max results (1-200, default: 50) |
| `type` | string | No | Filter by feedback type |
| `status` | string | No | Filter by status |
| `priority` | string | No | Filter by priority |

**Response (200 OK):** Returns array of all feedback ordered by priority and date

**Implementation:** [server/app/routers/feedback.py L136-L174](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L136-L174)

**Sources:** [server/app/routers/feedback.py L136-L174](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L136-L174)

---

#### GET /api/feedback/{feedback_id}

Retrieves a specific feedback submission.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Security:** Users can only view their own feedback unless they are `admin_general`

**Response (200 OK):** Returns single `FeedbackResponse`

**Error Responses:**

* `403 Forbidden`: Cannot view another user's feedback
* `404 Not Found`: Feedback not found

**Implementation:** [server/app/routers/feedback.py L176-L199](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L176-L199)

**Sources:** [server/app/routers/feedback.py L176-L199](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L176-L199)

---

#### PUT /api/feedback/{feedback_id}

Updates feedback status and admin response (admin_general only).

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Required Role:** `admin_general`

**Request Body:**

```

```

**Response (200 OK):** Returns updated `FeedbackResponse`

**Implementation:** [server/app/routers/feedback.py L201-L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L201-L233)

**Sources:** [server/app/routers/feedback.py L201-L233](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L201-L233)

---

#### DELETE /api/feedback/{feedback_id}

Deletes a feedback submission.

**Headers:**

```yaml
Authorization: Bearer <access_token>
```

**Security:** Users can delete their own feedback, or `admin_general` can delete any

**Response (200 OK):**

```

```

**Implementation:** [server/app/routers/feedback.py L235-L261](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L235-L261)

**Sources:** [server/app/routers/feedback.py L235-L261](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L235-L261)

---

## Role-Based Access Control Matrix

The following table summarizes endpoint access by role:

```

```

**Sources:** [server/app/routers/inventory.py L21-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L21-L36)

 [server/app/routers/maintenance_requests.py L24-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L24-L43)

 [server/app/routers/feedback.py L116-L152](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L116-L152)

### Endpoint Authorization Table

| Endpoint | student | instructor | supervisor | admin | admin_general |
| --- | --- | --- | --- | --- | --- |
| `POST /api/auth/login` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `POST /api/auth/register` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `GET /api/auth/me` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `PUT /api/auth/me` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `GET /api/inventory/` | ✓ (env) | ✓ (env) | ✓ (env) | ✓ (env) | ✓ (all) |
| `POST /api/inventory/` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `PUT /api/inventory/{id}` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `DELETE /api/inventory/{id}` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `PUT /api/inventory/{id}/verification` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `GET /api/maintenance-requests/` | ✓ (env) | ✓ (env) | ✓ (env) | ✓ (env) | ✓ (all) |
| `POST /api/maintenance-requests/` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `PUT /api/maintenance-requests/{id}` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `DELETE /api/maintenance-requests/{id}` | ✗ | ✗ | ✓ | ✓ | ✓ |
| `GET /api/feedback/` | ✓ (own) | ✓ (own) | ✓ (own) | ✓ (own) | ✓ (own) |
| `GET /api/feedback/all` | ✗ | ✗ | ✗ | ✗ | ✓ |
| `PUT /api/feedback/{id}` | ✗ | ✗ | ✗ | ✗ | ✓ |
| `GET /api/notifications/` | ✓ (own) | ✓ (own) | ✓ (own) | ✓ (own) | ✓ (own) |

**Legend:**

* ✓ = Allowed
* ✗ = Forbidden
* (env) = Environment-scoped
* (all) = System-wide access
* (own) = User's own data only

**Sources:** [server/app/routers/inventory.py L21-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L21-L36)

 [server/app/routers/inventory.py L66-L67](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L66-L67)

 [server/app/routers/inventory.py L82-L83](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L82-L83)

 [server/app/routers/maintenance_requests.py L116-L117](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/maintenance_requests.py#L116-L117)

 [server/app/routers/feedback.py L148-L152](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L148-L152)

---

## Common Response Schemas

### Error Response Format

All error responses follow this structure:

```

```

**HTTP Status Codes:**

* `400 Bad Request`: Invalid input data
* `401 Unauthorized`: Missing or invalid authentication token
* `403 Forbidden`: Insufficient permissions for the operation
* `404 Not Found`: Resource not found
* `500 Internal Server Error`: Server error

**Sources:** [server/app/routers/auth.py L28-L31](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L28-L31)

 [server/app/routers/inventory.py L33-L36](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L33-L36)

---

### Pagination Pattern

Endpoints supporting pagination use these query parameters:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `skip` | integer | 0 | Number of records to skip |
| `limit` | integer | Varies | Maximum records to return |

**Example:**

```
GET /api/feedback/?skip=20&limit=10
```

**Sources:** [server/app/routers/feedback.py L111-L112](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/feedback.py#L111-L112)

---

### Timestamp Format

All datetime fields use ISO 8601 format with UTC timezone:

```
2024-01-15T14:30:00.000000Z
```

**Sources:** [server/app/schemas/user.py L22-L24](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/schemas/user.py#L22-L24)

---

## Client API Constants Reference

The Flutter client defines all API endpoints as constants for consistency:

```

```

**Usage in Client:**

```

```

**Sources:** [client/lib/core/constants/api_constants.dart L1-L19](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/constants/api_constants.dart#L1-L19)

---

## Authentication Implementation Details

### JWT Token Structure

The system uses JWT tokens with the HS256 algorithm:

**Token Payload:**

```

```

**Token Validation:**

1. Extract token from `Authorization: Bearer <token>` header
2. Decode using `settings.SECRET_KEY` and `settings.ALGORITHM`
3. Extract `sub` (user ID) and `role` from payload
4. Query database to verify user exists and is active

**Implementation:** [server/app/routers/auth.py L54-L70](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L54-L70)

**OAuth2 Scheme Configuration:**

```

```

**Sources:** [server/app/routers/auth.py L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L18-L18)

 [server/app/routers/auth.py L54-L70](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L54-L70)

---

### get_current_user Dependency

Most protected endpoints use the `get_current_user` dependency:

```

```

**Dependency Chain:**

```

```

**Sources:** [server/app/routers/auth.py L54-L76](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/auth.py#L54-L76)

 [server/app/routers/inventory.py L21](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory.py#L21-L21)

---

## Supporting Routers (Brief Reference)

The following routers are included in the system but not detailed in this reference:

### Users Router (/api/users/)

* User CRUD operations
* Role management
* Environment assignment

### Environments Router (/api/environments/)

* Physical location management
* Warehouse flagging
* Environment linking via QR codes

### Inventory Checks Router (/api/inventory-checks/)

* Create and manage verification sessions
* Three-stage approval workflow
* Schedule integration

See [Inventory Verification System](/axchisan/GestionInventarioSENA/5-inventory-verification-system) for workflow details.

### QR Code Router (/api/qr)

* QR code generation for items and environments
* Signature-based payload validation

See [QR Code System](/axchisan/GestionInventarioSENA/8-qr-code-system) for implementation details.

### Schedules Router (/api/schedules)

* Schedule CRUD operations
* Verification schedule management

### Loans Router (/api/loans/)

* Equipment loan requests
* Approval workflow
* Warehouse item access

### Statistics Router (/api/stats/)

* Real-time metrics
* Role-based KPIs
* Trend analysis

See [Statistics Dashboard](/axchisan/GestionInventarioSENA/9.3-statistics-dashboard) for usage details.

### Reports Router (/api/reports/)

* PDF/Excel report generation
* MinIO storage integration
* Background processing

See [Report Generation](/axchisan/GestionInventarioSENA/9.1-report-generation) for report types.

### Audit Logs Router (/api/audit-logs/)

* Request audit trail
* Compliance logging
* Activity search and export

See [Audit & Compliance](/axchisan/GestionInventarioSENA/10-audit-and-compliance) for compliance features.

### System Alerts Router (/api/system-alerts/)

* Critical inventory alerts
* Threshold-based notifications
* Alert history

### Alert Settings Router (/api/alert-settings/)

* Alert threshold configuration
* Notification preferences

### Supervisor Reviews Router (/api/supervisor-reviews/)

* Supervisor approval tracking
* Review history

### Inventory Check Items Router (/api/inventory-check-items/)

* Item-level verification data
* Verification status tracking

### Maintenance History Router (/api/maintenance-history/)

* Maintenance activity log
* Status change tracking

**Sources:** [server/app/main.py L20-L39](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L20-L39)

---

## API Testing & Development

### CORS Configuration

The API is configured to allow cross-origin requests:

```

```

**Sources:** [server/app/main.py L10-L16](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L10-L16)

---

### Audit Middleware

All API requests pass through `AuditMiddleware` for compliance logging:

```

```

This middleware:

* Logs all requests with user context
* Captures request body and response status
* Filters sensitive data (passwords)
* Stores audit trail in database

See [Audit Middleware](/axchisan/GestionInventarioSENA/10.1-audit-middleware) for implementation details.

**Sources:** [server/app/main.py L18](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L18-L18)

---

### Root Endpoint

```
GET /
```

Returns a welcome message:

```

```

**Sources:** [server/app/main.py L41-L43](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/main.py#L41-L43)

---

This API reference provides comprehensive documentation for all endpoints in the SENA Inventory Management System. For implementation details on specific workflows, refer to the related wiki pages linked throughout this document.